# Code25Y

Song seung yul의 개인 학습 아카이브. Jekyll + GitHub Pages 기반.

**공개 주소**: <https://songseungyul.github.io/Code25Y/>

## 두 갈래 콘텐츠

| | 학습 기록 | 다음 20년 (시리즈) |
|---|---|---|
| 진입점 | `/` (홈, 주제 + 월별 목록) | `/next-20y/` |
| 레이아웃 | `_layouts/post.html` | `_layouts/memoir.html` |
| URL | `/study/YYYY/MM/DD/slug.html` | `/next-20y/NN-slug/` |
| 구분 방식 | front matter에 `series` 없음 | `series: next-20y` |
| 톤 | 기본 accent | `memoir-scope` — 다른 accent·활자 |

`series` 값이 있는 글은 홈·태그·검색의 학습 기록 흐름에서 제외되고 시리즈 쪽으로만 묶입니다.

## 구조

```
_config.yml         Jekyll 설정
_layouts/
  default.html      공통 셸 (머리글·꼬리글·테마)
  home.html         홈 — 월별 그룹 목록
  page.html         일반 페이지
  post.html         학습 기록
  memoir.html       시리즈 글 (다음 20년)
_posts/             게시물 (YYYY-MM-DD-title.md)
_drafts/            초안 (--drafts 로만 렌더)
assets/             CSS, 이미지, favicon
index.md            홈
next-20y.md         시리즈 랜딩 (질문 목록)
hospital-it-26y.md  구 URL 리다이렉트 스텁
_data/themes.yml    홈 주제 축 정의
tags.md             태그 인덱스
search.md           클라이언트 검색
to-study.md         앞으로 공부할 주제
about.md            소개
post-template.md    글쓰기 템플릿 (배포 제외)
404.html            404 페이지
robots.txt          검색엔진 크롤 정책
```

## 로컬 개발

```bash
bundle install
bundle exec jekyll serve --force_polling --drafts
# http://127.0.0.1:4000/Code25Y/
```

Windows에서는 `--force_polling` 이 없으면 파일 변경이 감지되지 않을 수 있습니다.
`--drafts` 는 `_drafts/` 안의 초안까지 미리보기합니다.

## 새 글 쓰기

### 학습 기록

1. `post-template.md`를 복사해 `_posts/YYYY-MM-DD-slug.md`로 저장
2. 카테고리는 `[study]` 또는 `[todo-study]` **하나만**
3. 세부 주제는 `tags:` 로 분류

### 시리즈 글 (다음 20년)

```yaml
---
layout: memoir
title: "..."
date: YYYY-MM-DD HH:MM:SS +0900
permalink: /next-20y/NN-slug/
tags: [hospital-it, ...]
series: next-20y
episode: N
excerpt: "..."
---
```

발행 후 `next-20y.md` 의 `planned` 목록에서 해당 항목을 지웁니다.
`episode` 는 질문 번호(Q1, Q2 …)로 쓰입니다.

## 배포

`main` 브랜치에 푸시하면 GitHub Pages가 자동 빌드합니다.
