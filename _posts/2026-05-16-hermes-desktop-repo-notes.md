---
layout: post
title: "hermes-desktop 저장소를 읽고 정리한 메모: Hermes를 위한 GUI는 어디까지 왔나"
date: 2026-05-16 14:54:48 +0900
categories: [study, notes]
excerpt: "fathah/hermes-desktop 저장소와 README, 실제 코드 구조를 함께 보며 Hermes Agent용 데스크톱 앱이 무엇을 해결하려는지 정리했다."
---

## 한 줄 요약
`hermes-desktop`은 단순히 Hermes 채팅창을 데스크톱으로 옮긴 프로젝트가 아니라, **설치·연결·프로필·메모리·스킬·스케줄·게이트웨이까지 한 번에 관리하려는 Electron 기반 운영 UI**에 더 가깝습니다.

## 왜 이 저장소를 남겨두나
Hermes Agent는 기능이 많습니다. 채팅만 있는 게 아니라 툴, 메모리, 스킬, 게이트웨이, 스케줄, 프로필, 원격 연결까지 들어갑니다. 그래서 CLI에 익숙하지 않은 사람에게는 진입 장벽이 분명 있습니다.

`fathah/hermes-desktop`은 바로 그 지점을 건드립니다.

이 저장소는 “Hermes를 GUI로 감싸면 어떤 경험이 되나?”라는 질문에 꽤 정면으로 답하고 있습니다. README만 보면 예쁜 데스크톱 래퍼처럼 보일 수도 있지만, 실제 코드를 같이 보면 목표가 더 큽니다.

- 첫 실행 설치 플로우
- 로컬 / 원격 / SSH 연결 모드
- 세션 검색과 재개
- 프로필 분리
- 메모리 / 스킬 / 툴셋 관리
- 스케줄과 메시징 게이트웨이 제어
- Hermes Office(Claw3d) 연동

즉, 이 프로젝트는 **Hermes를 쓰는 데 필요한 운영면 전체를 GUI로 옮기려는 시도**로 읽혔습니다.

## 저장소 정보
- 저장소: [fathah/hermes-desktop](https://github.com/fathah/hermes-desktop)
- 설명: *Desktop Companion for Hermes Agent*
- 라이선스: MIT
- 확인 시점 버전: `0.4.3` (`package.json` 기준)
- 정리 기준: 2026-05-16에 확인한 README, `package.json`, `src/main`, `src/preload`, `src/renderer` 구조

## 먼저 한 장으로 정리
<figure>
  <a href="{{ '/assets/images/posts/2026-05-16-hermes-desktop-architecture.svg' | relative_url }}" target="_blank" rel="noopener noreferrer">
    <img src="{{ '/assets/images/posts/2026-05-16-hermes-desktop-architecture.svg' | relative_url }}" alt="hermes-desktop의 화면, Electron 브리지, Hermes 연결 방식을 요약한 다이어그램" />
  </a>
  <figcaption>
    저장소를 읽고 직접 정리한 구조 요약. React 화면층, Electron main/preload, Hermes 연결 방식(local/remote/ssh)으로 나눠 보면 이해가 빨랐다.
  </figcaption>
</figure>

## 공부한 것

### 1. README가 말하는 핵심은 “CLI를 대신하는 GUI”다
README 첫 문장부터 방향이 분명합니다. 이 앱은 Hermes Agent를 설치하고, 설정하고, 대화하는 **네이티브 데스크톱 앱**입니다.

README에서 강조하는 포인트는 이렇습니다.

- Hermes를 `~/.hermes` 아래에 설치/관리한다.
- 공식 Hermes 설치 스크립트를 활용한다.
- 사용자가 터미널에서 직접 다루던 작업을 화면에서 처리하게 한다.
- 채팅뿐 아니라 세션, 프로필, 스킬, 메모리, 게이트웨이, 스케줄 등 여러 관리 화면을 제공한다.

이 설명만 봐도 “Hermes 채팅 앱”보다는 **Hermes 관리 콘솔** 쪽에 더 가깝다는 인상을 받습니다.

### 2. 실제 코드 구조도 그 방향과 잘 맞는다
코드를 조금만 보면 구조가 꽤 전형적이면서도 명확합니다.

- `src/main`: Electron 메인 프로세스
- `src/preload`: IPC 브리지
- `src/renderer/src`: React UI
- `src/shared/i18n`: 공용 번역 리소스
- `tests`: Vitest 기반 테스트

`electron.vite.config.ts`를 보면 빌드는 `electron-vite`를 중심으로 굴리고, 렌더러는 React + Tailwind 기반입니다. `package.json` 기준 주요 스택은 대략 이렇습니다.

- Electron 39
- React 19
- TypeScript 5.9
- Tailwind CSS 4
- `better-sqlite3`
- `i18next` / `react-i18next`
- `electron-updater`
- `vitest`

대충 묶어 말하면,
**Electron 셸 + React UI + TypeScript + SQLite 기반 상태/세션 관리 + IPC 브리지** 구조입니다.

### 3. 화면이 많다는 건 곧 목표 범위가 넓다는 뜻이다
README와 `src/renderer/src/screens`를 보면 화면 구성이 상당히 넓습니다.

대표적으로 보이는 화면은:

- Chat
- Sessions
- Agents
- Skills
- Models
- Memory
- Soul
- Tools
- Schedules
- Gateway
- Office
- Settings
- Kanban

이 중에서 인상적이었던 건 `Chat`보다 오히려 **운영 관리 화면의 폭**이었습니다.

예를 들어:
- `Agents`는 Hermes 프로필 분리
- `Memory`는 메모리 엔트리와 사용자 프로필 편집
- `Skills`는 설치된/번들 스킬 관리
- `Schedules`는 크론 작업 생성/중지/재실행
- `Gateway`는 메시징 플랫폼 연동 제어
- `Office`는 Claw3d 관련 화면

즉 이 프로젝트는 “AI와 대화한다”보다,
**Hermes라는 시스템을 데스크톱에서 운영한다**는 쪽으로 설계 범위가 잡혀 있습니다.

### 4. local / remote / ssh 3가지 연결 모드가 눈에 띈다
이 저장소에서 가장 좋게 본 부분 중 하나는 연결 방식을 처음부터 나눠서 다룬다는 점입니다.

`src/renderer/src/App.tsx`와 `src/preload/index.ts`를 보면 앱은 기본적으로 다음 세 가지 연결 모드를 염두에 둡니다.

- `local`
- `remote`
- `ssh`

README에서도 first-run 흐름이 나옵니다.

1. 로컬로 Hermes를 설치해서 쓸지,
2. 원격 Hermes API 서버에 붙을지,
3. 또는 SSH 터널 기반으로 원격 Hermes를 붙일지

를 선택하게 되어 있습니다.

이건 생각보다 중요합니다. 단순 GUI라면 로컬만 잘 붙이면 끝일 수 있는데, 이 프로젝트는 애초에 **Hermes를 어디서 돌리느냐**까지 제품 설계 안에 넣고 있습니다.

특히 로컬 모드에서는 `127.0.0.1:8642`로 붙고, 원격 모드에서는 URL + API key를 받으며, SSH 모드에서는 터널 상태까지 관리합니다. 이 부분은 “개인용 래퍼”를 넘어, **운영 환경이 나뉜 Hermes 사용자**를 꽤 의식하고 있다는 뜻으로 보였습니다.

### 5. preload 브리지가 거의 ‘전체 제품 API’처럼 보인다
`src/preload/index.ts`를 읽으면 이 앱이 실제로 무엇을 노출하는지 더 잘 보입니다.

브리지에서 다루는 기능이 상당히 넓습니다.

- 설치 상태 확인, 설치 진행, 버전 조회, 업데이트
- Hermes doctor 실행
- 환경 변수/설정 조회 및 저장
- 모델 설정
- 원격 연결 테스트
- SSH 터널 시작/종료
- 채팅 전송/중단/토큰 사용량 수신
- 세션 검색과 조회
- 프로필 생성/삭제/활성화
- 메모리 읽기/추가/수정/삭제
- SOUL.md 읽기/수정/리셋
- 스킬 목록/설치/삭제
- 툴셋 켜기/끄기
- 크론 작업 생성/제어
- 게이트웨이 시작/중지
- Claw3d 세팅

이걸 보면 `preload`는 단순 안전 브리지가 아니라,
**Hermes 운영 기능 전체를 UI 쪽에 연결하는 API 층**입니다.

### 6. 보안과 안정성 쪽도 생각보다 신경 쓴 흔적이 있다
Electron 앱은 자칫하면 보안이 느슨해지기 쉬운데, `src/main/index.ts`에서 보이는 기본 설정은 비교적 단정한 편이었습니다.

메인 윈도우 `webPreferences`를 보면:

- `nodeIntegration: false`
- `contextIsolation: true`
- `sandbox: true`
- `webSecurity: true`
- `allowRunningInsecureContent: false`

로 잡혀 있습니다.

또 코드 안에는 외부 URL 허용 여부를 검사하는 보안 유틸도 별도로 보입니다. 물론 이것만으로 “안전하다”고 단정할 수는 없지만, 최소한 **Electron 보안 기본기를 놓치지 않으려는 방향**은 읽힙니다.

테스트도 아예 없는 수준은 아닙니다. 테스트 파일 이름만 봐도:

- SSE parser
- IPC handlers
- preload API surface
- SSH remote
- session cache sync
- installer utils
- electron security

같은 주제가 들어 있습니다.

즉 이 저장소는 빠르게 화면만 붙인 데스크톱 앱이라기보다,
**운영상 깨지기 쉬운 부분을 테스트로 붙잡으려는 중인 프로젝트**로 보였습니다.

### 7. 국제화(i18n)와 다국어 확장도 꽤 진지하게 들어가 있다
README에는 “i18n ready” 정도로 적혀 있지만, 코드 구조를 보면 준비 수준이 더 분명합니다.

`src/shared/i18n/locales` 아래에 확인된 로케일만 해도:
- English
- 简体中文
- 日本語
- Português (BR)
- Bahasa Indonesia
- Español

등이 있습니다.

아직 번역의 완성도 자체는 직접 다 검증하지 않았지만, 적어도 구조적으로는 **처음부터 다국어 화면 확장을 염두에 둔 앱**입니다. Hermes 자체가 글로벌 사용자층을 겨냥한다는 점을 생각하면 자연스러운 선택으로 보였습니다.

### 8. 사용자 경험 면에서 진짜 좋은 점은 ‘설치와 운영의 중간’을 메워 준다는 것이다
이 저장소에서 제가 제일 좋게 본 부분은 화려한 UI보다 **애매한 중간 구간을 메워 준다**는 점입니다.

CLI 중심 도구는 보통 두 단계로 갈립니다.

1. 설치와 초기 설정에서 막히는 사람
2. 설치는 했지만 운영 관리가 번거로운 사람

`hermes-desktop`은 딱 이 두 지점을 겨냥합니다.

- 설치 상태 확인
- provider/API key 입력
- 원격 연결 검증
- 세션과 메모리 관리
- 스케줄과 게이트웨이 제어
- 로그/백업/디버그 덤프

이런 것들은 CLI에 익숙한 사람에게도 귀찮은 일이고, 처음 쓰는 사람에게는 더 큰 벽이 됩니다. 그래서 이 프로젝트의 실제 가치는 “채팅창이 예쁘다”보다,
**Hermes를 계속 쓰게 만드는 운영 UX를 만든다**는 데 있을 가능성이 큽니다.

## 저장소를 보며 정리한 메모

### 기술적으로 보이는 현재 인상
- 아직 **active development** 단계라서 기능과 동작이 바뀔 가능성이 크다.
- 앱의 목표는 명확하지만, 범위가 넓은 만큼 유지보수 난이도도 꽤 높아 보인다.
- 설치/업데이트/원격연결/게이트웨이/Claw3d까지 다 품으려면 플랫폼별 예외 처리가 계속 늘어날 수밖에 없다.

### README에서 눈에 띈 운영 이슈
- Windows: 코드 서명 없음 → SmartScreen 경고
- WSL: 설치 중 sudo TTY 이슈 가능
- Fedora: RPM GPG 서명 없음, 자동 업데이트 제한
- macOS: 코드 서명/노타리제이션 없음 → 수동 우회 필요

이건 단점이라기보다, **실제 데스크톱 배포가 얼마나 운영 난도가 높은지 보여 주는 흔적**처럼 보였습니다.

### 코드 규모를 아주 대략적으로 보면
간단히 파일 구성을 세어 보니 대략 이런 느낌이었습니다.

- 전체 파일 수: 약 309개
- `.ts`: 192개 / 약 20,874줄
- `.tsx`: 38개 / 약 9,511줄
- 테스트 파일: 19개
- `src/renderer/src/screens`: 36개 파일
- `src/shared/i18n/locales`: 120개 파일

정확한 LOC 측정 도구로 잰 값은 아니지만, 적어도 이 숫자만 봐도 저장소가 “토이 수준 GUI”는 이미 넘어섰다고 느껴졌습니다.

## 아직 헷갈리는 것
- 실제로 Hermes 본체 쪽 변화 속도를 이 앱이 얼마나 안정적으로 따라갈 수 있을지는 더 봐야 합니다.
- README에 보이는 기능 범위와 실제 각 화면의 완성도는 분리해서 봐야 할 것 같습니다.
- `Office(Claw3d)`와 `Kanban`이 장기적으로 핵심 기능이 될지, 아니면 확장 실험 성격이 더 강한지는 아직 잘 모르겠습니다.
- 원격/SSH 모드가 장점이긴 하지만, 이 부분은 플랫폼별 버그가 가장 많이 생길 구간이기도 해서 실제 사용자 경험은 더 확인이 필요합니다.

## 나중에 더 볼 것
- 직접 실행해 보고 first-run 설치 플로우가 어디까지 매끄러운지 확인하기
- remote / ssh 모드에서 연결 안정성이 어떤지 살펴보기
- session search, memory 편집, cron UI가 실제로 얼마나 실용적인지 보기
- Hermes 본체 업데이트가 빨라질 때 desktop 쪽 유지보수 부담이 커지는지 지켜보기
- 한국어 로케일이 아직 없다면 나중에 기여 가능성도 생각해 보기

## 작업 메모
처음에는 “Hermes용 Electron GUI구나” 정도로 생각했는데, 조금 더 보니 그보다 범위가 넓었습니다.

이 프로젝트는 단순히 CLI를 버튼으로 바꾸는 수준이 아니라,
**Hermes를 설치하고, 연결하고, 운영하고, 되돌아보고, 자동화하는 흐름 전체를 화면으로 감싸려는 시도**에 가깝습니다.

그래서 이 저장소를 보는 포인트도 단순합니다.

- 채팅 UI가 예쁜가?
보다
- Hermes를 덜 귀찮게 쓰게 만들어 주는가?

이 질문으로 보면 이 프로젝트의 의미가 더 잘 보입니다.

## 참고 링크
- [GitHub 저장소 - fathah/hermes-desktop](https://github.com/fathah/hermes-desktop)
- [README 원문](https://raw.githubusercontent.com/fathah/hermes-desktop/main/README.md)
- [Hermes Agent 공식 문서](https://hermes-agent.nousresearch.com/docs/)
- [Hermes Desktop 릴리스](https://github.com/fathah/hermes-desktop/releases/)

## 기준 날짜 메모
이 글은 **2026-05-16** 기준으로 저장소 README와 실제 코드 구조를 읽고 정리한 메모입니다. 기능 소개 글이라기보다, 나중에 다시 볼 수 있게 남겨 두는 구조화된 기록에 가깝습니다.
