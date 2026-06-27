---
title: "[데이터이해] 데이터란 무엇인가 — 타입과 구조 이해하기"
date: 2026-06-25 09:00:00 +0900
categories: [Study]
tags: [데이터이해]
toc: true
---

## 데이터의 종류

데이터를 다루기 전에 어떤 형태의 데이터인지 파악하는 것이 첫 번째입니다.

| 분류 | 예시 |
|---|---|
| 수치형 (연속) | 키, 몸무게, 온도 |
| 수치형 (이산) | 방문 횟수, 클릭 수 |
| 범주형 (명목) | 성별, 국가, 색상 |
| 범주형 (순서) | 학점, 만족도 등급 |

---

## Pandas 기본 구조

```python
import pandas as pd

df = pd.read_csv("data.csv")

# 기본 탐색
print(df.shape)       # (행, 열) 수
print(df.dtypes)      # 컬럼별 타입
print(df.isnull().sum())  # 결측치 수
print(df.describe())  # 수치형 기술통계
```

---

## 데이터 타입 변환

```python
# 문자 → 범주형
df["grade"] = df["grade"].astype("category")

# 문자 → 날짜
df["date"] = pd.to_datetime(df["date"])

# 타입 확인
df.info()
```

---

## 핵심 요약

- **데이터 타입**을 먼저 파악해야 올바른 분석 방법을 선택할 수 있다.
- `dtype`이 맞지 않으면 연산·시각화 모두 에러가 발생한다.
- 분석 전 항상 `shape`, `dtypes`, `isnull().sum()` 을 확인하는 습관을 들이자.
