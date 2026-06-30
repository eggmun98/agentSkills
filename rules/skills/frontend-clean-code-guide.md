# Frontend Clean Code Guide

Version: v3

Last updated: 2026-06-30

이 문서는 AI 에이전트와 사람이 프론트엔드 코드를 작성할 때 지켜야 할 코드 기준을 정리한다.

## 최종 목표: 사람이 읽기 쉬운 코드

이 가이드의 모든 규칙은 하나의 최종 목표를 위한 수단이다 — **다른 개발자(특히 이 코드를 처음 보는 사람)가 쉽게 읽고 이해할 수 있는 코드.**

규칙끼리 충돌할 때는 "사람이 읽기 더 쉬운 쪽"을 기준으로 판단한다. 기능 구현 속도와 유지보수성도 중요하지만, 결국 둘 다 "읽기 쉬움"에서 나온다.

### 코드는 책처럼 읽혀야 한다 (신문 기사 비유 / Stepdown Rule)

좋은 코드는 잘 쓴 글처럼 **위에서 아래로 한 번 쭉 읽으면 흐름이 이어진다.** (Robert C. Martin, *Clean Code* 의 Newspaper Metaphor / The Stepdown Rule)

- **신문**: 헤드라인(가장 중요·추상적)이 위, 내려갈수록 세부. 독자는 위에서 읽다가 필요한 만큼만 깊이 들어간다.
- **코드**: 파일 위에 "무엇을 하는가"(진입점·공개 함수), 그 아래에 그것이 호출하는 함수를 호출 순서대로, 또 그 아래 더 세부를 둔다.

사람은 작업 기억(working memory)이 좁다. 위에서 아직 모르는 것을 만나면 머릿속에 미지수로 들고 가거나 다른 곳으로 점프해야 한다. top-down(추상 → 구체, 호출자 → 피호출자)으로 배치하면 이름만 믿고 읽어 내려가다가 궁금할 때만 세부를 보면 되므로 부담이 적다.

처음 읽는 사람과 코드 리뷰어는 실제로 위에서 아래로 읽는다. (IDE의 "정의로 이동" 점프는 이미 코드를 아는 사람의 방식이다.) "사람이 처음 읽는 경우"를 기준으로 삼는다.

이 원칙의 구체 규칙은 아래 `코드 배치 순서 기준`에 있다.

## AI 에이전트 작업 전 체크리스트

작업을 시작하기 전에 아래 항목을 먼저 확인한다.

- 새 코드를 기존 큰 파일에 바로 추가하지 않는다.
- 이 코드의 책임이 어느 도메인/컴포넌트/함수에 속하는지 먼저 정한다.
- 계산, 판단, 변환은 가능하면 순수 함수로 작성한다.
- side effect는 함수명에 드러나게 작성한다.
- `any`, `as unknown as`, non-null assertion `!`를 사용하지 않는다.
- 중첩 삼항과 3단계 이상 `if` 중첩을 만들지 않는다.
- 공통화하기 전에 변경 이유가 같은지 확인한다.
- props drilling이나 거대한 state/store를 만들지 않는다.
- 숫자에 도메인 의미가 있으면 상수화한다.
- 예외적으로 규칙을 지키지 못하면 이유를 주석으로 남긴다.

## 작성 원칙

- 규칙은 추상적인 권장사항보다 코드 리뷰에서 판단 가능한 기준으로 작성한다.
- 예외가 필요한 경우, 코드 작성자가 이유를 짧게 남긴다.
- 새 기능은 기존 큰 파일에 바로 추가하기 전에 책임 위치를 먼저 확인한다.
- 이 문서는 항목별로 합의한 뒤 확정한다.

## 코드 설계 기본 원칙

가능하면 계산, 판단, 변환은 순수 함수로 작성한다.

side effect는 함수 바깥쪽 또는 orchestration 함수에 모은다.

응집도는 높인다. 같이 변경되는 코드는 가까이 둔다.

결합도는 낮춘다. 다른 도메인의 내부 구현에 의존하지 않는다.

공통화보다 변경 이유를 우선한다.

함수와 컴포넌트는 하나의 변경 이유만 가지게 한다.

이름, 파라미터, 반환 타입만 보고 동작을 예측할 수 있어야 한다.

설계가 애매하면 아래 질문으로 판단한다.

- 이 함수가 입력만으로 결과가 결정되는가?
- 이 코드들이 항상 같이 바뀌는가?
- 이 코드를 바꾸면 다른 기능이 같이 깨질 수 있는가?
- 인자가 많아지고 분기가 늘어나는가?
- 함수 이름과 다른 일을 하는가?

## 확정된 규칙

### 주석 작성 기준

주석은 무엇을 하는지보다 왜 이렇게 했는지를 설명할 때 작성한다.

일반적인 코드는 주석을 달지 않는다.

코드만 봐도 알 수 있는 설명 주석은 작성하지 않는다.

```ts
// 금지: 코드만 읽어도 알 수 있음
// winAmount가 0이면 return한다.
if (winAmount === 0) return;
```

예외 규칙을 적용한 경우 주석을 남긴다.

임시 코드(`TEMP:`)는 이유와 제거 조건을 남긴다.

긴 함수나 컴포넌트를 분리하지 않은 경우 이유를 남긴다.

타입 우회(`as`)를 사용한 경우 이유를 남긴다.

AI 에이전트가 일반적이지 않은 구조를 선택했다면 이유를 짧게 남긴다.

주석은 1~2줄로 짧게 작성한다.

```ts
// Spine 이벤트 순서가 깨지면 연출이 어긋나므로 이 함수에서 순서를 고정한다.
await playIntroSequence();
```

### if 중첩과 early return

기본 흐름은 early return을 우선 사용한다.

실패 조건, 무시 조건, 선행 조건은 함수 상단에서 먼저 반환해 핵심 로직의 들여쓰기를 줄인다.

```ts
function playBonus() {
	if (!isReady) return;
	if (!hasBonus) return;
	if (isDisabled) return;

	startBonus();
}
```

정상적인 A/B 분기는 `if/else`, `switch`, handler map, lookup table을 사용할 수 있다.

```ts
function handlePopup() {
	if (mode !== 'buy') return;
	if (!isOpen) return;

	if (isMobile) {
		showMobilePopup();
	} else {
		showDesktopPopup();
	}
}
```

`if` 중첩은 최대 2단계까지만 허용한다.

3단계 이상이 필요하면 다음 중 하나로 분리한다.

- guard clause / early return
- 별도 조건 함수
- 상태별 handler map
- `switch`
- lookup table

```ts
// 금지
if (isOpen) {
	if (isMobile) {
		if (hasBonus) {
			showBonus();
		}
	}
}

// 권장
if (!isOpen) return;
if (!isMobile) return;
if (!hasBonus) return;

showBonus();
```

이벤트 순서가 중요한 orchestration 코드에서는 3단계 이상을 예외적으로 허용할 수 있다. 단, 왜 분리하지 않았는지 주석으로 이유를 남긴다.

### 삼항 연산자

삼항 연산자는 한 줄에서 단순한 A/B 값 선택에만 사용한다.

```ts
// 허용
const icon = isMuted ? 'SoundOff.png' : 'SoundOn.png';
```

중첩 삼항은 금지한다.

```ts
// 금지
const icon = isMobile
	? volume === 0
		? 'MobileOff.png'
		: 'MobileOn.png'
	: volume === 0
		? 'DesktopOff.png'
		: 'DesktopOn.png';
```

중첩 삼항을 대체할 때는 아래 우선순위를 따른다.

1. 의미 있는 함수로 분리한다.
2. 분기가 짧고 해당 위치에서만 쓰이는 단순 A/B 선택이면 `if/else`로 풀 수 있다.
3. 상태값과 결과값의 단순 매핑이면 lookup table을 사용할 수 있다.

```ts
// 권장: 함수 분리
function getVolumeIcon({ isMobile, volume }: { isMobile: boolean; volume: number }) {
	if (isMobile && volume === 0) return 'MobileOff.png';
	if (isMobile) return 'MobileOn.png';
	if (volume === 0) return 'DesktopOff.png';

	return 'DesktopOn.png';
}

const icon = getVolumeIcon({ isMobile, volume });
```

```ts
// 허용: 짧은 로컬 분기
let nextAnimation: AnimationName;

if (isOpen) {
	nextAnimation = 'open';
} else {
	nextAnimation = 'close';
}
```

### 함수 네이밍

함수 이름은 동사로 시작한다.

함수 이름만 보고 무엇을 하는 함수인지 알 수 있어야 한다.

역할별 prefix는 아래 기준을 따른다.

- boolean 판단: `is`, `has`, `can`, `should`
- 값 조회 또는 계산: `get`, `compute`, `calculate`
- 객체나 payload 생성: `create`, `build`, `map`, `normalize`
- 상태 변경: `set`, `reset`, `update`, `apply`, `clear`
- 이벤트 처리: `handle`, `on`
- 애니메이션/사운드/UI 실행: `play`, `stop`, `show`, `hide`, `open`, `close`
- 검증: `validate`, `assert`
- 변환: `to`, `from`, `parse`, `format`

```ts
shouldSkipAnticipation();
canStartBuyBonus();
getVolumeIcon();
buildGoldenBallOverlay();
normalizeBoardSymbols();
applyGoalMultiplier();
handleBuyButtonPress();
playFreeSpinIntro();
formatWinAmount();
```

역할이 흐린 이름은 사용하지 않는다.

```ts
// 금지
bonus();
popupData();
goldenBall();
process();
doStuff();
check();
data();
```

`handle`은 DOM 이벤트, emitter 이벤트, user interaction 이벤트를 받아 처리하는 함수에만 사용한다.

### 이름 충돌 방지 기준

외부 라이브러리, 브라우저 API, 엔진 객체와 같은 이름을 서비스 코드에서 재사용하지 않는다.

기존 함수와 이름이 같으면 동작도 같아야 한다.

추가 동작이 있으면 이름에 드러낸다.

`sound`, `animation`, `state`, `event`, `http`, `storage` 같은 넓은 이름은 단독으로 쓰지 않는다.

라이브러리나 엔진 객체를 감싼 코드는 역할명을 붙인다.

- `Service`
- `Adapter`
- `Manager`
- `Controller`

```ts
// 비권장: http.get처럼 보이지만 인증 header를 추가한다.
const http = {
	get(url: string) {
		return httpLibrary.get(url, { headers: authHeaders });
	},
};
```

```ts
// 권장
const authHttpService = {
	getWithAuth(url: string) {
		return httpLibrary.get(url, { headers: authHeaders });
	},
};
```

### 같은 종류 함수의 반환 타입 기준

같은 prefix나 같은 역할의 함수는 반환 타입을 통일한다.

함수 이름만 보고 반환 타입과 side effect 여부를 예측할 수 있어야 한다.

- `isXxx`, `hasXxx`, `canXxx`, `shouldXxx`: `boolean`을 반환한다.
- `getXxx`, `computeXxx`, `calculateXxx`: 값을 반환하고 side effect를 만들지 않는다.
- `buildXxx`, `createXxx`, `mapXxx`, `normalizeXxx`: 객체, 배열, 값 변환 결과를 반환하고 side effect를 만들지 않는다.
- `applyXxx`, `updateXxx`, `resetXxx`, `clearXxx`: 상태를 변경하고 반환값을 만들지 않는다.
- `playXxx`, `showXxx`, `hideXxx`, `openXxx`, `closeXxx`: 실행 결과를 반환하지 않거나 `Promise<void>`를 반환한다.
- `validateXxx`: `ValidationResult`를 반환한다.
- `assertXxx`: 실패하면 throw하고, 성공하면 반환값을 만들지 않는다.

boolean 검증 함수에는 `validateXxx`를 사용하지 않는다.

boolean 검증은 `isXxx`, `hasXxx`, `canXxx`, `shouldXxx`를 사용한다.

```ts
type ValidationResult = { ok: true } | { ok: false; reason: string };
```

```ts
// 금지
function validateName(name: string) {
	return name.length > 0;
}

function validateAge(age: number) {
	if (age < 18) return { ok: false, reason: 'too young' };

	return { ok: true };
}
```

```ts
// 권장
function hasName(name: string): boolean {
	return name.length > 0;
}

function validateAge(age: number): ValidationResult {
	if (age < 18) return { ok: false, reason: 'too young' };

	return { ok: true };
}
```

### 숨은 side effect 금지 기준

함수 이름만 보고 예측할 수 없는 side effect를 만들지 않는다.

값을 조회하거나 계산하거나 변환하는 함수는 side effect를 만들지 않는다.

아래 prefix 함수는 side effect를 금지한다.

- `getXxx`
- `computeXxx`
- `calculateXxx`
- `buildXxx`
- `createXxx`
- `mapXxx`
- `normalizeXxx`
- `isXxx`
- `hasXxx`
- `canXxx`
- `shouldXxx`

side effect 예시는 다음과 같다.

- 상태 변경
- store/context 변경
- eventEmitter emit/broadcast
- 사운드/애니메이션 실행
- logging
- API/storage 접근
- timer 실행

```ts
// 금지
function getWinAmount() {
	eventEmitter.emit('win_checked');
	playWinSound();

	return state.winAmount;
}
```

side effect가 필요하면 함수명에 역할을 드러낸다.

- 상태 변경: `applyXxx`, `updateXxx`, `resetXxx`, `clearXxx`
- 이벤트 발행: `emitXxx`, `broadcastXxx`
- 사운드/애니메이션 실행: `playXxx`, `stopXxx`
- logging: `logXxx`
- 동기화: `syncXxx`
- 저장/조회 side effect: `saveXxx`, `loadXxx`

값 계산과 side effect는 분리한다.

```ts
const winAmount = getWinAmount();

logWinAmountChecked();
playWinSound();
applyWinAmount(winAmount);
```

### 함수 분리 기준

함수는 하나의 의도만 가진다.

한 함수 안에서 아래 역할이 2개 이상 섞이면 분리 후보로 본다.

- 조건 판단
- 데이터 변환
- 상태 변경
- 이벤트 발행
- 애니메이션 실행
- 사운드 실행
- API/storage 접근

일반 함수는 10줄 이하를 권장한다.

일반 함수가 11줄 이상이면 분리할 수 없는 이유를 주석으로 남긴다.

애니메이션/사운드/이벤트 시퀀스 함수는 20줄 이하를 권장한다.

시퀀스 함수가 21줄 이상이면 분리할 수 없는 이유를 주석으로 남긴다.

줄 수를 맞추기 위해 의미 없는 wrapper 함수나 단계 번호 함수는 만들지 않는다.

분리된 함수는 이름만 보고 역할을 이해할 수 있어야 한다.

```ts
// 금지
function doStep1() {}
function doStep2() {}
function doStep3() {}
```

```ts
// 권장
function canStartBuyBonus() {}
function applyBuyBonusBetMode() {}
function playFreeSpinIntroSequence() {}
```

### Svelte 컴포넌트 분리 기준

Svelte 컴포넌트는 렌더링과 간단한 interaction을 우선 담당한다.

게임 정책, 데이터 변환, 이벤트 순서, 복잡한 좌표/layout 계산은 `.ts` 함수로 분리한다.

Svelte 컴포넌트 전체는 150줄 이하를 권장한다.

`<script>` 영역은 80줄 이하를 권장한다.

컴포넌트 전체가 151줄 이상이거나 `<script>` 영역이 81줄 이상이면, 분리할 수 없는 이유를 주석으로 남긴다.

`<script>` 영역이 길어지는 경우, 우선 아래 항목을 `.ts` 파일로 분리한다.

- 조건 판단
- 데이터 변환
- 좌표/layout 계산
- 게임 상태 변경 정책
- 이벤트 payload 생성

아래 항목은 컴포넌트에 남길 수 있다.

- props 선언
- 간단한 derived 값
- 단순 UI interaction handler
- 렌더링에 직접 필요한 짧은 상태

컴포넌트가 150줄 이하라도 여러 책임이 섞이면 분리 후보로 본다.

### 같이 실행되지 않는 UI 분리 기준

상태에 따라 완전히 다른 UI나 side effect가 실행되면 한 컴포넌트 안에서 모두 처리하지 않는다.

같은 상태 기준으로 `if`, 삼항 연산자, lifecycle, animation 조건이 반복되면 컴포넌트 분리 후보로 본다.

상태별 UI가 서로 다른 책임을 가지면 상태별 컴포넌트로 분리한다.

```svelte
<!-- 비권장: viewer/admin 상태의 UI와 side effect가 한 컴포넌트에 섞인다. -->
{#if isViewer}
	<ViewerButton disabled />
{:else}
	<AdminButton on:click={playButtonAnimation} />
{/if}
```

```svelte
<!-- 권장 -->
{#if isViewer}
	<ViewerSubmitButton />
{:else}
	<AdminSubmitButton />
{/if}
```

상태가 단순하고 렌더링 차이가 작으면 한 컴포넌트 안의 짧은 분기는 허용한다.

분리 기준은 코드 줄 수보다 변경 이유와 side effect 차이를 우선한다.

### Props drilling 기준

컴포넌트가 직접 사용하지 않는 props를 자식에게 전달만 하지 않는다.

2단계 이상 같은 props를 전달만 하면 props drilling으로 본다.

props drilling이 보이면 먼저 컴포넌트 구조를 바꿔 composition으로 해결한다.

그래도 여러 하위 컴포넌트가 같은 상태를 공유해야 하면 Context 또는 store를 검토한다.

단, 컴포넌트의 역할을 설명하는 props는 허용한다.

Context 또는 store는 편해서 사용하는 것이 아니라 여러 깊은 하위 컴포넌트가 같은 값을 직접 필요로 할 때 사용한다.

```svelte
<!-- 금지: Body는 items를 쓰지 않고 List로 전달만 한다. -->
<Body {items}>
	<List {items} />
</Body>
```

```svelte
<!-- 권장: 부모가 필요한 컴포넌트를 직접 조립한다. -->
<Body>
	<List {items} />
</Body>
```

### 입력 상태와 검증 응집도 기준

입력값의 검증, 변환, 에러 처리는 변경 단위에 맞게 둔다.

필드가 독립적으로 바뀌면 필드 단위로 분리한다.

여러 필드가 서로 의존하면 폼 또는 패널 단위로 관리한다.

필드별 검증이 복잡하거나 재사용 가능하면 `validateXxxField` 함수로 분리한다.

전체 흐름 검증이면 `validateXxxForm` 또는 `validateXxxPanel` 함수로 둔다.

한 입력 컴포넌트가 값 관리, 검증, 에러 표시, 저장/적용 side effect를 모두 가지면 분리 후보로 본다.

입력 컴포넌트는 가능하면 렌더링과 사용자 입력 이벤트만 담당한다.

```ts
// 필드 단위 검증
function validateBetAmountField(value: number): ValidationResult {
	if (value <= 0) return { ok: false, reason: 'bet amount must be positive' };

	return { ok: true };
}
```

```ts
// 패널 전체 흐름 검증
function validateBuyBonusPanel(values: BuyBonusPanelValues): ValidationResult {
	if (!validateBetAmountField(values.betAmount).ok) {
		return { ok: false, reason: 'invalid bet amount' };
	}

	if (values.balance < values.cost) {
		return { ok: false, reason: 'not enough balance' };
	}

	return { ok: true };
}
```

### 넓은 state/store 금지 기준

여러 도메인의 상태를 하나의 거대한 state 또는 store에 계속 추가하지 않는다.

특정 기능 상태는 기능 단위 state 또는 store로 분리한다.

어떤 값 하나만 바뀌어도 관련 없는 UI나 로직이 같이 영향받으면 상태 분리 후보로 본다.

`values`, `controls`처럼 모든 상태와 setter를 한 번에 반환하는 구조는 주의한다.

새 상태를 추가하기 전에 해당 상태의 변경 이유와 사용하는 UI 범위를 먼저 확인한다.

```ts
// 비권장: 페이지나 게임 전체 상태가 계속 커진다.
const gameState = {
	buyBonus: {},
	freeSpin: {},
	popup: {},
	anticipation: {},
	sound: {},
};
```

```ts
// 권장: 기능 단위로 상태 책임을 분리한다.
const buyBonusState = createBuyBonusState();
const freeSpinState = createFreeSpinState();
const popupState = createPopupState();
```

공유 상태가 필요하면 먼저 실제로 여러 도메인에서 같은 의미로 쓰이는지 확인한다.

편의를 위해 전역 state/store에 올리지 않는다.

### 중복 코드 허용 기준

코드가 2번 반복됐다는 이유만으로 공통화하지 않는다.

변경 이유가 다르면 중복을 허용한다.

도메인이 다르면 중복을 허용한다.

앞으로 요구사항이 다르게 갈 가능성이 있으면 중복을 허용한다.

3번 이상 반복되고, 같은 이유로 함께 변경될 때만 공통화를 검토한다.

공통화하면 인자가 많아질 것 같으면 중복을 유지한다.

공통 함수가 `mode`, `type`, `variant`, `options` 분기로 커지면 잘못된 공통화로 본다.

```ts
// 허용: 지금은 비슷하지만 변경 이유가 다르면 분리 유지
openBuyBonusPopup();
openFreeSpinPopup();
```

```ts
// 주의: type 분기가 계속 늘어나면 잘못된 공통화
openGamePopup({ type: 'buyBonus' });
openGamePopup({ type: 'freeSpin' });
```

### 파일 크기와 파일 역할

일반 `.ts` 로직 파일은 150줄 이하를 권장한다.

일반 `.ts` 로직 파일이 151줄 이상이면 분리할 수 없는 이유를 주석으로 남긴다.

타입, 상수, asset map, config map 파일은 줄 수 제한을 적용하지 않는다.

단, 도메인이 섞이면 파일을 분리한다.

도메인은 하나의 기능/개념 단위다.

같이 읽히고 같이 변경되는 타입과 상수는 같은 도메인으로 본다.

변경 이유가 다르면 다른 파일로 분리한다.

```text
// 권장
symbolTypes.ts
bookEventTypes.ts
soundConstants.ts
animationConstants.ts
buyBonusOptions.ts
```

`types.ts`, `constants.ts` 같은 범용 파일에 모든 타입과 상수를 계속 추가하지 않는다.

가능하면 파일명에 도메인이 드러나게 작성한다.

### 같이 수정되는 파일 위치 기준

파일 위치는 파일 종류보다 변경 이유를 우선한다.

특정 기능에서만 쓰는 컴포넌트, 타입, 상수, 함수는 그 기능 폴더 안에 둔다.

전역 `components`, `utils`, `constants`, `types`에는 여러 도메인에서 쓰는 공용 코드만 둔다.

한 기능을 삭제할 때 관련 파일을 같이 삭제할 수 있어야 한다.

다른 도메인 내부 파일을 직접 import하지 않는다.

공용으로 올리기 전에는 최소 2개 이상의 도메인에서 같은 의미로 쓰이는지 확인한다.

```text
// 비권장
components/BuyBonusPopup.svelte
constants/buyBonus.ts
types/buyBonus.ts
utils/buyBonus.ts
```

```text
// 권장
game/
  buyBonus/
    BuyBonusPopup.svelte
    buyBonusConstants.ts
    buyBonusTypes.ts
    buildBuyBonusData.ts
    playBuyBonusSequence.ts

  freeSpin/
    FreeSpinCounter.svelte
    freeSpinConstants.ts
    freeSpinTypes.ts
```

### 매직 넘버 기준

의미가 있는 숫자는 코드에 직접 쓰지 않고 상수로 분리한다.

특히 아래 값은 상수화를 우선 검토한다.

- 애니메이션 시간
- delay
- 좌표
- scale
- alpha
- zIndex
- reel index
- symbol id
- multiplier
- 확률/가중치

상수 이름에는 의미나 단위를 드러낸다.

```ts
const FREE_SPIN_INTRO_DELAY_MS = 300;
const POPUP_OPEN_SCALE = 1.08;
const MULTIPLIER_BADGE_Z_INDEX = 20;
const GOLDEN_BALL_SYMBOL_ID = 11;
```

단위가 있는 값은 이름에 단위를 붙인다.

- 시간: `_MS`
- 좌표: `_X`, `_Y`
- 크기: `_WIDTH`, `_HEIGHT`
- 배율: `_SCALE`
- 투명도: `_ALPHA`
- zIndex: `_Z_INDEX`
- id: `_ID`
- 개수: `_COUNT`
- 배수: `_MULTIPLIER`

`0`, `1`, `-1`도 도메인 의미가 있으면 상수화한다.

```ts
const NO_WIN_AMOUNT = 0;
const BASE_MULTIPLIER = 1;
const NOT_FOUND_INDEX = -1;
const FIRST_REEL_INDEX = 0;
```

단순 배열 index, 빈 배열 체크, 증감처럼 관용적으로 읽히는 값은 직접 사용할 수 있다.

```ts
if (items.length === 0) return;

const firstItem = items[0];
const lastItem = items[items.length - 1];

count += 1;
```

CSS 숫자는 이 프로젝트에서 주요 기준으로 보지 않는다.

컴포넌트 props로 전달하는 숫자는 같은 의미로 반복되거나 게임 UI 의미가 있으면 상수화한다.

위치, 크기, scale, alpha, delay, zIndex, symbol/reel 관련 값은 상수화를 우선 검토한다.

한 번만 쓰이는 단순 배치 숫자는 props에 직접 작성할 수 있다.

TS/게임 로직과 같이 쓰이는 숫자는 반드시 TS 상수로 둔다.

상수는 해당 기능 도메인 파일에 둔다. 여러 도메인에서 쓰면 공용 constants로 올린다.

### utils 파일 기준

`utils.ts`에는 여러 도메인에서 재사용할 수 있는 순수 공용 함수만 둔다.

아래 조건을 모두 만족해야 `utils.ts`에 둘 수 있다.

- 특정 게임 기능 이름을 모른다.
- 특정 이벤트 타입을 모른다.
- 특정 컴포넌트 상태를 모른다.
- 입력값만으로 결과가 결정되는 pure function이다.
- 최소 2개 이상의 도메인에서 재사용될 가능성이 있다.

하나의 도메인에서만 쓰는 함수는 `utils.ts`에 두지 않고 해당 도메인 파일에 둔다.

```ts
// utils.ts 허용
clamp(value, min, max);
isNonNullable(value);
formatNumber(value);
```

```ts
// utils.ts 금지
getBuyBonusCost();
shouldSkipAnticipation();
normalizeBoardSymbols();
buildGoldenBallOverlay();
mapBookEventToAnimation();
```

`utils.ts`에는 side effect가 있는 함수를 둘 수 없다.

side effect 예시는 다음과 같다.

- 전역 상태 읽기/쓰기
- store/context 접근
- eventEmitter broadcast/subscribe
- 사운드/애니메이션 실행
- API/localStorage/sessionStorage 접근
- timer 실행

side effect helper가 필요하면 `utils.ts`가 아니라 역할이 드러나는 별도 파일로 분리한다.

```text
timeUtils.ts
soundEffects.ts
storageAdapter.ts
```

### 이벤트 handler map 기준

이벤트 handler map 파일에는 이벤트 이름과 handler 연결을 우선 둔다.

handler map 안에 긴 로직을 직접 작성하지 않는다.

실제 로직은 이벤트별 함수 또는 도메인별 파일로 분리한다.

```ts
// 권장
export const bookEventHandlerMap = {
	reveal: handleReveal,
	winInfo: handleWinInfo,
	freeSpinTrigger: handleFreeSpinTrigger,
};
```

handler 함수가 여러 도메인을 동시에 다루면 orchestration 함수로 보고, 계산/판단/변환은 별도 함수로 분리한다.

### 이벤트 핸들러 책임 기준

`handleXxx` 함수는 이벤트 처리 흐름을 보여주는 orchestration 함수로 둔다.

복잡한 판단, 계산, 변환, 상태 변경, 애니메이션/사운드 실행을 `handleXxx` 안에 직접 작성하지 않는다.

`handleXxx` 안에서는 역할이 분리된 작은 함수를 순서대로 호출한다.

```ts
function handleBuyBonus(event: BuyBonusEvent) {
	if (!canBuyBonus(event)) return;

	const buyBonusData = buildBuyBonusData(event);

	applyBuyBonusState(buyBonusData);
	playBuyBonusSequence();
	openBuyBonusPopup();
}
```

역할별 함수명은 아래 기준을 따른다.

- 조건 판단: `isXxx`, `hasXxx`, `canXxx`, `shouldXxx`
- 데이터 생성/변환: `buildXxx`, `mapXxx`, `normalizeXxx`
- 상태 변경: `applyXxx`, `updateXxx`, `resetXxx`
- 애니메이션/사운드 실행: `playXxx`, `stopXxx`, `playXxxSequence`
- UI 표시 변경: `showXxx`, `hideXxx`, `openXxx`, `closeXxx`

`handleXxx` 함수는 20줄 이하를 권장한다.

`handleXxx` 함수가 21줄 이상이면 분리할 수 없는 이유를 주석으로 남긴다.

### 복잡한 조건식 작성 기준

조건식은 한 줄에서 바로 이해할 수 있는 수준으로 작성한다.

`&&`와 `||`가 섞이면 의미 있는 boolean 함수로 분리한다.

조건이 3개 이상이면 `shouldXxx`, `canXxx`, `isXxx`, `hasXxx` 함수로 분리한다.

부정 조건 `!`가 여러 개 있으면 긍정 이름의 함수로 분리한다.

괄호가 2단계 이상 필요하면 함수로 분리한다.

```ts
// 금지
if ((isFreeSpin && hasMultiplier && !isSkipping) || (isBuyBonus && balance >= cost && !isDisabled)) {
	startBonus();
}
```

```ts
// 권장
if (canStartBonus()) {
	startBonus();
}
```

분리한 조건 함수는 내부 구현보다 의도를 먼저 드러내야 한다.

```ts
function canStartBonus() {
	if (isSkipping) return false;
	if (isDisabled) return false;

	return isFreeSpinWithMultiplier() || hasEnoughBalanceForBuyBonus();
}
```

### 시점 이동 줄이기 기준

코드를 이해하기 위해 위아래, 다른 파일, 다른 상수를 계속 따라가야 하는 구조를 줄인다.

단순한 조건은 과도하게 추상화하지 않는다.

읽는 사람이 의미를 이해하기 위해 3번 이상 이동해야 하면 구조를 다시 검토한다.

lookup table은 상태가 많거나 매핑이 안정적일 때 사용한다.

상태가 2개뿐이고 조건이 단순하면 `if` 또는 `switch`로 직접 드러낼 수 있다.

```ts
// 주의: 조건 하나를 이해하려고 여러 위치를 따라가야 한다.
const policy = getPolicyByRole(userRole);

if (policy.canInvite) {
	showInviteButton();
}
```

```ts
// 허용: 상태가 적고 조건이 단순하면 직접 드러낸다.
if (userRole === 'admin') {
	showInviteButton();
}
```

추상화는 읽는 맥락을 줄일 때 사용한다.

추상화 때문에 오히려 이동해야 하는 위치가 늘어나면 직접 드러내는 쪽을 검토한다.

### 코드 배치 순서 기준

함수와 코드는 읽는 순서대로 배치한다.

파일이나 컴포넌트를 위에서 아래로 읽을 때 흐름이 자연스럽게 이어지게 한다.

진입점(공개 함수, orchestration 함수, `handleXxx`)을 위에 두고, 그 함수가 호출하는 헬퍼를 아래에 호출 순서대로 둔다.

의미를 이해하려고 위아래로 스크롤을 반복하게 만들지 않는다.

한 함수에서만 쓰는 헬퍼는 그 함수 근처에 둔다.

상수와 타입은 사용처에서 멀어지지 않게 파일 상단이나 처음 쓰이는 위치 근처에 둔다.

```ts
// 금지: 헬퍼가 위에 흩어져 있어 진입점을 찾으려 아래까지 내려가고, 흐름을 보려 다시 올라온다.
function buildRevealBoard() {}
function applyRevealBoard() {}
function playRevealSequence() {}

export function handleReveal() {
	const board = buildRevealBoard();
	applyRevealBoard(board);
	playRevealSequence();
}
```

```ts
// 권장: 진입점이 위, 호출되는 헬퍼가 아래에 호출 순서대로. 위에서 아래로 한 번에 읽힌다.
export function handleReveal() {
	const board = buildRevealBoard();
	applyRevealBoard(board);
	playRevealSequence();
}

function buildRevealBoard() {}
function applyRevealBoard() {}
function playRevealSequence() {}
```

호이스팅이나 순환 참조 때문에 순서를 바꿔야 하면 이유를 주석으로 남긴다.

### 타입 우회 기준

`any`는 기본적으로 사용하지 않는다.

타입을 알 수 없는 값은 `any` 대신 `unknown`으로 받고, 타입 좁히기 함수로 검증한다.

```ts
function isBuyBonusEvent(value: unknown): value is BuyBonusEvent {
	if (typeof value !== 'object') return false;
	if (value === null) return false;

	return 'cost' in value;
}
```

`as` type assertion은 아래 경계에서만 허용한다.

- 외부 데이터 파싱 경계
- 라이브러리 타입 정의가 실제 런타임과 맞지 않는 경우
- DOM, Spine, Pixi 등 엔진 객체 경계

`as`를 사용할 때는 바로 위에 이유를 주석으로 남긴다.

```ts
// Spine 런타임에서 실제로 제공하지만 타입 정의에는 누락되어 있다.
const slot = spineObject as SpineSlotWithAttachment;
```

`as unknown as`는 금지한다.

non-null assertion `!`는 금지한다.

```ts
// 금지
const element = document.querySelector('.popup')!;
```

값이 없을 수 있으면 guard clause로 먼저 확인한다.

```ts
const element = document.querySelector('.popup');

if (!element) return;

openPopup(element);
```

### 임시 코드와 디버그 코드 기준

`console.log`, `console.debug`, `console.error`는 개발 중 확인 용도로 사용할 수 있다.

단, `console.*` 호출이 기능 동작에 영향을 주면 안 된다.

```ts
// 금지
if (console.log(result)) {
	startBonus();
}
```

빌드 과정에서 제거되는 것을 전제로 하며, 중요한 오류 처리는 `console.error`만으로 끝내지 않는다.

`TODO:`는 허용한다.

`TODO:`를 남길 때는 이유와 다음 행동을 함께 적는다.

```ts
// TODO: Spine 리소스 로딩 순서가 확정되면 preload 단계로 이동한다.
```

`FIXME:`는 원칙적으로 금지한다.

알고 있는 버그나 깨진 동작을 `FIXME:`로 남긴 채 완료 코드로 보지 않는다.

임시 하드코딩, 강제 return, mock 데이터, 테스트용 플래그는 최종 코드에 남기지 않는다.

정말 임시 코드가 필요하면 `TEMP:` 주석으로 이유와 제거 조건을 남긴다.

```ts
// TEMP: 서버 이벤트 스키마 배포 전까지 기존 payload와 호환한다. 스키마 배포 후 제거한다.
```
