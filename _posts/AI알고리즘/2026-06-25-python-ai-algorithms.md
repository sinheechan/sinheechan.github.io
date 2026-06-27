---
title: "[AI 알고리즘] 머신러닝 핵심 알고리즘 개념 정리"
date: 2026-06-25 12:00:00 +0900
categories: [Study, Python]
tags: [AI알고리즘]
toc: true
---

## 머신러닝의 분류

![머신러닝 학습 유형](/assets/img/ai-algorithms/ml-types.svg){: style="margin:0;"}

```
지도학습 (Supervised)
  ├── 분류 (Classification): 로지스틱 회귀, 랜덤 포레스트, SVM, XGBoost
  └── 회귀 (Regression): 선형 회귀, Ridge, Lasso

비지도학습 (Unsupervised)
  ├── 군집화 (Clustering): K-Means, DBSCAN
  └── 차원 축소: PCA, t-SNE

강화학습 (Reinforcement): Q-Learning, PPO
```

---

## scikit-learn 기본 흐름

```python
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report

# 1. 데이터 분할
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# 2. 스케일링
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test = scaler.transform(X_test)

# 3. 모델 학습
model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)

# 4. 평가
y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))
```

---

## 알고리즘 선택 기준

| 상황 | 추천 알고리즘 |
|---|---|
| 데이터 적고 해석 중요 | 로지스틱 회귀, 결정 트리 |
| 정확도 우선 (정형 데이터) | XGBoost, LightGBM |
| 비선형 복잡한 패턴 | 랜덤 포레스트, SVM |
| 이미지·텍스트·시계열 | 딥러닝 (PyTorch / TensorFlow) |

---

## 과적합 방지 핵심 기법

```python
from sklearn.model_selection import cross_val_score

# 교차 검증
scores = cross_val_score(model, X, y, cv=5, scoring="f1_macro")
print(f"CV F1: {scores.mean():.4f} ± {scores.std():.4f}")

# 정규화 (Ridge)
from sklearn.linear_model import Ridge
ridge = Ridge(alpha=1.0)
```

---

## 핵심 요약

- **데이터 특성과 목적**에 맞는 알고리즘을 선택하는 것이 성능보다 중요하다.
- 항상 `train_test_split` → `fit` → `predict` → `evaluate` 순서를 지킨다.
- 교차 검증(CV)으로 과적합 여부를 반드시 확인한다.
