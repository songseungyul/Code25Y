---
layout: post
title: "Egonex-AI/Understand-Anything를 읽고 정리한 메모: 코드베이스를 지식 그래프로 바꿔서 배우는 방식"
date: 2026-08-02 15:29:31 +0900
categories: [study, notes]
excerpt: "Egonex-AI/Understand-Anything 저장소의 README와 CLAUDE.md, 구조 문서를 바탕으로 이 프로젝트가 왜 단순 코드 시각화가 아니라 온보딩과 이해를 위한 지식 그래프 도구인지 정리했다."
---

## 한 줄 요약
`Understand Anything`은 **코드베이스나 위키, 문서를 Tree-sitter 기반 구조 분석과 LLM 기반 의미 분석으로 지식 그래프로 바꾼 뒤, 대시보드·검색·투어·질문 기능으로 '읽는 코드'를 '탐색하는 코드'로 바꾸려는 도구**로 보였습니다.

## 왜 이 글을 남기나
큰 저장소를 볼 때 늘 비슷한 문제가 생깁니다.

- 파일이 너무 많아서 어디서 시작해야 할지 모르겠고
- 함수/클래스 관계를 머릿속으로만 추적해야 하고
- 전체 구조와 실제 비즈니스 흐름이 따로 노는 경우가 많습니다.

`Understand Anything`은 이 문제를 “요약을 더 잘해 준다” 수준이 아니라,
**코드베이스를 아예 지식 그래프로 재구성해서 탐색 대상으로 바꾸자**는 방향으로 풀려고 합니다.

그래서 이 프로젝트는 단순한 코드 인덱서나 문서 생성기보다,
**신규 팀원 온보딩 + 구조 이해 + 변경 영향 파악**을 위한 학습 도구에 더 가깝게 느껴졌습니다.

## 저장소 정보
- 저장소: [Egonex-AI/Understand-Anything](https://github.com/Egonex-AI/Understand-Anything)
- 설명: *Turn any code into an interactive knowledge graph you can explore, search, and ask questions about.*
- 라이선스: MIT
- 기본 브랜치: `main`
- 대표 언어: TypeScript
- 확인 시점 스타 / 포크 / 오픈 이슈: **77,074 / 6,458 / 259**
- 저장소 생성 시점: 2026-03-15
- 확인한 최신 클론 커밋: `fe8c5bc`
- 정리 기준: 2026-08-02에 확인한 `README.md`, `CLAUDE.md`, `package.json`, 문서 검색 결과, 실제 디렉터리 구조

## 먼저 인상부터 보면
<figure>
  <a href="{{ '/assets/images/posts/2026-08-02-understand-anything-hero.png' | relative_url }}" target="_blank" rel="noopener noreferrer">
    <img src="{{ '/assets/images/posts/2026-08-02-understand-anything-hero.png' | relative_url }}" alt="Understand Anything 대표 소개 이미지" />
  </a>
  <figcaption>
    출처: <a href="https://github.com/Egonex-AI/Understand-Anything" target="_blank" rel="noopener noreferrer">Egonex-AI/Understand-Anything README</a>
  </figcaption>
</figure>

README의 핵심 문구는 꽤 명확합니다.

- *Stop reading code blind.*
- *Start seeing the big picture.*

즉 이 프로젝트는 코드를 더 빨리 읽게 하려는 게 아니라,
**코드의 큰 그림을 먼저 보게 하려는 도구**입니다.

## 공부한 것

### 1. 이 프로젝트의 핵심은 “그래프를 그린다”가 아니라 “그래프로 가르친다”에 가깝다
README에서 가장 인상적이었던 문장은 이것이었습니다.

> The goal isn't a graph that wows you with how complex your codebase is — it's a graph that quietly teaches you how every piece fits together.

이 문장이 이 프로젝트의 성격을 잘 설명한다고 느꼈습니다.

많은 그래프 도구는 결국 “복잡함을 더 멋지게 보여 주는 시각화”에 머무르기 쉽습니다. 그런데 `Understand Anything`은 적어도 문서상으로는 그 반대편을 지향합니다.

- 복잡함 과시보다 이해 보조
- 시각적 인상보다 온보딩 순서
- 구조 출력보다 학습 경로 제공

그래서 이 프로젝트는 데이터 시각화 툴보다,
**그래프를 매개로 한 코드 학습 인터페이스**에 더 가깝게 보였습니다.

### 2. 단순 코드 구조뿐 아니라 비즈니스 로직과 지식 베이스까지 다루려 한다
README를 보면 대상 범위가 생각보다 넓습니다.

기본은 코드베이스 분석이지만, 기능이 세 갈래로 나뉩니다.

1. **Structural graph**
   - 파일, 함수, 클래스, 의존성 그래프
2. **Domain view**
   - 코드가 실제 비즈니스 도메인/플로우/스텝과 어떻게 연결되는지 표현
3. **Knowledge base analysis**
   - Karpathy 스타일 LLM wiki 같은 지식 베이스를 그래프로 변환

즉 이 프로젝트는 “코드를 그래프로 만든다”에서 끝나지 않고,
**코드 구조 / 비즈니스 구조 / 위키 지식 구조를 같은 탐색 패러다임 안에 넣으려는 시도**로 읽힙니다.

이게 흥미로운 이유는, 실제로 프로젝트를 이해할 때 필요한 정보가 항상 소스코드만은 아니기 때문입니다.

- 코드가 무엇을 호출하는가
- 이 코드가 어떤 업무 흐름에 속하는가
- 팀 지식 문서에서는 이 개념이 어떻게 설명되는가

`Understand Anything`은 이 세 층을 한데 묶으려는 야심이 있습니다.

### 3. Quick Start 흐름은 꽤 선명하다: analyze → dashboard → chat/explain/diff/onboard
README 기준 기본 사용 흐름은 명확합니다.

1. 플러그인 설치
2. `/understand`
3. `/understand-dashboard`
4. 필요에 따라 `/understand-chat`, `/understand-diff`, `/understand-explain`, `/understand-onboard`, `/understand-domain`, `/understand-knowledge`

즉 이 프로젝트의 진입점은 결국 `/understand`입니다.

이 명령이 프로젝트 전체를 스캔해서 `.ua/knowledge-graph.json`을 만들고, 이후의 대부분 기능은 그 그래프를 활용합니다.

개인적으로 이 점이 좋았습니다. 도구가 복잡해 보여도 내부 중심 데이터가 명확합니다.

- 1차 산출물: 지식 그래프 JSON
- 2차 산출물: 대시보드/질문/영향 분석/온보딩/도메인 해석

즉 “무엇이 핵심 데이터인가?”가 분명한 구조입니다.

### 4. 구조 분석은 Tree-sitter, 의미 분석은 LLM으로 나누는 하이브리드 설계가 핵심이다
README의 `Under the Hood`와 `CLAUDE.md`를 보면 이 프로젝트의 기술적 핵심은 분명합니다.

- **Tree-sitter (deterministic)**
- **LLM (semantic)**

문서가 말하는 역할 분리는 대략 이렇습니다.

#### Tree-sitter 쪽이 맡는 것
- imports / exports
- function / class definitions
- call sites
- inheritance
- fingerprint 기반 incremental change detection

#### LLM 쪽이 맡는 것
- plain-English summaries
- architectural layer assignments
- business-domain mapping
- guided tours
- language concept callouts

이 분리는 꽤 설득력이 있습니다.

정적 분석만으로는 “이 파일이 왜 존재하는가”를 설명하기 어렵고,
반대로 LLM만으로는 구조 사실을 재현 가능하게 추출하기 어렵습니다.

그래서 `Understand Anything`은
**구조는 결정론적으로, 의미는 추론적으로** 다루는 하이브리드 모델을 취합니다.

이건 단순 기능 조합이 아니라, 코드 이해 도구로서 꽤 자연스러운 설계처럼 보였습니다.

### 5. multi-agent pipeline도 역할이 비교적 선명하다
README에 나온 `/understand` 파이프라인은 다음 역할들로 나뉩니다.

- `project-scanner`
- `file-analyzer`
- `architecture-analyzer`
- `tour-builder`
- `graph-reviewer`
- `domain-analyzer` (`/understand-domain`)
- `article-analyzer` (`/understand-knowledge`)

그리고 `CLAUDE.md`를 보면 저장소 내부 구조도 이 파이프라인을 중심으로 정리되어 있습니다.

- `packages/core`
- `packages/dashboard`
- `src/` 스킬 소스
- `skills/`
- `agents/`

즉 이 프로젝트는 그냥 “한 번 LLM을 부른다”가 아니라,
**스캔 → 파일 분석 → 아키텍처 해석 → 투어 생성 → 리뷰** 식의 파이프라인으로 이해 작업을 쪼갠 구조입니다.

이건 결과 품질뿐 아니라 유지보수 측면에서도 의미가 큽니다. 어떤 단계가 흔들리는지 추적하기 쉬워지기 때문입니다.

### 6. incremental update와 auto-update가 실사용성을 크게 좌우할 것 같다
README에서 현실적으로 중요한 포인트는 여기에 있다고 느꼈습니다.

초기 `/understand`는 전체 코드베이스를 분석하므로 토큰을 많이 쓸 수 있습니다. 문서도 이 점을 솔직하게 경고합니다. 대신 이후에는 **incremental by default**입니다.

즉 바뀐 파일만 다시 분석합니다.

또 `/understand --auto-update`를 통해 commit 후 자동 갱신 흐름까지 제공합니다.

이 점이 중요한 이유는, 이런 도구는 첫 데모가 아니라 **계속 최신 상태를 유지할 수 있느냐**가 실제 가치와 직결되기 때문입니다.

- 한 번만 멋지게 그래프를 만드는 건 데모이고
- 변경분만 따라가며 최신 그래프를 유지하는 건 도구입니다.

그 점에서 `Understand Anything`은 적어도 방향성은 꽤 실용적입니다.

### 7. 다국어와 다중 플랫폼 지원에 상당히 공을 들인 프로젝트로 보인다
README에서 아주 눈에 띄는 부분 중 하나는 플랫폼 확장입니다.

문서상 지원 범위가 매우 넓습니다.

- Claude Code
- Cursor
- VS Code + GitHub Copilot
- Copilot CLI
- Codex
- OpenCode
- Gemini CLI
- Hermes
- Cline
- Kiro
- Trae
- Nanobot
- 기타 등등

즉 이 프로젝트는 특정 한 에이전트 생태계 안에서만 살기보다,
**'코드 이해 스킬' 자체를 여러 AI 코딩 플랫폼에 이식 가능한 형태로 패키징하려는 시도**로 보입니다.

또 README는 언어 지원도 강조합니다.

- `en`
- `zh`
- `zh-TW`
- `ja`
- `ko`
- `ru`

그리고 첫 실행 시 대화 언어를 감지해 저장한다는 설명도 있습니다.

이건 결국 이 도구를 단순 내부 실험이 아니라,
**실제 여러 사용자 환경에 배포 가능한 제품**으로 보려는 흔적으로 읽혔습니다.

### 8. 팀 공유 관점에서 graph JSON을 커밋 가능한 산출물로 본 점이 좋다
README의 `Share the Graph with Your Team` 섹션도 개인적으로 좋았습니다.

핵심 아이디어는 간단합니다.

- 그래프는 결국 JSON이다.
- 한 번 생성해서 `.ua/`를 커밋하면
- 팀원은 LLM 없이도 뷰어로 그래프를 열 수 있다.

즉 이 프로젝트는 결과물을 “세션 안에서만 쓰는 일회성 분석물”이 아니라,
**팀이 같이 버전 관리할 수 있는 문서/아티팩트**로 봅니다.

이건 굉장히 실용적인 관점입니다.

왜냐하면 온보딩 자료나 구조 문서는 자주 낡지만, 코드와 함께 버전 관리되는 그래프라면 그나마 최신성을 유지하기 쉬우니까요.

또 viewer를 별도 tarball / npx 경로로 제공하는 점도,
**분석 실행자와 결과 소비자를 분리하려는 설계**로 보였습니다.

### 9. 대시보드는 단순 graph viewer가 아니라 탐색형 학습 UI를 지향한다
`CLAUDE.md`를 보면 대시보드 철학도 조금 더 구체적으로 보입니다.

- graph-first layout
- 75% graph + 360px sidebar
- persona-adaptive UI
- file explorer tree
- source viewer
- schema validation on graph load

즉 대시보드는 “예쁜 그래프 화면”이 아니라,
**그래프 / 파일 / 설명 / 코드 원문을 오가며 학습하는 UI**를 의도한 것으로 보입니다.

특히 README의 기능 목록도 이 방향과 맞닿아 있습니다.

- guided tours
- fuzzy & semantic search
- diff impact analysis
- persona-adaptive UI
- language concepts

이걸 보면 `Understand Anything`의 대시보드는 시각화보다
**학습 보조, 질의 응답, 맥락 전환**에 더 초점을 맞추고 있는 것으로 읽혔습니다.

### 10. 실제로는 ‘거대한 코드베이스를 처음 접하는 사람’을 위한 도구라는 정체성이 분명하다
README가 던지는 대표 시나리오는 아주 직관적입니다.

> You just joined a new team. The codebase is 200,000 lines of code. Where do you even start?

이 문장이 이 도구의 가장 좋은 요약처럼 느껴졌습니다.

즉 이 프로젝트는 고급 아키텍처 시각화 도구이기도 하지만,
더 본질적으로는 **새 팀원 / 새 프로젝트 / 낯선 모노레포 앞에서 길을 잃지 않게 해 주는 도구**를 지향합니다.

그 점에서 개인적으로는,
이 도구의 가장 강한 유스케이스가 “AI가 다 해준다”가 아니라
**사람이 더 빨리 이해하도록 도와준다**는 데 있다고 봤습니다.

## 저장소를 보며 정리한 메모

### 이 프로젝트가 특히 흥미로운 이유
- 구조 분석과 의미 분석의 역할 분리가 비교적 명확하다.
- 결과물을 지식 그래프 JSON으로 남겨 재사용성과 팀 공유성을 확보한다.
- 코드뿐 아니라 비즈니스 도메인과 위키 지식까지 같은 탐색 방식으로 묶으려 한다.
- 여러 AI 코딩 플랫폼에 붙는 형태로 배포 전략을 가져간다.

### 현실적으로 좋아 보이는 지점
실제 코드베이스 이해에서 중요한 것은 “요약을 몇 줄 더 잘한다”보다,
**어디서 시작해야 하고, 무엇이 무엇과 연결되고, 내가 바꾼 것이 어디까지 영향을 미치는지**를 빠르게 알게 해 주는 것입니다.

그 점에서 `Understand Anything`은 꽤 좋은 문제 정의를 갖고 있습니다.

- 신규 팀원 온보딩
- 대형 저장소 구조 탐색
- 변경 영향 파악
- 위키/문서 지식 연결
- 그래프 기반 학습 경로 생성

### 동시에 조심해서 봐야 할 점
물론 이런 도구는 기대치가 너무 높아지기 쉽습니다.

- 그래프가 있다고 해서 자동으로 이해가 쉬워지는 것은 아닐 수 있음
- LLM이 만든 요약/레이어/도메인 해석은 틀릴 수 있음
- 아주 큰 저장소에서는 초기 토큰 비용이 큼
- 멀티 플랫폼 지원이 넓은 만큼 실제 품질 편차가 있을 수 있음

즉 이 프로젝트는 강력하지만,
**정답 생성기라기보다 이해를 돕는 보조 레이어**로 보는 편이 더 현실적일 것 같습니다.

## 아직 헷갈리는 것
- 매우 큰 모노레포에서 그래프 품질과 성능이 어디까지 유지되는지 더 봐야 합니다.
- domain view가 실제 비즈니스 프로세스를 얼마나 정확하게 포착하는지도 더 사례를 보고 싶습니다.
- knowledge wiki 분석 기능이 실제 장문 문서 집합에서 얼마나 안정적으로 관계를 뽑는지도 궁금합니다.
- 다중 플랫폼 지원이 문서상으로는 넓지만, 각 플랫폼별 실제 사용감은 더 확인이 필요합니다.

## 나중에 더 볼 것
- 실제 저장소 하나에 붙여서 onboarding tour 품질 확인하기
- diff impact analysis가 PR 리뷰 전에 어느 정도까지 유용한지 보기
- `.ua/knowledge-graph.json`을 팀 리포지토리에 커밋하는 방식이 실제 협업에서 잘 굴러가는지 보기
- Hermes 환경에 설치했을 때 어떤 UX로 동작하는지 직접 확인하기

## 참고 링크
- [GitHub - Egonex-AI/Understand-Anything](https://github.com/Egonex-AI/Understand-Anything)
- [README.md](https://github.com/Egonex-AI/Understand-Anything/blob/main/README.md)
- [CLAUDE.md](https://github.com/Egonex-AI/Understand-Anything/blob/main/CLAUDE.md)
- [Live Demo](https://understand-anything.com/demo/)

## 기준 날짜 메모
이 글은 **2026-08-02** 기준으로 저장소 공개 페이지와 로컬로 확인한 소스 파일을 바탕으로 정리했습니다. 스타 수, 포크 수, 이슈 수, 지원 플랫폼, 명령어 구조, 내부 파이프라인은 이후 달라질 수 있습니다.
