# Stake Front Game Architecture Principles

Version: v1
Last updated: 2026-07-20

이 문서는 `stake-front-game-architecture.md`를 대체하지 않는다.
기존 문서가 목표 폴더 구조와 레이어를 설명한다면, 이 문서는 Stake 프론트 게임에서 실제 리팩토링 판단을 할 때 지켜야 할 설계 원칙을 정리한다.

최우선 목표는 **BookEvent 재생 흐름을 안전하게 읽고 바꿀 수 있는 코드**다.

## 결론

Stake 프론트 게임에는 전통적인 MVC, MVVM, Clean Architecture를 그대로 적용하기보다 아래 구조가 더 잘 맞는다.

```text
Math/RGS BookEvent
-> Playback Orchestrator
-> Feature Model
-> State Action
-> Presentation Sequence
-> Adapter
-> Svelte/Pixi Component
```

이 구조의 핵심은 프론트가 결과를 다시 계산하지 않고, 서버가 준 `BookEvent[]`를 순서대로 재생한다는 점이다.
따라서 설계의 중심은 "데이터 입력을 화면 결과로 변환하는 앱"이 아니라 "검증 가능한 이벤트 재생기"여야 한다.

## 설계 원칙

### 1. BookEvent는 진실이고, 프론트 계산은 보조다

프론트는 math/RGS가 내려준 결과를 재생한다.
릴 결과, 당첨 금액, 프리스핀 진입, multiplier, expanding wild 결과를 프론트가 임의로 재계산하면 안 된다.

허용되는 프론트 계산:

- 표시 좌표 변환
- animation 대상 추출
- fallback presentation 판단
- legacy book 호환 normalize
- layout별 rendering 위치 계산

금지되는 프론트 계산:

- 당첨 여부 재판정
- 최종 보드 결과 재구성
- 서버 금액과 다른 win amount 생성
- free spin mode를 서버 계약보다 우선해서 추론
- expanding wild 결과를 화면 상태만 보고 새로 결정

### 2. Handler는 목차, feature가 내용이다

`bookEventHandlerMap.ts`는 긴 로직을 담는 파일이 아니라 이벤트 라우팅 facade다.

권장:

```ts
export const bookEventHandlerMap = {
	reveal: handleReveal,
	winInfo: handleWinInfo,
	freeSpinTrigger: handleFreeSpinTrigger,
};
```

각 `handleXxx`는 아래 순서로 읽혀야 한다.

```text
normalize event
-> build feature model
-> apply state
-> play presentation
-> settle state
```

handler 안에서 다음 행동이 보이지 않고 `stateGame`, `stateBet`, `eventEmitter`, `waitForTimeout`이 뒤섞이면 분리 대상이다.

### 3. 계산, 상태 변경, presentation side effect를 섞지 않는다

한 함수 안에 아래 역할이 2개 이상 강하게 섞이면 분리한다.

- BookEvent payload normalize
- 조건 판단
- 상태 변경
- eventEmitter broadcast
- sound 실행
- animation 실행
- timer 대기
- 다음 BookEvent 탐색

권장 함수 이름:

```text
normalizeRevealEvent
buildRevealPresentationModel
applyRevealBoardState
playRevealPresentationSequence
settleRevealState
```

이름만 보고 side effect 여부를 알 수 있어야 한다.

### 4. State는 owner가 있어야 한다

전역 `stateGame`은 공용 runtime state만 둔다.
feature 전용 상태는 feature state 모듈이 소유한다.

전역에 둘 수 있는 것:

- board
- gameType
- 공통 spin status
- 공통 presentation token

feature state로 옮길 것:

- expanding wild fish action 진행 상태
- popup별 resolver
- animation local status
- feature overlay visibility
- feature 전용 pending timer
- reel별 임시 presentation flag

상태 변경은 직접 대입보다 action 함수로 감싼다.

```ts
applyExpandingWildCarryDropState(reels);
resetScatterPresentationState();
applyFreeSpinEnterState(model);
```

### 5. Presentation Sequence는 adapter를 통해 실행한다

`eventEmitter.broadcast`, sound, Spine/Pixi, Tween은 도메인 함수에서 직접 알면 안 된다.
복잡한 연출은 sequence 또는 command로 표현한다.

작은 흐름:

```ts
await playFreeSpinIntroSequence(model);
```

복잡하고 테스트가 필요한 흐름:

```ts
const commands = buildRevealPresentationCommands(model);
await executePresentationCommands(commands);
```

Command를 모든 곳에 강제하지 않는다.
순서 검증이 중요하거나, sound/animation/state effect가 많이 섞이는 곳부터 적용한다.

### 6. Svelte 컴포넌트는 렌더링에 머문다

Svelte 컴포넌트에 남길 수 있는 것:

- props
- 짧은 derived value
- Pixi/Spine 렌더링
- local interaction
- 단순 mount/unmount cleanup

컴포넌트에서 빼야 하는 것:

- BookEvent 탐색
- feature 규칙 판단
- 여러 단계 animation sequence
- 서버 payload normalize
- board raw symbol 직접 수정
- 긴 eventEmitter subscribe policy

컴포넌트가 길어졌을 때 하위 컴포넌트만 먼저 만들면 문제가 숨는다.
먼저 계산과 sequence를 `.ts` 파일로 빼고, 그 다음 렌더링 단위를 나눈다.

### 7. 리팩토링은 동작 보존과 구조 변경을 분리한다

리팩토링 PR에서 버그를 같이 고치지 않는다.
버그처럼 보여도 먼저 현재 동작을 테스트로 고정하고, 별도 변경으로 수정한다.

금지:

- 파일 이동과 await 순서 변경을 같이 수행
- 이름 변경과 payload shape 변경을 같이 수행
- presentation sequence 분리 중 sound timing 변경
- state 분리 중 초기화 타이밍 변경

허용:

- 순수 함수 추출
- named handler 추출
- 타입 이름 명확화
- state action wrapper 추가
- 기존 순서를 그대로 실행하는 sequence 함수 추가

### 8. 테스트는 화면보다 순서를 먼저 고정한다

Stake 게임 리팩토링에서 가장 중요한 회귀는 "보이는 모양"보다 "이벤트 재생 순서"다.

우선 테스트:

1. BookEvent handler가 호출하는 state action 순서
2. broadcast/sound/animation command 순서
3. pure model 변환 결과
4. stale token 또는 다음 spin 누수 방지
5. Svelte component rendering

테스트 이름은 계약을 드러내야 한다.

```text
reveal applies server board before winInfo presentation
freeSpinTrigger keeps scatter paybox before intro
expandingWild carryDrop does not remove newly revealed wilds
```

### 9. 이름은 문서 용어와 lifecycle을 따른다

게임별 docs의 용어를 우선한다.
단, 너무 넓은 이름은 lifecycle이나 역할을 붙인다.

나쁜 이름:

```text
data
event
phase
actions
wilds
handle
process
temp
```

좋은 이름:

```text
revealEvent
orderedFishTransferActions
newTriggeredExpandingWilds
carriedStickyExpandingWilds
freeSpinIntroPresentation
winInfoPaylineLoopToken
```

`old`와 `new`만으로 부족하면 도메인 의미를 붙인다.

```text
old_reel_multiplier -> carriedStickyWildMultipliers
new_reel_multiplier -> newlyTriggeredWildMultipliers
```

### 10. 기존 게임에는 Strangler 방식으로 적용한다

기존 게임을 한 번에 이상적인 폴더 구조로 옮기지 않는다.
큰 파일 주위에 새 구조를 만들고, 검증된 단위만 옮긴다.

권장 순서:

```text
current behavior test
-> pure function extraction
-> named handler extraction
-> feature model extraction
-> state action wrapper
-> presentation sequence extraction
-> component slimming
-> folder move
```

금지 순서:

```text
folder redesign
-> import repair
-> behavior debugging
```

## 권장 적용 구조

신규 게임이나 큰 feature는 아래 구조를 기본으로 한다.

```text
src/game/
  book/
    bookEventHandlerMap.ts
    handlers/
      revealHandler.ts
      winInfoHandler.ts
      freeSpinHandler.ts

  features/
    expandingWild/
      expandingWildModel.ts
      expandingWildState.ts
      expandingWildPresentation.ts
      expandingWildSequence.ts
      expandingWildAdapter.ts
      expandingWild.test.ts

    freeSpin/
      freeSpinModel.ts
      freeSpinState.ts
      freeSpinPresentation.ts
      freeSpinSequence.ts

  presentation/
    commands.ts
    executePresentationCommands.ts
    soundAdapter.ts
    animationAdapter.ts

  state/
    runtimeState.ts
    boardState.ts

src/components/
  expandingWild/
  freeSpin/
  board/
```

구형 게임은 기존 구조를 유지해도 된다.
다만 큰 파일에 새 로직을 추가하지 않고, 새 기능부터 `features/*`에 둔다.

## 5007에 적용한다면

5007은 이 원칙을 적용하기 좋은 후보지만, 바로 전체 구조를 바꾸면 위험하다.

우선순위:

1. `bookEventHandlerMap.ts`에서 reveal/expanding wild pure function을 분리한다.
2. `runExpandingWildPhases`를 model 생성과 sequence 실행으로 나눈다.
3. `stateGame.expandingWilds` 직접 조작을 `expandingWildState.ts` action으로 감싼다.
4. `ExpandingWilds.svelte`의 fish move 계산과 roulette timing 계산을 `.ts`로 뺀다.
5. 기존 `bookEventHandlerMap.*.test.ts`에 순서 보존 테스트를 추가한다.

이 순서가 안전한 이유는 `reveal.board` 적용, expanding wild carryDrop, winInfo/payline loop, free spin transition 순서를 보존하면서 책임만 좁힐 수 있기 때문이다.

## 설계 선택 기준

아래 질문에 "예"라고 답할 수 있으면 좋은 구조다.

- BookEvent 이름으로 진입점을 찾을 수 있는가?
- handler가 목차처럼 읽히는가?
- 계산 함수가 eventEmitter, Svelte, Pixi, stateBet을 모르는가?
- 상태 변경 함수 이름만 보고 무엇을 바꾸는지 알 수 있는가?
- presentation 순서를 테스트할 수 있는가?
- 컴포넌트가 BookEvent 계약을 몰라도 렌더링할 수 있는가?
- 다음 spin, free spin 전환, skip, autoplay에서 stale state가 남지 않는가?

하나라도 "아니오"라면 파일 수보다 책임 경계부터 다시 본다.

