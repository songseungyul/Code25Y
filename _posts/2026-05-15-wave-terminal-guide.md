---
layout: post
title: "Wave Terminal 정리: 무엇이 좋은지, 어떻게 쓰는지, 어디에 잘 맞는지"
date: 2026-05-15 14:20:00 +0900
categories: [study, notes]
excerpt: "Wave Terminal의 공식 사이트, 문서, 릴리즈 노트를 기준으로 핵심 기능과 사용 흐름을 정리했다."
---

## 한 줄 요약
Wave Terminal은 **터미널 + 파일 미리보기 + 에디터 + 웹뷰 + AI 보조 + 원격 작업**을 한 공간에 묶으려는 오픈소스 터미널입니다.
CLI를 유지하면서도, 개발할 때 계속 브라우저와 에디터를 오가는 흐름을 줄이는 쪽에 강점이 있습니다.

## 왜 찾아보게 됐나
보통 터미널을 쓰다 보면 다음 흐름이 자주 반복됩니다.

- 터미널에서 명령 실행
- 로그를 보다가 문서 검색
- 파일 내용을 다시 열어봄
- 원격 서버에 붙어서 수정
- AI에게 에러 원인 질문

이 과정에서 도구가 계속 쪼개집니다.
Wave Terminal은 이걸 **터미널 안의 작업 공간(workspace)** 으로 합치려는 방향이 뚜렷했습니다.

## Wave Terminal이 뭔가
공식 사이트와 문서 기준으로 Wave는 다음처럼 설명됩니다.

- **오픈소스** 터미널
- **AI-native terminal**
- macOS / Linux / Windows 지원
- 파일 미리보기, 웹 브라우징, 에디터, AI 보조를 터미널 워크플로에 통합

공식 소개 문구에서 가장 핵심적인 표현은 이겁니다.

> terminal features with graphical capabilities like file previews, web browsing, and AI assistance

즉 단순한 “예쁜 터미널”이 아니라,
**그래픽 요소를 같이 쓰는 개발 작업 공간형 터미널**에 가깝습니다.

## 공식 기준으로 보이는 핵심 기능
제가 공식 홈페이지, 문서, 릴리즈 노트를 보고 정리한 핵심은 아래입니다.

### 1. 워크스페이스와 분할 레이아웃
Wave는 화면을 분할해서 다음 요소들을 한 화면에 같이 둘 수 있습니다.

- 터미널 블록
- 파일/디렉터리 프리뷰
- 내장 에디터
- 웹뷰
- 대시보드/위젯

즉,
- 왼쪽은 터미널
- 오른쪽은 README 프리뷰
- 아래는 로컬 웹앱

같은 식의 배치가 자연스럽습니다.

### 2. 파일 미리보기와 내장 에디터
공식 홈페이지 기준으로 다음 유형을 인라인 프리뷰할 수 있습니다.

- Markdown
- 이미지
- HTML
- CSV
- 오디오/비디오 등

그리고 VSCode 스타일의 내장 GUI 에디터가 있어서,
특히 **원격 파일 수정**을 강하게 밀고 있습니다.

이 지점은 꽤 중요합니다.
보통 SSH로 들어가서 `vim`, `nano`, `sed`로 급하게 수정하던 흐름을,
Wave는 **원격 서버 파일도 GUI 에디터로 편집**하는 쪽으로 가져갑니다.

### 3. 원격 작업과 Durable SSH Sessions
릴리즈 노트 기준으로 v0.14.0에서 **Durable SSH Sessions**가 강조됩니다.

이 기능의 의미는:
- 네트워크가 잠깐 끊겨도
- 연결 환경이 바뀌어도
- Wave를 다시 열어도

원격 SSH 세션이 더 잘 이어지도록 설계되었다는 점입니다.

원격 서버를 오래 붙들고 로그 보거나 배포 확인할 때,
이건 꽤 실용적인 포인트입니다.

### 4. WSL / SSH 연결 지원
공식 문서의 Connections 페이지 기준으로 현재 대표 연결 대상은:

- SSH remote connections
- local WSL connections

입니다.

특히 WSL을 로컬 연결 대상으로 다루는 점은,
Windows + WSL 조합에서 개발하는 사람에게 꽤 잘 맞습니다.

### 5. Wave AI
Wave는 자체적으로 **Wave AI**를 제공합니다.
공식 설명상 핵심은:

- context-aware terminal assistant
- terminal output, widgets, filesystem 접근 가능
- 여러 모델 사용 가능
- BYOK(Bring Your Own Key) 가능
- 로컬 모델(Ollama, LM Studio)도 연결 가능

즉 “채팅창 하나 더 붙은 기능”보다는,
**현재 작업 공간 문맥을 아는 보조 도구** 쪽을 지향합니다.

### 6. `wsh` 라는 핵심 CLI
Wave를 단순 GUI 앱이 아니라 실제 도구처럼 보이게 만드는 건 `wsh`입니다.
공식 문서 기준으로 `wsh`는 Wave의 핵심 CLI입니다.

이걸로 할 수 있는 일은 대략 이런 것들입니다.

- 파일 열기
- 프리뷰 열기
- 웹 블록 열기
- 새 블록에서 명령 실행
- AI 사이드바에 내용 보내기
- 변수 저장/불러오기
- 원격/로컬 파일 사이 복사

즉,
Wave의 GUI 요소를 **명령줄에서 조작**할 수 있습니다.
이게 꽤 Wave다운 포인트였습니다.

## 설치는 어떻게 하나
공식 사이트 기준으로 지원 플랫폼은:

- macOS
- Linux
- Windows

입니다.

Linux 배포 형식도 비교적 다양하게 안내됩니다.

- Snap
- AppImage
- `.deb`
- `.rpm`
- `.zip`
- `pacman`

설치는 세부 패키지/플랫폼별로 달라질 수 있으니,
실제 설치는 아래 공식 경로를 보는 게 안전합니다.

- 공식 다운로드: [waveterm.dev/download](https://www.waveterm.dev/download)
- 공식 시작 문서: [docs.waveterm.dev/gettingstarted](https://docs.waveterm.dev/gettingstarted)

## 실제로 어떻게 쓰는지
제가 문서를 보면서 “처음 쓰면 이렇게 접근하면 되겠다”라고 느낀 흐름을 정리하면 아래와 같습니다.

### 1단계: 일반 터미널처럼 시작
처음에는 그냥 일반 터미널처럼 씁니다.

- 프로젝트 디렉터리 열기
- 명령 실행
- 로그 보기
- Git 작업하기

이 단계에서는 Wave를 “기존 터미널 대체재”처럼 써도 됩니다.

### 2단계: 프리뷰와 웹뷰를 붙이기
그다음부터 차이가 납니다.

예를 들어:
- README를 옆에 띄워두기
- 실행한 로컬 웹앱을 웹 블록으로 열기
- CSV나 Markdown 결과를 바로 프리뷰하기

공식 문서 예시 흐름은 이런 식입니다.

```bash
wsh view .
wsh view README.md
wsh run -m -- npm run dev
wsh web open http://localhost:3000
```

이 흐름은 “터미널에서 실행하고, 브라우저로 옮겨가서 보고, 다시 돌아오는” 왕복을 줄이는 쪽입니다.

### 3단계: `wsh`로 블록 중심으로 쓰기
문서에서 특히 눈에 띈 건,
Wave는 화면의 시각 요소를 **block** 단위로 다룬다는 점입니다.

예를 들면:

```bash
wsh web open github.com
wsh run -x -- npm test
wsh getmeta
```

의미는 대략 이렇습니다.
- 웹 블록 열기
- 새 블록에서 테스트 실행 후 자동 종료
- 현재 블록 정보 확인

이쯤부터는 그냥 터미널 앱이 아니라,
**CLI로 조작하는 개발 워크스페이스**처럼 느껴질 것 같습니다.

### 4단계: 원격 작업에 붙이기
Wave가 특히 잘 맞을 수 있는 장면은 원격 작업입니다.

공식 문서 예시는:

```bash
wsh ssh -i ~/.ssh/mykey.pem dev@server
wsh edit /etc/nginx/nginx.conf
wsh run -- tail -f /var/log/app.log
```

즉,
- SSH 접속
- 원격 파일 편집
- 원격 로그 추적

을 한 환경 안에서 처리합니다.

서버 작업이 잦다면 꽤 매력적인 흐름입니다.

### 5단계: AI를 “현재 출력 문맥”과 함께 쓰기
문서에서 인상적이었던 예시는 이겁니다.

```bash
ls -la | wsh ai - "what are the largest files here?"
```

즉 표준출력 결과를 AI에 바로 넘겨서,
현재 보고 있는 결과를 기준으로 질문할 수 있습니다.

이런 방식은:
- 에러 로그 설명
- 디렉터리 상태 요약
- 테스트 출력 분석
- 변경 파일 검토 초안

같은 곳에서 잘 맞아 보입니다.

## `wsh`에서 눈에 띄는 기능
### 1. 변수 저장
문서상 `wsh setvar`, `wsh getvar`가 있습니다.

```bash
wsh setvar API_KEY=abc123
wsh getvar API_KEY
```

지속되는 상태를 workspace/tab 수준으로 보관할 수 있다는 점은,
단순 alias보다 조금 더 구조화된 작업에 유용해 보입니다.

### 2. 원격에서 로컬 파일 접근
이건 꽤 특이합니다.
문서 기준으로 원격 머신에서 로컬 파일 경로를 다음처럼 다룰 수 있습니다.

```bash
wsh file cat wsh://local/~/config/app.json
wsh file cp ./results.txt wsh://local/~/results.txt
```

즉 원격과 로컬 사이를 넘나드는 파일 흐름을,
Wave가 하나의 작업 공간처럼 다루려는 의도가 분명합니다.

### 3. 새 블록에서 커맨드 실행
```bash
wsh run -x -- npm test
```

이런 식으로 별도 블록에서 명령을 돌리고,
필요하면 자동으로 닫을 수도 있습니다.

긴 로그, 서버 실행, 테스트를 각각 블록으로 분리하고 싶을 때 유용해 보입니다.

## 어떤 사람에게 잘 맞을까
제가 보기에 Wave는 아래 유형에게 특히 잘 맞습니다.

### 잘 맞는 경우
- 터미널 중심으로 일하지만 브라우저/에디터 왕복이 잦은 사람
- 원격 서버 작업이 많은 사람
- WSL / SSH / 로컬 환경을 자주 넘나드는 사람
- AI를 터미널 출력과 같이 쓰고 싶은 사람
- 단순 탭 나열보다 워크스페이스 레이아웃을 선호하는 사람

### 덜 맞을 수도 있는 경우
- 터미널은 최대한 가볍고 단순해야 하는 사람
- GUI 요소가 섞이는 것을 오히려 방해로 느끼는 사람
- tmux + vim + browser 조합이 이미 완전히 손에 익은 사람

즉 Wave는 “CLI 순정주의”보다는,
**CLI를 유지하되 그래픽 기능을 같은 작업면에 붙이고 싶은 사람**에게 더 잘 맞습니다.

## 장점으로 보인 점
공식 자료 기준으로 특히 좋게 보인 점은 아래입니다.

- 오픈소스
- 로컬 데이터 저장 강조
- 계정 없이 사용 가능
- 다양한 AI 제공자 / 로컬 모델 연결 가능
- 원격 작업 시나리오가 강함
- 단순 채팅이 아니라 workspace-aware 방향
- `wsh`로 자동화/스크립팅 접점이 있음

## 아쉬울 수 있는 점
반대로 초반 진입 장벽이 있을 수 있습니다.

- 일반 터미널보다 개념이 많음
- block, workspace, widget, `wsh` 개념을 익혀야 함
- “설치 후 바로 끝” 타입보다 적응 시간이 필요한 도구로 보임

그래서 처음부터 모든 기능을 쓰기보다,
1. 일반 터미널처럼 쓰기
2. 프리뷰 추가
3. 원격 편집 붙이기
4. `wsh`와 AI 붙이기

순서로 확장하는 게 좋아 보입니다.

## 내가 처음 써본다면 이렇게 시작할 것 같다
1. 로컬 프로젝트를 연다
2. `README.md`를 프리뷰로 띄운다
3. 개발 서버를 새 블록에서 실행한다
4. 로컬 웹앱을 웹 블록으로 연다
5. 테스트 실패 로그를 AI에 넘겨 요약받는다
6. 원격 서버가 필요하면 SSH 연결과 원격 편집까지 확장한다

이 흐름이면 Wave의 장점을 비교적 빨리 체감할 수 있을 것 같습니다.

## 마무리
Wave Terminal은 단순히 “AI가 붙은 터미널”이라고 보기보다,
**터미널 중심 개발 환경을 하나의 작업 공간으로 확장하려는 도구**로 보는 쪽이 맞아 보입니다.

특히 아래 조합이 핵심입니다.

- 터미널
- 파일 프리뷰
- 웹뷰
- 원격 연결
- AI
- `wsh` 자동화

이걸 한 화면 안에 묶고 싶은 사람이라면,
한 번쯤 직접 써볼 가치가 있는 도구라고 느꼈습니다.

## 참고 링크
- 공식 사이트: [https://www.waveterm.dev/](https://www.waveterm.dev/)
- 공식 문서 홈: [https://docs.waveterm.dev/](https://docs.waveterm.dev/)
- Getting Started: [https://docs.waveterm.dev/gettingstarted](https://docs.waveterm.dev/gettingstarted)
- `wsh` 문서: [https://docs.waveterm.dev/wsh](https://docs.waveterm.dev/wsh)
- Connections 문서: [https://docs.waveterm.dev/connections](https://docs.waveterm.dev/connections)
- Release Notes: [https://docs.waveterm.dev/releasenotes](https://docs.waveterm.dev/releasenotes)
- GitHub: [https://github.com/wavetermdev/waveterm](https://github.com/wavetermdev/waveterm)

## 메모
이 글은 **2026-05-15 기준**으로 공식 사이트, 공식 문서, GitHub 저장소 설명, 릴리즈 노트를 바탕으로 정리했습니다.
세부 UI나 설치 절차는 버전에 따라 바뀔 수 있으니 실제 사용 전 최신 문서를 함께 확인하는 것이 좋습니다.
