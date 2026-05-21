---
layout: post
title: "‘클로드 디자인 말고 코덱스에서 Open Design’이라는 말이 왜 나오는지 자세히 정리"
date: 2026-05-22 01:34:37 +0900
categories: [study, notes]
excerpt: "Threads에서 본 '클로드 디자인 사용하지 말고 코덱스에서 OpenDesign을 써보라'는 짧은 문장을 출발점으로, Claude Design·Codex·Open Design의 실제 관계와 차이를 공식 자료와 프로젝트 문서를 바탕으로 다시 정리했다."
---

## 한 줄 요약
Threads 원문은 두 줄짜리 강한 추천에 가깝지만, 관련 자료를 자세히 따라가 보니 이 문장의 핵심은 **‘Claude Design’이라는 닫힌 제품 자체를 무조건 버리라는 뜻이라기보다, 디자인 생성 워크플로를 특정 벤더 안에 묶지 말고 Codex 같은 에이전트와 Open Design 같은 오픈 레이어로 옮겨 보라**는 제안에 더 가깝다고 느껴졌다.

## 왜 이 글을 남겨두나
짧은 SNS 글은 방향은 빠르게 알려 주지만, 맥락은 거의 남기지 않는다.

이번 Threads 원문도 딱 그랬다.

> 클로드 디자인 사용하지 마시고  
> 코덱스에서 OpenDesign 을 사용해보세요

이 문장만 보면 바로 이런 질문이 생긴다.

- Claude Design이 정확히 무엇인가
- Open Design은 단순 대체 UI인지, 아니면 별도 워크플로 레이어인지
- 왜 하필 Codex와 같이 쓰라는 말이 붙는가
- 실제로 더 좋은 건지, 아니면 단지 더 자유로운 건지
- 돈, 락인, 배포, 확장성 관점에서 무엇이 달라지는가

그래서 이번 글은 단순 요약보다, **이 주장 뒤에 깔린 제품 구조와 설계 철학 차이**를 중심으로 정리해 둔다.

## 원문에서 말한 핵심
Threads 원문은 아주 짧지만, 압축하면 대략 세 가지 주장으로 읽힌다.

1. **Claude Design 대신 Open Design을 보라.**
2. **그 Open Design은 Codex와 함께 쓰는 흐름이 괜찮다.**
3. **디자인 생성 툴을 선택할 때 결과물 품질만 보지 말고 작업 방식 자체를 보라.**

실제로 원문에 붙은 이미지도 이 해석을 강화한다. 화면에는 **Open Design**이라는 이름과 함께 다음 같은 기능이 보인다.

- Prototype
- Live artifact
- Slide deck
- Image
- Video
- Hyperframes
- Audit
- From Figma
- From folder
- From template

즉, 이 글은 단순히 “예쁜 시안 하나 뽑아준다”는 얘기보다, **프로토타입·덱·이미지·비디오까지 포함한 넓은 제작 환경**을 추천하는 문맥으로 읽는 편이 맞다.

## 먼저 제품들을 분리해서 이해해야 한다

### 1. Claude Design은 Anthropic이 만든 완성형 제품이다
Anthropic은 2026년 4월 **Claude Design**을 발표했다. 공식 소개에 따르면 Claude Design은 Claude와 함께 다음 같은 시각 작업을 만드는 제품이다.

- 디자인 시안
- 프로토타입
- 슬라이드
- 원페이저
- 랜딩 페이지
- 코드 기반 인터랙티브 경험

공식 설명에서 특히 눈에 띄는 포인트는 다음이다.

- **Claude Opus 4.7** 기반
- Claude Pro / Max / Team / Enterprise 사용 가능
- 대화, 인라인 코멘트, 직접 편집, 슬라이더 기반 수정 지원
- 팀의 **디자인 시스템**을 자동 적용 가능
- 프롬프트, 이미지, 문서, 웹 캡처, 코드베이스에서 시작 가능
- **Canva / PDF / PPTX / standalone HTML** 내보내기 지원
- 결과물을 **Claude Code**로 넘겨 빌드 handoff 가능

즉 Claude Design은 단순한 실험실이 아니라, **기획 → 프로토타입 → 브랜드 일관성 → 빌드 handoff**까지 한 제품 안에서 닫아 놓은 상용 디자인 워크플로에 가깝다.

### 2. Codex는 원래 ‘디자인 툴’이 아니라 에이전트 운영 중심의 코딩 앱이다
OpenAI의 **Codex app**은 공식적으로 “command center for agents”로 소개된다. 핵심은:

- 여러 에이전트를 병렬로 운영
- 긴 작업을 장시간 돌림
- skills와 automations로 워크플로 확장
- worktree 기반으로 충돌을 줄이며 같은 저장소를 동시에 다룸
- CLI / IDE / 앱 간 세션과 설정을 공유

즉 Codex는 본질적으로 **코드 작업을 맡기는 운영 도구**다. 그런데 OpenAI는 동시에 디자인 관련 사용례도 꽤 밀고 있다.

예를 들면:

- **스크린샷과 디자인 브리프를 코드로 구현**하는 프런트엔드 디자인 use case
- **Playwright**로 시각 검증을 반복하는 흐름
- **Figma MCP server**를 통해 Figma ↔ Codex를 양방향으로 오가는 워크플로

그래서 Codex는 순수 디자인 캔버스라기보다, **디자인 결과물을 코드와 연결해서 실제 제품까지 밀어붙이는 데 강한 에이전트 환경**이라고 보는 편이 맞다.

### 3. Open Design은 완성형 제품이라기보다 ‘얇은 오픈 레이어’에 가깝다
Open Design 쪽 문서와 GitHub 설명을 보면 가장 자주 반복되는 표현이 있다.

- **The open-source alternative to Claude Design**
- **We don't ship an agent. Yours is good enough.**
- **Skills are files, not plugins.**
- **Design systems are portable Markdown, not theme JSON.**

이 말들을 풀면 Open Design은 이렇게 이해할 수 있다.

- 자체 모델을 파는 서비스가 아니다
- 이미 쓰고 있는 에이전트 CLI를 디자인 엔진으로 쓴다
- 스킬은 `SKILL.md` 파일 묶음이다
- 디자인 시스템은 `DESIGN.md` 파일 묶음이다
- 로컬 daemon + web app 구조로 돌아간다
- **BYOK(Bring Your Own Key)** 가 기본 철학이다

즉 Open Design은 Claude Design처럼 “하나의 닫힌 제품”이라기보다, **Claude / Codex / Gemini / Qwen 같은 기존 에이전트 위에 디자인 워크플로를 씌우는 오픈소스 계층**에 가깝다.

## 왜 하필 “Codex에서 Open Design”이라는 표현이 나오는가

### 1. Open Design은 Codex를 공식 지원하는 쪽으로 자신을 설명한다
Open Design 공식 사이트와 GitHub 문서를 보면, 기존 에이전트 CLI들을 자동 감지해서 디자인 엔진으로 사용한다고 설명한다. 그 목록에는 **Codex**도 포함된다.

Open Design이 그리는 그림은 대략 이렇다.

- 사용자는 이미 신뢰하는 에이전트가 있다
- Open Design이 그 에이전트에 **스킬 + 디자인 시스템 + daemon + preview loop**를 붙인다
- 그러면 기존 코딩 에이전트가 **디자인 제작 에이전트**처럼 동작한다

그래서 “Codex에서 Open Design을 사용해보라”는 말은 결국 **Codex 자체가 이미 파일·프로젝트·에이전트 운영에 강하니, 그 위에 Open Design 레이어를 얹는 조합이 괜찮다**는 제안이다.

### 2. Codex는 디자인을 코드와 연결하는 쪽에서 강점이 있다
이 조합이 말이 되는 이유는 Codex가 단순 채팅이 아니라 다음을 잘하는 구조이기 때문이다.

- 프로젝트 폴더를 기반으로 문맥을 유지
- 여러 작업을 병렬로 돌림
- skills를 통해 반복 워크플로를 재사용
- 디자인 참조를 코드화하고 검증 루프까지 포함시킴
- Figma MCP를 통해 **디자인 ↔ 코드 왕복**을 연결

OpenAI 공식 문서의 프런트엔드 디자인 use case도 결국 이렇게 요약할 수 있다.

> 스크린샷, 디자인 브리프, 레퍼런스를 입력하고 → 기존 디자인 시스템에 맞춰 코드로 구현하고 → Playwright로 화면을 검증하며 반복 수정한다.

이건 Claude Design식 “캔버스 중심 디자인”과는 조금 다르다. 더 정확히 말하면 **디자인을 코드 결과물과 분리하지 않고 바로 구현 대상으로 다루는 흐름**이다.

### 3. Open Design도 결국 ‘파일 기반 워크플로’에 베팅하고 있다
Open Design의 문서에서 가장 인상적인 건 스킬·시스템·어댑터가 전부 **텍스트 파일 중심**이라는 점이다.

- `SKILL.md`: 작업 능력의 단위
- `DESIGN.md`: 미감/브랜드 시스템의 단위
- adapter: 어떤 에이전트를 붙일지 정하는 얇은 연결층
- daemon: 이 네 가지를 묶는 실행 루프

이 구조는 Codex가 좋아하는 작업 방식과도 잘 맞는다.

- 저장소 중심
- 파일 중심
- 재사용 가능한 규칙 중심
- 반복 가능한 자동화 중심

즉 Codex와 Open Design 조합은 우연히 붙는 게 아니라, **둘 다 채팅창보다 파일과 워크플로를 더 중요한 단위로 본다**는 점에서 잘 맞물린다.

## Claude Design과 Open Design은 무엇이 실제로 다른가

### 1. 가장 큰 차이는 품질보다 ‘소유권’에 있다
Open Design 공식 블로그는 아주 노골적으로 이렇게 말한다.

> Claude Design is a product. `open-codesign` is a clone. Open Design is a layer.

이 문장이 거의 핵심 요약이다.

- **Claude Design**: Anthropic이 운영하는 완성형 상용 제품
- **Open Design**: 에이전트 위에 얹는 오픈 레이어

그래서 두 도구의 차이는 단지 “어느 쪽이 더 예쁜 결과를 뽑는가”가 아니다. 더 중요한 차이는 아래에 있다.

- 누가 모델을 정하는가
- 누가 프롬프트 스택을 통제하는가
- 작업 데이터가 어디를 거치는가
- 스킬과 시스템을 수정·감사·포크할 수 있는가
- 과금 구조를 사용자가 직접 제어할 수 있는가

### 2. Claude Design은 편한 대신 닫혀 있다
Anthropic의 공식 발표를 보면 Claude Design은 확실히 매력적이다.

- 빠르게 첫 시안을 만든다
- 팀 디자인 시스템을 읽어 브랜드 일관성을 맞춘다
- 결과물을 Canva, PPTX, HTML 등으로 내보낸다
- Claude Code로 자연스럽게 handoff 된다

하지만 Open Design 쪽 비교 글이 지적하는 구조적 tradeoff도 분명하다.

- **모델이 사실상 Claude에 고정**된다
- 런타임이 **호스팅형**이다
- 내부 프롬프트/툴 동작을 사용자가 감사하거나 포크하기 어렵다
- 과금이 구독 중심이라 팀 규모가 커지면 부담이 커질 수 있다

즉 Claude Design은 “바로 잘 굴러가는 제품”으로서 강하지만, **내 워크플로를 내가 소유하는 구조**와는 거리가 있다.

### 3. Open Design은 자유로운 대신 아직 거칠다
반대로 Open Design은 자유가 많다.

- 기존 에이전트를 그대로 사용 가능
- OpenAI-compatible endpoint면 붙이기 쉬움
- 로컬 우선, BYOK 우선
- 스킬과 디자인 시스템을 파일로 관리 가능
- 필요하면 포크와 수정이 쉬움

하지만 이것이 곧바로 “더 완성된 제품”을 의미하지는 않는다. Open Design 공식 블로그도 현재 문제를 꽤 솔직하게 적고 있다.

예를 들어 공개된 글에는 이런 이슈들이 나온다.

- 일부 provider 흐름에서 **Finish Design** 같은 마무리 단계가 깨짐
- Windows + 특정 모델 조합에서 stdin/CLI 문제가 발생
- 어떤 어댑터는 긴 프롬프트에서 플랫폼 제약을 받음
- 로컬 모델 warmup 때문에 연결 테스트가 늦어짐

즉 Open Design은 철학적으로는 매우 매력적이지만, 아직은 **오픈소스 툴체인의 현실적인 거침과 어댑터 경계 문제**를 함께 감수해야 한다.

## 그러면 왜 사람들은 Claude Design보다 Open Design에 끌릴까

### 1. 벤더 락인을 피하고 싶어서
이게 가장 큰 이유로 보인다.

Claude Design은 강력하지만 Claude 생태계 안에 묶인다. 반면 Open Design은 문서상으로:

- Claude
- Codex
- Gemini
- Cursor
- Copilot
- OpenCode
- Hermes
- Qwen

같은 다양한 에이전트를 어댑터로 붙이는 방향을 택한다.

이 차이는 단순 취향 문제가 아니다. 장기적으로는 아래에 영향을 준다.

- 모델 비용 최적화
- 특정 모델 품질 변화 대응
- 회사 보안 정책 대응
- 내부 self-hosted 모델 활용 가능성
- 팀 전체 워크플로의 이식성

### 2. 디자인 결과물을 코드 제작 루프에 더 밀착시키고 싶어서
Claude Design은 디자인-우선 제품이고, Codex는 코드-우선 에이전트다.

그래서 개발자나 디자인 엔지니어 입장에서는 후자가 더 편할 때가 많다.

- 시안을 만드는 즉시 파일이 생기고
- 저장소 문맥 안에서 수정하고
- 검증 루프를 자동화하고
- 바로 구현까지 연결할 수 있기 때문이다.

Open Design은 이 사이를 메우려 한다. 즉, **디자인을 캔버스에서 끝내지 않고 코드 작업 시스템 안으로 끌고 들어오는 레이어**가 되려는 것이다.

### 3. 프롬프트가 아니라 ‘워크플로’를 재사용하고 싶어서
Open Design은 스킬과 디자인 시스템을 파일로 둔다. 이건 꽤 중요하다.

매번 “이런 느낌의 랜딩 페이지 만들어줘”라고 다시 설명하는 대신,

- 어떤 skill을 쓸지
- 어떤 design system을 쓸지
- 어떤 출력 형식을 만들지
- 어떤 검증 단계를 거칠지

를 재사용 가능한 자산으로 굳혀 둘 수 있기 때문이다.

이건 AI 디자인에서 꽤 중요한 변화다. **좋은 한 번짜리 프롬프트보다, 반복 가능한 작업 레이어를 만드는 쪽**으로 무게가 실린다.

## Codex + Open Design 조합이 특히 잘 맞는 사람

### 잘 맞을 가능성이 큰 경우
- 이미 **Codex / Claude Code / Cursor 같은 에이전트형 도구**를 자주 쓰는 사람
- 디자인 결과물을 결국 **코드와 저장소 안에서 관리**해야 하는 사람
- Figma, 코드, 프로토타입, 발표자료를 한 흐름으로 엮고 싶은 사람
- 벤더 락인보다 **이식성, 포크 가능성, BYOK**를 중시하는 사람
- 로컬 우선 / 오픈소스 우선 도구를 선호하는 사람

### 덜 맞을 수 있는 경우
- 설치/세팅보다 즉시 결과가 더 중요한 사람
- 로컬 daemon, adapter, CLI 같은 개념이 번거로운 사람
- 팀 차원에서 **완성도 높은 managed product**를 원하고 비용 감수 의사가 있는 사람
- 디자인 툴의 안정성과 polished UX를 최우선으로 보는 사람

## 내가 가져갈 포인트
- **Threads 원문은 단순 툴 추천처럼 보이지만, 실제로는 ‘닫힌 디자인 제품’보다 ‘오픈 워크플로 레이어’를 선택하라는 제안에 가깝다.**
- **Claude Design은 완성형 제품으로서 강하고, Open Design은 소유권과 확장성에서 강하다.**
- **Codex는 순수 디자인 툴은 아니지만, 디자인을 코드 제작 루프에 밀착시키는 환경으로 꽤 강하다.**
- **Open Design이 Codex와 잘 맞는 이유는 둘 다 채팅보다 파일·프로젝트·반복 가능한 워크플로를 더 중요한 단위로 보기 때문이다.**
- **다만 Open Design은 아직 오픈소스 툴체인 특유의 거친 부분과 adapter 이슈를 감수해야 한다.**

## 아직 헷갈리는 것
- 실제 시안 품질만 놓고 볼 때, Claude Design과 Open Design + Codex 조합 중 어느 쪽이 더 일관된 결과를 내는지는 더 많은 실사용 비교가 필요하다.
- Open Design의 site/repo 문서에는 skills·systems 숫자가 빠르게 변하고 일부 섹션 간 수치 차이도 보여서, 현재 스펙은 꽤 빠르게 움직이는 것으로 보인다.
- Figma 중심 협업이 강한 팀에서 Open Design의 파일 기반 시스템이 얼마나 자연스럽게 녹는지도 더 봐야 한다.
- 디자인 직군과 개발 직군이 섞인 팀에서, 누가 어떤 레이어를 관리하는 것이 가장 효율적인지도 아직 정답이 명확하지 않다.

## 나중에 더 볼 것
- Open Design을 실제로 로컬에 띄워 **Codex adapter** 기준으로 어느 정도까지 안정적으로 동작하는지 직접 확인해 보기
- Codex + Figma MCP + Playwright 검증 루프를 따로 정리해 보기
- Claude Design의 handoff bundle과 Open Design의 skill/system 구조를 비교해 보기
- 랜딩 페이지, 슬라이드, 앱 프로토타입처럼 서로 다른 산출물에서 어떤 조합이 더 유리한지 케이스별로 나눠 보기

## 참고 링크
- [원문 Threads 글](https://www.threads.com/@koreaaiacademy/post/DYmPKefkeb3?xmt=AQG0vsPwXacxZgqtaJQQn2D3-l0EtsjysqYq1vDWMfNfN-R5ocRqG0XlZZT9a8996mTV_sWI&slof=1)
- [Anthropic - Introducing Claude Design by Anthropic Labs](https://www.anthropic.com/news/claude-design-anthropic-labs)
- [OpenAI - Introducing the Codex app](https://openai.com/index/introducing-the-codex-app)
- [OpenAI Developers - Building frontend UIs with Codex and Figma](https://developers.openai.com/blog/building-frontend-uis-with-codex-and-figma)
- [OpenAI Developers - Build responsive front-end designs](https://developers.openai.com/codex/use-cases/frontend-designs)
- [Open Design 공식 사이트](https://open-design.ai)
- [Open Design GitHub 저장소](https://github.com/nexu-io/open-design)
- [Open Design - The open-source alternative to Claude Design](https://open-design.ai/blog/open-source-alternative-to-claude-design)
- [Open Design - BYOK design workflow: run Claude, Codex, or Qwen on your own key](https://open-design.ai/blog/byok-design-workflow-claude-codex-qwen/)
- [Open Design - BYOK reality check: 5 things that break in Open Design today](https://open-design.ai/blog/byok-reality-check-5-things-that-break/)
- [Figma Developer Docs - Figma MCP server tools and prompts](https://developers.figma.com/docs/figma-mcp-server/tools-and-prompts)
- [Figma Developer Docs - Code to canvas](https://developers.figma.com/docs/figma-mcp-server/code-to-canvas)

## 기준 날짜 메모
이 글은 **2026-05-22** 기준으로, 사용자가 공유한 Threads 글과 Anthropic / OpenAI / Figma / Open Design 공식 자료를 함께 보고 정리한 메모다.  
원문은 매우 짧은 추천 문장이라서, 이 글에서는 그 추천을 그대로 옮기기보다 **제품 구조, 락인, 워크플로, 실제 tradeoff**를 분리해 다시 해석하는 데 초점을 맞췄다.
