---
layout: single
title: "[web/typescript] 값(Value)"
categories: web/typescript
---

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 요약

## 문법 - 값(Value)

- 모든 프로그래밍 언어에는 내장된 기본 데이터 타입이 있고, 보통 그 내용이 언어마다 다름.
- Javascript와 Typescript의 기본 데이터 타입에 관해 살펴봄.

#### Javascript의 기본 데이터 타입

> [MDN web docs - JavaScript의 타입과 자료구조](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Data_structures)

- 기본 자료형(primitive)
  - boolean
  - null
  - undefined
  - number (en-US)
  - string
  - symbol (ECMAScript6에 추가됨)
- 별도로 object도 있음

#### Typescript의 기본 데이터 타입

> [Typescript github.io - 기본 타입](https://typescript-kr.github.io/pages/basic-types.html)

- boolean
- number
- string
- array
- tuple
- enum
- any
- void
- null and undefined
- never
- object

#### tuple : 특수한 배열, 요소의 타입과 갯수가 고정된 형태

```ts
let x: [string, number];
x = ["hello", 3]; // success
x = [3, "hello"]; // fail
```

#### enum

```ts
// javascript
const Color = {
  Red: 1,
  Green: 2,
  Blue: 3,
};

// typescript
enum Color {
  Red,
  Green,
  Blue,
}

Color.Red;
```

- 결국 ts도 js로 변경되면서 const로 바뀌긴 함.
- enum은 기본적으로 0부터 시작함.
- `=`을 사용해 각 값을 따로 지정도 가능.

> 실제 enum을 선호하지는 않음.

- 객체는 데이터가 될 수 있는데, enum은 코드임.
- 서버나 외부로부터 데이터를 가지고 올 때, 변환시키기가 까다로움
- 예) 2 라는 데이터를 받았을 때, enum은 이걸 Color.Green으로 변경해줘야햠.

#### Any

- 모든 데이터 타입을 의미
- 자바스크립트 개발자가 타입스크립트로 전환할 때 소프트랜딩을 위해 제공한 것으로 보임.
- 되도록이면 사용하지 않는 것을 권장함.

#### Void

- 대표적으로 함수가 리턴 값이 없을 때 타입을 명시하기위해 사용

#### Null and Undefined

- null : 아무것도 없다는 의미
- undefined : 아직 정의되어있지 않다는 의미

#### never

- 절대 발생할 수 없는 타입
- return값이 발생하지 않는 경우 : 항상 오류를 발생시키거나, return에 도달할 수 없는 함수의 return 타입

```ts
// 항상 오류를 발생시키는 함수, return 값이 발생하지 않음
function error(message: string): never {
  throw new Error(message);
}

// return 값에 도달할 수 없는 함수
function infiniteLoop(): never {
  while (true) {}
}
```

#### 객체 (Object)

- 기본값 유형을 제외하고, 거의 모든 것이 객체로 만들어져 있음. (함수 포함)
- 다르게 생각해보면, 객체도 값이기 때문에, 자바스크립트 코드의 대부분 값이라고 말할 수도 있음.

#### 값으로 취급되는 무엇이든 변수에 넣을 수 있다

> 값이라고 하는 표현은 데이터보다 더 확장된 개념임.
