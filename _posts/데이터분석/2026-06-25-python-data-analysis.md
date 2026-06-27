---
title: "[데이터분석] EDA — 탐색적 데이터 분석 흐름 정리"
date: 2026-06-25 11:00:00 +0900
categories: [Study, Python]
tags: [데이터분석]
toc: true
---

## EDA란?

**Exploratory Data Analysis (탐색적 데이터 분석)** 은 모델링 전에 데이터의 구조·분포·이상치·관계를 파악하는 과정입니다.

---

## 기술통계 확인

```python
import pandas as pd

df = pd.read_csv("data.csv")

# 분포 요약
df.describe()

# 범주형 컬럼 빈도
df["category"].value_counts()

# 상관계수
df.corr(numeric_only=True)
```

---

## 시각화

```python
import matplotlib.pyplot as plt
import seaborn as sns

# 히스토그램 — 분포 확인
df["age"].hist(bins=20)
plt.title("Age Distribution")
plt.show()

# 박스플롯 — 이상치 확인
sns.boxplot(x="group", y="score", data=df)
plt.show()

# 히트맵 — 상관관계
sns.heatmap(df.corr(numeric_only=True), annot=True, fmt=".2f", cmap="coolwarm")
plt.show()
```

---

## 결측치·이상치 처리

```python
# 결측치 비율 확인
missing_ratio = df.isnull().sum() / len(df) * 100

# 중앙값으로 대체
df["salary"].fillna(df["salary"].median(), inplace=True)

# IQR 기반 이상치 제거
Q1, Q3 = df["score"].quantile([0.25, 0.75])
IQR = Q3 - Q1
df = df[(df["score"] >= Q1 - 1.5 * IQR) & (df["score"] <= Q3 + 1.5 * IQR)]
```

---

## EDA 체크리스트

- [ ] 데이터 크기 (`shape`) 및 타입(`dtypes`) 확인
- [ ] 결측치 비율 파악
- [ ] 수치형 변수 분포 시각화
- [ ] 범주형 변수 빈도 확인
- [ ] 이상치 탐지 (박스플롯 / IQR)
- [ ] 주요 변수 간 상관관계 분석

---

## 핵심 요약

EDA는 "데이터에 질문을 던지는 과정"이다.  
좋은 EDA 하나가 잘못된 모델링보다 훨씬 많은 인사이트를 준다.
