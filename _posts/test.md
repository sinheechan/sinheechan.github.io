# 블로그 업그레이드 적용 가이드

Chirpy 블로그를 **홈(포트폴리오+대시보드) / 카테고리 / 아카이브** 3탭 구조의
앱 느낌 블로그로 개편한 파일 모음입니다. 아래 경로 그대로 repo 에 복사하면 됩니다.

## 1. 파일 배치 (경로 그대로 덮어쓰기 / 추가)

| 파일 | 위치 | 작업 |
|------|------|------|
| `home.html` | `_layouts/home.html` | **새로 추가** (테마 기본 home 레이아웃을 덮어씀) |
| `portfolio.yml` | `_data/portfolio.yml` | **새로 추가** (기술스택·프로젝트 내용) |
| `categories.md` | `_tabs/categories.md` | **교체** |
| `archives.md` | `_tabs/archives.md` | **교체** |
| `jekyll-theme-chirpy.scss` | `assets/lib/jekyll-theme-chirpy.scss` | **교체** (스타일 전체) |

## 2. 삭제할 파일

더 이상 쓰지 않는 탭 두 개는 지웁니다.

```
rm _tabs/tags.md
rm _tabs/about.md
```

`index.html` 은 그대로 두세요 (`layout: home` 유지 → 새 대시보드가 렌더링됨).

## 3. 탭 구성 결과

- **홈** : 프로필 + 기술스택 + 프로젝트 카드 + 통계(글/카테고리/태그/최근수정) + 최근 글 + 태그 클라우드
- **카테고리** (order 1) : 글을 올릴 때마다 1차 카테고리로 자동 그룹핑. 검색 + 펼치기/접기
- **아카이브** (order 2) : 연도별 타임라인 + 태그 필터. 홈 태그 클릭 시 이 탭으로 연결

> 글을 `_posts` 에 올리기만 하면 통계·카테고리·태그·타임라인이 **전부 자동 집계**됩니다.
> 별도 관리가 필요 없습니다.

## 4. 포트폴리오 내용 수정 방법

`_data/portfolio.yml` 한 파일만 고치면 홈의 기술스택/프로젝트가 바뀝니다.
(현재는 예시 값이 들어있으니 본인 내용으로 교체하세요.)

```yaml
skills:
  - category: Language
    items: [Python, SQL]      # 원하는 만큼 추가

projects:
  - name: 프로젝트명
    period: 2025
    description: 한 줄 설명
    tags: [Kafka, Airflow]
    link: ""                  # 링크 없으면 빈 문자열
```

## 5. 글 작성 시 권장 형식 (카테고리/태그 자동 분류용)

```yaml
---
title: 제목
date: 2026-06-21 12:00:00 +0900
categories: [알고리즘, DP]    # [1차, 2차] — 2차는 카테고리 탭에서 하위로 표시
tags: [python, dp]
---
```

## 6. 로컬 확인

```
bundle exec jekyll s -l
```

## 검증 내역

- Liquid 블록 균형 / YAML 프런트매터 / SCSS 중괄호 균형: 이상 없음
- 카테고리 1차/하위 그룹핑 로직, 아카이브 연도 그룹 div 개폐 로직: 다중 글 시뮬레이션 통과
- 라이트/다크 모드 렌더링: 두 모드 모두 정상 (Chirpy 7.5 에 없던 `--card-border` 변수는
  테마별 fallback 으로 보강)