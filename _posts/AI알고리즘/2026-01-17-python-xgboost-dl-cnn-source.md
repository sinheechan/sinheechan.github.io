---
title: "[AI 알고리즘] XGBoost · 딥러닝 · CNN 코드 정리"
date: 2026-01-17 00:00:00 +0900
categories: [Study, Python]
tags: [Python, AI 알고리즘]
toc: true
---

## XGBoost 코드

```python
import xgboost as xgb
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

X, y = load_breast_cancer(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

model = xgb.XGBClassifier(
    n_estimators=200,
    learning_rate=0.1,
    max_depth=4,
    subsample=0.8,
    colsample_bytree=0.8,
    eval_metric='logloss',
    early_stopping_rounds=20,
    random_state=42
)

model.fit(
    X_train, y_train,
    eval_set=[(X_test, y_test)],
    verbose=False
)

y_pred = model.predict(X_test)
print(f"XGBoost Accuracy : {accuracy_score(y_test, y_pred):.4f}")
```

---

## 딥러닝 (MLP) 코드

```python
import numpy as np
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
import tensorflow as tf
from tensorflow import keras

X, y = load_breast_cancer(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test  = scaler.transform(X_test)

model = keras.Sequential([
    keras.layers.Dense(64, activation='relu', input_shape=(X_train.shape[1],)),
    keras.layers.Dropout(0.3),
    keras.layers.Dense(32, activation='relu'),
    keras.layers.Dropout(0.3),
    keras.layers.Dense(1, activation='sigmoid')
])

model.compile(
    optimizer='adam',
    loss='binary_crossentropy',
    metrics=['accuracy']
)

history = model.fit(
    X_train, y_train,
    epochs=50,
    batch_size=32,
    validation_split=0.2,
    verbose=0
)

loss, acc = model.evaluate(X_test, y_test, verbose=0)
print(f"DL (MLP) Accuracy : {acc:.4f}")
```

---

## CNN 코드 (MNIST 이미지 분류)

```python
import tensorflow as tf
from tensorflow import keras

# 데이터 로드 및 전처리
(X_train, y_train), (X_test, y_test) = keras.datasets.mnist.load_data()

X_train = X_train.reshape(-1, 28, 28, 1) / 255.0
X_test  = X_test.reshape(-1, 28, 28, 1) / 255.0

# CNN 모델 구성
model = keras.Sequential([
    # 합성곱층 1 : 특징 추출
    keras.layers.Conv2D(32, kernel_size=(3, 3), activation='relu', input_shape=(28, 28, 1)),
    keras.layers.MaxPooling2D(pool_size=(2, 2)),

    # 합성곱층 2 : 더 복잡한 특징 추출
    keras.layers.Conv2D(64, kernel_size=(3, 3), activation='relu'),
    keras.layers.MaxPooling2D(pool_size=(2, 2)),

    # 완전 연결층
    keras.layers.Flatten(),
    keras.layers.Dropout(0.5),
    keras.layers.Dense(128, activation='relu'),
    keras.layers.Dense(10, activation='softmax')  # 0~9 숫자 분류
])

model.compile(
    optimizer='adam',
    loss='sparse_categorical_crossentropy',
    metrics=['accuracy']
)

model.fit(X_train, y_train, epochs=5, batch_size=64, validation_split=0.1, verbose=1)

loss, acc = model.evaluate(X_test, y_test, verbose=0)
print(f"CNN Accuracy : {acc:.4f}")
```

---

## 핵심 요약

- **XGBoost** : 그래디언트 부스팅 + 정규화 — 정형 데이터 분류·회귀에서 강력한 성능
- **MLP** : 완전 연결 신경망 — 정형 데이터에 딥러닝을 적용할 때 기본 구조
- **CNN** : 합성곱층으로 공간적 특징을 추출 — 이미지 분류에 최적화됨
