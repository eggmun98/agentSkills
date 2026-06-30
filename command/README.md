# AI Command Skills Guide

이 문서는 `command/` 폴더에 있는 AI 스킬 커맨드들의 역할, 사용 시점, 사용 예시를 정리한 가이드입니다.

목표는 단순히 AI에게 “코드 짜줘”라고 시키는 것이 아니라, 작업을 더 안전하고 일관되게 굴리는 것입니다. AI도 절차를 안 주면 대충 달려가다 벽에 머리를 박습니다. 안타깝게도 꽤 자주 그럽니다.

---

## Recommended Default Workflow

일반적인 기능 개발이나 버그 수정은 아래 흐름을 기본으로 사용합니다.

```text
/discuss → /prd → /plan → /tdd → /red → /green → /refactor → /verify → /work-report → /retrospect → /next-task
```

작은 작업은 각 단계를 짧게 처리하고, 큰 작업은 각 단계를 자세히 작성합니다.

---

## Command Summary

| Command | Purpose | Best Used When |
|---|---|---|
| `/flow` | 전체 워크플로우 자동 실행 | 기능 개발, 버그 수정, 리팩터링을 처음부터 끝까지 진행할 때 |
| `/go` | 현재 상황에 맞는 다음 커맨드 선택 | 지금 뭘 해야 할지 애매할 때 |
| `/discuss` | 의도 정렬 | 바로 코딩하면 위험하거나 요구사항이 흐릿할 때 |
| `/prd` | 요구사항 정리 | 제품 동작, UI/UX, 유저 플로우가 중요한 작업일 때 |
| `/plan` | 구현 계획 수립 | 코드 변경 전에 영향 범위와 순서를 잡을 때 |
| `/tdd` | 테스트 전략 수립 | 기능 동작을 테스트로 보호하고 싶을 때 |
| `/red` | 실패 테스트 작성 | TDD에서 먼저 깨지는 테스트를 만들 때 |
| `/green` | 최소 구현 | 실패 테스트를 통과시키는 최소 변경을 할 때 |
| `/refactor` | 구조 개선 | 동작은 되지만 코드 구조가 불안할 때 |
| `/verify` | 완료 검증 | AI가 “다 했습니다”라고 말한 뒤 정말 됐는지 확인할 때 |
| `/work-report` | 작업 보고서 작성 | 작업 후 맥락과 변경 사항을 남길 때 |
| `/retrospect` | 회고와 규칙화 | 실패, 삽질, 애매했던 점을 다음 규칙으로 바꿀 때 |
| `/next-task` | 다음 작업 추천 | 작업 후 다음 우선순위를 뽑을 때 |
| `/debug` | 원인 분석 | 버그 원인을 모를 때 |
| `/fix` | 버그 수정 | 원인이 어느 정도 보이고 최소 수정이 필요할 때 |
| `/research` | 조사 | 구현 전에 기술, 코드, 대안을 조사할 때 |
| `/issue` | 이슈 작성 | 작업을 티켓/이슈 형태로 쪼갤 때 |
| `/pr` | PR 작성/리뷰 준비 | 변경 내용을 리뷰 가능한 형태로 정리할 때 |
| `/deploy` | 배포 준비 | 배포 전 체크리스트와 릴리즈 노트를 만들 때 |
| `/create-command` | 새 커맨드 생성 | 반복 작업을 새로운 스킬로 만들고 싶을 때 |
| `/inbox` | 잡다한 메모 정리 | 아이디어, 버그, TODO가 뒤섞였을 때 |
| `/para` | PARA 방식 정리 | 프로젝트/영역/자료/아카이브로 지식 정리할 때 |
| `/component-review` | 컴포넌트 구조 리뷰 | 프론트엔드 컴포넌트 책임이 섞였는지 볼 때 |
| `/state-review` | 상태 흐름 리뷰 | 상태 관리가 꼬이거나 복잡해졌을 때 |
| `/ui-review` | UI/UX 리뷰 | 화면이 기술적으로는 되지만 제품 품질이 의심될 때 |

---

# Core Workflow Commands

## `/flow`

### What it does

전체 개발 워크플로우를 실행하는 메인 커맨드입니다.

`/discuss → /prd → /plan → /tdd → /red → /green → /refactor → /verify → /work-report → /retrospect → /next-task` 순서로 작업을 진행합니다.

### When to use

- 기능 하나를 처음부터 끝까지 만들 때
- 버그 수정 과정을 체계적으로 진행하고 싶을 때
- AI가 바로 코드부터 만지는 걸 막고 싶을 때
- 작업 결과를 검증하고 문서까지 남기고 싶을 때
- “일단 해줘”가 아니라 “제대로 굴려줘”가 필요할 때

### Example

```text
/flow
채팅방에서 메시지를 보낸 뒤 가끔 스크롤이 맨 아래로 내려가지 않아.
원인을 파악하고, 수정하고, 검증하고, 작업 보고서까지 남겨줘.
```

### Notes

가장 많이 쓰게 될 메인 커맨드입니다. 다만 작은 오타 수정 같은 작업에 쓰면 문서가 배보다 커지는 참사가 발생합니다. 인간은 늘 절차로 스스로를 묶는 이상한 재능이 있으니 적당히 씁니다.

---

## `/go`

### What it does

현재 상황을 보고 어떤 커맨드를 실행해야 할지 판단합니다.

예를 들어 요구사항이 불명확하면 `/discuss`, 버그 원인을 모르면 `/debug`, 구현이 끝난 것 같으면 `/verify`를 추천합니다.

### When to use

- 지금 어떤 커맨드를 써야 할지 모르겠을 때
- 작업 중간에 다음 단계가 애매할 때
- AI에게 현재 상태를 보고 다음 행동을 고르게 하고 싶을 때

### Example

```text
/go
지금 회원가입 화면 리팩터링을 하다가 중간에 멈췄어.
현재 상태를 보고 다음에 뭘 해야 할지 판단해줘.
```

### Notes

`/flow`가 전체 코스를 달리는 커맨드라면, `/go`는 지금 위치에서 다음 갈림길을 고르는 커맨드입니다.

---

## `/discuss`

### What it does

바로 구현하지 않고 사용자의 의도, 요구사항, 제약 조건을 먼저 맞춥니다.

### When to use

- 요구사항이 애매할 때
- 구현 전에 방향을 먼저 잡고 싶을 때
- AI가 혼자 멋대로 코딩할 것 같은 불길한 예감이 들 때
- 제품 동작이나 UX 기준을 먼저 합의해야 할 때

### Example

```text
/discuss
담서 채팅방에서 새 메시지가 왔을 때 사용자에게 더 자연스럽게 보여주고 싶어.
바로 구현하지 말고 어떤 방식이 좋을지 먼저 이야기해줘.
```

### Good output should include

- 사용자가 원하는 것에 대한 재진술
- 애매한 부분
- 가정
- 가능한 방향
- 구현 전 확인해야 할 질문

---

## `/prd`

### What it does

흐릿한 아이디어를 제품 요구사항 문서로 정리합니다.

### When to use

- 기능 요구사항이 불명확할 때
- UI/UX, 유저 플로우, 비즈니스 로직이 포함된 작업일 때
- 개발 전에 “성공 기준”을 정해야 할 때
- 기획서 없이 바로 개발하면 나중에 인간들이 서로를 탓할 것 같을 때

### Example

```text
/prd
책 기반 소개팅 앱에서 사용자가 매칭 요청을 보낸 뒤 상대가 수락/거절할 수 있는 기능을 만들고 싶어.
요구사항을 정리해줘.
```

### Good output should include

- 배경
- 목표
- 비목표
- 사용자 시나리오
- 기능 요구사항
- UI/UX 요구사항
- 예외 케이스
- 성공 기준

---

## `/plan`

### What it does

코드를 수정하기 전에 구현 계획을 세웁니다.

### When to use

- 어떤 파일을 수정해야 할지 먼저 파악하고 싶을 때
- 작업 순서를 정리하고 싶을 때
- 큰 변경을 작은 단계로 나누고 싶을 때
- 코드 변경 전 위험 요소를 확인하고 싶을 때

### Example

```text
/plan
프로필 수정 화면에서 닉네임, 자기소개, 대표 책을 수정할 수 있게 만들고 싶어.
관련 파일을 찾고 구현 계획을 세워줘.
```

### Good output should include

- 목표
- 현재 구조 이해
- 관련 파일
- 단계별 구현 계획
- 검증 방법
- 위험 요소

---

## `/tdd`

### What it does

구현 전에 테스트 전략을 세웁니다.

### When to use

- 기능 동작을 테스트로 보호하고 싶을 때
- 버그가 재발하지 않게 만들고 싶을 때
- 어떤 테스트를 작성해야 할지 모르겠을 때
- 자동화 테스트가 어렵다면 수동 검증 기준이라도 잡고 싶을 때

### Example

```text
/tdd
채팅 메시지 전송 후 optimistic update가 정상 동작하는지 테스트하고 싶어.
어떤 테스트를 작성하면 좋을지 전략을 세워줘.
```

### Good output should include

- 보호해야 할 동작
- 테스트 케이스
- Red 단계
- Green 단계
- Refactor 단계
- 자동/수동 검증 방법

---

## `/red`

### What it does

TDD의 Red 단계입니다. 먼저 실패하는 테스트를 작성합니다.

### When to use

- 기능 구현 전에 실패 테스트를 만들고 싶을 때
- 버그를 재현하는 테스트를 먼저 만들고 싶을 때
- 기존 코드가 잘못된 동작을 하고 있음을 테스트로 고정하고 싶을 때

### Example

```text
/red
채팅 메시지 전송 후 FlatList가 마지막 메시지로 스크롤되어야 한다는 실패 테스트를 먼저 작성해줘.
아직 실제 구현은 수정하지 마.
```

### Good output should include

- 추가한 테스트
- 기대하는 실패 이유
- 실행한 테스트 명령어
- 실패 결과

---

## `/green`

### What it does

TDD의 Green 단계입니다. 실패 테스트를 통과시키는 최소 구현만 합니다.

### When to use

- 실패 테스트를 통과시키고 싶을 때
- 과한 리팩터링 없이 동작만 먼저 맞추고 싶을 때
- 버그 수정의 최소 변경을 만들고 싶을 때

### Example

```text
/green
방금 작성한 스크롤 테스트가 통과되도록 최소한의 구현만 해줘.
관련 없는 리팩터링은 하지 마.
```

### Good output should include

- 변경 요약
- 수정 파일
- 테스트 결과
- 아직 남은 정리 작업

---

## `/refactor`

### What it does

동작은 유지하면서 코드 구조를 개선합니다.

### When to use

- 기능은 되지만 코드가 지저분할 때
- 컴포넌트 책임이 섞였을 때
- 중복 로직이 많을 때
- AI가 만든 코드가 “일단 되긴 하는데 미래의 나를 찌를 것” 같을 때

### Example

```text
/refactor
회원가입 화면의 동작은 유지하면서 상태 관리와 컴포넌트 책임을 정리해줘.
기능 변경은 하지 마.
```

### Good output should include

- 리팩터링 목표
- 유지해야 할 동작
- 변경 내용
- 검증 결과
- 남은 위험 요소

---

## `/verify`

### What it does

작업이 실제로 완료되었는지 검증합니다.

### When to use

- AI가 구현 완료라고 했을 때
- 배포 전 최종 확인이 필요할 때
- 테스트, 타입 체크, 빌드, 수동 검증을 정리하고 싶을 때
- “되겠지”라는 위험한 인간적 낙관을 제거하고 싶을 때

### Example

```text
/verify
프로필 수정 기능이 실제로 요구사항대로 동작하는지 검증해줘.
정상 상태, 빈 값, 네트워크 실패, 저장 후 화면 반영까지 확인해줘.
```

### Good output should include

- 검증 단계
- Pass / Fail / Not Verified 결과
- 발견된 문제
- 최종 상태
- 다음 조치

---

## `/work-report`

### What it does

작업이 끝난 뒤 변경 사항과 맥락을 문서로 남깁니다.

### When to use

- 작업이 완료된 뒤 기록을 남기고 싶을 때
- 다음 AI나 개발자가 이어받기 쉽게 만들고 싶을 때
- 왜 이런 결정을 했는지 보존하고 싶을 때
- 커밋은 많은데 머릿속에는 아무것도 남지 않는 불쾌한 상황을 막고 싶을 때

### Example

```text
/work-report
이번 채팅방 스크롤 수정 작업에 대한 보고서를 작성해줘.
변경 파일, 결정 이유, 검증 결과, 남은 위험을 포함해줘.
```

### Good output should include

- 목표
- 변경 요약
- 수정 파일
- 주요 결정
- 검증 결과
- 남은 작업
- 다음 작업자가 알아야 할 맥락

---

## `/retrospect`

### What it does

작업 과정에서 배운 점, 실패 원인, 다음에 반복하지 않을 규칙을 정리합니다.

### When to use

- AI가 잘못된 방향으로 작업했을 때
- 같은 실수를 반복하지 않게 만들고 싶을 때
- 프로젝트 규칙이나 체크리스트에 추가할 내용을 뽑고 싶을 때
- 작업 후 “이번 삽질의 의미”를 억지로라도 회수하고 싶을 때

### Example

```text
/retrospect
이번 작업에서 AI가 바로 코딩을 시작해서 요구사항을 잘못 이해했어.
무엇이 문제였고 다음부터 어떤 규칙을 추가하면 좋을지 정리해줘.
```

### Good output should include

- 무슨 일이 있었는지
- 무엇이 잘못됐는지
- 근본 원인
- 놓친 신호
- 다음에 적용할 규칙
- 체크리스트 추가 항목

---

## `/next-task`

### What it does

현재 상태를 기준으로 다음에 할 작업을 추천합니다.

### When to use

- 작업 하나가 끝난 뒤 다음 우선순위를 정하고 싶을 때
- TODO가 많아서 무엇부터 해야 할지 모를 때
- 현재 프로젝트 상태를 보고 가장 효과적인 다음 행동을 뽑고 싶을 때

### Example

```text
/next-task
현재 담서 앱에서 채팅 안정화 작업이 끝났어.
다음으로 어떤 작업을 하는 게 가장 좋을지 추천해줘.
```

### Good output should include

- 현재 상태
- 후보 작업
- 우선순위
- 추천 작업
- 추천 이유
- 완료 기준

---

# Problem Solving Commands

## `/debug`

### What it does

버그를 바로 고치지 않고 원인을 먼저 분석합니다.

### When to use

- 버그 원인을 모를 때
- 증상만 있고 어디가 문제인지 애매할 때
- 여러 원인 후보를 비교해야 할 때
- 무작정 수정하다가 더 망칠 것 같을 때

### Example

```text
/debug
iOS Safari에서 게임 소켓 연결이 pending 상태로 멈추는 문제가 있어.
원인을 바로 고치지 말고 가능한 원인과 확인 순서를 먼저 정리해줘.
```

### Good output should include

- 증상
- 재현 방법
- 관련 파일
- 흐름 분석
- 원인 후보
- 가장 가능성 높은 원인
- 수정 제안
- 검증 계획

---

## `/fix`

### What it does

원인이 어느 정도 보이는 버그를 최소 변경으로 수정합니다.

### When to use

- 버그 원인이 명확하거나 거의 확실할 때
- 빠르게 안전한 수정이 필요할 때
- 관련 없는 리팩터링 없이 문제만 고치고 싶을 때

### Example

```text
/fix
로딩 팝업의 텍스트 위치가 가로 모드에서 아래로 밀리는 문제가 있어.
레이아웃 계산 부분만 최소 수정해줘.
```

### Good output should include

- 버그 요약
- 원인
- 수정 전략
- 변경 내용
- 검증 결과
- 사이드 이펙트 가능성

---

## `/research`

### What it does

구현 전에 코드, 기술, 대안, 패턴을 조사합니다.

### When to use

- 처음 보는 영역을 수정해야 할 때
- 어떤 라이브러리나 접근이 맞는지 비교해야 할 때
- 기존 코드 패턴을 파악해야 할 때
- 바로 구현하면 삽질 가능성이 커 보일 때

### Example

```text
/research
현재 프로젝트에서 React Query를 어떤 패턴으로 쓰고 있는지 조사해줘.
새 API 연동 전에 기존 query key, mutation, error handling 방식을 파악하고 싶어.
```

### Good output should include

- 조사 목표
- 확인된 사실
- 기존 패턴
- 가능한 선택지
- 장단점
- 추천 방향
- 남은 질문

---

# Collaboration Commands

## `/issue`

### What it does

작업을 이슈 형태로 정리합니다.

### When to use

- 작업을 GitHub Issue, Redmine, Jira 티켓으로 만들고 싶을 때
- 큰 작업을 작게 쪼개야 할 때
- 다른 사람이나 AI가 이어받을 수 있게 만들고 싶을 때

### Example

```text
/issue
회원가입 단계에서 책 선택을 건너뛸 수 없도록 변경하는 작업을 이슈로 정리해줘.
배경, 현재 동작, 기대 동작, 완료 조건을 포함해줘.
```

### Good output should include

- 이슈 제목
- 타입
- 배경
- 현재 동작
- 기대 동작
- 작업 범위
- 완료 조건
- 관련 파일
- 위험 요소

---

## `/pr`

### What it does

PR 설명과 리뷰 체크리스트를 작성합니다.

### When to use

- PR을 올리기 전 설명을 정리할 때
- 변경 의도와 검증 결과를 리뷰어에게 전달해야 할 때
- 위험 영역을 명확히 표시하고 싶을 때

### Example

```text
/pr
이번 PR은 FootballWorld v59에서 릴판 정비율과 BuyBonus 팝업 위치를 수정한 작업이야.
리뷰어가 보기 좋은 PR 설명을 작성해줘.
```

### Good output should include

- PR 제목
- 요약
- 변경 사항
- 검증 내용
- 리뷰 포인트
- 위험 영역
- 추가 메모

---

## `/deploy`

### What it does

배포 전 체크리스트, 릴리즈 노트, 롤백 계획을 정리합니다.

### When to use

- 웹, 앱, 게임 릴리즈 전
- EAS Submit, 스테이지 배포, 프로덕션 배포 전
- 환경 변수, 버전, 에셋, 빌드 결과를 확인해야 할 때

### Example

```text
/deploy
담서 iOS 1.0.1 버전을 제출하기 전에 배포 체크리스트와 릴리즈 노트를 작성해줘.
Apple 로그인, 신고/차단, 약관 링크 확인을 포함해줘.
```

### Good output should include

- 릴리즈 요약
- 포함된 변경 사항
- 배포 전 체크리스트
- 검증 명령어
- 롤백 계획
- 릴리즈 노트
- 배포 권장 여부

---

# Organization Commands

## `/create-command`

### What it does

새로운 반복 작업용 커맨드를 만듭니다.

### When to use

- 같은 지시를 여러 번 반복하고 있을 때
- 특정 작업 절차를 AI에게 학습시키고 싶을 때
- 프로젝트만의 커스텀 스킬을 만들고 싶을 때
- “이거 매번 설명하기 귀찮은데?”라는 감정이 올라올 때. 그 감정은 대체로 자동화의 신호입니다.

### Example

```text
/create-command
Stake 게임 QA 대응용 커맨드를 만들고 싶어.
입력으로 QA 문구와 관련 화면 정보를 주면, 원인 분석, 수정 계획, 검증 체크리스트를 뽑아주는 커맨드였으면 해.
```

### Good output should include

- 커맨드 이름
- 목적
- 사용 시점
- 입력값
- 규칙
- 워크플로우
- 출력 형식
- 사용 예시
- 금지 행동

---

## `/inbox`

### What it does

뒤섞인 메모, 아이디어, 버그, TODO를 분류하고 작업 가능한 형태로 정리합니다.

### When to use

- 메모가 지저분하게 쌓였을 때
- 아이디어와 버그와 TODO가 한 덩어리로 섞였을 때
- 무엇부터 처리해야 할지 모르겠을 때

### Example

```text
/inbox
아래 메모들을 정리해줘.
- 채팅 이미지 느림
- 소개팅 앱 레퍼럴 어렵다
- 프로필 책 선택 UX 이상함
- 인스타 광고 비용 높음
- iOS 알림 테스트 필요
```

### Good output should include

- 분류된 inbox
- 실행 가능한 작업
- 질문이 필요한 항목
- 빠르게 처리할 수 있는 작업
- 영향이 큰 작업
- 추천 다음 단계

---

## `/para`

### What it does

PARA 방식으로 정보와 작업을 정리합니다.

PARA는 다음 네 가지입니다.

- Projects: 명확한 결과물이 있는 현재 진행 작업
- Areas: 지속적으로 관리해야 하는 영역
- Resources: 참고 자료
- Archives: 끝났거나 비활성화된 자료

### When to use

- 프로젝트와 자료가 뒤섞였을 때
- 개인 지식 관리 체계를 만들고 싶을 때
- 사이드 프로젝트, 업무, 공부 자료를 분리하고 싶을 때

### Example

```text
/para
내 현재 작업들을 PARA 방식으로 정리해줘.
담서, 게임 업무, 블로그, 이직 준비, AI 스킬 문서, 투자 공부가 섞여 있어.
```

### Good output should include

- Projects
- Areas
- Resources
- Archives
- 이동 추천
- 다음 리뷰 대상

---

# Frontend Review Commands

## `/component-review`

### What it does

프론트엔드 컴포넌트의 책임과 구조를 리뷰합니다.

### When to use

- 컴포넌트가 너무 커졌을 때
- UI, 비즈니스 로직, 데이터 패칭, 사이드 이펙트가 섞였을 때
- props가 복잡해졌을 때
- 재사용성과 테스트 가능성이 떨어졌을 때

### Example

```text
/component-review
ProfileEditScreen 컴포넌트가 너무 커진 것 같아.
책 선택, 이미지 업로드, 닉네임 검증, 저장 로직이 다 섞여 있는데 책임 분리를 리뷰해줘.
```

### Good output should include

- 컴포넌트 맵
- 책임이 섞인 부분
- 분리하면 좋은 경계
- 우선순위
- 안전한 리팩터링 계획

---

## `/state-review`

### What it does

프론트엔드 상태 흐름을 리뷰합니다.

### When to use

- 상태가 꼬일 때
- local state, global state, server state가 섞였을 때
- optimistic update, loading, error 상태가 불안할 때
- 같은 데이터가 여러 곳에 중복 저장될 때

### Example

```text
/state-review
채팅방 상태 흐름을 리뷰해줘.
서버 메시지, optimistic message, unread count, scroll position 상태가 서로 꼬일 위험이 있는지 보고 싶어.
```

### Good output should include

- 상태 맵
- source of truth
- 위험한 상태
- 현재 문제
- 추천 변경
- 안전한 리팩터링 계획

---

## `/ui-review`

### What it does

화면이 제품 품질인지 UI/UX 관점에서 리뷰합니다.

### When to use

- 화면은 나오지만 어색할 때
- AI가 만든 UI가 너무 평균적인 SaaS 냄새를 풍길 때
- spacing, hierarchy, interaction, loading, empty, error 상태를 점검하고 싶을 때
- 기능은 되지만 사용자 경험이 의심될 때

### Example

```text
/ui-review
회원가입 책 선택 화면의 UI/UX를 리뷰해줘.
사용자가 어떤 책을 골라야 하는지 명확한지, 빈 상태와 로딩 상태가 자연스러운지 봐줘.
```

### Good output should include

- 전체 인상
- 잘 된 점
- 문제점
- 심각도
- 수정 제안
- 제품 리스크
- 최종 판정

---

# Suggested Usage Patterns

## 1. New Feature

```text
/flow
새 기능 설명...
```

또는 수동으로:

```text
/discuss → /prd → /plan → /tdd → /red → /green → /refactor → /verify → /work-report
```

---

## 2. Bug with Unknown Cause

```text
/debug
버그 증상 설명...
```

그다음:

```text
/fix → /verify → /work-report → /retrospect
```

---

## 3. UI Quality Improvement

```text
/ui-review
화면 설명 또는 스크린샷 기준...
```

그다음:

```text
/plan → /green → /verify
```

---

## 4. State Management Cleanup

```text
/state-review
상태 흐름 설명...
```

그다음:

```text
/plan → /refactor → /verify
```

---

## 5. Component Refactor

```text
/component-review
컴포넌트 설명...
```

그다음:

```text
/plan → /refactor → /verify → /work-report
```

---

## 6. Before Release

```text
/deploy
이번 배포 내용 설명...
```

필요하면:

```text
/verify → /pr → /deploy
```

---

## 7. After a Failed AI Attempt

```text
/retrospect
이번 AI 작업에서 무엇이 잘못됐는지 정리해줘.
다음부터 command나 common rule에 추가할 규칙을 뽑아줘.
```

---

# Recommended Starting Set

처음부터 모든 커맨드를 완벽하게 쓰려고 하지 않아도 됩니다. 그러면 도구를 쓰는 게 아니라 도구를 모시는 사제가 됩니다.

가장 먼저 익숙해질 커맨드는 아래입니다.

```text
/discuss
/plan
/debug
/fix
/refactor
/verify
/work-report
```

프론트엔드 작업이 많다면 다음 세 개를 빠르게 추가합니다.

```text
/ui-review
/state-review
/component-review
```

전체 자동 흐름이 필요할 때는 다음을 사용합니다.

```text
/flow
```

---

# Final Principle

AI에게 단순히 일을 시키지 말고, 일하는 방식을 시켜야 합니다.

좋은 커맨드는 AI를 똑똑하게 만드는 마법 주문이 아닙니다. AI가 덜 헛돌고, 덜 착각하고, 덜 자신감 있게 망하도록 울타리를 세우는 장치입니다.

결국 중요한 것은 다음 세 가지입니다.

1. 의도를 먼저 맞춘다.
2. 작은 단위로 실행한다.
3. 반드시 검증하고 기록한다.

코드가 바뀌었다고 작업이 끝난 것이 아닙니다. 요구사항을 만족했고, 검증됐고, 다음 사람이 이어갈 수 있을 때 작업이 끝난 것입니다.
