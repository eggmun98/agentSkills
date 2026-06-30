# Test Code Guide

Version: v1

Last updated: 2026-06-25

이 문서는 평상시에 기능을 개발하거나 버그를 수정할 때 작성하는 테스트 코드 기준을 정리한다.

목표는 테스트를 통해 코드가 요구사항대로 동작한다는 신뢰를 만들고, 이후 변경에서 회귀를 빠르게 발견하는 것이다.

## 기본 원칙

테스트는 구현 세부가 아니라 동작을 검증한다.

테스트는 읽는 사람이 요구사항을 이해할 수 있게 작성한다.

테스트가 실패하면 무엇이 깨졌는지 빠르게 알 수 있어야 한다.

테스트는 불안정하면 안 된다.

테스트 코드는 production 코드만큼 읽기 쉬워야 한다.

## 리팩토링 테스트와의 차이

평상시 테스트는 올바른 요구사항을 검증한다.

리팩토링 전 테스트는 현재 동작을 고정한다.

버그 수정 테스트는 기존 버그를 재현한 뒤, 수정 후 올바른 동작을 검증한다.

```ts
// 평상시 기능 테스트
it('multiplier가 0이면 배수 텍스트를 표시하지 않는다', () => {});

// 리팩토링 전 동작 고정 테스트
it('현재 구현상 multiplier가 0이면 배수 텍스트를 표시하지 않는다', () => {});

// 버그 수정 테스트
it('positions가 비어 있으면 freeSpinRetrigger intro를 실행하지 않는다', () => {});
```

## 테스트 작성 순서

기능 개발이나 버그 수정 시 테스트는 아래 순서로 작성한다.

1. 요구사항 또는 버그 조건을 한 문장으로 정리한다.
2. 가장 중요한 정상 케이스를 테스트한다.
3. 실패, 빈 값, 경계값을 테스트한다.
4. side effect가 있다면 호출 여부와 순서를 테스트한다.
5. 불필요한 구현 세부 검증을 제거한다.

테스트를 먼저 작성할 수 있으면 먼저 작성한다.

이미 구현한 뒤라도 테스트는 요구사항 기준으로 다시 정리한다.

## AAA 패턴

기본 테스트 구조는 AAA 패턴을 사용한다.

- Arrange: 입력과 상태를 준비한다.
- Act: 테스트 대상 동작을 실행한다.
- Assert: 결과를 검증한다.

```ts
it('multiplier가 양수이면 배수 텍스트를 표시한다', () => {
	// Arrange
	const rawSymbol = { name: 12, ball: true, multiplier: 5 };

	// Act
	const result = shouldShowBallDetachedMultiplierText({
		rawSymbol,
		state: 'static',
		animationName: 'idle',
	});

	// Assert
	expect(result).toBe(true);
});
```

테스트가 짧고 명확하면 AAA 주석은 생략할 수 있다.

## Given When Then

시나리오나 사용자 흐름 테스트는 Given-When-Then 구조를 사용한다.

- Given: 어떤 상태에서
- When: 어떤 행동을 하면
- Then: 어떤 결과가 나와야 한다

```ts
it('사용자가 사운드를 끄면 BGM과 효과음이 모두 mute 상태가 된다', async () => {
	// Given
	render(SoundButton);

	// When
	await clickMuteButton();

	// Then
	expect(soundState.isMuted).toBe(true);
});
```

UI, game flow, event flow 테스트는 이 구조가 읽기 쉽다.

## 테스트 이름

테스트 이름은 기대 동작을 문장으로 작성한다.

아래 형태를 권장한다.

- `A이면 B한다`
- `A일 때 B를 표시한다`
- `A가 없으면 B를 실행하지 않는다`
- `A 이후 B 순서로 실행한다`

```ts
it('hideMultiplierText가 true이면 배수 텍스트를 표시하지 않는다', () => {});
```

아래 이름은 사용하지 않는다.

```ts
// 금지
it('test 1', () => {});

// 금지
it('should work', () => {});

// 금지
it('returns true', () => {});
```

## 무엇을 테스트할지

테스트 우선순위는 아래 순서를 따른다.

1. 사용자에게 보이는 결과
2. 도메인 규칙
3. 이벤트, 사운드, 애니메이션 실행 순서
4. state/store 변경 결과
5. API 요청 payload와 응답 처리
6. 순수 함수의 반환값

private 함수보다 public behavior를 우선 테스트한다.

순수 함수는 입력과 출력이 명확하므로 단위 테스트로 검증한다.

side effect가 있는 코드는 호출 결과와 외부 변화까지 검증한다.

## 테스트 종류

순수 함수 테스트는 입력과 출력만 검증한다.

컴포넌트 테스트는 렌더 결과와 사용자 interaction을 검증한다.

도메인 flow 테스트는 이벤트 순서와 state 변경을 검증한다.

통합 테스트는 여러 모듈이 함께 동작하는 흐름을 검증한다.

E2E 테스트는 실제 사용자 핵심 흐름만 검증한다.

모든 것을 E2E로 테스트하지 않는다.

가능하면 낮은 비용의 테스트에서 많은 규칙을 검증하고, E2E는 핵심 smoke와 주요 사용자 흐름에 집중한다.

## 경계값 테스트

조건문이 있으면 경계값을 테스트한다.

아래 값은 우선 확인한다.

- `0`
- `1`
- `-1`
- 빈 배열
- 빈 문자열
- `null`
- `undefined`
- 최소값
- 최대값
- threshold 바로 아래 값
- threshold와 같은 값
- threshold 바로 위 값

```ts
it.each([
	[0, false],
	[1, true],
] as const)('multiplier가 %s이면 표시 여부는 %s다', (multiplier, expected) => {
	expect(getShouldShowMultiplier(multiplier)).toBe(expected);
});
```

경계값의 의미가 도메인에 있으면 테스트 이름이나 변수명에 의미를 드러낸다.

## Table Test

같은 규칙을 여러 입력으로 검증할 때 table test를 사용한다.

```ts
it.each([
	['static', 'idle', true],
	['win', 'default', true],
	['land', 'paybox', false],
] as const)('%s/%s 상태의 배수 텍스트 표시 여부는 %s다', (state, animationName, expected) => {
	expect(
		shouldShowBallDetachedMultiplierText({
			rawSymbol: { name: 12, ball: true, multiplier: 5 },
			state,
			animationName,
		}),
	).toBe(expected);
});
```

입력 조합이 많아져서 읽기 어려우면 테스트를 분리한다.

## Mock 기준

Mock은 외부 의존성을 끊기 위해 사용한다.

Mock 대상은 아래로 제한한다.

- API
- timer
- sound player
- animation player
- event emitter
- storage
- browser API

비즈니스 로직 자체를 mock하지 않는다.

Mock은 호출 여부, 호출 순서, payload를 검증하는 데 사용한다.

Mock이 많아지면 테스트 대상의 결합도가 높은 신호로 본다.

## 비동기 테스트

비동기 테스트는 반드시 완료 시점을 명확히 한다.

`await` 없이 Promise를 방치하지 않는다.

timer가 있으면 fake timer 사용을 우선 고려한다.

비동기 테스트에서는 아래 항목을 확인한다.

- loading 상태
- 성공 상태
- 실패 상태
- callback 호출 후 상태
- 순차 실행이 필요한 호출 순서

```ts
it('저장에 실패하면 에러 메시지를 표시한다', async () => {
	await submitForm();

	expect(screen.getByText('저장에 실패했습니다')).toBeInTheDocument();
});
```

## UI 테스트

UI 테스트는 사용자가 보는 결과와 행동을 기준으로 작성한다.

가능하면 DOM 구조보다 역할, 텍스트, 상태를 기준으로 검증한다.

아래 항목을 우선 테스트한다.

- 중요한 텍스트가 표시되는가
- 버튼을 누르면 의도한 동작이 발생하는가
- disabled, selected, checked 상태가 맞는가
- 에러 메시지가 표시되는가
- loading 상태가 표시되는가

게임 UI처럼 canvas, Pixi, Spine 기반인 경우에는 DOM 역할보다 props, emitter, animation call, visible flag를 검증할 수 있다.

## Snapshot 기준

Snapshot은 제한적으로 사용한다.

아래 경우에는 사용할 수 있다.

- 큰 payload 변환 결과
- 안정적인 설정 객체
- 이벤트 순서 목록
- 렌더 트리 전체보다 작은 구조적 결과

아래 경우에는 사용하지 않는다.

- 자주 바뀌는 UI 전체 snapshot
- 의미를 읽기 어려운 거대한 snapshot
- 실패해도 무엇이 문제인지 알기 어려운 snapshot

Snapshot을 업데이트할 때는 기대 동작이 바뀐 이유를 먼저 확인한다.

## 테스트 데이터

테스트 데이터는 테스트 안에서 의미가 보여야 한다.

공통 fixture는 여러 테스트가 같은 도메인 데이터를 공유할 때만 만든다.

fixture가 너무 크면 테스트의 의도가 흐려진다.

기본 객체가 필요하면 factory 함수를 사용할 수 있다.

```ts
function createBallSymbol(overrides: Partial<RawSymbol> = {}): RawSymbol {
	return {
		name: 12,
		ball: true,
		multiplier: 5,
		...overrides,
	};
}
```

factory 기본값은 가장 평범한 정상 케이스로 둔다.

## 테스트에서 피할 것

아래 방식은 피한다.

- 테스트 이름이 모호한 경우
- 한 테스트가 여러 기능을 검증하는 경우
- 구현 내부 변수나 함수 호출에 과하게 의존하는 경우
- 모든 의존성을 mock해서 실제 동작을 검증하지 못하는 경우
- 무의미하게 큰 snapshot
- 테스트 간 state가 공유되는 경우
- 랜덤 값이나 실제 시간에 의존하는 경우
- 실패 원인을 알 수 없는 assertion

테스트는 독립적으로 실행되어야 한다.

테스트 실행 순서에 의존하지 않는다.

## 버그 수정 테스트

버그를 수정할 때는 먼저 버그를 재현하는 테스트를 작성한다.

테스트가 실패하는 것을 확인한 뒤 코드를 수정한다.

수정 후에는 같은 테스트가 통과해야 한다.

버그 수정 테스트 이름은 기대 동작을 기준으로 작성한다.

```ts
it('scatter가 같은 reel에 여러 개 있어도 hit 사운드는 한 번만 재생한다', () => {});
```

버그 원인을 테스트 이름에 과하게 쓰지 않는다.

## 완료 조건

테스트 작성이 끝났다면 아래 항목을 확인한다.

- 테스트 이름만 읽어도 요구사항을 알 수 있다.
- 실패 시 원인을 좁힐 수 있다.
- 정상 케이스와 주요 경계값이 포함되어 있다.
- mock이 필요한 곳에만 사용되었다.
- 테스트 간 state 공유가 없다.
- production 코드 변경 없이 테스트만 단독으로 이해할 수 있다.

