---
layout: post
title: "microsoft/skill-recorder를 읽고 정리한 메모: 사람이 한 번 한 일을 에이전트용 스킬로 바꾸는 방식"
date: 2026-08-02 14:46:33 +0900
categories: [study]
tags: [repo-notes, skill, microsoft]
excerpt: "microsoft/skill-recorder 저장소의 README, INSTALL, Windows 검증 문서, evals 구조를 읽고 이 프로젝트가 무엇을 만들려는지와 왜 흥미로운지 정리했다."
---

## 한 줄 요약
`microsoft/skill-recorder`는 **사람이 실제로 화면에서 한 작업을 한 번 녹화한 뒤, 그것을 AI 에이전트가 다시 쓸 수 있는 `SKILL.md`나 자동화 절차로 일반화하려는 도구**로 보였습니다.

## 왜 이 글을 남기나
요즘 에이전트 도구를 보다 보면 두 흐름이 자주 보입니다.

1. 처음부터 프롬프트로 절차를 설계하는 방식
2. 사람이 이미 잘하는 작업을 관찰해서 절차로 뽑아내는 방식

`skill-recorder`는 분명히 두 번째 쪽입니다. 이 점이 흥미로웠습니다.

보통 “AI가 대신 해준다”는 말은 추상적으로 들리기 쉽습니다. 그런데 이 저장소는 그 중간을 아주 구체적으로 만듭니다.

- 내가 직접 작업을 한 번 수행한다.
- 화면, 앱 전환, URL, 클립보드, 선택적 음성 설명을 기록한다.
- 그 기록을 바탕으로 전체 의도와 단계 목록을 만든다.
- 거기서 재사용 가능한 스킬이나 자동화로 바꾼다.

즉, 이 프로젝트는 단순한 화면 녹화기가 아니라 **사람의 실제 작업을 에이전트가 실행 가능한 절차로 번역하는 인터페이스**에 가깝습니다.

## 저장소 정보
- 저장소: [microsoft/skill-recorder](https://github.com/microsoft/skill-recorder)
- 라이선스: MIT
- 기본 브랜치: `main`
- 대표 언어: TypeScript
- 확인 시점 스타 / 포크 / 오픈 이슈: **399 / 48 / 26**
- `package.json` 기준 버전: `0.3.1`
- 저장소 생성 시점: 2026-07-29
- 정리 기준: 2026-08-02에 확인한 `README.md`, `INSTALL.md`, `WINDOWS-VALIDATION.md`, `evals/README.md`, `package.json`, 실제 디렉터리 구조

## 먼저 화면부터 보면
<figure>
  <a href="{{ '/assets/images/posts/2026-08-02-skill-recorder-recorder.png' | relative_url }}" target="_blank" rel="noopener noreferrer">
    <img src="{{ '/assets/images/posts/2026-08-02-skill-recorder-recorder.png' | relative_url }}" alt="Skill Recorder 녹화 창 화면" />
  </a>
  <figcaption>
    출처: <a href="https://github.com/microsoft/skill-recorder" target="_blank" rel="noopener noreferrer">microsoft/skill-recorder README</a>
  </figcaption>
</figure>

<figure>
  <a href="{{ '/assets/images/posts/2026-08-02-skill-recorder-library.png' | relative_url }}" target="_blank" rel="noopener noreferrer">
    <img src="{{ '/assets/images/posts/2026-08-02-skill-recorder-library.png' | relative_url }}" alt="Skill Recorder 세션 라이브러리와 분석 화면" />
  </a>
  <figcaption>
    출처: <a href="https://github.com/microsoft/skill-recorder" target="_blank" rel="noopener noreferrer">microsoft/skill-recorder README</a>
  </figcaption>
</figure>

README의 두 장면만 봐도 제품의 흐름은 꽤 선명합니다.

- 왼쪽은 **기록(record)**
- 오른쪽은 **해석(analyze) 후 구조화된 절차로 바꾸기**

결국 핵심은 “녹화” 자체가 아니라, **녹화에서 절차를 뽑아내는 단계**에 있습니다.

## 공부한 것

### 1. 이 프로젝트의 핵심은 RPA 재생보다 ‘스킬 추출’에 더 가깝다
README에서 가장 중요한 문장은 이것이라고 느꼈습니다.

> Record yourself doing a task once, then turn it into a skill your AI agent can repeat.

여기서 포인트는 “한 번 녹화한 행동을 그대로 재생한다”가 아닙니다. 오히려 그 반대에 가깝습니다.

README는 결과물이 다음 두 가지라고 설명합니다.

- **Skill**: 에이전트가 필요할 때 실행하는 `SKILL.md`
- **Automation**: 스케줄이나 트리거에 걸 수 있는 같은 절차의 자동화 버전

그리고 더 인상적이었던 건, 이 절차가 **UI 클릭 재생보다 에이전트의 네이티브 툴을 우선한다**는 점입니다. 예시로 `gh` CLI나 `web_fetch`가 직접 언급됩니다.

이건 매우 중요한 차이로 보입니다.

- 전통적인 RPA는 “사람이 눌렀던 UI를 다시 누른다” 쪽에 가깝고
- `skill-recorder`는 “사람이 하려던 의도를 이해하고 더 적절한 도구로 일반화한다” 쪽에 가깝습니다.

즉, 이 저장소는 녹화 자체보다도 **녹화를 에이전트 친화적인 절차 표현으로 바꾸는 번역기**에 더 가깝습니다.

### 2. 작업 흐름은 surprisingly 단순하다: Record → Analyze → Create
README 기준 제품 흐름은 4단계입니다.

1. **Record**: 전역 단축키(`⌘⇧R` / `Ctrl+Shift+R`)로 작업 기록 시작
2. **Control**: 항상 위에 떠 있는 작은 바에서 마이크 상태와 녹화 상태 제어
3. **Analyze**: GitHub Copilot이 전체 의도 + 순서 있는 단계 목록 재구성
4. **Create**: 승인된 분석으로부터 Skill/Automation 생성

이 구조가 좋은 이유는, 사용자가 해야 할 일이 생각보다 많지 않기 때문입니다.

사용자는 처음부터 완성된 절차를 설계하지 않아도 됩니다. 그냥 평소 하던 일을 한 번 보여 주면 됩니다. 그다음의 구조화는 도구가 맡습니다.

이 점에서 `skill-recorder`는 “프롬프트를 잘 써야 한다”는 진입장벽을 낮추려는 시도로 읽혔습니다.

### 3. 프라이버시 모델은 꽤 명확하다
이 프로젝트를 볼 때 가장 먼저 드는 걱정은 당연히 이것입니다.

> “화면을 녹화한다면, 무엇이 내 컴퓨터 밖으로 나가나?”

README의 `What gets captured` 섹션은 이 부분을 꽤 명확하게 적어 둡니다.

기록 중에는 로컬에서 처리됩니다.

- 녹화
- 저장
- 프레임 추출
- 선택적 음성 설명 전사

이 단계에서는 외부로 나가지 않습니다. 다만 사용자가 **Analyze**를 누르는 순간에는 GitHub Copilot 처리를 위해 다음 정보가 클라우드로 전송됩니다.

- 이벤트 타임라인
- 창/문서 제목
- URL
- 클립보드 미리보기
- 추출된 화면 이미지
- 음성 설명 텍스트

그래서 이 도구는 “기록은 로컬, 해석은 클라우드”로 이해하면 될 것 같습니다.

또 README는 매우 직접적으로 비밀정보를 녹화하지 말라고 경고합니다.

- 비밀번호
- 토큰
- API 키
- 기타 민감 정보

이건 단순한 주의 문구가 아니라, 제품의 사용 경계선 자체라고 봐야 할 것 같습니다.

### 4. 선택적 음성 설명(narration)이 단순 부가 기능이 아니라 중요한 힌트처럼 보인다
README와 문서들을 읽으며 느낀 건, 이 도구는 단순히 화면만 보는 것이 아니라 **사용자의 설명까지 함께 의도 신호로 쓴다**는 점입니다.

선택적으로 음성 설명을 켜면 Whisper 기반 전사가 로컬에서 돌아가고, 첫 사용 시 약 252MB 모델을 받는다고 적혀 있습니다. 지원 언어도 99개로 넓습니다.

이 기능이 중요한 이유는 명확합니다.

사람이 실제 작업을 할 때, 화면만 봐서는 안 보이는 맥락이 많습니다.

- 왜 지금 이 페이지를 보는지
- 무엇을 복사하려는지
- 어떤 기준으로 다음 행동을 고르는지

음성 설명은 이 숨은 의도 정보를 보완해 줍니다.

즉 `skill-recorder`는 단순한 화면 관찰이 아니라, **행동 + 주변 문맥 + 사용자 설명**을 함께 모아 절차를 추출하려는 도구라고 보는 편이 더 정확해 보입니다.

### 5. 설치 전략도 흥미롭다: 완성 바이너리 배포보다 ‘source release’ 중심
README와 `INSTALL.md`를 보면 이 프로젝트는 일반적인 “앱 바이너리 다운로드”보다 **커밋 고정(source release) 설치**를 매우 강하게 밀고 있습니다.

핵심은 이렇습니다.

- 릴리스는 특정 40자리 커밋 SHA에 고정된다.
- 설치 스크립트는 그 커밋의 소스만 가져온다.
- 로컬 머신에서 Node.js 24 런타임과 의존성을 받아 직접 빌드한다.
- 전역 설치 없이 사용자 영역에 `Skill Recorder (Source)` 앱을 만든다.

이 방식은 편의성 면에서는 다소 무겁지만, 대신 다음 장점이 있습니다.

- 무엇을 실행하는지 커밋 단위로 고정 가능
- 설치 스크립트 검증 흐름 제공
- 의존성/라이선스 확인 절차를 문서화
- 로컬 빌드와 배포용 패키지를 명확히 구분

즉 이 프로젝트는 “쉽게 설치되는 앱”이면서도 동시에 **재현 가능성과 공급망 경계**를 꽤 신경 쓰는 쪽으로 설계되어 있습니다.

### 6. 기술 스택은 Electron 앱이지만, 문제의식은 에이전트 워크플로 쪽에 있다
`package.json`을 보면 기반은 비교적 익숙합니다.

- Electron 43
- React 19
- TypeScript
- Vite
- `sharp`
- `zod`
- `@github/copilot-sdk`
- `@huggingface/transformers`
- `koffi`

표면만 보면 “Electron 데스크톱 앱”입니다. 하지만 실제 초점은 UI 프레임워크보다 **어떤 데이터를 모아 어떤 절차를 만들 것인가**에 있습니다.

또 코드 구조를 보면 `electron/ipc.ts`에서 다음 흐름이 꽤 또렷이 드러납니다.

- 녹화 상태 확인
- narration 상태/다운로드/전사
- 분석(describer)
- skill builder
- automation builder

즉 앱의 본체는 그냥 데스크톱 셸이 아니라,
**Recorder + Describer + SkillBuilder + AutomationBuilder**의 파이프라인입니다.

### 7. `evals/README.md`를 보면 이 팀이 어디를 진짜 어렵다고 보는지 드러난다
이 저장소에서 개인적으로 가장 좋게 본 파일 중 하나가 `evals/README.md`였습니다.

여기서는 변동성이 큰 부분을 이렇게 규정합니다.

- 녹화 자체가 아니라
- **captured signals를 overall intent + ordered steps로 바꾸는 describer**

그리고 이를 검증하기 위해 fixture 기반 평가를 둡니다. 즉 실제 화면 녹화의 불안정성을 평가 대상으로 삼지 않고, **고정된 synthetic event stream**을 넣어서 describer 품질만 따로 본다는 뜻입니다.

이 관점이 좋았습니다.

왜냐하면 이 제품에서 정말 어려운 지점은 “녹화가 되었는가”보다,
**기록된 이벤트에서 사람의 작업 의도를 얼마나 잘 재구성하느냐**이기 때문입니다.

문서에 나온 평가 시나리오도 현실적인 편입니다.

- 웹에서 표를 보고 스프레드시트에 옮기기
- 인보이스 행 추출
- 리서치 후 노트 정리
- 연락처 수집
- 릴리스 노트 작성
- GitHub 이슈/PR 처리
- Azure 배포

즉 이 프로젝트는 단순 데모가 아니라, **실제 반복 사무/지식노동을 스킬로 일반화하는 문제**를 꽤 의식하고 있습니다.

### 8. 빌더 평가에서 ‘브라우저보다 네이티브 툴 우선’ 철학이 더 선명해진다
`evals/README.md`의 builder/skillbuilder 설명을 보면 이 팀이 어떤 회귀를 두려워하는지도 드러납니다.

실제 회귀 사례로 문서가 직접 언급하는 것은 이런 종류입니다.

- GitHub 관련 작업을 일반화할 때
- 굳이 브라우저(Playwright)로 몰고 가는 대신
- 원래는 더 적합한 `gh` CLI를 써야 한다는 것

즉 이 제품은 “녹화했으니 브라우저를 다시 클릭해라”가 목표가 아닙니다.
오히려 **적절한 1급 도구가 있으면 그쪽으로 일반화해야 한다**는 철학이 강합니다.

이 점은 개인적으로 매우 중요해 보였습니다.

좋은 에이전트 자동화는 화면을 닮는 것이 아니라,
**사람이 하던 일의 목적을 더 안정적인 실행 경로로 다시 표현하는 것**에 가깝기 때문입니다.

### 9. Windows ARM64까지 본다는 점은 장난감 프로젝트와 결이 다르다
`WINDOWS-VALIDATION.md`는 생각보다 진지한 문서였습니다.

여기서는 단순히 “Windows도 지원함”이라고 끝내지 않습니다.

- Windows 11 x64 / ARM64 모두 지원
- Electron, Koffi, Sharp, ONNX Runtime, Copilot CLI 아키텍처 확인
- `ffmpeg-static` 제거 배경 설명
- Chromium 기반 미디어 처리 흐름 설명
- 패키지 검증 절차 설명
- ARM64 실기기 수동 스모크 테스트 체크리스트 제공

특히 눈에 띈 건, 예전 Windows ARM64 설치 문제의 원인이 `ffmpeg-static`이었고, 이를 제거하면서 Chromium 기반 경로로 정리했다는 설명입니다.

이건 그냥 README용 홍보가 아니라, 실제 운영 이슈를 겪고 정리한 느낌이 강합니다.

즉 이 저장소는 “아이디어 소개용 리포지토리”라기보다 **실제로 여러 환경에서 굴릴 생각이 있는 애플리케이션 프로젝트**에 더 가깝습니다.

## 저장소를 보며 정리한 메모

### 이 프로젝트가 특히 흥미로운 이유
- 에이전트 스킬을 처음부터 글로 설계하지 않고 **관찰 가능한 실제 작업**에서 뽑아낸다.
- UI 재생보다 **네이티브 도구 사용으로 일반화**하려는 방향이 분명하다.
- “기록은 로컬, 분석은 클라우드”라는 데이터 경계를 비교적 솔직하게 적어 둔다.
- eval 문서를 보면, 무엇이 어려운지와 어떤 회귀를 막으려는지가 잘 보인다.

### 내가 느낀 현실적인 가치
이 도구의 진짜 가치는 “AI가 컴퓨터를 대신 쓴다”가 아니라,
**사람이 이미 잘하는 업무를 절차 자산으로 바꿔 준다**는 데 있을 것 같습니다.

이건 개인 생산성 도구일 수도 있고, 팀 온보딩 도구일 수도 있습니다.

예를 들어 누군가가 매주 반복적으로 하는 작업이 있다면,
그 사람에게 문서부터 쓰라고 하는 대신 **한 번 실제로 수행하게 하고 그걸 스킬로 바꾸는 흐름**이 더 자연스러울 수 있습니다.

### 동시에 조심해야 할 점
하지만 장점만 있는 건 아닙니다.

- 녹화 기반이라 민감정보 관리가 어렵습니다.
- 실제 사람이 한 행동이 항상 최적 절차는 아닐 수 있습니다.
- “한 번 한 행동”에서 “일반적인 절차”로 넘어가는 순간 추론 오류가 생길 수 있습니다.
- GitHub Copilot 접근권이 사실상 핵심 의존성입니다.

즉 이 프로젝트는 강력하지만,
**그대로 재생하는 도구가 아니라 일반화하는 도구**이기 때문에 더 조심스럽게 봐야 합니다.

## 아직 헷갈리는 것
- 생성된 `SKILL.md`나 automation의 품질이 실제 현업 반복 작업에서 어느 정도까지 안정적인지는 더 사례를 봐야 합니다.
- 브라우저/앱/클립보드/음성 설명을 섞어 수집하는 만큼, 긴 작업에서 노이즈 제거가 얼마나 잘 되는지도 궁금합니다.
- macOS가 1차 타깃인데, Windows 지원이 장기적으로 어디까지 동일한 경험을 낼 수 있을지도 더 지켜봐야 합니다.
- 이 도구가 개인 실험용을 넘어 팀의 작업 표준화 도구로도 자리 잡을 수 있을지는 아직 판단이 어렵습니다.

## 나중에 더 볼 것
- 실제로 하나의 반복 작업을 녹화했을 때 `SKILL.md`가 얼마나 읽기 좋게 나오는지
- 생성된 skill이 Hermes 같은 다른 에이전트 생태계에서도 잘 이식되는지
- 분석 단계에서 어떤 종류의 작업이 잘 일반화되고, 어떤 작업은 잘 안 되는지
- 장기적으로는 이 흐름이 “사람이 스킬을 직접 문서화하는 방식”을 얼마나 대체할 수 있는지

## 참고 링크
- [GitHub - microsoft/skill-recorder](https://github.com/microsoft/skill-recorder)
- [README.md](https://github.com/microsoft/skill-recorder/blob/main/README.md)
- [INSTALL.md](https://github.com/microsoft/skill-recorder/blob/main/INSTALL.md)
- [WINDOWS-VALIDATION.md](https://github.com/microsoft/skill-recorder/blob/main/WINDOWS-VALIDATION.md)
- [evals/README.md](https://github.com/microsoft/skill-recorder/blob/main/evals/README.md)

## 기준 날짜 메모
이 글은 **2026-08-02** 기준으로 저장소 공개 페이지와 로컬로 확인한 소스 파일을 바탕으로 정리했습니다. 스타 수, 포크 수, 이슈 수, 문서 구조는 이후 달라질 수 있습니다.
