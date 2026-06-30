# Refactoring Test Guide

Version: v1

Last updated: 2026-06-25

이 문서는 리팩토링 전에 작성해야 하는 테스트 기준을 정리한다.

목표는 코드를 바꾸기 전에 현재 동작을 고정해서, 리팩토링 후에도 전과 후의 동작이 같다는 것을 확인하는 것이다.

## 기본 원칙

리팩토링 전 테스트는 올바른 동작을 새로 정의하는 테스트가 아니다.

리팩토링 전 테스트는 현재 코드가 실제로 하는 동작을 고정하는 테스트다.

현재 동작에 버그가 있어도 리팩토링 단계에서는 그 버그를 그대로 테스트한다.

버그 수정용 테스트는 리팩토링 테스트와 분리해서 작성한다.

## 테스트 작성 순서

테스트는 아래 순서로 작성한다.

1. 현재 코드가 하는 일을 관찰한다.
2. 입력과 실행 조건을 최소로 만든다.
3. 결과, 호출 순서, 상태 변경을 기록한다.
4. 현재 동작을 테스트로 고정한다.
5. 테스트가 실패하면 코드를 바꾸지 말고 테스트 기대값이 현재 동작과 맞는지 먼저 확인한다.

테스트가 현재 동작을 통과한 뒤에만 리팩토링을 시작한다.

## AAA 패턴

기본 테스트 구조는 AAA 패턴을 사용한다.

AAA는 Arrange, Act, Assert를 뜻한다.

- Arrange: 테스트에 필요한 상태와 입력을 준비한다.
- Act: 테스트 대상 동작을 한 번 실행한다.
- Assert: 결과를 검증한다.

```ts
it('현재 구현상 multiplier가 0이면 배수 텍스트를 표시하지 않는다', () => {
	// Arrange
	const rawSymbol = { name: 12, ball: true, multiplier: 0 };

	// Act
	const result = shouldShowBallDetachedMultiplierText({
		rawSymbol,
		state: 'static',
		animationName: 'idle',
	});

	// Assert
	expect(result).toBe(false);
});
```

Act 단계에서 여러 동작을 섞지 않는다.

하나의 테스트에서 검증 대상 동작은 하나로 유지한다.

## Given When Then

테스트 이름이나 테스트 구조가 시나리오 중심이면 Given-When-Then을 사용한다.

- Given: 어떤 상태에서
- When: 어떤 일이 일어나면
- Then: 어떤 결과가 나온다

```ts
it('프리스핀 중 최종 win 이벤트가 오면 현재 구현상 outro 후 basegame 상태로 돌아간다', async () => {
	// Given
	stateGame.gameType = 'freegame';

	// When
	await handleFinalWin(finalWinEvent);

	// Then
	expect(stateGame.gameType).toBe('basegame');
});
```

게임 이벤트, UI 흐름, 비동기 sequence 테스트는 Given-When-Then이 읽기 쉽다.

## Characterization Test

기존 코드가 복잡하거나 의도가 불분명하면 Characterization Test를 작성한다.

Characterization Test는 코드가 의도한 동작보다 현재 실제 동작을 설명한다.

리팩토링 전에는 이 방식이 가장 중요하다.

테스트 이름에는 현재 동작임을 드러낸다.

```ts
it('freeSpinRetrigger는 positions가 비어도 현재 구현상 추가 intro와 counter update를 수행한다', async () => {
	// 현재 동작을 보존하기 위한 테스트다. 버그 여부는 별도 작업에서 판단한다.
});
```

아래처럼 개선된 기대 동작을 테스트 이름에 쓰지 않는다.

```ts
// 금지: 리팩토링 전 테스트가 아니라 버그 수정 테스트에 가깝다.
it('positions가 비어 있으면 freeSpinRetrigger를 실행하지 않는다', async () => {});
```

## Golden Master

입력과 출력이 크거나 이벤트 순서가 복잡하면 Golden Master 방식을 사용할 수 있다.

Golden Master는 현재 출력, 호출 순서, 이벤트 목록을 snapshot처럼 저장해두고 리팩토링 후 같은지 비교하는 방식이다.

아래 항목은 Golden Master 후보가 된다.

- book event 처리 후 emitter 호출 순서
- sound 호출 순서
- animation 호출 순서
- store/state 변경 순서
- 큰 payload 변환 결과
- board symbol 배열 변환 결과

Golden Master는 동작 보존에는 강하지만, 기대값이 커지면 읽기 어려워진다.

따라서 작은 단위 테스트로 표현할 수 있으면 작은 테스트를 우선한다.

## 테스트 이름

테스트 이름은 동작을 문장으로 설명한다.

리팩토링 전 테스트에서는 아래 표현을 허용한다.

- `현재 구현상`
- `기존 동작대로`
- `현재는`
- `버그 여부와 별개로`

```ts
it('현재 구현상 land 상태의 라이트볼은 spine slot 배수 텍스트를 표시하지 않는다', () => {});
```

테스트 이름에 구현 세부만 쓰지 않는다.

```ts
// 금지
it('case 1', () => {});

// 금지
it('should return false', () => {});
```

## 무엇을 테스트할지

리팩토링 전에는 public behavior를 우선 테스트한다.

아래 항목을 우선순위로 둔다.

1. 이벤트 순서
2. visible UI 결과
3. sound, animation 호출 순서
4. store/state 변경 결과
5. payload 변환 결과
6. 순수 함수의 반환값

private 구현 세부는 가능하면 테스트하지 않는다.

단, 큰 함수를 분리하기 전에 현재 동작을 고정해야 한다면 내부 helper를 임시로 테스트할 수 있다.

## 하나의 테스트 범위

하나의 테스트는 하나의 동작을 검증한다.

여러 상태를 한 테스트에 넣으면 실패 원인을 찾기 어렵다.

단, 같은 규칙의 여러 입력값을 검증하는 table test는 허용한다.

```ts
it.each([
	['static', 'idle', true],
	['win', 'default', true],
	['land', 'paybox', false],
] as const)('현재 구현상 %s/%s 배수 텍스트 표시 여부는 %s다', (state, animationName, expected) => {
	expect(
		shouldShowBallDetachedMultiplierText({
			rawSymbol: { name: 12, ball: true, multiplier: 5 },
			state,
			animationName,
		}),
	).toBe(expected);
});
```

table test는 입력과 기대값의 의미가 명확할 때만 사용한다.

## Mock 기준

Mock은 외부 의존성을 끊기 위해 사용한다.

아래 대상은 mock 또는 spy로 검증할 수 있다.

- event emitter
- sound player
- animation player
- timer
- API
- storage

Mock이 테스트 대상의 동작을 대신 만들면 안 된다.

Mock은 호출 여부, 호출 순서, payload를 확인하는 용도로만 사용한다.

## 비동기 테스트

비동기 테스트는 기존 `await` 경계를 보존하는지 확인해야 한다.

아래 항목을 검증한다.

- 먼저 실행되는 호출
- await 이후 실행되는 호출
- callback이 호출된 뒤 실행되는 처리
- timer가 끝난 뒤 실행되는 처리

비동기 흐름을 테스트할 때는 실제 시간 대기보다 fake timer를 우선 고려한다.

단, animation complete callback이나 Spine event callback은 실제 구현의 호출 순서를 최대한 비슷하게 재현한다.

## UI 컴포넌트 테스트

컴포넌트 리팩토링 전에는 렌더 결과와 이벤트 연결을 테스트한다.

아래 항목을 우선 검증한다.

- 조건부 렌더링
- 표시되는 텍스트
- props로 전달되는 값
- click, press, pointer event 연결
- mount 시 실행되는 side effect
- animation trigger 조건

스타일 전체를 테스트하지 않는다.

단, 게임 UI에서 위치, scale, alpha, slot name이 동작에 영향을 주면 테스트할 수 있다.

## 테스트에서 피할 것

아래 방식은 피한다.

- 테스트 하나에 여러 기능을 섞기
- 현재 동작이 아니라 원하는 동작을 기대값으로 쓰기
- 구현 내부 순서를 과하게 테스트하기
- 의미 없는 snapshot 남발
- 테스트를 통과시키기 위해 production 코드를 수정하기
- 리팩토링과 테스트 기대값 변경을 같은 작업에 섞기

테스트 기대값을 바꿔야 한다면 리팩토링이 아니라 동작 변경 작업인지 먼저 확인한다.

## 리팩토링 전 완료 조건

리팩토링을 시작하기 전에 아래 조건을 만족해야 한다.

- 변경 대상의 현재 동작을 설명하는 테스트가 있다.
- 테스트가 현재 코드에서 통과한다.
- 이벤트, 사운드, 애니메이션 순서가 중요한 경우 순서 검증이 있다.
- 기존 버그로 의심되는 동작은 테스트 이름이나 주석에 현재 동작임을 표시했다.
- 테스트와 리팩토링 변경을 가능하면 별도 작업으로 나눴다.

