---
layout: single
title: "[web/nextjs] 기본 다크 모드 Disable"
categories: web/nextjs
---

Next.js 프로젝트는 기본적으로 다크모드를 사용할 수 있도록 되어있었고, 이걸 disable 하고 싶었음.

> global.css의 @media (prefer-color-scheme: dark) 안에 내용을 주석처리하면 됨.

```css
@media (prefers-color-scheme: dark) {
  /* :root {
    --background: #0a0a0a;
    --foreground: #ededed;
  } */
}
```
