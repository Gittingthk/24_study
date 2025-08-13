---
id: -1
tags: ""
is_public: true
last_synced: ""
---
좋아—**비동기 시퀀스(100 fps)로 RF를 잘 모으고/보내고/처리**하려면 아래 것들만 챙기면 된다. “왜 필요한지 + 간단 공식/룰”까지 붙였어.

---

# 1) 타이밍/레이트(PRF, timeToNextAcq)

- **PRF 결정**: `timeToNextAcq = T_acq` → `PRF = 1/T_acq`
    
    - 에코가 다 돌아올 **최소 시간**을 보장:
        
        Tacq  ≥  Tlisten+여유,    Tlisten=NfsT_{acq} \;\ge\; T_{\text{listen}} + \text{여유} \quad,\;\; T_{\text{listen}}=\frac{N}{f_s}Tacq​≥Tlisten​+여유,Tlisten​=fs​N​
        
        또는 깊이 기준: Tacq≥2zmax⁡c+여유T_{acq}\ge \frac{2z_{\max}}{c}+\text{여유}Tacq​≥c2zmax​​+여유.
        
- **깊이↔PRF 상한**: PRF≤c2zmax⁡PRF \le \tfrac{c}{2z_{\max}}PRF≤2zmax​c​. 깊이 커지면 PRF 낮춰야 **range ambiguity** 방지.
    
- **Doppler(참고)**: vmax⁡≈PRF⋅c4f0v_{\max}\approx \dfrac{PRF \cdot c}{4 f_0}vmax​≈4f0​PRF⋅c​ (파이프라인 설계 시 과도한 PRF 하락 주의).
    

---

# 2) 샘플링/깊이(fs, N, 대역폭)

- **Nyquist(직접 RF 샘플링)**: fs≥2(f0+B2)f_s \ge 2\bigl(f_0+\tfrac{B}{2}\bigr)fs​≥2(f0​+2B​). 짧은 펄스(대역폭↑)면 **fs 요구↑**.
    
- **수신 시간창**: Tlisten=N/fsT_{\text{listen}} = N/f_sTlisten​=N/fs​ → 깊이 zmax⁡≈c Tlisten2z_{\max}\approx \dfrac{c\,T_{\text{listen}}}{2}zmax​≈2cTlisten​​.
    
- **N 선택 감**: 2048(얕은), 4096(중간), 8192(깊은). 2의 거듭제곱은 FFT·DSP에 유리.
    

---

# 3) 송신 파형(f₀, cycles, 에너지)

- **cycles 줄이면** 대역폭↑, 축해상도↑, **SNR↓**.
    
- **cycles 늘리면** 대역폭↓, 해상도↓, **SNR/침투↑**.
    
- 제약: **출력/MI/TI**(안전), **드라이브 전압/듀티**(발열), **프로브 대역폭**.
    
- 필요 시 **코딩(Chirp, Barker/Golay) + 압축**으로 “해상도+SNR” 동시 노림(처리 복잡도↑).
    

---

# 4) 빔포밍/지오메트리

- **TX 지연**: `computeTXDelays`로 포커스/플레인웨이브 세팅.
    
- **RX 동적 포커싱/Apodization**: 축해상도/사이드로브 제어.
    
- **각도 합성(plane-wave compounding)**: 화질↑ vs 프레임레이트↓—PRF/버퍼 여유 고려.
    

---

# 5) 수신 경로/게인·필터

- **TGC**: 심도별 이득 곡선 적정(과증폭→클리핑/노이즈↑).
    
- **InputFilter/LowPassCoef**: 안티앨리어싱·대역 제한.
    
- **클리핑 방지**: 전압/게인 적정, `Receive.Apod` 확인.
    
- **sampleMode**(대역/정밀도 옵션) 일관성 유지.
    

---

# 6) 버퍼링/전송(DMA) 설계

- **numFrames 충분히 크게**(더블/트리플 이상 → 실전 32~128 권장).
    
    - 거친 추산:
        
        Nframes  ≳  1+max⁡(TDMA, Tproc)Tacq+여유(1 2)N_{\text{frames}} \;\gtrsim\; 1+\frac{\max(T_{DMA},\,T_{proc})}{T_{acq}} + \text{여유(1~2)}Nframes​≳1+Tacq​max(TDMA​,Tproc​)​+여유(1 2)
- **전송 단위**: 프레임 단위 `transferToHost`(자잘한 쪼개기 금지 → 오버헤드↑).
    
- **처리 전략(비동기)**: “**가장 최근 DMA 완료 프레임만** 처리·표시”. 느려도 최신성 유지.
    

**대역폭/메모리 계산 예**

- 한 프레임 크기 = rowsPerFrame × colsPerFrame × 2 bytes(int16)
    
    - 예) 2048×128×2 ≈ **0.5 MiB**
        
- 전송량(100 fps) ≈ 50 MiB/s (여유/메타 제외). 디스크·RAM·PCIe 여유 확인.
    

---

# 7) 이벤트/시퀀스 안전장치

- **Async 패턴**: `rcv(i)`→`transferToHost`→(다음 프레임)… **처리 이벤트는 분리**.
    
- **동기 명령 생략**: 최신 프레임만 처리하면 `waitForTransferComplete/markTransferProcessed/sync` 불필요(덮어쓰기 윈도 확인).
    
- **인덱스 주의**: `Receive(i).framenum=i`, 순서 섞지 말 것. 잘못 점프하면 교착/타임아웃.
    

---

# 8) 실시간 표시/GUI

- **UI 슬라이더/에디트 박스**: `VsSlider` + `UIValue` → `assignin('base','myPlotChnl',…)`.
    
- **콜백 관리**: `text2cell`로 가독성↑, `/tmp` 생성 코드 `edit`로 디버그.
    
- **Freeze/RcvDataLoop**: 실수집 정지→버퍼 반복 재생, `simulateMode=2`와 유사.
    

---

# 9) 재현성/데이터 자산화

- **저장**: `save('MyRcvData.mat','RcvData','-v7.3')` + 메타(`Resource`, `Trans`, `Receive`, `TW`, `TX`, `TGC`, `lastFrame`).
    
- **재실행(오프라인)**: `load` 후 `simulateMode=2`—하드웨어 없이 동일 처리.
    

---

# 10) 신호처리/AI 파이프라인

- **전처리**: IQ(힐버트)→엔벌로프→로그압축→정규화.
    
- **실시간**: MATLAB↔Python(Engine/소켓) 스트리밍, 최신 프레임만 추론.
    
- **학습**: 라벨/메타 동기, 데이터 분할, 증강(깊이·게인·노이즈).
    

---

# 11) 시스템·안전·품질

- **SpeedOfSound**(기본 1540 m/s) 오차 → 깊이 스케일 틀어짐.
    
- **MI/TI/출력 제한** 준수(발열·안전).
    
- **클럭/지터**·동기(외부 트리거) 필요 시 TTL/Sync 사용.
    
- **디버그**: `tic/toc`로 처리시간 측정, `keyboard` 중단점, `lastFrame` 확인.
    

---

## 마지막 체크(한 줄 요약)

- **밖(주기)**: `timeToNextAcq`가 에코·DMA·여유를 커버하도록 설정(→ PRF 확정).
    
- **안(샘플링)**: fs,Nf_s, Nfs​,N으로 대역폭·깊이 충족(Nyquist).
    
- **버퍼**는 넉넉히(32~128), **DMA는 프레임 단위**, **처리는 최신 프레임만**.
    
- **파형 cycles/게인/안전**은 해상도–SNR–침투 트레이드오프에 맞춰 조정.
    

필요하면 네 현재 스크립트 값을 넣어 **권장 PRF·N·numFrames·메모리/전송량 표**로 바로 계산해 줄게.

Ask ChatGPT