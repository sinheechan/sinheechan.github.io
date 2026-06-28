---
title: "[Python] Pandas 데이터 핸들링 완전 정복"
date: 2026-06-25 09:00:00 +0900
categories: [Study, Python]
tags: [pandas, python, 데이터분석]
toc: true
---

- pandas는 파이썬 데이터 분석의 핵심 라이브러리로, 표 형태(행·열) 데이터를 효율적으로 다룰 수 있음.
- 이 문서 하나로 pandas의 주요 기능을 대부분 커버할 수 있도록 구성함.

---

## 1. 설치 및 임포트

```python
pip install pandas
```

```python
import pandas as pd
import numpy as np  # 함께 자주 사용됨
```

---

## 2. DataFrame 생성

- DataFrame은 pandas의 핵심 자료구조로, 행과 열을 가진 2차원 테이블임.

```python
# 딕셔너리로 생성
df = pd.DataFrame({
    "name":   ["Alice", "Bob", "Charlie", "David"],
    "age":    [25, 30, 35, 28],
    "score":  [88.5, 92.0, 79.3, 85.1],
    "passed": [True, True, False, True]
})

print(df)
```

```
      name  age  score  passed
0    Alice   25   88.5    True
1      Bob   30   92.0    True
2  Charlie   35   79.3   False
3    David   28   85.1    True
```

```python
# 리스트 + columns 지정
df2 = pd.DataFrame(
    [[1, "a"], [2, "b"], [3, "c"]],
    columns=["num", "letter"]
)

# Series로 생성
s = pd.Series([10, 20, 30], index=["x", "y", "z"], name="values")
print(s)
```

```
x    10
y    20
z    30
Name: values, dtype: int64
```

---

## 3. 데이터 불러오기 / 저장

- 실무에서 가장 많이 쓰이는 포맷은 CSV·Excel·JSON임.

```python
# CSV 읽기
df = pd.read_csv("data.csv")
df = pd.read_csv("data.csv", encoding="utf-8", index_col=0)
df = pd.read_csv("data.csv", usecols=["name", "age"])   # 필요한 컬럼만
df = pd.read_csv("data.csv", nrows=100)                 # 처음 100행만

# Excel 읽기
df = pd.read_excel("data.xlsx", sheet_name="Sheet1")

# JSON 읽기
df = pd.read_json("data.json")

# 저장
df.to_csv("output.csv", index=False)
df.to_excel("output.xlsx", index=False)
```

---

## 4. 기본 탐색

- 데이터를 처음 받으면 아래 순서로 확인하는 것이 기본임.

```python
df.shape          # 행, 열 수
```

```
(4, 4)
```

```python
df.dtypes         # 각 컬럼의 데이터 타입
```

```
name       object
age         int64
score     float64
passed       bool
dtype: object
```

```python
df.info()         # 타입 + 결측치 + 메모리 요약
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 4 entries, 0 to 3
Data columns (total 4 columns):
 #   Column  Non-Null Count  Dtype  
---  ------  --------------  -----  
 0   name    4 non-null      object 
 1   age     4 non-null      int64  
 2   score   4 non-null      float64
 3   passed  4 non-null      bool   
dtypes: bool(1), float64(1), int64(1), object(1)
memory usage: 228.0+ bytes
```

```python
df.describe()     # 수치형 컬럼 기술통계
```

```
             age      score
count   4.000000   4.000000
mean   29.500000  86.225000
std     4.203173   5.250992
min    25.000000  79.300000
25%    27.250000  83.475000
50%    29.000000  86.800000
75%    31.250000  89.625000
max    35.000000  92.000000
```

```python
df.head(3)        # 처음 n행
df.tail(2)        # 마지막 n행
df.sample(2)      # 무작위 n행
df.columns        # 컬럼 이름 목록
df.index          # 인덱스 정보
df.values         # numpy 배열로 변환
```

---

## 5. 인덱싱과 선택

### 컬럼 선택

```python
df["name"]              # 단일 컬럼 → Series
df[["name", "score"]]   # 복수 컬럼 → DataFrame
```

```
0      Alice
1        Bob
2    Charlie
3      David
Name: name, dtype: object
```

### loc — 라벨 기반

```python
df.loc[0]              # 0번 인덱스 행
df.loc[1:3]            # 1~3 인덱스 행 (끝 포함)
df.loc[0, "name"]      # 0행, name 컬럼
df.loc[:, "age":"score"]   # 모든 행, age~score 컬럼
```

```
name     Alice
age         25
score     88.5
passed    True
Name: 0, dtype: object
```

### iloc — 위치 기반

```python
df.iloc[0]             # 첫 번째 행
df.iloc[0, 1]          # 0행 1열
df.iloc[1:3, 0:2]      # 1~2행, 0~1열
df.iloc[-1]            # 마지막 행
```

```
   name  age
1   Bob   30
2  Charlie  35
```

---

## 6. 필터링 (조건부 선택)

```python
# 단일 조건
df[df["age"] > 28]
```

```
      name  age  score  passed
1      Bob   30   92.0    True
2  Charlie   35   79.3   False
```

```python
# 복합 조건 (&, |, ~)
df[(df["age"] > 25) & (df["passed"] == True)]
```

```
    name  age  score  passed
1    Bob   30   92.0    True
3  David   28   85.1    True
```

```python
# isin — 특정 값 포함 여부
df[df["name"].isin(["Alice", "David"])]

# between — 범위 조건
df[df["score"].between(80, 90)]

# str.contains — 문자열 포함
df[df["name"].str.contains("li")]

# query — SQL 스타일 필터링 (가독성 좋음)
df.query("age > 28 and passed == True")
```

```
    name  age  score  passed
1    Bob   30   92.0    True
3  David   28   85.1    True
```

---

## 7. 정렬

```python
# 단일 컬럼 정렬
df.sort_values("score", ascending=False)
```

```
      name  age  score  passed
1      Bob   30   92.0    True
0    Alice   25   88.5    True
3    David   28   85.1    True
2  Charlie   35   79.3   False
```

```python
# 복수 컬럼 정렬
df.sort_values(["passed", "score"], ascending=[False, True])

# 인덱스 정렬
df.sort_index(ascending=True)

# 정렬 결과를 원본에 반영
df.sort_values("age", inplace=True)
df = df.reset_index(drop=True)  # 인덱스 재정렬
```

---

## 8. 결측치 처리

```python
# 결측치 주입 (예시용)
df.loc[1, "score"] = None
df.loc[3, "age"] = None

# 결측치 확인
df.isnull()           # 각 셀 True/False
df.isnull().sum()     # 컬럼별 결측치 수
```

```
name      0
age       1
score     1
passed    0
dtype: int64
```

```python
df.notnull().sum()    # 결측치가 아닌 수
df.isnull().any()     # 결측치 있는 컬럼
```

```python
# 결측치 제거
df.dropna()                      # 결측치 있는 행 전체 제거
df.dropna(subset=["score"])      # 특정 컬럼 기준
df.dropna(how="all")             # 모든 컬럼이 결측일 때만 제거

# 결측치 채우기
df.fillna(0)                     # 0으로 채우기
df["score"].fillna(df["score"].mean())  # 평균으로 채우기
df.fillna(method="ffill")        # 앞 값으로 채우기 (forward fill)
df.fillna(method="bfill")        # 뒤 값으로 채우기 (backward fill)
df.interpolate()                 # 선형 보간
```

---

## 9. 데이터 타입 변환

```python
df["age"].dtype       # 현재 타입 확인
```

```
dtype('float64')
```

```python
# 숫자 변환
df["age"] = df["age"].astype(int)
df["score"] = pd.to_numeric(df["score"], errors="coerce")  # 변환 불가 → NaN

# 문자 → 날짜
df["date"] = pd.to_datetime(df["date"])
df["date"] = pd.to_datetime(df["date"], format="%Y-%m-%d")

# 범주형 변환 (메모리 절약, 속도 향상)
df["name"] = df["name"].astype("category")

# bool 변환
df["passed"] = df["passed"].astype(bool)

print(df.dtypes)
```

```
name      category
age          int64
score      float64
passed        bool
dtype: object
```

---

## 10. 컬럼 추가·수정·삭제

```python
# 컬럼 추가
df["grade"] = ["B", "A", "C", "B"]
df["score_norm"] = df["score"] / 100          # 연산으로 추가
df["senior"] = df["age"] >= 30                # 조건식으로 추가

# 컬럼명 변경
df.rename(columns={"name": "student", "score": "점수"}, inplace=True)

# 컬럼 순서 변경
df = df[["student", "age", "점수", "passed", "grade"]]

# 컬럼 삭제
df.drop(columns=["grade"], inplace=True)
df.drop(columns=["age", "passed"], inplace=True)

# 행 삭제
df.drop(index=2, inplace=True)
df = df.reset_index(drop=True)
```

---

## 11. 그룹화와 집계 (groupby)

- SQL의 `GROUP BY`와 동일한 개념임.

```python
# 기본 groupby + 집계
df.groupby("grade")["score"].mean()
```

```
grade
A    92.0
B    86.8
C    79.3
Name: score, dtype: float64
```

```python
# 다중 컬럼 그룹화
df.groupby(["grade", "passed"])["score"].mean()

# agg — 여러 집계 함수 동시 적용
df.groupby("grade")["score"].agg(["mean", "max", "min", "count"])
```

```
       mean   max   min  count
grade                         
A      92.0  92.0  92.0      1
B      86.8  88.5  85.1      2
C      79.3  79.3  79.3      1
```

```python
# 컬럼별로 다른 집계 함수 적용
df.groupby("grade").agg({
    "score": ["mean", "std"],
    "age":   "max"
})

# size — 그룹별 행 수
df.groupby("grade").size()

# transform — 그룹 통계값을 원본 인덱스에 맞게 반환 (컬럼 추가에 유용)
df["grade_avg"] = df.groupby("grade")["score"].transform("mean")

# filter — 그룹 단위 조건 필터링
df.groupby("grade").filter(lambda x: x["score"].mean() > 85)
```

---

## 12. 병합 (merge / concat)

### merge — SQL JOIN과 동일

```python
df_a = pd.DataFrame({"id": [1, 2, 3], "name": ["Alice", "Bob", "Charlie"]})
df_b = pd.DataFrame({"id": [1, 2, 4], "score": [88, 92, 75]})

# inner join (기본값) — 양쪽에 모두 있는 것만
pd.merge(df_a, df_b, on="id")
```

```
   id   name  score
0   1  Alice     88
1   2    Bob     92
```

```python
# left join — 왼쪽 기준 (없으면 NaN)
pd.merge(df_a, df_b, on="id", how="left")
```

```
   id     name  score
0   1    Alice   88.0
1   2      Bob   92.0
2   3  Charlie    NaN
```

```python
# right / outer join
pd.merge(df_a, df_b, on="id", how="right")
pd.merge(df_a, df_b, on="id", how="outer")

# 컬럼명이 다를 때
pd.merge(df_a, df_b, left_on="id", right_on="user_id")
```

### concat — 단순 연결

```python
# 행 방향 연결 (위아래 붙이기)
pd.concat([df_a, df_b], axis=0, ignore_index=True)

# 열 방향 연결 (옆으로 붙이기)
pd.concat([df_a, df_b], axis=1)
```

---

## 13. apply / map / assign

```python
# apply — 행/열 단위 함수 적용
df["score"].apply(lambda x: "pass" if x >= 80 else "fail")
```

```
0     pass
1     pass
2     fail
3     pass
Name: score, dtype: object
```

```python
# 행 단위 apply (axis=1)
df.apply(lambda row: row["score"] * 1.1 if row["passed"] else row["score"], axis=1)

# map — Series 값 치환 (딕셔너리 매핑)
df["passed"].map({True: "합격", False: "불합격"})
```

```
0      합격
1      합격
2    불합격
3      합격
Name: passed, dtype: object
```

```python
# assign — 메서드 체인으로 컬럼 추가
df = (df
      .assign(score_norm=df["score"] / 100)
      .assign(label=lambda x: x["score_norm"].apply(lambda v: "high" if v > 0.85 else "low"))
)
```

---

## 14. 날짜·시간 처리

```python
df["date"] = pd.to_datetime(["2024-01-01", "2024-03-15", "2024-07-20", "2024-11-30"])

# 날짜 구성요소 추출 (.dt accessor)
df["year"]    = df["date"].dt.year
df["month"]   = df["date"].dt.month
df["day"]     = df["date"].dt.day
df["weekday"] = df["date"].dt.day_name()   # 요일 이름
df["quarter"] = df["date"].dt.quarter

print(df[["date", "year", "month", "weekday"]])
```

```
        date  year  month    weekday
0 2024-01-01  2024      1     Monday
1 2024-03-15  2024      3     Friday
2 2024-07-20  2024      7   Saturday
3 2024-11-30  2024     11   Saturday
```

```python
# 날짜 연산
df["date"] + pd.Timedelta(days=7)          # 7일 뒤
(df["date"].max() - df["date"].min()).days  # 날짜 차이 (일 수)

# 기간별 리샘플링 (시계열 집계)
df.set_index("date").resample("M")["score"].mean()   # 월별 평균
df.set_index("date").resample("Q")["score"].sum()    # 분기별 합계
```

---

## 15. 문자열 처리 (str accessor)

```python
df["name"] = ["Alice Kim", "bob lee", "CHARLIE PARK", "david choi"]

df["name"].str.upper()         # 대문자
df["name"].str.lower()         # 소문자
df["name"].str.title()         # 첫 글자만 대문자
df["name"].str.strip()         # 공백 제거
df["name"].str.len()           # 문자열 길이
df["name"].str.replace("a", "@", regex=False)
df["name"].str.contains("lee") # 포함 여부 (True/False)
df["name"].str.startswith("A")
df["name"].str.split(" ")      # 분리 → list
```

```
0    [Alice, Kim]
1      [bob, lee]
2    [CHARLIE, PARK]
3    [david, choi]
Name: name, dtype: object
```

```python
# 분리한 열로 만들기
df[["first", "last"]] = df["name"].str.split(" ", expand=True)
```

```
   first    last
0  Alice     Kim
1    bob     lee
2  CHARLIE  PARK
3  david    choi
```

---

## 16. 중복 처리

```python
# 중복 확인
df.duplicated()                      # 행 단위 중복 여부
df.duplicated(subset=["name"])       # 특정 컬럼 기준
df.duplicated().sum()                # 중복 행 수
```

```
0    False
1    False
2    False
3    False
dtype: bool
```

```python
# 중복 제거
df.drop_duplicates()                           # 중복 행 제거
df.drop_duplicates(subset=["name"], keep="first")  # 첫 번째만 유지
df.drop_duplicates(subset=["name"], keep="last")   # 마지막만 유지
```

---

## 17. pivot / melt — 데이터 형태 변환

```python
# 넓은 형태 → 긴 형태 (melt)
df_wide = pd.DataFrame({
    "name":  ["Alice", "Bob"],
    "math":  [90, 85],
    "english": [88, 92]
})

df_long = df_wide.melt(id_vars="name", var_name="subject", value_name="score")
print(df_long)
```

```
    name  subject  score
0  Alice     math     90
1    Bob     math     85
2  Alice  english     88
3    Bob  english     92
```

```python
# 긴 형태 → 넓은 형태 (pivot)
df_long.pivot(index="name", columns="subject", values="score")
```

```
subject  english  math
name                  
Alice         88    90
Bob           92    85
```

```python
# pivot_table — 집계 포함
df.pivot_table(index="grade", columns="passed", values="score", aggfunc="mean")
```

---

## 18. 유용한 함수 모음

```python
# 값 빈도수
df["grade"].value_counts()
df["grade"].value_counts(normalize=True)   # 비율
```

```
B    2
A    1
C    1
Name: grade, dtype: int64
```

```python
# 고유값
df["grade"].unique()       # 고유값 배열
df["grade"].nunique()      # 고유값 수

# 누적합 / 누적곱
df["score"].cumsum()
df["score"].cumprod()

# 순위
df["score"].rank(ascending=False)

# 구간 나누기
pd.cut(df["score"], bins=[0, 60, 80, 100], labels=["C", "B", "A"])
pd.qcut(df["score"], q=4, labels=["Q1", "Q2", "Q3", "Q4"])  # 사분위수 기준

# 상관계수
df[["age", "score"]].corr()
```

```
          age     score
age      1.00     -0.43
score   -0.43      1.00
```

```python
# 조건부 값 대입
import numpy as np
df["label"] = np.where(df["score"] >= 85, "high", "low")

# 다중 조건
conditions = [
    df["score"] >= 90,
    df["score"] >= 80,
]
choices = ["A", "B"]
df["grade2"] = np.select(conditions, choices, default="C")
```

---

## 빠른 참조 요약

| 작업 | 코드 |
|------|------|
| 파일 읽기 | `pd.read_csv()` / `pd.read_excel()` |
| 기본 확인 | `df.shape` / `df.info()` / `df.describe()` |
| 행/열 선택 | `df.loc[]` (라벨) / `df.iloc[]` (위치) |
| 조건 필터 | `df[df["col"] > 0]` / `df.query("col > 0")` |
| 결측치 처리 | `df.isnull()` / `df.fillna()` / `df.dropna()` |
| 그룹 집계 | `df.groupby("col").agg({"col2": "mean"})` |
| 병합 | `pd.merge(df1, df2, on="key", how="left")` |
| 함수 적용 | `df["col"].apply(func)` |
| 날짜 추출 | `df["date"].dt.year` / `.dt.month` |
| 문자열 처리 | `df["col"].str.contains()` / `.str.split()` |
| 형태 변환 | `df.melt()` / `df.pivot_table()` |
| 중복 제거 | `df.drop_duplicates()` |
