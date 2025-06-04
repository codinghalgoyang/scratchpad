---
layout: single
title: "[web/typescript] 식별자"
categories: web/typescript
---

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 요약

## 문법 - 식별자

> [MDN web docs - 식별자]
> (https://developer.mozilla.org/ko/docs/Glossary/Identifier)

- 코드에서 변수, 함수, 객체내 속성을 식별하기 위한 문자열
- 쉽게 말하면, **변수명, 함수명, 객체내 속성명임**
- 대소문자 구별함, <U>유니코드</U> 글자임, `$`, `_`, 숫자(0-9)로 구성
- 숫자로 시작하거나 공백을 포함할 수 없음

```ts
let age = 10;
function setAge() {}
const o = {
  age: 10,
};
// 여기에서 age, setAge, o, o안에 age가 식별자임
```

- 식별자와 문자열을 다름
- javascript에서 식별자를 문자열로 바꿀 수 있는 방법은 없음
- 어떤 경우, 문자열을 분석해 식별자로 사용할 수 있음

```ts
// 위에서 말하는 어떤 경우는 computed property임.
// computed property란 객체의 속성명을 []을 감싸고 그 안에 문자열을 넣는 문법임
// computed property는 문자열을 사용하기 때문에, 중간에 띄어쓰기도 가능함
const o = {
  age: 10,
  ["name"]: "kim",
  ["how tall"]: 170,
};

// 띄어쓰기가 있는 경우, 접근시 .을 사용하지 못하고 ['문자열'] 형태로 사용해야함.
console.log(o.name);
console.log(o["how tall"]);
```
