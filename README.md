# Code25Y

Song seung yul의 개인 학습 아카이브. Jekyll + GitHub Pages 기반.

**공개 주소**: <https://songseungyul.github.io/Code25Y/>

## 구조

```
_config.yml         Jekyll 설정
_layouts/           default, home, page, post 레이아웃
_posts/             게시물 (YYYY-MM-DD-title.md)
assets/             CSS, 이미지, favicon
about.md            소개
index.md            홈
search.md           클라이언트 검색
tags.md             태그 인덱스
notes.md            짧은 메모 (계획 단계)
to-study.md         앞으로 공부할 주제
post-template.md    글쓰기 템플릿 (배포 제외)
404.html            404 페이지
robots.txt          검색엔진 크롤 정책
```

## 로컬 개발

```bash
bundle install
bundle exec jekyll serve
# http://127.0.0.1:4000/Code25Y/
```

## 새 글 쓰기

1. `post-template.md`를 복사해 `_posts/YYYY-MM-DD-slug.md`로 저장
2. 카테고리는 `[study]` (또는 `[todo-study]`) 하나만
3. `tags`로 세부 주제 분류
4. 홈에 노출하고 싶은 글은 front matter에 `featured: true`

## 배포

`main` 브랜치에 푸시하면 GitHub Pages가 자동 빌드합니다.
