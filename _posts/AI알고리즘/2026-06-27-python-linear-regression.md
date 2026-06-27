---
title: "[AI 알고리즘] 선형회귀와 다항회귀 정리"
date: 2026-06-27 00:00:00 +0900
categories: [Study, Python]
tags: [Python, AI 알고리즘]
toc: true
---

## 학습 순서

- 학습순서 : 원리이해 > 가설(사용되는 함수) > 비용(손실)함수

---

## 참고 - 시그모이드 함수 (로지스틱 회귀 메모)

- 선형 결합값을 시그모이드 함수로 0~1 확률로 변환하여 분류함
- 선형 회귀에 시그모이드 함수를 씌워 출력값을 확률(0~1)로 변환함
- 출력 확률이 0.5 이상이면 클래스 1, 미만이면 클래스 0 (※ 원본 메모 일부 누락 추정)

---

## 1. 작동 원리 : 단순선형회귀

### 1) 기초 개념

- 선형회귀란 x와 y의 선형적인 관계를 구해 값을 예측하는 것
- x는 어떤 값이든 적용될 수 있으며, 기울기와 절편의 값을 알게 된다면 원하는 x값을 대입했을 때 y값을 얻을 수 있음 (y = ax+b)
- 인공지능에서는 기울기 a를 w(weight)라는 변수로 표현하며, 가중치 또는 계수라고 부름
- b(bias)는 수학에서와 동일하게 절편이라고 부름

![선형회귀 기본 개념](/assets/img/ai-algorithms/linear-regression.svg){: style="margin:0;"}

### 2) 가중치와 계수 구하기

- **목표** : 예측값과 실제값의 차이(오차)를 최소화하는 직선 찾기
- 선형회귀에서는 가중치와 절편을 평균제곱오차(Mean Squared Error - MSE)를 최소화하는 방법으로 구할 수 있음
- 평균제곱오차란, 데이터의 오차를 제곱하여 데이터의 개수만큼 나눈 값을 의미함
- 아래 그래프에서 오차가 5인 점(dot)을 살펴보면, 실제 데이터의 값은 9이지만 방정식은 4라고 인지하고 있음 → 이 경우 오차는 +5가 됨
- 오차가 -2인 점을 살펴보면, 실제 값은 3인데 방정식에서는 5라고 인지하고 있음
- 오차는 음수가 될 수도 있고 양수가 될 수도 있기 때문에 오차에 제곱을 해주는 것이며, 오차의 평균치를 구하기 위해 데이터의 개수로 나누어 주는 것임
- 이 오차를 최소화하는 방정식을 구하면, y값을 예측할 수 있는 최적의 모델을 만들어낼 수 있음

---

## 2. 다항회귀

### 1) 기초 개념

- 다항회귀는 데이터가 직선이 아닌 곡선으로 나타날 때 사용하는 회귀법임
- 현실 데이터는 단순 비례(직선)보다 복잡한 경우가 훨씬 많음
- 차수(degree)란 방정식의 항의 수를 의미하며, 차수가 높을수록 곡선이 데이터에 더 잘 맞춰짐
- 차수를 높이면 모델의 오차가 줄어드는 장점이 있음

![다항회귀 차수와 과대적합](/assets/img/ai-algorithms/polynomial-regression.svg){: style="margin:0;"}

### 2) 다항회귀 표현하기

- **특징(feature)** 이란 결과값(target)을 예측하기 위해 사용하는 입력 데이터를 말함 (예: 생선 무게를 예측하기 위한 생선의 길이)
- 이 특징에 차수를 부여해 제곱, 세제곱 등 여러 특징을 추가로 만들어낼 수 있음
- 특징이 많을수록 모델은 더 정확한 예측이 가능함
- 단, 차수를 지나치게 높이면 **과대적합(Overfitting)** 이 발생할 수 있음 (훈련 데이터에만 너무 딱 맞아버리는 현상)
- 이를 방지하기 위해 sklearn은 **릿지(Ridge)** 와 **라쏘(Lasso)** 라는 규제 클래스를 제공함
- **규제(Regularization)** 란 과대적합을 막기 위해 모델의 파라미터(차수)를 자동으로 조절해주는 것임

---

## 코드 예제

```python
# ============================================================
# 선형회귀 (Linear Regression) - California Housing 데이터셋
# ============================================================

import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.datasets import fetch_california_housing
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import mean_squared_error, r2_score

# ─────────────────────────────
# 1. 데이터 로드
# ─────────────────────────────
housing = fetch_california_housing()

# DataFrame으로 변환
df = pd.DataFrame(housing.data, columns=housing.feature_names)
df['Price'] = housing.target  # 타겟: 주택 중간 가격 (단위: $100,000)

print("데이터 shape:", df.shape)
print(df.head())

# ─────────────────────────────
# 2. 특성(X)과 타겟(y) 분리
# ─────────────────────────────
X = df.drop(columns=['Price'])
y = df['Price']

# ─────────────────────────────
# 3. 학습/테스트 데이터 분할 (8:2)
# ─────────────────────────────
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# ─────────────────────────────
# 4. 특성 스케일링 (표준화)
#    → 선형회귀는 스케일에 민감하므로 StandardScaler 적용
# ─────────────────────────────
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)  # 학습 데이터로 fit 후 transform
X_test_scaled  = scaler.transform(X_test)        # 테스트 데이터는 transform만 (data leakage 방지)

# ─────────────────────────────
# 5. 모델 학습
# ─────────────────────────────
model = LinearRegression()
model.fit(X_train_scaled, y_train)

# ─────────────────────────────
# 6. 예측
# ─────────────────────────────
y_pred = model.predict(X_test_scaled)

# ─────────────────────────────
# 7. 성능 평가
# ─────────────────────────────
mse  = mean_squared_error(y_test, y_pred)
rmse = np.sqrt(mse)
r2   = r2_score(y_test, y_pred)

print(f"\n모델 성능")
print(f"  RMSE : {rmse:.4f}  ← 예측 오차 평균 (낮을수록 좋음)")
print(f"  R²   : {r2:.4f}  ← 설명력 (1에 가까울수록 좋음)")

# ─────────────────────────────
# 8. 회귀 계수 확인
#    → 계수 절댓값이 클수록 해당 특성이 가격에 미치는 영향이 큼
# ─────────────────────────────
coef_df = pd.DataFrame({
    'Feature'    : housing.feature_names,
    'Coefficient': model.coef_
}).sort_values('Coefficient', ascending=False)

print("\n회귀 계수 (표준화된 특성 기준):")
print(coef_df.to_string(index=False))

# ─────────────────────────────
# 9. 시각화: 실제값 vs 예측값
# ─────────────────────────────
plt.figure(figsize=(8, 6))
plt.scatter(y_test, y_pred, alpha=0.3, color='steelblue', label='예측값')
plt.plot([y_test.min(), y_test.max()],
         [y_test.min(), y_test.max()],
         'r--', linewidth=2, label='완벽한 예측 (y=x)')
plt.xlabel('실제 가격')
plt.ylabel('예측 가격')
plt.title(f'실제값 vs 예측값  (R² = {r2:.3f})')
plt.legend()
plt.tight_layout()
plt.show()
```

---

## 핵심 요약

- 선형회귀는 x와 y의 선형 관계를 구해 값을 예측하는 모델임
- 가중치와 절편은 평균제곱오차(MSE)를 최소화하는 방법으로 구함
- 다항회귀는 곡선 형태의 데이터를 다룰 때 사용하며, 차수를 높일수록 모델이 데이터에 더 잘 맞춰짐
- 차수를 과도하게 높이면 과대적합이 발생할 수 있으며, 이를 방지하기 위해 릿지·라쏘 같은 규제 기법을 사용함