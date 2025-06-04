---
layout: single
title: "[web/typescript] 복사와 참조"
categories: web/typescript
---

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 요약

#### 주요 내용

> - 할당시, 기본 자료형은 **복사**가 일어나고, 객체는 **참조**가 일어남
> - (내가 헷갈린 것) python과 달리 javascript에서는 기본 자료형은 진짜 복사가 일어남.
> - 객체는 참조가 일어나기 때문에, 객체의 내용을 바꾸면 본래의 객체의 내용도 변경이 일어남.

## 참조와 복사

```ts
// 기본 자료형 할당
let a = 10;
let b = a; // 이떄, 새로운 10이 복사되서 b에 들어감.
b = 20;
console.log(a); // 20

// 객체 할당
let o = {
  isLoading: false,
};
let o2 = o; // 이때, o2는 o와 같은 객체를 가리키게 됨.

o2.isLoading = true;
console.log(o.isLoading); // true
```
