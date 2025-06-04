---
layout: single
title: "[web/typescript] 함수 Overview"
categories: web/typescript
---

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 정리

#### 주요 내용

> interface : 객체를 묘사할 때 사용
> type alias : 그외 사용

## 함수

#### 익명함수

- 함수는 값으로 취급되기 때문에 변수에 넣을 수 있음.

```ts
const func = function () {
  return 100;
}; // 여기서 익명함수는 값(식)이기 때문에 ; 을 붙임
```

#### 즉시 실행 함수

- 함수가 만들어지자 마자 즉시 한 번 실행함
- 이름도 없고, 변수에도 들어가 있지 않기 때문에 단 한 번만 실행됨.

```ts
(function () {...})(); // 익명 함수를 바로 호출함.
```

#### 가변인자 함수

> 자바스크립트 함수의 특징은 파라미터와 인자의 개수가 달라도 호출이 됨.

```js
// js 함수 특징을 사용해 가변인자 함수 구현
// 문제는 함수 안에 내용을 보지 않고서는 가변인자로 넣어줘야하는지 알 수 없음
const sum() {
	let s = 0;
	for (let i = 0; i < arguments.length ; i++) {
		s += i;
	}
	return s;
}

sum(1, 2);
sum(1, 2, 3, 4, 5);
```

#### 전개 파라미터 (rest parameter)

> 가변인자 함수 사용시, 함수의 외형(함수의 시그니처)에 가변인자를 넣어주는지가 보이지 않는 문제를 해결

```js
const sum(...args) {
	let s = 0;
	for (let i = 0; i < args.length ; i++) {
		s += i;
	}
	return s;
}

sum(1, 2);
sum(1, 2, 3, 4, 5);
```

#### 함수를 호출하는 3가지 방법

- ()
- call() : 인자가 들어가기 때문에 데이터가 변경되면 함수를 호출하는 코드도 변경되어야 함.
- apply() : array가 들어가기 때문에 데이터가 변경되면 함수를 호출하는 코드는 변경되지 않음.

```ts
const sum(...args) {
	let s = 0;
	for (let i = 0; i < args.length ; i++) {
		s += i;
	}
	return s;
}

// call, apply의 첫 번째 인자는 context가 들어감. 여기에서는 일단 null을 넣음
// context 관련해서는 추후 학습
// call은 10, 20, 30 과 같이 인자를 넣어줌
// apply는 배열 형태로 인자를 넣어줌
sum(10, 20, 30);
func.call(null, 10, 20, 30);
func.apply(null, [10, 20, 30])
```

#### 화살표 함수 (익명함수 중 하나임)

- function과는 다름. context나 prototype 섹션을 학습하고 나면 좀 더 이해할 수 있음
- 코드 블록이 한 줄이고 그 식이 return 값인 경우, {}를 생략할 수 있음 (return도 생략되는 거임)
- 인자가 하나일 땐, ()도 생략 가능

```ts
const plusTen = (x) => x + 10;
```

#### 생성기 함수 (generator)

- 실행 중간에 함수를 멈췄다가 다시 해당 부분부터 함수를 실행할 수 있는 함수
- function 뒤에 `*`가 붙는 문법을 사용함
- 최초 호출시, 함수가 실행되지 않고, 객체 하나를 반환함.
- 해당 객체의 `next()`를 호출되면 `yield`라는 키워드를 사용해 중간 값을 내보내고 정지함.

```ts
function* gen() {
  yield 10;
  yield 20;
  yield 30;
}

const g = gen();
g.next(); // 10
g.next(); // 20
g.next(); // 30
```

#### 비동기 함수

- Promist의 비동기 코드 구성을 동기적(순차적)으로 코드를 작성할 수 있게 만들어 줌

```ts
async function myTask() {
  await doWork();
  console.log("doWork is finished");
}
```
