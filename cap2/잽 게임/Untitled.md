아래 순서는 **“복싱 잽·어퍼컷·비정상 움직임(4 클래스) IMU 데이터셋”** 을 6주 내 확보한다는 가정을 두고 작성한 **엔드-투-엔드 수집·검증 프로세스 로드맵**입니다.  
(이미 사용 중인 Wear OS 워치 → FastAPI 서버 → CSV 저장 구조를 그대로 활용한다고 가정)

---

## 0. 목표 & 범위 확정 (~D-2)

|항목|결정 내용|
|---|---|
|**센서 사양**|워치 내 3축 ACC + 3축 GYRO, ±16 g / ±2000 dps|
|**샘플링 주파수**|**100 Hz** (50–200 Hz가 HAR에 표준, 배터리·대역폭 효율성 균형) ([Physical Activity and Health](https://paahjournal.com/articles/10.5334/paah.441?utm_source=chatgpt.com "Impact of Sensor-Axis Combinations on Machine Learning Accuracy ..."))|
|**윈도/오버랩**|1 s(100 샘플) 창, 50 % 오버랩 – 한 번의 펀치(≈0.3–0.6 s) 전체 포함|
|**라벨**|Strong-jab · Weak-jab · Uppercut · Abnormal(나머지)|
|**필요 양**|**10 분/클래스/사람 × 10명 ≈ 4 시간** 생 데이터 → 윈도 기준 **≥ 40 k** 샘플 (DeepConvLSTM 10⁵ ~ 10⁶ 파라미터 모델 안정 수렴선) ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC11484779/?utm_source=chatgpt.com "A large dataset of wrist-worn activity tracker data collected in the wild ..."), [ResearchGate](https://www.researchgate.net/publication/367315691_IMU-based_human_activity_recognition_and_payload_classification_for_low-back_exoskeletons?utm_source=chatgpt.com "(PDF) IMU-based human activity recognition and payload ..."))|

---

## 1. 하드웨어 & 소프트웨어 준비 (Week 1)

1. **워치 펌웨어**—센서 off-axis 보정, 타임스탬프 μs 단위 동기화.
    
2. **FastAPI 수집 엔드포인트 개선**
    
    - `/collect` POST Body에 `user_id`, `session_id`, `label` 필드 추가.
        
    - 서버 시계와 워치 시계 차이를 NTP로 매세션 리셋.
        
3. **실시간 모니터링 대시보드** – 스트림 값·패킷 loss·배터리 표시.
    

---

## 2. 프로토콜 설계 · IRB/동의서 (Week 1)

|세션 흐름|세부 단계|
|---|---|
|**A. 준비(5 min)**|착용 위치 고정·캘리브레이션 모션(정지 3 s → 팔 흔들기 3 s).|
|**B. 펀치 블록**|Strong-jab 20회 → 휴식 30 s → Weak-jab 20회 → … (각 블록 2 세트).|
|**C. Abnormal**|워치 착용 후 일상 동작(키보드, 걷기 등) 2 min × 3.|
|**D. 동영상 동기**|스마트폰 60 fps 영상 촬영 + 초시계 소리 ‘삑’ 동시 녹화(라벨 검증용).|

- **참가자**: 성별·키·체중 다양 10 명, 서면 동의·익명 코드화.
    
- **안전**: 장갑·손목 보호대, 휴식 사이클 포함.
    

---

## 3. 데이터 수집 운영 (Weeks 2-4)

1. **파일 구조 예시**
    

```
data/
 └─ subj01/
     └─ sess01/
         ├─ imu.csv      # 100 Hz, 6축 + timestamp
         ├─ meta.json    # {fps, label_map, device_fw, ...}
         └─ video.mp4
```

2. **현장 체크리스트**
    

|항목|Pass 기준|
|---|---|
|패킷 누락|< 0.5 %|
|시계 드리프트|< ±10 ms / 10 min|
|센서 포화|ACC < ±14 g, GYRO < ±1800 dps|
|라벨 타임코드|펀치 시작-끝 오차 < ±100 ms|

---

## 4. 라벨링 & QC (Weeks 3-5, 병행)

1. **1차 자동 태깅**: 세션 중 워치-UI 버튼으로 블록 단위 라벨 기록.
    
2. **2차 세밀 검수**:
    
    - 영상에서 프레임별 펀치 피크 찾기 → Python 스크립트로 CSV 타임스탬프 매칭.
        
    - `labeler.py`: 키프레임 클릭 → ±0.15 s 구간 라벨 수정.
        
3. **품질 메트릭**: 각 라벨 구간 내 가속도 peak-to-peak > 2 g (weak-jab는 1 g 초과) 규칙 기반 outlier 탐색 → 수작업 제외.
    

---

## 5. 데이터 정제 & 증강 (Week 5)

|단계|내용|
|---|---|
|**정규화**|센서별 `(x - μ) / σ` (훈련 세트 통계).|
|**증강**|Gaussian noise (σ=0.05 g), 랜덤 ±5° 회전, 시간-워핑 ±10 %.|
|**윈도 슬라이싱**|1 s / 0.5 s stride → `X.shape = (N, 100, 6)`.|
|**클래스 밸런스**|`RandomSampler(weights)` 또는 SMOTE-for-time-series.|

---

## 6. 베이스라인 학습 · 피드백 (Week 6)

1. **PyTorch DeepConvLSTM**
    
    - Adam 1e-3, 100 epoch, early-stop patience 10.
        
    - k-fold(5) cross-val → macro F1 기준.
        
2. **실시간 추론 검증** – Raspberry Pi 4 + ONNX Runtime (FP16) @ 10 ms/window.
    
3. **데이터 갭 분석** – confusion matrix에서 오판율 높은 동작 확인 → “Week 7 추가 촬영 목록” 작성.
    

---

## 7. PyTorch ↔ TensorFlow 차이 운영 팁

|단계|PyTorch 중심 개발|TensorFlow 활용 시 대안|
|---|---|---|
|**수집**|FastAPI → PyArrow → Torch DataLoader 직행 (`torch.from_numpy`)|`tf.data.Dataset.from_tensor_slices`|
|**증강**|`torchvision.transforms` 유사 Custom 1D 변환|`tf.signal` / `tf.keras.layers.Random*`|
|**훈련 해석**|Lightning + WandB 실험 추적|Keras Callbacks + TensorBoard|
|**엣지 배포**|ONNX → TensorRT, LibTorch C++|`tflite_convert` → TFLite Micro / Edge-TPU|

---

### 전체 일정 스냅샷

|Week|핵심 산출물|
|---|---|
|1|하드웨어 설정 완료, IRB 승인, 프로토콜 문서|
|2-4|원본 데이터(4 시간↑), 메타·영상 동기|
|3-5|검수 완료된 라벨 CSV, 정제·증강 스크립트|
|5-6|PyTorch 베이스라인 모델(.pt, .onnx), 성능 리포트|
|6 →|오류 분석 & 추가 수집/튜닝 사이클|

---

## 빠른 시작 체크리스트

-  워치 100 Hz raw 스트림 확인
    
-  `/collect` API 스키마 확장 & 로깅 테스트
    
-  참가자 10 명 모집(키·성별 다양)
    
-  세션 1차 파일 형태 샘플 생성 → QC 스크립트 통과
    
-  Lightning 훈련 스크립트 skeleton 준비
    

프로세스 중 막히는 단계(라벨링 GUI, QC 스크립트, ONNX 최적화 등)가 있으면 언제든 말씀 주세요. 필요한 코드·툴 모두 이어서 제공해 드리겠습니다!