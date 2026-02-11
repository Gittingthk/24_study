좋아. 그대로 **논문/프로토콜에 바로 붙일 수 있는 Markdown 구조**로 정리해줄게.  
(수식은 LaTeX 형식으로 유지)

---

# Feature Definition for FT-based Boundary-Aware SWE

---

# 0️⃣ 기본 전제 (공통 기반)

### 입력 데이터

[  
v(z, x, t) \quad \text{(axial velocity)}  
]

### ST-map 정의

[  
S(x, t) = \mathrm{mean}_z , |v(z,x,t)|  
]  
또는  
[  
S(x, t) = \max_z |v(z,x,t)|  
]

### Ridge 정의

ST-map에서 최대 에너지 또는 위상 일관성을 따라 형성되는 전파 궤적

[  
x(t) \quad \text{또는} \quad t(x)  
]

---

# 1️⃣ ST-map / Propagation 기반 Feature (8)

---

## 1. Mean Phase Velocity (Ridge Slope)

### 의미

- 주 전파 모드의 평균 속도
    
- Bulk + guided 모드가 혼합된 effective phase velocity
    

### 계산

1. ST-map에서 ridge 추출 (RANSAC 또는 max-energy tracking)
    
2. 선형 근사:
    

[  
x(t) = c_p t + b  
]

3. 기울기:
    

[  
c_p = \frac{dx}{dt}  
]

---

## 2. Std of Phase Velocity (Dispersion Strength)

### 의미

- Ridge의 곡률/휘어짐 → 주파수 의존 속도 차이
    
- FT-induced dispersion proxy
    

### 계산

- 시간 구간별 local slope 계산  
    [  
    c_p^{(i)}  
    ]
    
- 표준편차:  
    [  
    \sigma_{c_p} = \mathrm{std}(c_p^{(i)})  
    ]
    

---

## 3. Ridge Energy Ratio

### 의미

- 전체 에너지 중 전파 모드 에너지 비율
    

### 계산

[  
\frac{\sum_{(x,t)\in \text{ridge}} S(x,t)^2}  
{\sum_{x,t} S(x,t)^2}  
]

---

## 4. Ridge Continuity Score

### 의미

- Ridge가 끊기는 정도 (fragmentation index)
    

### 계산 예

- Ridge length / total time span  
    또는
    
- valid ridge point ratio
    

---

## 5. Group Velocity (ToF-based)

### 의미

- 에너지 packet 이동 속도
    
- Phase velocity와 의도적으로 구분
    

### 계산

1. 각 위치에서 peak arrival time:  
    [  
    t_{\text{peak}}(x)  
    ]
    
2. ToF 회귀:  
    [  
    t(x) = \frac{x}{c_g} + b  
    ]
    

---

## 6. Arrival Time Variance

### 의미

- Wavefront roughness
    

### 계산

[  
\mathrm{var}\left(t_{\text{peak}}(x)\right)  
]

---

## 7. Early/Late Energy Ratio

### 의미

- 초기 bulk vs 후기 반사/유도모드 비율
    

### 계산

[  
\frac{\sum_{t<t_0} S(x,t)^2}  
{\sum_{t>t_0} S(x,t)^2}  
]

---

## 8. Temporal Skewness

### 의미

- 시간 에너지 분포 비대칭성
    

### 계산

- 시간 marginal 분포의 skewness
    

---

# 2️⃣ Correlation / Coherence 기반 Feature (4)

---

## Cross-Correlation (CC) 정의

[  
\mathrm{CC}(x_1,x_2) =  
\max_{\tau}  
\frac{\langle v(x_1,t), v(x_2,t+\tau)\rangle}  
{|v_1||v_2|}  
]

### 물리적 의미

- 위상 일관성 (coherence)
    
- FT 증가 → 점탄성 증가 → CC 감소
    

---

## 9. Mean CC

[  
\mathrm{mean}\left(\mathrm{CC}(x_i,x_{i+1})\right)  
]

---

## 10. CC Drop Rate

거리 증가에 따른 지수적 감소 모델:

[  
\mathrm{CC}(x) \approx e^{-x/L_c}  
]

---

## 11. Axial vs Lateral CC Ratio

[  
\frac{\mathrm{CC}_{axial}}  
{\mathrm{CC}_{lateral}}  
]

→ Boundary-induced decorrelation 지표

---

## 12. Decorrelation Length

[  
L_c  
]

→ CC가 (1/e)로 감소하는 거리

---

# 3️⃣ Boundary / Mode-Sensitive Feature (4)

> 본 논문의 boundary-aware 핵심 feature

---

## 13. Guided-Mode Energy Ratio

### 의미

- Bulk vs Lamb-like 모드 에너지 비율
    

### 계산

[  
\frac{E_{\text{guided}}}  
{E_{\text{total}}}  
]

(DF mask 또는 ω–k domain 분리 후 계산)

---

## 14. Reflection Asymmetry Index

### 의미

- 좌/우 반사 에너지 비대칭
    

### 계산

[  
\frac{|E_{left} - E_{right}|}  
{E_{left} + E_{right}}  
]

---

## 15. Dispersion Curvature (ω–k Nonlinearity)

### 의미

- Phase velocity의 주파수 의존 비선형성
    

### 계산

1. ST-map → 2D FFT
    
2. ω–k ridge 추출
    
3. 2차 다항식 피팅 후 residual 계산
    

---

## 16. Free-Surface Sensitivity Index

### 의미

- ARF 위치(BG vs B)에 따른 feature 차이
    

### 계산

[  
|f_{BG} - f_{B}|  
]

---

# 🎯 요약

|Feature Group|물리적 질문|
|---|---|
|ST-map|파동이 어떻게 가는가|
|CC|얼마나 일관되게 가는가|
|Boundary/mode|경계가 어떻게 바꾸는가|

Freeze–Thaw 변화는 위 세 영역 모두에 흔적을 남기며,  
따라서 16-feature 구성은 FT-dependent boundary-aware signature를 정량화하는 구조로 타당하다.

---

원하면 다음 단계로:

- 🔹 Methods 섹션용 문장형 버전
    
- 🔹 코드용 `extract_features.m` 구조
    
- 🔹 발표용 한 장 요약 슬라이드 버전 (도식 + 정의 축약)
    

어디로 갈까?