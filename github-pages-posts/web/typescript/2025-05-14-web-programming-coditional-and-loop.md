---
layout: single
title: "[web/typescript] 조건문 & 반복문"
categories: web/typescript
---

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 정리

#### 조건문 주요 내용

> - 실행문이 한 줄인 경우, if문의 {}를 생략가능하지만, 추후 변경을 생각해 생략하지 않는걸 추천함.
> - false, 0, '' (빈문자열), null, undefined는 거짓으로 판단함.
> - 모든 객체는 참으로 판단함.

#### 반복문 주요 내용

> - for of는 배열을 순회할 때 편함.
> - for in은 객체의 키를 순회할 때 편함.

## 조건문 & 반복문

```ts
// for, while, do{}while()은 생략함.

const arr = ["a", "b", "c", "d"];

for (const item of arr) {
  console.log(item);
}

const obj = {
  color: "red",
  width: 200,
  height: 200,
};

for (const key in obj) {
  console.log(key); // 'color', 'width', 'height'
}
```
