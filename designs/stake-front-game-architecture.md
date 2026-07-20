# Stake Front Game Architecture Guide

Version: v1
Last updated: 2026-07-20

이 문서는 Stake 프론트 게임 프로젝트를 설계하거나 리팩토링할 때 적용할 공통 구조 기준이다.

최우선 목표는 **사람이 읽기 쉬운 코드**다. 처음 보는 개발자가 book event 흐름, 게임 규칙, 화면 실행 위치를 빠르게 따라갈 수 있어야 한다.

## 핵심 판단 기준

아래 우선순위를 항상 지킨다.

1. 사람이 위에서 아래로 읽으며 맥락을 파악할 수 있는가?
2. book event 하나가 어떤 상태와 화면 효과로 이어지는지 추적 가능한가?
3. 계산, 상태 변경, animation/sound side effect가 분리되어 있는가?
4. 기능 하나를 고칠 때 봐야 하는 파일 범위가 좁은가?
5. 리팩토링 전후 이벤트 순서와 화면 동작을 검증할 수 있는가?

단순히 파일 수를 늘리거나 디자인 패턴 이름을 붙이는 것은 목표가 아니다.

## 기본 아키텍처

Stake 프론트 게임은 아래 흐름으로 설계한다.

```text
book event
-> book handler
-> feature/domain model
-> feature state
-> presentation command
-> component / animation / sound
```

각 레이어 책임은 분명해야 한다.

- `book`: math/RGS에서 받은 book event를 해석하는 입구
- `feature/domain`: 게임 규칙, 조건 판단, payload 변환, feature 상태 소유
- `presentation`: animation, sound, eventEmitter, UI 표시 같은 side effect 실행
- `components`: Svelte/Pixi 렌더링과 짧은 UI interaction

## 권장 폴더 구조

신규 게임 또는 큰 리팩토링은 아래 구조를 기준으로 설계한다.

```text
src/
  game/
    actor.ts
    context.ts
    eventEmitter.ts
    stateApp.ts
    stateLayout.ts
    stateXstate.ts

    book/
      bookEventHandlerMap.ts
      bookEventContext.ts
      bookEventNavigation.ts
      bookEventSequence.ts

      handlers/
        revealHandler.ts
        tumbleHandler.ts
        freeSpinHandler.ts
        winHandler.ts
        featureHandler.ts

    features/
      board/
        boardModel.ts
        boardState.ts
        boardPresentation.ts

      tumble/
        tumbleModel.ts
        tumbleState.ts
        tumblePresentation.ts

      freeSpin/
        freeSpinModel.ts
        freeSpinState.ts
        freeSpinPresentation.ts

      win/
        winModel.ts
        winAmountState.ts
        winPresentation.ts

      featureName/
        featureNameModel.ts
        featureNameState.ts
        featureNamePresentation.ts
        featureNameSequence.ts

    presentation/
      commands/
        presentationCommand.ts
        executePresentationCommand.ts

      animation/
        animationSpeed.ts
        playAnimationSequence.ts

      sound/
        soundCommands.ts

    config/
      assets.ts
      constants.ts
      symbols.ts
      winLevelMap.ts

    types/
      types.ts
      typesBookEvent.ts
      typesEmitterEvent.ts

  components/
    board/
    symbol/
    tumble/
    freeSpin/
    win/
    featureName/
    popup/
    BottomUI/
    ui/
    sound/

  stories/
  tests/
  i18n/
  routes/
```

작은 구형 게임은 납작한 `game/` 구조를 유지할 수 있다. 단, 아래 조건 중 하나라도 해당하면 `features/`와 `book/handlers/`로 분리한다.

- `bookEventHandlerMap.ts`가 300줄을 넘는다.
- 한 feature의 Svelte 컴포넌트가 300줄을 넘는다.
- 한 book event handler가 state, sound, animation, board mutation을 동시에 다룬다.
- feature 전용 helper가 `components/` 루트나 `game/utils.ts`에 늘어난다.

## Book Layer

`bookEventHandlerMap.ts`는 facade다. 이벤트 이름과 handler 연결만 둔다.

```ts
export const bookEventHandlerMap = {
	reveal: handleReveal,
	winInfo: handleWinInfo,
	tumbleBoard: handleTumbleBoard,
	freeSpinTrigger: handleFreeSpinTrigger,
	setWin: handleSetWin,
	finalWin: handleFinalWin,
};
```

금지:

- `bookEventHandlerMap.ts` 안에 긴 anonymous handler 작성
- book event 순서 탐색과 animation 실행을 같은 함수에 작성
- `stateGame`, `stateBet`, `eventEmitter`를 handler map 안에서 직접 많이 조작

`handleXxx` 함수는 orchestration 목차처럼 읽혀야 한다.

```ts
export async function handleFeatureStart(event: FeatureStartEvent, context: BookEventContext) {
	const model = buildFeatureModel(event);
	applyFeatureStartState(model);

	await playFeatureStartPresentation(model, context);

	applyFeatureReadyState(model);
}
```

## Feature Layer

기능 단위로 파일을 모은다. 파일 종류보다 변경 이유를 우선한다.

```text
features/goldenBall/
  goldenBallModel.ts
  goldenBallExpansion.ts
  goldenBallState.ts
  goldenBallPresentation.ts
  goldenBallSequence.ts
```

파일 역할:

- `model`: 타입, 도메인 개념, status union
- `state`: 해당 feature가 소유하는 상태와 상태 변경 함수
- `presentation`: 화면 실행에 필요한 view model 또는 command 생성
- `sequence`: animation/sound 실행 순서를 가진 orchestration

도메인 계산은 Svelte, Pixi, eventEmitter를 몰라야 한다.

```ts
export function shouldOpenGoalScene(input: GoalSceneInput): boolean {
	if (input.kickers.length === 0) return false;
	return input.goldenBalls.some((goldenBall) => input.kickerReels.has(goldenBall.reel));
}
```

## State Ownership

전역 `stateGame`에 모든 상태를 계속 추가하지 않는다.

권장:

```text
features/goldenBall/goldenBallState.ts
features/kicker/kickerState.ts
features/freeSpin/freeSpinState.ts
```

`stateGame`에는 공통 runtime 상태만 둔다.

- board
- gameType
- 공통 spin/tumble 진행 상태
- 여러 feature가 같은 의미로 공유하는 최소 상태

feature 전용 overlay, resolver, animation status, covered positions는 feature state가 소유한다.

컴포넌트가 `stateGame.board[reel].reelState.symbols[row].rawSymbol` 같은 내부 구조를 직접 수정하면 안 된다. 필요하면 `applyBoardSymbolVisibility`, `applyBoardSymbolPresentationState`처럼 명명된 state 함수로 감싼다.

## Presentation Command

book event와 실제 UI 실행 사이에 command를 둘 수 있다. 복잡한 게임일수록 권장한다.

```ts
export type PresentationCommand =
	| { type: 'playSound'; name: SoundName; forcePlay?: boolean }
	| { type: 'hideBoardSymbols'; positions: Position[] }
	| { type: 'showBoardSymbols'; positions: Position[] }
	| { type: 'playSymbolAnimation'; positions: Position[]; animationName: string }
	| { type: 'playFeatureSequence'; sequence: FeaturePresentationSequence };
```

효과:

- book event 해석과 UI 실행을 분리한다.
- 테스트에서 command 배열을 검증할 수 있다.
- animation/sound 순서 변경 위험을 줄인다.

Command가 과해지는 작은 게임에서는 직접 `playXxxSequence`를 호출해도 된다. 단, 계산과 side effect는 분리한다.

## Dependency Rules

의존 방향은 아래처럼 흐른다.

```text
book -> features -> presentation -> components
```

허용:

- `book/handlers/*`는 feature의 model/state/presentation/sequence를 import할 수 있다.
- `features/*/*Presentation.ts`는 presentation command type을 import할 수 있다.
- `presentation/*`는 eventEmitter, sound, animation adapter를 import할 수 있다.
- `components/*`는 필요한 feature state 또는 view model을 읽을 수 있다.

금지:

- feature pure model이 Svelte 컴포넌트를 import
- feature pure model이 eventEmitter, Tween, Pixi, Spine, stateBet을 import
- comptate를 onent가 book handler 또는 book navigation을 import
- 다른 feature의 내부 s직접 수정
- `game/utils.ts`를 feature 전용 로직의 쓰레기통처럼 사용

다른 feature와 협력해야 하면 내부 파일을 직접 import하지 말고 명시적인 public API를 만든다.

```text
features/kicker/index.ts
features/goldenBall/index.ts
```

`index.ts`는 외부에서 써도 되는 타입과 함수만 export한다.

## Component Layer

Svelte 컴포넌트는 렌더링을 담당한다.

컴포넌트에 남길 수 있는 것:

- props 선언
- 짧은 derived 값
- 렌더링에 필요한 local state
- Spine/Pixi 표시
- 짧은 UI interaction handler

컴포넌트에서 빼야 하는 것:

- book event 탐색
- feature 규칙 판단
- win amount 계산
- board raw symbol 직접 수정
- 여러 단계 animation sequence
- eventEmitter subscribe 안의 긴 정책 로직

컴포넌트가 길어지면 아래 순서로 분리한다.

1. 조건 판단과 좌표 계산을 `.ts` pure function으로 이동
2. animation sequence를 `features/*/*Sequence.ts`로 이동
3. 상태 변경을 `features/*/*State.ts`로 이동
4. 렌더링 단위를 하위 컴포넌트로 분리

## Naming Rules

함수 prefix는 side effect 여부를 드러내야 한다.

- `is`, `has`, `can`, `should`: boolean 판단, side effect 금지
- `get`, `compute`, `calculate`: 값 조회/계산, side effect 금지
- `build`, `create`, `map`, `normalize`: 객체/배열 생성 또는 변환, side effect 금지
- `apply`, `update`, `reset`, `clear`: 상태 변경
- `play`, `stop`: animation/sound 실행
- `show`, `hide`, `open`, `close`: UI 표시 변경
- `handle`: book/emitter/user event 처리 orchestration
- `emit`, `broadcast`: eventEmitter 호출

넓은 이름을 피한다.

금지 예:

```text
utils.ts
feature.ts
data.ts
process.ts
handle.ts
state.ts
animation.ts
```

허용 예:

```text
goldenBallExpansion.ts
kickerGoalSceneState.ts
freeSpinIntroPresentation.ts
buildTumbleBoardSettleCommand.ts
```

## File Size Rules

권장 기준:

- 일반 `.ts` 로직 파일: 150줄 이하
- book handler 파일: 250줄 이하
- Svelte 컴포넌트: 150줄 이하
- Svelte `<script>`: 80줄 이하
- handler 함수: 20줄 이하
- sequence 함수: 40줄 이하

초과할 수는 있지만, 초과 이유가 구조상 명확해야 한다. “기존이 그래서”는 이유가 아니다.

## Design Patterns To Use

### Facade

`bookEventHandlerMap.ts`는 외부에서 보는 단일 입구다. 내부 feature 구조를 숨긴다.

### Feature Slice

기능별로 model, state, presentation, sequence를 같은 폴더에 둔다. 한 feature를 삭제할 때 관련 파일을 함께 삭제할 수 있어야 한다.

### Command

복잡한 presentation은 command로 표현한다. 테스트는 command 생성 결과와 실행 순서를 검증한다.

### Adapter

외부 API나 넓은 side effect는 adapter로 감싼다.

- eventEmitter
- sound
- Tween
- Spine/Pixi bridge
- state-shared의 `stateBet`

도메인 함수가 외부 라이브러리 객체를 직접 알면 안 된다.

### State Machine

서로 배타적인 UI/animation 상태는 여러 boolean이 아니라 union type으로 표현한다.

```ts
type FeatureStatus = 'idle' | 'preparing' | 'playing' | 'settling' | 'complete';
```

## Anti Patterns

아래 패턴이 보이면 구조 개선 후보로 본다.

- `bookEventHandlerMap.ts`에서 모든 이벤트 로직을 직접 처리한다.
- `components/` 루트에 feature 전용 컴포넌트와 helper가 계속 늘어난다.
- `utils.ts`, `types.ts`, `constants.ts`가 여러 도메인의 변경 이유를 동시에 가진다.
- Svelte 컴포넌트가 `eventEmitter.subscribeOnMount` 안에서 긴 게임 정책을 처리한다.
- 컴포넌트가 board raw symbol, global state, animation resolver를 직접 조작한다.
- boolean flag가 여러 개 조합되어 사실상 status machine처럼 동작한다.
- handler가 다음 book event를 직접 탐색한 뒤 곧바로 animation을 실행한다.
- 테스트가 component snapshot에만 있고 book event 순서와 side effect 순서를 고정하지 않는다.

안티 패턴을 발견해도 바로 대규모 리라이트하지 않는다. 먼저 책임 경계와 현재 동작 테스트를 만든다.

## Testing Rules

복잡한 게임 리팩토링은 테스트 없이 진행하지 않는다.

우선순위:

1. book event handler의 broadcast/state 변경 순서 고정
2. feature pure function 테스트
3. presentation command 생성 테스트
4. component rendering/interaction 테스트
5. storybook book 재생 검증

리팩토링 테스트는 현재 동작을 보존해야 한다. 버그처럼 보여도 리팩토링 중에는 고치지 않는다.

## Migration Strategy

기존 Stake 게임을 이 구조로 바꿀 때는 한 번에 전체 폴더를 갈아엎지 않는다.

1. 현재 `bookEventHandlerMap.ts`의 이벤트별 side effect 순서를 문서화하거나 테스트로 고정한다.
2. 가장 작은 pure function부터 feature 폴더로 옮긴다.
3. handler 안의 anonymous 함수를 `handleXxx` named function으로 바꾼다.
4. 한 이벤트 handler를 `book/handlers/*Handler.ts`로 이동한다.
5. 해당 handler가 쓰는 feature 전용 state와 presentation helper를 feature 폴더로 이동한다.
6. Svelte 컴포넌트에서 계산/판단 로직을 `.ts` 파일로 빼고 테스트한다.
7. eventEmitter 직접 호출이 많으면 presentation command 또는 sequence로 모은다.
8. 모든 이동 후 import boundary 위반을 점검한다.

권장 순서:

```text
pure function 추출
-> named handler 추출
-> handler 파일 이동
-> feature state 분리
-> presentation sequence 분리
-> component 축소
```

금지 순서:

```text
폴더 전체 이동
-> import 깨짐 수정
-> 동작 맞추기
```

이 순서는 동작 변경 위험이 크고 리뷰하기 어렵다.

## Refactoring Workflow For Agents

에이전트에게 설계 또는 리팩토링을 시킬 때 아래 순서를 따르게 한다.

1. 현재 게임의 `src/game`, `src/components`, `src/game/bookEventHandlerMap.ts` 구조를 먼저 읽는다.
2. book event flow 문서나 `typesBookEvent.ts`를 확인한다.
3. 큰 파일과 혼합 책임을 찾는다.
4. 변경 전 현재 동작을 고정할 테스트 또는 검증 지점을 정한다.
5. 한 번에 하나의 구조 변경만 수행한다.
6. handler map은 facade로 줄이고, 실제 로직은 feature handler로 이동한다.
7. 순수 계산을 먼저 추출하고 테스트한다.
8. 그 다음 state 변경 함수와 presentation sequence를 분리한다.
9. 마지막에 Svelte 컴포넌트를 얇게 만든다.

금지:

- 리팩토링 중 이벤트 순서 변경
- `await` 위치 변경
- 사운드/애니메이션 순서 변경
- payload shape 변경
- bug fix와 structure change를 한 PR에 섞기

## Review Checklist

설계 또는 PR 리뷰에서 아래 질문에 답할 수 있어야 한다.

- 새 개발자가 book event 이름으로 진입 파일을 찾을 수 있는가?
- 이 feature의 규칙, 상태, presentation 위치가 한 폴더 안에서 보이는가?
- 순수 함수와 side effect 함수가 이름만 보고 구분되는가?
- handler는 목차처럼 읽히는가?
- 컴포넌트는 렌더링 책임만 주로 갖는가?
- `utils.ts`, `types.ts`, `constants.ts`에 feature 전용 코드가 섞이지 않았는가?
- 다른 feature 내부 state를 직접 건드리지 않는가?
- 이벤트, sound, animation, await 순서가 보존되는가?
- 리팩토링과 버그 수정이 섞이지 않았는가?
- 테스트가 현재 동작 또는 새 구조의 핵심 계약을 고정하는가?

## Agent Prompt Template

설계 작업을 맡길 때는 이렇게 지시한다.

```text
Stake 프론트 게임 구조를 설계한다.
최우선 목표는 사람이 읽기 쉬운 코드와 빠른 맥락 파악이다.
agentSkills/designs/stake-front-game-architecture.md를 기준으로 현재 게임의 src/game, src/components, bookEventHandlerMap.ts를 읽고,
book event flow -> feature/domain -> state -> presentation -> component 순서로 폴더 구조와 리팩토링 단계를 제안하라.
동작 변경은 제안하지 말고, 리팩토링 전 고정해야 할 테스트도 같이 제안하라.
```

리팩토링 구현을 맡길 때는 이렇게 지시한다.

```text
agentSkills/designs/stake-front-game-architecture.md와 behavior-preserving-refactoring-guide.md를 기준으로 리팩토링한다.
이번 작업에서는 동작을 바꾸지 않는다.
먼저 현재 이벤트 순서와 side effect를 확인하고, 동작 고정 테스트를 추가하거나 기존 테스트를 지정하라.
그 다음 하나의 feature 또는 하나의 handler만 분리하라.
```

## Acceptance Criteria

구조 개선 완료 기준:

- book event handler map이 이벤트 연결 facade로 읽힌다.
- 각 feature의 규칙, 상태, presentation이 같은 feature 폴더에서 찾힌다.
- Svelte 컴포넌트가 book event와 도메인 규칙을 직접 알지 않는다.
- side effect 함수명에 side effect가 드러난다.
- 이벤트, sound, animation, state update 순서를 테스트나 명시적 검증으로 보존한다.
- 새 개발자가 `book -> feature -> state/presentation -> component` 경로로 문제를 추적할 수 있다.
