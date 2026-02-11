좋아 🔥  
이번엔 **Obsidian Latex Suite 기준으로 바로 타이핑 가능한 수식** +  
**각 feature의 물리적 의미 설명**을 같이 정리해줄게.

(그대로 노트에 붙여도 되고, 발표 슬라이드 설명 스크립트로 써도 됨)

---

# 📌 0️⃣ 기본 전제

입력 데이터는 axial velocity:

```
dm
v(z,x,t)
```

ST-map 정의:

```
dm
S(x,t) = \mathrm{mean}_z |v(z,x,t)|
```

또는

```
S(x,t) = \max_z |v(z,x,t)|
```

---

# 1️⃣ ST-map / Propagation 기반 Feature (8)

---

## 1. Mean Phase Velocity (ridge slope)

```
dm
x(t) = c_p t + b
```

```
c_p = dx/dt
```

### 의미

- ST-map에서 보이는 **주 전파 궤적의 평균 기울기**
    
- 실제로는 bulk + guided 모드가 섞인 **effective phase velocity**
    
- FT 증가 → 점탄성 증가 → 속도 감소 또는 불안정
    

👉 “파동이 평균적으로 얼마나 빠르게 가는가”

---

## 2. Std of Phase Velocity (Dispersion Strength)

```
dm
\sigma_{c_p} = \mathrm{std}(c_p^{(i)})
```

### 의미

- 시간 구간별 local slope의 표준편차
    
- Ridge가 곧지 않으면 dispersion이 있다는 뜻
    
- FT 증가 → microstructure 복잡 → dispersion 증가
    

👉 “속도가 주파수에 따라 얼마나 흔들리는가”

---

## 3. Ridge Energy Ratio

```
dm
\frac{\sum_{(x,t)\in \text{ridge}} S(x,t)^2}
{\sum_{x,t} S(x,t)^2}
```

### 의미

- 전체 에너지 중 “전파 가능한 coherent ridge” 비율
    
- FT 증가 → 산란/감쇠 증가 → ridge energy 감소
    

👉 “전파 모드가 얼마나 또렷한가”

---

## 4. Ridge Continuity Score

(수식보다 정의가 중요)

```
ridge length / total time span
```

### 의미

- Ridge가 중간에 끊기는지 여부
    
- CC drop과 직결
    
- FT 증가 → ridge fragmentation 증가
    

👉 “전파가 중간에 무너지는가”

---

## 5. Group Velocity (ToF 기반)

```
dm
t(x) = \frac{x}{c_g} + b
```

### 의미

- 에너지 packet 이동 속도
    
- Phase velocity와 구분되는 정보
    
- FT에 덜 민감할 수도 → phase vs group 차이가 중요한 feature
    

👉 “에너지가 실제로 얼마나 빠르게 이동하는가”

---

## 6. Arrival Time Variance

```
dm
\mathrm{var}(t_{\text{peak}}(x))
```

### 의미

- 도달 시간의 공간적 불균일성
    
- Wavefront roughness
    
- FT 증가 → wavefront irregularity 증가
    

👉 “파동 front가 얼마나 고르지 않은가”

---

## 7. Early / Late Energy Ratio

```
dm
\frac{\sum_{t<t_0} S(x,t)^2}
{\sum_{t>t_0} S(x,t)^2}
```

### 의미

- 초기 bulk wave vs 후기 반사/유도모드 비율
    
- Boundary 영향 직접 반영
    
- FT 변화 → 반사/감쇠 패턴 변화
    

👉 “초기 전파 vs 반사 모드 비율”

---

## 8. Temporal Skewness

```
skewness(time-marginal energy)
```

### 의미

- 시간 에너지 분포 비대칭성
    
- FT 증가 → tail 길어짐
    

👉 “파형이 한쪽으로 늘어지는가”

---

# 2️⃣ Correlation / Coherence Feature (4)

---

## CC 정의

```
dm
\mathrm{CC}(x_1,x_2)
=
\max_\tau
\frac{\langle v(x_1,t), v(x_2,t+\tau)\rangle}
{\|v_1\|\|v_2\|}
```

### 의미

- 이웃 위치에서의 위상 유사도
    
- Coherence 유지 정도
    
- FT 증가 → 점탄성/산란 증가 → CC 감소
    

👉 “파동이 얼마나 일관되게 유지되는가”

---

## 9. Mean CC

```
\mathrm{mean}(\mathrm{CC}(x_i,x_{i+1}))
```

### 의미

- 전체 전파 품질
    

---

## 10. CC Drop Rate

```
dm
\mathrm{CC}(x) \approx e^{-x/L_c}
```

### 의미

- 거리 증가에 따른 coherence 붕괴 속도
    
- (L_c) = decorrelation length
    

👉 “얼마나 빨리 coherence가 무너지는가”

---

## 11. Axial vs Lateral CC Ratio

```
dm
\frac{\mathrm{CC}_{axial}}
{\mathrm{CC}_{lateral}}
```

### 의미

- Axial은 안정, lateral은 boundary 영향 큼
    
- 경계로 인한 lateral decorrelation 측정
    

👉 “경계가 전파를 얼마나 흐트러뜨리는가”

---

## 12. Decorrelation Length

```
L_c
```

### 의미

- CC가 (1/e)로 감소하는 거리
    
- Effective propagation length
    

---

# 3️⃣ Boundary / Mode-Sensitive Feature (4)

> 이 4개가 논문의 핵심 차별점

---

## 13. Guided-Mode Energy Ratio

```
dm
\frac{E_{\text{guided}}}{E_{\text{total}}}
```

### 의미

- Bulk vs Lamb-like 모드 분리
    
- FT 증가 → guided mode 비율 변화
    

👉 “경계 유도 모드가 얼마나 존재하는가”

---

## 14. Reflection Asymmetry Index

```
dm
\frac{|E_{left} - E_{right}|}
{E_{left} + E_{right}}
```

### 의미

- 좌/우 반사 에너지 비대칭
    
- ARF 위치 민감
    

👉 “경계 반사가 대칭인가”

---

## 15. Dispersion Curvature

```
dm
\omega-k \text{ ridge fit residual}
```

### 의미

- ω–k domain에서 비선형성
    
- FT 증가 → viscoelastic dispersion 증가
    

👉 “속도-주파수 관계가 얼마나 휘는가”

---

## 16. Free Surface Sensitivity Index

```
dm
|f_{BG} - f_B|
```

### 의미

- ARF 위치(BG vs B)에 따른 feature 차이
    
- Boundary-aware 모델의 결정적 지표
    

👉 “경계 위치 변화에 얼마나 민감한가”

---

# 🎯 전체 개념 요약 (발표용 멘트)

- Propagation → 파동이 어떻게 가는가
    
- Coherence → 얼마나 일관되게 가는가
    
- Boundary → 경계가 그것을 어떻게 바꾸는가
    

Freeze–Thaw는 이 세 영역 모두에 영향을 준다.  
따라서 16D feature는 FT-dependent boundary signature를 정량화하는 구조이다.

---

원하면 다음 단계:

- 🔥 MLP 구조까지 포함한 발표용 “Figure 1 전체 스토리 설명 스크립트”
    
- 🔥 각 feature의 물리적 연결 그림으로 설명
    
- 🔥 교수님 질문 대비용 방어 논리 정리
    

어디까지 밀어볼까?