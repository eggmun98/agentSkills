# Rules Skills Guide

이 문서는 `rules/skills/` 폴더에 있는 rules 스킬들의 역할, 사용 시점, 사용 예시를 정리한다.

`commands/skills/`가 AI에게 **어떤 절차로 작업할지** 알려주는 실행형 스킬이라면,  
`rules/skills/`는 AI가 작업 중 **어떤 기준을 지켜야 하는지** 알려주는 규칙형 스킬이다.

즉:

```txt
commands = 무엇을 어떤 순서로 할지 정하는 실행 스킬
rules    = 작업 중 어떤 기준을 지킬지 정하는 규칙 스킬
```

AI에게 그냥 “고쳐줘”라고만 하면 코드가 돌아가긴 하는데, 나중에 사람이 울면서 치우는 코드가 나올 수 있다. 이 문서는 그 울음소리를 조금 줄이기 위한 장치다.

---

## Folder Structure

```txt
commands/
  README.md
  skills/
    flow.md
    discuss.md
    plan.md
    fix.md
    refactor.md
    verify.md
    ...

rules/
  README.md
  skills/
    behavior-preserving-refactoring-guide.md
    frontend-clean-code-guide.md
    refactoring-test-guide.md
    test-code-guide.md
```

---

## How to Think About This Structure

### `commands/README.md`

커맨드 스킬 전체 설명서다.

예를 들면 아래 내용을 설명한다.

- 어떤 command가 있는지
- 각 command를 언제 쓰는지
- command끼리 어떻게 조합하는지
- `/flow`가 전체 작업 흐름을 어떻게 실행하는지

---

### `commands/skills/`

실제로 AI에게 실행시킬 command 스킬들이 들어간다.

예시:

```txt
flow.md
plan.md
fix.md
refactor.md
verify.md
```

이 폴더의 파일들은 보통 이런 역할을 한다.

```txt
/discuss  → 의도 정렬
/plan     → 구현 계획
/fix      → 버그 수정
/refactor → 리팩토링
/verify   → 검증
/flow     → 전체 워크플로우 실행
```

---

### `rules/README.md`

rules 스킬 전체 설명서다.

예를 들면 아래 내용을 설명한다.

- 어떤 rule 스킬이 있는지
- 각 rule 스킬을 언제 적용해야 하는지
- command에서 rule을 어떻게 참조해야 하는지
- 기능 개발, 버그 수정, 리팩토링 상황별로 어떤 rule을 써야 하는지

---

### `rules/skills/`

AI가 작업 중 지켜야 할 규칙 스킬들이 들어간다.

예시:

```txt
frontend-clean-code-guide.md
test-code-guide.md
refactoring-test-guide.md
behavior-preserving-refactoring-guide.md
```

이 폴더의 파일들은 보통 이런 역할을 한다.

```txt
frontend-clean-code-guide.md              → 프론트엔드 코드 품질 기준
test-code-guide.md                        → 기능 개발/버그 수정 테스트 기준
refactoring-test-guide.md                 → 리팩토링 전 현재 동작 고정 테스트 기준
behavior-preserving-refactoring-guide.md  → 동작 보존 리팩토링 기준
```

---

# Rule Skills

## 1. `frontend-clean-code-guide.md`

### Purpose

프론트엔드 코드를 작성하거나 수정할 때 지켜야 하는 코드 품질 기준을 정의한다.

이 스킬은 AI 에이전트가 새 코드를 작성하거나 기존 코드를 수정할 때, 코드가 너무 커지거나, 책임이 섞이거나, 이름이 흐려지거나, side effect가 숨는 문제를 막기 위해 사용한다.

한마디로 말하면:

> 기능은 되는데 코드가 점점 괴물이 되는 사태를 막는 스킬이다.

인간은 원래 “일단 되게만 하자”라고 말하고 3개월 뒤 과거의 자신을 저주한다. 이 문서는 그 저주를 조금 줄이기 위한 안전장치다.

---

### When to Use

아래 상황에서 사용한다.

- 새 기능을 구현할 때
- 기존 프론트엔드 코드를 수정할 때
- 컴포넌트가 커지고 있을 때
- 함수 책임이 애매할 때
- 조건문이 복잡해질 때
- 상태/store가 커지고 있을 때
- `utils.ts`, `types.ts`, `constants.ts`가 잡동사니 창고가 되어갈 때
- AI가 코드를 추가했는데 구조가 불안해 보일 때
- 리뷰 전에 코드 품질을 점검하고 싶을 때

---

### Main Focus

이 스킬은 특히 아래 항목을 본다.

- 함수 네이밍
- 함수 책임 분리
- hidden side effect 방지
- `if` 중첩 제한
- 중첩 삼항 금지
- Svelte 컴포넌트 분리 기준
- props drilling 방지
- 넓은 state/store 방지
- 매직 넘버 상수화
- 도메인 기반 파일 위치
- utils 남용 방지
- 이벤트 핸들러 책임 분리
- 타입 우회 제한
- 임시 코드와 디버그 코드 기준

---

### Example Usage

```txt
Use `rules/skills/frontend-clean-code-guide.md`.

Review this component before implementation.
Check whether responsibilities are mixed, whether state is too broad, and whether function names follow the project rules.
Do not refactor yet. First report issues and suggest a safe refactor plan.
```

```txt
Use `rules/skills/frontend-clean-code-guide.md`.

I added a new BuyBonus popup feature.
Review the code against the frontend clean code guide.
Focus on function naming, side effects, component responsibility, magic numbers, and store usage.
```

```txt
Use `rules/skills/frontend-clean-code-guide.md`.

Before adding this new state to the global store, check whether it really belongs there.
If not, suggest a feature-level state structure.
```

---

### Good Output Should Include

```md
## Frontend Clean Code Review

### Overall Assessment
...

### Issues Found
1. ...
2. ...

### Rule Violations
- ...
- ...

### Suggested Fixes
1. ...
2. ...

### Safe Refactor Plan
1. ...
2. ...

### Must Not Change
- Existing behavior
- Event order
- Public API
```

---

### Recommended Related Commands

이 스킬은 아래 commands와 같이 쓰기 좋다.

- `/plan`
- `/fix`
- `/refactor`
- `/component-review`
- `/state-review`
- `/ui-review`
- `/verify`
- `/flow`

---

## 2. `test-code-guide.md`

### Purpose

일반적인 기능 개발이나 버그 수정 시 작성하는 테스트 코드 기준을 정의한다.

이 스킬은 테스트가 구현 세부가 아니라 실제 요구사항과 동작을 검증하도록 만들기 위해 사용한다.

한마디로 말하면:

> 테스트가 “있긴 있는데 아무것도 믿을 수 없는 장식품”이 되는 걸 막는 스킬이다.

테스트 커버리지 숫자만 보고 마음의 평화를 얻는 건 인간이 만든 또 하나의 미신이다. 이 문서는 그 미신을 조금 덜 믿게 해준다.

---

### When to Use

아래 상황에서 사용한다.

- 새 기능을 개발할 때
- 버그를 수정할 때
- 테스트 케이스를 추가할 때
- 기존 테스트가 너무 구현 세부에 의존할 때
- 테스트 이름이 모호할 때
- mock이 너무 많아질 때
- snapshot이 너무 커질 때
- 비동기 테스트가 불안정할 때
- UI 테스트 기준이 필요할 때
- 회귀 방지 테스트를 작성할 때

---

### Main Focus

이 스킬은 특히 아래 항목을 본다.

- 요구사항 중심 테스트
- AAA 패턴
- Given-When-Then
- 테스트 이름 작성 기준
- 사용자에게 보이는 결과 우선
- 도메인 규칙 테스트
- 이벤트/사운드/애니메이션 순서 테스트
- 경계값 테스트
- table test 기준
- mock 기준
- 비동기 테스트 기준
- UI 테스트 기준
- snapshot 사용 제한
- 테스트 데이터/factory 기준
- 버그 수정 테스트 작성 순서

---

### Example Usage

```txt
Use `rules/skills/test-code-guide.md`.

Write tests for this new multiplier display rule.
Focus on behavior, edge cases, and readable test names.
Use table tests only if the input combinations remain easy to understand.
```

```txt
Use `rules/skills/test-code-guide.md`.

Before fixing this bug, write a failing test that reproduces the current bug.
The test name should describe the expected correct behavior, not the internal cause.
```

```txt
Use `rules/skills/test-code-guide.md`.

Review these tests.
Check whether they verify behavior instead of implementation details.
Also check whether mocks are overused.
```

---

### Good Output Should Include

```md
## Test Code Review / Plan

### Target Behavior
...

### Test Cases
1. Normal case
2. Edge case
3. Failure case
4. Regression case

### Test Structure
AAA / Given-When-Then

### Mock Strategy
...

### Things to Avoid
...

### Suggested Test Names
- ...
- ...
```

---

### Recommended Related Commands

이 스킬은 아래 commands와 같이 쓰기 좋다.

- `/tdd`
- `/red`
- `/green`
- `/fix`
- `/verify`
- `/work-report`
- `/flow`

---

## 3. `refactoring-test-guide.md`

### Purpose

리팩토링 전에 현재 동작을 고정하는 테스트 기준을 정의한다.

이 스킬은 “더 좋은 동작”을 테스트하는 게 아니라, “현재 코드가 실제로 하는 동작”을 테스트로 고정하기 위해 사용한다.

한마디로 말하면:

> 리팩토링하다가 몰래 기능 수정까지 섞는 참사를 막는 스킬이다.

리팩토링이라고 해놓고 버그 수정, 연출 변경, 조건 변경까지 같이 하면 그건 리팩토링이 아니라 코드판 비빔밥이다.

---

### When to Use

아래 상황에서 사용한다.

- 리팩토링을 시작하기 전
- 기존 코드의 의도가 불분명할 때
- 큰 함수를 분리하기 전
- 이벤트 순서가 중요한 코드를 정리하기 전
- 사운드/애니메이션 호출 순서가 중요한 게임 코드를 리팩토링하기 전
- store/state 변경 순서를 보존해야 할 때
- 현재 동작이 이상하지만 일단 보존해야 할 때
- characterization test가 필요할 때
- Golden Master 테스트가 필요할 때

---

### Main Focus

이 스킬은 특히 아래 항목을 본다.

- 현재 동작 고정
- characterization test
- Golden Master
- AAA 패턴
- Given-When-Then
- 현재 구현임을 드러내는 테스트 이름
- 이벤트 순서 테스트
- sound/animation 호출 순서 테스트
- store/state 변경 결과 테스트
- payload 변환 결과 테스트
- 비동기 sequence 테스트
- UI 컴포넌트 리팩토링 전 테스트
- 리팩토링 전 완료 조건

---

### Important Difference

`test-code-guide.md`와 다르게 이 스킬은 **올바른 요구사항**을 테스트하지 않는다.

이 스킬은 **현재 구현의 실제 동작**을 테스트한다.

예시:

```ts
// 일반 기능 테스트
it('positions가 비어 있으면 freeSpinRetrigger를 실행하지 않는다', async () => {});

// 리팩토링 전 동작 고정 테스트
it('freeSpinRetrigger는 positions가 비어도 현재 구현상 추가 intro와 counter update를 수행한다', async () => {
  // 현재 동작을 보존하기 위한 테스트다.
  // 버그 여부는 별도 작업에서 판단한다.
});
```

---

### Example Usage

```txt
Use `rules/skills/refactoring-test-guide.md`.

Before refactoring this function, write characterization tests that lock the current behavior.
Do not fix suspicious behavior.
If something looks like a bug, document it separately.
```

```txt
Use `rules/skills/refactoring-test-guide.md`.

I want to refactor this game event handler.
First create tests that preserve current event order, sound order, animation order, and state update order.
```

```txt
Use `rules/skills/refactoring-test-guide.md`.

The current function is too large and unclear.
Before splitting it, identify observable behavior and write tests that pass against the current implementation.
```

---

### Good Output Should Include

```md
## Refactoring Test Plan

### Current Behavior to Preserve
...

### Characterization Tests
1. ...
2. ...

### Event / Side Effect Order
...

### Golden Master Candidate
Yes / No

### Suspicious Behavior
- Location:
- Current behavior:
- Why suspicious:
- Should not be fixed during refactoring:

### Ready to Refactor?
Yes / No
```

---

### Recommended Related Commands

이 스킬은 아래 commands와 같이 쓰기 좋다.

- `/research`
- `/debug`
- `/tdd`
- `/red`
- `/refactor`
- `/verify`
- `/work-report`
- `/flow`

---

## 4. `behavior-preserving-refactoring-guide.md`

### Purpose

기존 동작을 바꾸지 않고 코드 구조만 개선하는 리팩토링 기준을 정의한다.

이 스킬은 리팩토링 중에 기능 변경, 버그 수정, 순서 변경, 비동기 동작 변경이 섞이지 않도록 막기 위해 사용한다.

한마디로 말하면:

> “정리만 했어요”라고 말하면서 동작을 바꿔버리는 범죄를 막는 스킬이다.

물론 대부분 악의는 없다. 그냥 인간과 AI가 둘 다 부주의할 뿐이다. 아주 위로가 된다.

---

### When to Use

아래 상황에서 사용한다.

- 실제 리팩토링을 수행할 때
- 긴 함수를 작은 함수로 분리할 때
- 복잡한 조건식을 함수로 추출할 때
- 파일을 도메인 기준으로 이동할 때
- 컴포넌트에서 로직을 `.ts` 파일로 분리할 때
- 이름을 개선할 때
- 순수 계산 로직을 분리할 때
- 이벤트 핸들러 orchestration을 정리할 때
- 리팩토링 PR을 리뷰할 때
- 리팩토링과 기능 변경이 섞였는지 확인할 때

---

### Main Focus

이 스킬은 특히 아래 항목을 보존한다.

- 조건식 결과
- 이벤트 실행 순서
- 사운드 재생 순서
- 애니메이션 실행 순서
- store/state 업데이트 순서
- `await` 위치
- timer, delay, duration 값
- event name
- payload shape
- 외부 public API
- 기존 버그처럼 보이는 동작

---

### Allowed Changes

아래 변경은 동작이 같다는 검증이 가능하면 허용한다.

- 긴 함수 분리
- 복잡한 조건식 추출
- 중복된 순수 계산 로직 추출
- 파일 이동
- 내부 변수명 개선
- 내부 함수명 개선
- 타입 정의 분리
- 상수 이름 부여
- 컴포넌트에서 계산/판단/변환 로직 분리

---

### Forbidden Changes

리팩토링 중에는 아래 변경을 하지 않는다.

- 조건 결과 변경
- 이벤트 순서 변경
- 사운드/애니메이션 순서 변경
- `await` 추가/삭제/이동
- delay/duration 변경
- payload shape 변경
- event name 변경
- 외부 import 이름 변경
- 버그처럼 보이는 로직 수정
- UI 개선
- 성능 개선
- 기획 변경

이런 걸 하고 싶으면 별도 작업으로 분리한다. 귀찮다. 하지만 나중에 원인 모를 회귀를 추적하는 것보다 싸다.

---

### Example Usage

```txt
Use `rules/skills/behavior-preserving-refactoring-guide.md`.

Refactor this large event handler without changing behavior.
Preserve event order, sound order, animation order, state update order, and await boundaries.
Do not fix suspicious bugs during this refactor.
```

```txt
Use `rules/skills/behavior-preserving-refactoring-guide.md`.

Move the calculation logic out of this Svelte component into a TypeScript helper.
The rendered result and event handler behavior must remain exactly the same.
```

```txt
Use `rules/skills/behavior-preserving-refactoring-guide.md`.

Review this refactoring diff.
Check whether any behavior change was mixed into the refactor.
Focus on condition results, await boundaries, event payloads, and public exports.
```

---

### Good Output Should Include

```md
## Behavior-Preserving Refactor Plan

### Refactor Goal
...

### Behavior That Must Stay the Same
- ...
- ...

### Allowed Changes
- ...
- ...

### Forbidden Changes
- ...
- ...

### Step-by-Step Refactor
1. ...
2. ...
3. ...

### Verification Plan
- Tests:
- Build:
- Manual check:

### Suspicious Behavior Found
...
```

---

### Recommended Related Commands

이 스킬은 아래 commands와 같이 쓰기 좋다.

- `/plan`
- `/refactor`
- `/verify`
- `/work-report`
- `/retrospect`
- `/flow`

---

# Recommended Usage Patterns

## 1. 새 기능 개발

새 기능을 만들 때는 아래 조합이 좋다.

```txt
/flow
→ rules/skills/test-code-guide.md
→ rules/skills/frontend-clean-code-guide.md
```

직접 지시할 때:

```txt
Use `rules/skills/test-code-guide.md` and `rules/skills/frontend-clean-code-guide.md`.

Implement this feature with behavior-focused tests.
Keep frontend code readable, responsibility-separated, and consistent with the clean code guide.
```

---

## 2. 버그 수정

버그 수정은 아래 조합이 좋다.

```txt
/debug
→ rules/skills/test-code-guide.md
→ /fix
→ /verify
```

직접 지시할 때:

```txt
Use `rules/skills/test-code-guide.md`.

First write a failing test that reproduces this bug.
Then fix the bug with the smallest safe change.
After the fix, verify that the test passes and no related behavior regressed.
```

---

## 3. 리팩토링 전 준비

리팩토링 전에 먼저 현재 동작을 고정한다.

```txt
rules/skills/refactoring-test-guide.md
→ /red
→ current behavior tests pass
```

직접 지시할 때:

```txt
Use `rules/skills/refactoring-test-guide.md`.

Before refactoring, create characterization tests for the current behavior.
Do not fix bugs.
Do not change expected behavior.
Only lock the current implementation.
```

---

## 4. 실제 리팩토링

현재 동작 테스트가 생긴 뒤에 리팩토링한다.

```txt
rules/skills/behavior-preserving-refactoring-guide.md
→ /refactor
→ /verify
```

직접 지시할 때:

```txt
Use `rules/skills/behavior-preserving-refactoring-guide.md`.

Now refactor the code while preserving behavior.
Do not change condition results, event order, sound order, animation order, await boundaries, payload shape, or public exports.
```

---

## 5. 프론트엔드 코드 리뷰

프론트엔드 코드 리뷰에는 아래 조합이 좋다.

```txt
rules/skills/frontend-clean-code-guide.md
→ /component-review
→ /state-review
→ /verify
```

직접 지시할 때:

```txt
Use `rules/skills/frontend-clean-code-guide.md`.

Review this frontend implementation.
Focus on component responsibility, state ownership, hidden side effects, naming, magic numbers, file location, and unnecessary abstraction.
```

---

# Rule Selection Guide

| Situation | Rule Skill |
|---|---|
| 새 기능 테스트 작성 | `test-code-guide.md` |
| 버그 수정 테스트 작성 | `test-code-guide.md` |
| 리팩토링 전 현재 동작 고정 | `refactoring-test-guide.md` |
| 현재 버그처럼 보이는 동작도 보존해야 할 때 | `refactoring-test-guide.md` |
| 리팩토링 실제 수행 | `behavior-preserving-refactoring-guide.md` |
| 리팩토링 PR 리뷰 | `behavior-preserving-refactoring-guide.md` |
| 프론트엔드 코드 품질 리뷰 | `frontend-clean-code-guide.md` |
| 컴포넌트 책임 검토 | `frontend-clean-code-guide.md` |
| 상태/store 구조 검토 | `frontend-clean-code-guide.md` |
| 함수명, side effect, 파일 위치 검토 | `frontend-clean-code-guide.md` |

---

# How to Reference These Rules from Commands

`flow.md`, `refactor.md`, `fix.md`, `tdd.md`, `verify.md` 같은 command 스킬에서 아래처럼 rules를 참조하면 좋다.

```md
Before working, check relevant rule skills in `rules/skills/`.

- For frontend code quality, follow `rules/skills/frontend-clean-code-guide.md`.
- For normal feature or bug tests, follow `rules/skills/test-code-guide.md`.
- Before refactoring, follow `rules/skills/refactoring-test-guide.md`.
- During refactoring, follow `rules/skills/behavior-preserving-refactoring-guide.md`.
```

---

# Recommended Command Integration

## In `commands/skills/flow.md`

```md
Before implementation, select and apply relevant rule skills:

- If writing frontend code, apply `rules/skills/frontend-clean-code-guide.md`.
- If writing normal feature or bug tests, apply `rules/skills/test-code-guide.md`.
- If preparing for refactoring, apply `rules/skills/refactoring-test-guide.md`.
- If performing refactoring, apply `rules/skills/behavior-preserving-refactoring-guide.md`.
```

---

## In `commands/skills/refactor.md`

```md
Before refactoring:

1. Check `rules/skills/refactoring-test-guide.md`.
2. Confirm current behavior is locked by tests or documented verification.
3. Then apply `rules/skills/behavior-preserving-refactoring-guide.md`.
4. Refactor only structure, naming, file location, or responsibility.
5. Do not change behavior.
```

---

## In `commands/skills/fix.md`

```md
When fixing a bug:

1. Use `rules/skills/test-code-guide.md`.
2. Write or describe a failing test that reproduces the bug.
3. Fix with the smallest safe change.
4. Do not mix unrelated refactoring.
5. Verify the test passes.
```

---

## In `commands/skills/verify.md`

```md
During verification:

- If this was a feature or bug fix, verify against `rules/skills/test-code-guide.md`.
- If this was refactoring, verify against `rules/skills/behavior-preserving-refactoring-guide.md`.
- If this touched frontend code, also check `rules/skills/frontend-clean-code-guide.md`.
```

---

# Final Recommendation

추천 구조는 아래와 같다.

```txt
commands/
  README.md
  skills/
    flow.md
    discuss.md
    prd.md
    plan.md
    tdd.md
    red.md
    green.md
    refactor.md
    verify.md
    work-report.md
    retrospect.md
    next-task.md
    debug.md
    fix.md
    issue.md
    research.md
    inbox.md
    para.md
    create-command.md
    ui-review.md
    state-review.md
    component-review.md
    deploy.md
    pr.md
    go.md

rules/
  README.md
  skills/
    behavior-preserving-refactoring-guide.md
    frontend-clean-code-guide.md
    refactoring-test-guide.md
    test-code-guide.md
```

Recommended mental model:

```txt
commands = AI가 어떤 절차로 일할지 정하는 실행 스킬
rules    = AI가 어떤 기준을 지켜야 하는지 정하는 규칙 스킬
```

Do not mix them.

`commands`와 `rules`가 섞이면 미래의 너는 고통받는다. 미래의 너는 아직 아무 잘못도 하지 않았다.
