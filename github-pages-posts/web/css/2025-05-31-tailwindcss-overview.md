---
layout: single
title: "[web/css] tailwindcss Overview"
categories: web/css
---

#### tailwindcss란?

- 자주 쓰는 css를 미리 많이 만들어놓고 class명만 가져다 쓰는 방법

#### tailwindcss 단점

- 무거움
  - 빌드시 최적화됨
- 더러움
- 숙련도 필요
- 스타일 재사용이 어려움
  - react 써서 컴포넌트로 빼면 됨

#### tailwindcss 단위

- rem 단위 기준으로 class명이 작동함
- 1rem === 16px(브라우저에 따라 달라짐)

#### tailwindcss box-sizing

- Tailwind CSS는 기본적으로 모든 요소에 box-sizing: border-box;를 적용함
- 간단히 말해, Tailwind CSS를 사용하면 width나 height를 지정할 때 padding과 border가 해당 너비/높이에 포함되어 계산됨

#### tailwindcss 핵심 요소

```
// bg
bg-black
bg-pink-400

// text
text-pink
text-2xl // 크기
text-[60px] // 크기
font-bold
font-[100]

// border
border-8
border-black
rounded-full

// w, h, p, m : width / height / padding / margin
w-64
h-64
p-64
px-64 // x 축에만 적용
py-64 // y 축에만 적용
m-64
mx-64 // x 축에만 적용
my-64 // y 축에만 적용

// flex
flex
flex-col // flex-row가 default 방향임
justify-center // 주축 정렬
item-center // cross축 정렬
gap-64

// hover, transition
- hover:bg-purple
- transition
```
