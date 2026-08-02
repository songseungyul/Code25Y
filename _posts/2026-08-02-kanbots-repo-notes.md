---
layout: post
title: "leodavinci1/kanbots를 읽고 정리한 메모: 칸반 보드 자체가 멀티 에이전트 작업실이 되는 방식"
date: 2026-08-02 14:54:19 +0900
categories: [study]
tags: [repo-notes, ai-agent, kanban, orchestration]
excerpt: "leodavinci1/kanbots 저장소의 README와 architecture, agents 문서를 읽고, 이 프로젝트가 왜 단순 칸반 앱이 아니라 에이전트 오케스트레이션 UI에 가까운지 정리했다."
---

## 한 줄 요약
`kanbots`는 단순한 칸반 앱이 아니라, **이슈 보드 위에서 여러 에이전트 CLI를 병렬로 돌리고, 각 실행을 별도 worktree에 격리하며, 필요하면 스스로 태스크를 쪼개고 QA까지 돌리게 만드는 로컬 중심 멀티 에이전트 작업 환경**으로 보였습니다.

## 왜 이 글을 남기나
요즘 에이전트 도구를 보다 보면 두 가지 질문이 계속 남습니다.

1. 에이전트를 한 번 실행하는 것과, 여러 작업을 동시에 운영하는 것은 어떻게 다른가?
2. 실제 개발 프로젝트에서는 채팅창보다 **작업 보드와 브랜치 관리**가 더 중요하지 않은가?

`kanbots`는 바로 그 두 질문 쪽으로 많이 기울어져 있습니다.

이 프로젝트는 “에이전트에게 한 번 시켜 본다”보다,
**작업을 카드 단위로 쌓아 두고, 각 카드에 서로 다른 에이전트를 붙이고, 그 결과를 worktree 단위로 관리하는 개발 운영 UI**를 만들려는 쪽에 가깝습니다.

그래서 개인적으로는 “또 하나의 AI 앱”이라기보다,
**칸반 보드를 에이전트 운영 콘솔로 바꾸려는 시도**로 읽혔습니다.

## 저장소 정보
- 저장소: [leodavinci1/kanbots](https://github.com/leodavinci1/kanbots)
- 설명: *Local collaboration interface for working on a kanban board where each task is either a Claude Code or Codex agent.*
- 라이선스: MIT
- 기본 브랜치: `main`
- 대표 언어: TypeScript
- 확인 시점 스타 / 포크 / 오픈 이슈: **579 / 52 / 6**
- 저장소 생성 시점: 2026-04-25
- 확인한 최신 클론 커밋: `7b256a0`
- 정리 기준: 2026-08-02에 확인한 `README.md`, `docs/architecture.md`, `docs/agents.md`, `package.json`, 실제 패키지 구조

## 먼저 화면부터 보면
<figure>
  <a href="{{ '/assets/images/posts/2026-08-02-kanbots-board-overview.png' | relative_url }}" target="_blank" rel="noopener noreferrer">
    <img src="{{ '/assets/images/posts/2026-08-02-kanbots-board-overview.png' | relative_url }}" alt="kanbots 보드 개요 화면" />
  </a>
  <figcaption>
    출처: <a href="https://github.com/leodavinci1/kanbots" target="_blank" rel="noopener noreferrer">leodavinci1/kanbots README</a>
  </figcaption>
</figure>

<figure>
  <a href="{{ '/assets/images/posts/2026-08-02-kanbots-run-detail.png' | relative_url }}" target="_blank" rel="noopener noreferrer">
    <img src="{{ '/assets/images/posts/2026-08-02-kanbots-run-detail.png' | relative_url }}" alt="kanbots 에이전트 실행 상세와 decision prompt 화면" />
  </a>
  <figcaption>
    출처: <a href="https://github.com/leodavinci1/kanbots" target="_blank" rel="noopener noreferrer">leodavinci1/kanbots README</a>
  </figcaption>
</figure>

첫 화면만 봐도 성격이 꽤 분명합니다.

- 왼쪽은 **보드 중심 작업 관리**
- 오른쪽은 **각 카드에 붙은 에이전트 실행의 실시간 스레드**

즉, 이 프로젝트의 핵심 화면은 채팅창이 아니라 **보드 + 런 디테일**입니다.

## 공부한 것

### 1. `kanbots`의 핵심은 “에이전트를 쓰는 칸반”이 아니라 “칸반이 에이전트 런타임이 되는 것”에 가깝다
README 첫 문장은 아주 세게 방향을 잡습니다.

> A kanban board that runs 11 agent CLIs in parallel.

중요한 건 여기서 “11 agent CLIs”보다도 **runs in parallel** 쪽이라고 느꼈습니다.

즉 `kanbots`는 단순히 Claude Code나 Codex 중 하나를 연결하는 앱이 아닙니다. 작업 카드마다 독립적인 실행 컨텍스트를 만들고, 그것들을 동시에 굴릴 수 있는 운영 환경을 만들려는 쪽입니다.

README 기준 지원 대상은 다음과 같습니다.

- Claude Code
- Codex
- Gemini
- Cursor
- GitHub Copilot CLI
- Amp
- OpenCode
- Droid
- CCR
- Qwen
- ACP-compatible CLI 전반

이 목록만 보면 “지원 에이전트가 많다” 정도로 보일 수 있지만, 더 중요한 건 **각 런을 별도 worktree로 격리해서 병렬로 굴리는 방식**입니다.

결국 이 저장소는 모델 비교 앱이라기보다,
**여러 에이전트를 프로젝트 보드 문맥에서 운용하는 오케스트레이션 UI**에 가깝습니다.

### 2. local-first라는 점이 생각보다 중요하다
README를 읽으며 좋게 본 포인트 중 하나는 **local-first issues**를 기본으로 둔 점입니다.

이 프로젝트는 두 가지 작업 모드를 둡니다.

- `local`: `.kanbots/db.sqlite`에 이슈 저장
- `github`: GitHub Issues와 동기화

이 구분이 중요한 이유는, 현실의 많은 작업이 꼭 GitHub 이슈로 시작하지 않기 때문입니다.

개인 프로젝트나 실험 저장소에서는
- 이슈를 굳이 GitHub에 안 올리고 싶을 수 있고
- 아직 정제되지 않은 잡일을 먼저 로컬에서 쌓고 싶을 수 있고
- 외부 공개 없이 혼자 에이전트를 돌리고 싶을 수 있습니다.

`kanbots`는 이 점을 잘 짚습니다.

즉 처음부터 “GitHub에 묶인 협업 도구”가 아니라,
**로컬 SQLite를 기본값으로 하고 필요할 때만 GitHub 모드로 올라가는 구조**입니다. 이건 제품 철학이 꽤 실용적이라는 뜻으로 읽혔습니다.

### 3. `.kanbots/` 디렉터리 설계가 제품의 경계선을 잘 보여 준다
README에서 `.kanbots/` 구조를 따로 보여 주는 부분도 인상적이었습니다.

핵심 구성은 이렇습니다.

- `db.sqlite`: 이슈, 스레드, 런, 설정
- `config.json`: 워크스페이스 설정
- `worktrees/`: 각 에이전트 런의 별도 작업 트리
- `attachments/`: 카드/채팅 첨부
- `mcp-runtime/`: MCP 관련 임시 설정
- `promote/`: worktree 결과를 실제 커밋으로 승격시키는 staging 구간

그리고 README는 “이 디렉터리와 worktree 밖에는 쓰지 않는다”고 못 박습니다.

이런 식의 경계 선언은 꽤 중요합니다. 에이전트 도구를 쓸 때 가장 불안한 지점 중 하나가 “도대체 어디를 건드리나?”인데, `kanbots`는 적어도 작업 범위를 **workspace 내부의 명시된 운영 디렉터리**로 묶으려는 방향이 분명합니다.

### 4. worktree를 1급 개념으로 둔 것이 이 프로젝트의 핵심 설계 같다
README와 `docs/agents.md`를 함께 보면, `kanbots`의 본질은 칸반보다도 사실 **worktree 운영**에 더 가까운 면이 있습니다.

에이전트 런 흐름은 대략 이렇습니다.

1. 카드에서 Dispatch
2. `.kanbots/worktrees/issue-<n>-<runId>/` 생성
3. 기본 브랜치에서 분기
4. 해당 worktree 안에서 `claude -p` 또는 `codex exec` 실행
5. 끝나면 결과를 preview / promote / PR / discard 중 하나로 처리

이 구조가 좋은 이유는, 각 에이전트 런을 채팅 세션이 아니라 **실제 분리된 개발 작업 공간**으로 취급하기 때문입니다.

즉 에이전트가 뭔가를 건드려도 메인 작업 공간을 바로 더럽히지 않고,
각 런마다 별도의 브랜치와 디렉터리에서 결과를 남깁니다.

이건 실전 개발에서 꽤 중요한 감각입니다.

- 병렬 에이전트 실행 가능
- 결과 비교 가능
- 실패한 런 버리기 쉬움
- 성공한 런만 promote 가능

결국 `kanbots`는 에이전트 채팅 앱이 아니라,
**여러 개의 에이전트 브랜치를 보드 기반으로 관리하는 툴**이라고 보는 편이 더 정확해 보입니다.

### 5. 안전장치가 의외로 명확하다: pre-push hook + explicit promotion
이 저장소를 보면서 가장 좋게 본 부분 중 하나는 “에이전트에게 권한을 많이 주되, 마지막 publish는 사람 손에 남긴다”는 감각입니다.

`docs/agents.md`에는 다음이 분명하게 적혀 있습니다.

- 각 worktree에 `pre-push` hook 설치
- 에이전트가 원격 push 시도하면 실패
- 실제 브랜치 반영은 `Promote commit` 또는 `Open draft PR` 같은 명시적 사용자 액션으로만 수행

이건 좋은 균형처럼 보였습니다.

에이전트가 worktree 안에서 자유롭게 도구를 쓰게 하면서도,
**원격으로 결과를 퍼블리시하는 마지막 단계는 사람 승인 하에 둔다**는 뜻이기 때문입니다.

요즘 에이전트 도구에서 가장 무서운 순간은 “알아서 publish까지 해버릴 때”인데, `kanbots`는 그 지점을 꽤 명시적으로 막고 있습니다.

### 6. 런 디테일 화면은 사실상 ‘에이전트 관제창’이다
README와 `docs/agents.md`를 보면 카드 상세/런 상세 화면에서 보여 주는 정보가 꽤 많습니다.

- `tool_use` / `tool_result` 실시간 스트리밍
- decision prompt
- 모델 / 경과 시간 / 토큰 / 비용
- worktree / branch 정보
- reply 박스와 slash commands (`/spec`, `/review`, `/split`)

즉 이 프로젝트는 “에이전트가 돌아간다”에서 멈추지 않고,
**그 런을 사람이 계속 관찰하고 개입할 수 있는 UI**를 제공합니다.

특히 decision prompt는 중요해 보였습니다. 에이전트가 모호한 선택지에 부딪히면 런을 멈추고, UI 쪽에서 사용자의 선택을 받아 다시 이어 갑니다.

이건 완전 자동화와 완전 수동 사이의 중간지대를 잘 만든 사례처럼 느껴졌습니다.

### 7. `autopilot`은 이 프로젝트를 평범한 칸반 도구에서 끌어올리는 기능 같다
README의 autopilot 설명과 `docs/agents.md`의 확장 설명을 보면, 이 프로젝트를 정말 다르게 만드는 건 아마 여기일 겁니다.

autopilot에는 두 종류가 있습니다.

- `feature-dev`
- `qa`

#### `feature-dev`
- persona roster를 round-robin으로 돌림
- 병렬 슬롯 최대 4개
- 작업 중 스스로 subtasks를 쪼갤 수 있음
- 새로 쪼개진 child card를 다른 슬롯이 가져가 계속 진행

즉 이건 그냥 “에이전트를 여러 번 돌린다”가 아니라,
**작업이 진행되면서 backlog 자체가 다시 만들어지고, 그 새 작업을 다른 에이전트들이 병렬로 처리하는 루프**입니다.

#### `qa`
- `typecheck`, `tests`, `lint`, `build`, `e2e` 같은 check 명령 수행
- 필요하면 dev server 감시
- 실패한 체크마다 fix run 생성
- 통과할 때까지 재시도

이 구조를 보면 `kanbots`는 단순히 카드 dispatch 도구가 아니라,
**개발-분할-검증-수정의 반복 사이클을 보드 위에서 굴리는 오케스트레이터**가 되려는 것으로 보입니다.

### 8. personas 개념이 단순 프롬프트 프리셋보다 더 운영적인 의미를 가진다
`docs/agents.md`를 읽으면서 persona 기능도 꽤 흥미로웠습니다.

보통 persona라고 하면 말투나 관점 프롬프트 정도로 생각하기 쉬운데, 여기서는 autopilot의 병렬 슬롯 배정과 연결됩니다.

예를 들면
- Product Manager
- Senior Engineer
- Reviewer
- Tester
같은 역할이 로스터에 들어가고,
라운드로빈으로 각 슬롯이 다음 persona를 가져가 child run을 수행합니다.

이건 꽤 재밌는 설계입니다.

즉 persona가 장식이 아니라,
**병렬 작업의 역할 분담 장치**로 쓰입니다. 다시 말해 `kanbots`는 여러 모델을 동시에 쓰는 것보다,
**여러 관점의 작업자 역할을 보드 운영 안에 끼워 넣는 방식**에 더 관심이 있어 보입니다.

### 9. 아키텍처 문서를 보면 Electron 앱이지만 설계 중심은 API/IPC/SQLite 쪽이다
`docs/architecture.md`는 꽤 잘 정리된 문서였습니다.

여기서 눈에 띄는 건 다음입니다.

- Electron main process
- React + Vite renderer
- SQLite(`better-sqlite3`)
- IPC bridge
- agent supervisor
- MCP server

그리고 명시적으로 적어 둔 문장이 있습니다.

> There is no HTTP server for the renderer — every renderer→main call goes over Electron IPC.

즉 기본 UI 통신은 전부 Electron IPC를 쓰고, MCP server를 위해서만 별도의 local-only tool bridge를 둡니다.

패키지 구조도 꽤 분리가 잘 되어 있습니다.

- `@kanbots/core`
- `@kanbots/local-store`
- `@kanbots/dispatcher`
- `@kanbots/llm`
- `@kanbots/api`
- `@kanbots/mcp`
- `@kanbots/web`
- `@kanbots/desktop`

이걸 보면 이 프로젝트는 그냥 하나의 Electron 폴더에 모든 걸 몰아넣은 앱이 아니라,
**로컬 스토어, 런타임, 브리지, UI, MCP 확장성을 분리한 제품형 구조**에 가깝습니다.

### 10. MCP server 지원은 칸반 보드를 다른 에이전트 생태계에 열어 주는 포인트다
README에서 짧게 보이지만 꽤 중요한 기능이 MCP server입니다.

`kanbots-mcp-server`를 통해 Cursor, Claude Desktop 등 MCP-aware 클라이언트가 보드를 구동할 수 있다고 되어 있습니다.

이건 단순 부가기능이 아니라 의미가 큽니다.

왜냐하면 `kanbots`가 자기 앱 안에만 갇힌 보드가 아니라,
**외부 에이전트 도구들이 접근 가능한 작업 운영 백엔드**로도 확장될 수 있다는 뜻이기 때문입니다.

다시 말해 이 프로젝트는 “에이전트 UI”이면서 동시에,
**에이전트 운영 상태를 외부에 노출하는 로컬 협업 레이어**가 되려는 기미도 보입니다.

## 저장소를 보며 정리한 메모

### 이 프로젝트가 특히 흥미로운 이유
- 칸반 보드를 단순한 이슈 시각화가 아니라 **에이전트 실행 단위**로 쓴다.
- 각 런을 worktree와 branch에 격리해 개발 흐름과 자연스럽게 붙인다.
- autopilot이 단순 반복 실행이 아니라 **병렬 분해 + 재할당 + QA 루프**까지 포함한다.
- publish 경로를 명시적으로 통제해서, 에이전트의 자유와 사람의 최종 승인 사이 균형을 잡으려 한다.

### 현실적으로 좋아 보이는 지점
실제 개발 현장에서는 “에이전트가 똑똑한가?”보다
**여러 실행 결과를 어떻게 운영하고 비교하고 승인하느냐**가 더 중요할 때가 많습니다.

그 점에서 `kanbots`는 꽤 좋은 문제 설정을 하고 있습니다.

- 카드 단위 작업 관리
- 런별 스레드 관찰
- branch/worktree 분리
- promote / PR / discard 흐름
- 비용과 rate limit 노출

즉 결과물을 대화가 아니라 **운영 가능한 작업 객체**로 다룬다는 점이 장점처럼 보였습니다.

### 동시에 보이는 부담
다만 좋아 보이는 만큼 복잡성도 큽니다.

- 여러 CLI의 인증 상태와 설치 상태를 계속 맞춰야 함
- worktree가 많이 생기면 관리 부담이 커질 수 있음
- autopilot이 subtasks를 잘못 증식시키면 오히려 보드가 복잡해질 수 있음
- 비용, rate limit, containment mode 등 운영 파라미터가 많아 초반 진입장벽이 있을 수 있음

즉 `kanbots`는 가벼운 개인 TODO 앱이 아니라,
**멀티 에이전트 개발 운영을 진지하게 돌리고 싶은 사람에게 맞는 도구**에 더 가까워 보입니다.

## 아직 헷갈리는 것
- 실제 장기 사용에서 보드와 worktree 수가 커졌을 때 UX가 얼마나 버티는지는 더 봐야 합니다.
- 11개 CLI 지원이 강점이긴 하지만, 실제로는 몇 개가 정말 안정적으로 같은 수준 경험을 주는지도 궁금합니다.
- autopilot의 subtask 생성이 장점이 될지, backlog 오염원이 될지는 실제 사례를 더 봐야 알 것 같습니다.
- local mode와 github mode 사이를 오갈 때 어떤 운영 규칙이 가장 자연스러운지도 더 확인이 필요합니다.

## 나중에 더 볼 것
- 실제로 작은 프로젝트 하나를 넣고 `feature-dev` autopilot이 어떻게 보드를 키우고 줄이는지 보기
- `qa` 모드가 어느 정도까지 자율 수정 루프로 버틸 수 있는지 확인하기
- MCP server를 붙였을 때 외부 에이전트가 보드를 어떻게 활용하는지 살펴보기
- Hermes의 kanban/worker 개념과 비교해서, 어디가 더 로컬 앱 친화적이고 어디가 더 에이전트 오케스트레이션에 적합한지 정리해 보기

## 참고 링크
- [GitHub - leodavinci1/kanbots](https://github.com/leodavinci1/kanbots)
- [README.md](https://github.com/leodavinci1/kanbots/blob/main/README.md)
- [Architecture](https://github.com/leodavinci1/kanbots/blob/main/docs/architecture.md)
- [Agents](https://github.com/leodavinci1/kanbots/blob/main/docs/agents.md)

## 기준 날짜 메모
이 글은 **2026-08-02** 기준으로 저장소 공개 페이지와 로컬로 확인한 소스 파일을 바탕으로 정리했습니다. 스타 수, 포크 수, 이슈 수, 지원 에이전트 목록, 내부 구조는 이후 달라질 수 있습니다.
