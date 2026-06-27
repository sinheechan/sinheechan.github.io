---
title: "[프로그래밍] 데이터 분석에 자주 쓰는 Python 문법 정리"
date: 2026-06-25 10:00:00 +0900
categories: [Study, Python]
tags: [Python, 프로그래밍]
toc: true
---

## 리스트 컴프리헨션

반복문보다 간결하고 빠른 리스트 생성 방법입니다.

```python
# 일반 반복문
squares = []
for x in range(10):
    squares.append(x ** 2)

# 컴프리헨션
squares = [x ** 2 for x in range(10)]

# 조건 포함
evens = [x for x in range(20) if x % 2 == 0]
```

---

## 함수와 람다

```python
# 일반 함수
def normalize(x, min_val, max_val):
    return (x - min_val) / (max_val - min_val)

# 람다 (간단한 연산에 활용)
double = lambda x: x * 2

# map / filter와 함께 사용
data = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x * 2, data))
filtered = list(filter(lambda x: x > 2, data))
```

---

## 딕셔너리 조작

```python
# 딕셔너리 컴프리헨션
word_count = {word: len(word) for word in ["python", "data", "analysis"]}

# 키·값 순회
for key, val in word_count.items():
    print(f"{key}: {val}")

# get으로 안전하게 접근
score = word_count.get("missing_key", 0)
```

---

## f-string 포매팅

```python
name = "신희찬"
score = 98.5

print(f"이름: {name}, 점수: {score:.1f}")
# 이름: 신희찬, 점수: 98.5
```

---

## 핵심 요약

- 컴프리헨션은 가독성과 성능을 동시에 잡는다.
- 람다는 단순 연산에만 쓰고, 복잡한 로직은 일반 함수로 작성한다.
- f-string은 Python 3.6+ 에서 가장 권장되는 포매팅 방식이다.
