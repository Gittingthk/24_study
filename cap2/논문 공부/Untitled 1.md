## B-mode 가이드 SWI 물성 분석 + ML/DL 워크플로우

---

## 0단계 | 연구 질문 고정 (제일 중요)

✔ **Research Question 1 (핵심)**

> _Can structural information from B-mode imaging guide the interpretation of shear wave propagation patterns for mechanical property estimation?_

✔ **Research Question 2 (보조)**

> _Can learning-based models reduce the scale mismatch between bulk mechanical testing (UTM) and localized SWI measurements?_

☐ 위 두 문장을 **논문 끝날 때까지 변경 금지**

---

## 1단계 | 실험 설계 & 데이터 구조 고정

### 1-1. Phantom 설계

✔ PVA freeze–thaw cycle 명확화 (예: 1, 3, 5 cycles)  
✔ geometry 고정 (두께, 직경, boundary)  
✔ embedding condition 기록 (gel, contact condition)

☐ variability source 목록화

- boundary reflection
    
- heterogeneity
    
- shear attenuation
    

---

### 1-2. UTM 프로토콜 (Ground Truth)

✔ preload 정의  
✔ strain rate 고정 (or head speed + 환산)  
✔ Young’s modulus extraction window 고정  
✔ **bulk modulus vs local stiffness 구분 명시**

☐ 동일 샘플 n ≥ 3 반복

---

### 1-3. Ultrasound 데이터 수집

✔ 동일 probe / depth / focus  
✔ 동일 ROI physical size (mm 기준)  
✔ B-mode + SWI **동일 좌표계** 확보

☐ raw data 구조 예시:

`Sample_01/  ├─ Bmode/  ├─ SWI_velocity/  ├─ SWI_displacement/  └─ UTM.csv`

---

## 2단계 | B-mode 기반 구조 정보 추출 (방향 2)

### 2-1. ROI 정의 전략

✔ **B-mode 기반 ROI 정의**

- boundary-aware
    
- artifact 제외
    

☐ 수동 vs semi-auto 구분

---

### 2-2. 구조 feature (석사 적정선)

✔ texture features (GLCM)  
✔ intensity statistics  
✔ boundary curvature / thickness

☐ feature 수 제한 (≤ 30)

🔒 end-to-end CNN은 나중에

---

## 3단계 | SWI propagation 패턴 분석 (방향 2 핵심)

### 3-1. 단순 평균 ❌

✔ spatial gradient of velocity  
✔ heterogeneity index  
✔ propagation symmetry / attenuation proxy

예:

- velocity variance
    
- depth-wise decay slope
    

☐ “왜 평균만 쓰면 안 되는지” 그림으로 설명

---

### 3-2. 물리적 해석 연결

✔ shear wave speed ≠ bulk modulus  
✔ boundary & geometry effect 명시

👉 **여기서 물리 기반 감점 방지**

---

## 4단계 | Physics-guided ML 설계 (방향 3)

### 4-1. 모델 포지션 명확화

✔ pure black-box ❌  
✔ **physics-aware feature + ML** ⭕

예:

- 입력:
    
    - B-mode structure features
        
    - SWI propagation features
        
- 출력:
    
    - UTM Young’s modulus
        

---

### 4-2. 모델 선택 (석사 현실적)

✔ Random Forest  
✔ SVR  
✔ shallow NN

☐ CNN은 비교용 1개만

---

### 4-3. Physics constraint 적용

✔ feature normalization (dimensionless)  
✔ monotonicity check (cycle ↑ → stiffness ↑)

🔒 PINN loss coupling은 확장용

---

## 5단계 | Scale mismatch 문제 해결 (방향 4)

### 5-1. 명시적으로 선언

✔ “UTM = bulk”  
✔ “SWI = local effective stiffness”

☐ reviewer가 좋아하는 문장:

> _We explicitly acknowledge the scale mismatch rather than assuming direct equivalence._

---

### 5-2. AI의 역할 정의

✔ AI = **mapping function**  
✔ SWI 단독으로 안 맞는 이유 제시  
✔ B-mode가 보정 정보 역할

---

## 6단계 | 검증 & 비교 실험

### 6-1. Baseline 필수

✔ SWI mean → linear regression  
✔ SWI features only → ML  
✔ **B-mode + SWI → ML (proposed)**

---

### 6-2. 통계

✔ RMSE  
✔ R²  
✔ Bland–Altman (가능하면)

☐ p-value 남용 금지

---

## 7단계 | 논문 구조 (거의 그대로 씀)

1. **Introduction**
    
    - SWI 한계
        
    - B-mode는 왜 쓸모없는 게 아니라 “미사용 자원”인지
        
2. **Methods**
    
    - Phantom
        
    - UTM
        
    - Ultrasound
        
    - Feature extraction
        
    - ML
        
3. **Results**
    
    - 패턴 시각화
        
    - scale mismatch 증명
        
    - AI 개선 효과
        
4. **Discussion**
    
    - 왜 평균이 안 되는가
        
    - 왜 구조 정보가 필요한가
        

---

## 8단계 | 석사 성공 기준 체크리스트

☐ “기존 연구 반복” 지적 피함  
☐ AI 없으면 성립 안 되는 구조  
☐ 물리적 설명 가능  
☐ phantom에서 논리 완결  
☐ human tissue로 확장 가능성 언급