---
title: "[AI 알고리즘] LightGBM 정리"
date: 2026-01-17 00:00:00 +0900
categories: [Study, Python]
tags: [Python, AI 알고리즘]
toc: true
---

## 개요

- LightGBM(Light Gradient Boosting Machine)은 Microsoft가 개발한 그래디언트 부스팅 프레임워크로, **대용량 데이터에서 XGBoost보다 빠르고 메모리 효율적**임
- 트리를 **리프 중심(Leaf-wise)** 으로 성장시키는 것이 핵심 차별점임

---

## 기본 이론

XGBoost와 같은 그래디언트 부스팅 계열이지만 두 가지 핵심 최적화를 추가함

- **GOSS(Gradient-based One-Side Sampling)** : 기울기가 큰 샘플은 모두 유지하고, 작은 샘플은 일부만 추출 → 데이터를 줄이면서 정보 손실을 최소화함
- **EFB(Exclusive Feature Bundling)** : 서로 거의 동시에 0이 아닌 값을 갖지 않는 특성들을 하나로 묶어 특성 수를 줄임

---

## 작동 원리

- XGBoost는 **레벨 중심(Level-wise)** 으로 트리를 성장시켜 같은 깊이의 모든 노드를 분할함
- LightGBM은 **리프 중심(Leaf-wise)** 으로 성장시켜 손실을 가장 많이 줄이는 리프만 우선 분할함
- 결과적으로 더 적은 반복으로 더 낮은 손실에 도달할 수 있음

---

## 주요 개념

| 파라미터 | 설명 |
|----------|------|
| `num_leaves` | 트리 당 최대 리프 수 (기본 31) |
| `learning_rate` | 학습률 |
| `n_estimators` | 트리 수 |
| `min_child_samples` | 리프 최소 샘플 수 (과적합 방지) |
| `feature_fraction` | 각 트리에 사용할 특성 비율 |

---

## 장단점

**장점**
- XGBoost 대비 학습 속도가 빠르고 메모리 사용량이 적음
- 범주형 특성을 자체적으로 처리할 수 있음 (`categorical_feature`)
- 대용량·고차원 데이터에 특히 강함

**단점**
- 리프 중심 성장으로 데이터가 적으면 과대적합이 발생하기 쉬움
- `num_leaves`가 너무 크면 모델이 복잡해짐 — `min_child_samples`로 제어 필요

---

## XGBoost vs LightGBM 요약

| 항목 | XGBoost | LightGBM |
|------|---------|---------|
| 트리 성장 | 레벨 중심 | 리프 중심 |
| 속도 | 빠름 | 더 빠름 |
| 메모리 | 보통 | 적음 |
| 소규모 데이터 | 안정적 | 과적합 주의 |
