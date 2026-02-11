MLP

좋아, 이제 **“논문에서 방어 가능한 정의” 기준으로** 하나씩 정리해줄게.  
아래 설명은 전부 **네가 이미 갖고 있는 데이터 흐름**  
(IQ → AxVel → ST-map → CC) 위에서 **추가 가정 없이** 얻을 수 있게 설명할게.

---

# 0️⃣ 기본 전제 (공통 기반)

- 입력 데이터  
    [  
    v(z, x, t) \quad \text{(axial velocity)}  
    ]
    
- ST-map  
    [  
    S(x, t) = \text{mean or max}_z , |v(z,x,t)|  
    ]
    
- ridge:  
    ST-map에서 **최대 에너지(또는 위상 일관성)**를 따라 형성되는 전파 궤적  
    → ( x(t) ) 또는 ( t(x) )
    

---

# 1️⃣ ST-map / propagation 기반 feature (8)

## 1. mean phase velocity (ridge slope)

**의미**

- ST-map에서 보이는 “주 전파 모드”의 평균 속도
    
- bulk + guided 모드가 섞인 **effective phase velocity**
    

**계산**

1. ST-map에서 ridge 추출 (RANSAC or max-energy tracking)
    
2. 선형 근사  
    [  
    x(t) = c_p , t + b  
    ]
    
3. slope = ( c_p )
    

➡️ FT ↑ → 점탄성 ↑ → **phase velocity 감소 또는 불안정**

---

## 2. std of phase velocity (dispersion strength)

**의미**

- ridge가 “곧지 않다” → 주파수/모드별 속도 차이
    
- **FT-induced dispersion**의 proxy
    

**계산**

- ridge를 time window별로 local slope 계산
    
- 표준편차:  
    [  
    \sigma_{c_p} = \mathrm{std}(c_p^{(i)})  
    ]
    

➡️ FT ↑ → microstructure ↑ → dispersion ↑

---

## 3. ridge energy ratio

**의미**

- 전체 에너지 중 “전파 가능한 모드”가 차지하는 비율
    
- FT로 인해 산란/감쇠되면 감소
    

**계산**  
[  
\frac{\sum_{(x,t)\in \text{ridge}} S(x,t)^2}{\sum_{x,t} S(x,t)^2}  
]

---

## 4. ridge continuity score

**의미**

- ridge가 끊기는 정도
    
- FT ↑ → CC drop → ridge fragmentation
    

**계산 예**

- ridge length / total time span
    
- 또는 valid ridge point 비율
    

---

## 5. group velocity (ToF-based)

**의미**

- 에너지 packet 이동 속도
    
- phase velocity와 **의도적으로 다른 정보**
    

**계산**

1. 각 x에서 peak arrival time:  
    [  
    t_\text{peak}(x)  
    ]
    
2. ToF regression:  
    [  
    t(x) = \frac{x}{c_g} + b  
    ]
    

➡️ FT 변화에 더 둔감할 수도 → **phase vs group 차이가 중요한 feature**

---

## 6. arrival time variance

**의미**

- 도달 시간의 공간적 불균일성
    
- FT로 인해 **wavefront roughness** 증가
    

**계산**  
[  
\mathrm{var}\big(t_\text{peak}(x)\big)  
]

---

## 7. early / late energy ratio

**의미**

- 초기 bulk wave vs 후기 반사/유도모드 비율
    
- boundary sensitivity 직접 반영
    

**계산**  
[  
\frac{\sum_{t < t_0} S(x,t)^2}{\sum_{t > t_0} S(x,t)^2}  
]

---

## 8. temporal skewness

**의미**

- 파형 에너지 분포의 비대칭성
    
- FT ↑ → energy tail 길어짐
    

**계산**

- 시간축 marginal 분포의 skewness
    

---

# 2️⃣ Correlation / coherence 기반 feature (4)

## CC란 뭔데?

**정의**

- 이웃 위치에서의 velocity time signal 유사도  
    [  
    \mathrm{CC}(x_1,x_2) = \max_\tau  
    \frac{\langle v(x_1,t), v(x_2,t+\tau)\rangle}{|v_1||v_2|}  
    ]
    

**물리적 의미**

- 파동이 **위상 일관성(coherence)**을 유지하며 전파되는가
    
- FT ↑ → 점탄성/산란 ↑ → CC ↓
    

---

## 9. mean CC

- 모든 인접 쌍의 평균 CC  
    ➡️ propagation quality
    

---

## 10. CC drop rate

**의미**

- 거리 증가에 따른 coherence 붕괴 속도
    

**계산**  
[  
\mathrm{CC}(x) \approx e^{-x/L_c}  
]

---

## 11. axial vs lateral CC ratio

**의미**

- axial은 안정, lateral은 경계 영향 큼  
    → **boundary-induced decorrelation** 지표
    

---

## 12. decorrelation length

**의미**

- CC가 (1/e)로 감소하는 거리
    
- effective propagation length
    

---

# 3️⃣ Boundary / mode-sensitive feature (4)

> ⚠️ 이 4개가 **“우리는 경계를 쓰는 논문이다”**의 핵심

---

## 13. guided-mode energy ratio

**의미**

- bulk vs guided(Lamb-like) 모드 에너지 분리
    
- FT에 따라 guided 모드 점유율 변화
    

**계산**

- DF / FFT mask 후  
    [  
    \frac{E_{\text{guided}}}{E_{\text{total}}}  
    ]
    

---

## 14. reflection asymmetry index

**의미**

- 경계 반사로 인한 좌/우 비대칭
    
- ARF 위치 민감
    

**계산**  
[  
\frac{|E_\text{left} - E_\text{right}|}{E_\text{left} + E_\text{right}}  
]

---

## 15. dispersion curvature (ω–k nonlinearity)

**의미**

- phase velocity가 주파수에 따라 얼마나 비선형인지
    
- FT ↑ → 점탄성 ↑ → curvature ↑
    

**계산**

- ST-map → 2D FFT
    
- ridge in ( \omega\text{-}k )
    
- second-order fit residual
    

---

## 16. free-surface sensitivity index

**의미**

- “같은 FT인데 ARF 위치(BG vs B)에 따라 얼마나 달라지나?”
    
- **boundary-aware의 결정타 feature**
    

**계산**  
[  
|f_\text{BG} - f_\text{B}|  
]

---

# 🎯 한 문장으로 요약하면

- **ST-map features**: “파동이 어떻게 가는가”
    
- **CC features**: “얼마나 일관되게 가는가”
    
- **Boundary/mode features**: “경계가 그 파동을 어떻게 바꾸는가”
    

➡️ **FT는 세 영역 모두에 흔적을 남김**  
➡️ 그래서 16 feature 설계는 논리적으로 매우 탄탄함

---

다음 단계로 내가 바로 해줄 수 있는 것:

1. `extract_features.m` **함수 스켈레톤 (입력/출력 명확)**
    
2. 각 feature별 **QC threshold 추천**
    
3. 이걸 그대로 쓸 수 있는 **Methods 문단 초안**
    

👉 다음으로 **코드부터 갈까, 논문 문장부터 갈까?**