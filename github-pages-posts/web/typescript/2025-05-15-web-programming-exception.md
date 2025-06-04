---
layout: single
title: "[web/typescript] 예외 (Exception)"
categories: web/typescript
---

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 정리

#### 주요 내용

> - 오류를 반환하는 방법보다 예외 구문을 사용하는게 유리할 때가 많다.
> - throw는 에러를 상위 호출 스택으로 전파한다.
> - try에서 open하거나 할당한 내용들을 정리하는 부분을 finally에 쓰는 패턴이 자주 사용된다.

## 예외 (Exception)

#### 오류를 처리하는 방법 2가지

- 오류를 반환하는 방법 : 매번 오류를 검사해야 되니깐 구조가 복잡해짐
- 예외 구문을 사용하는 방법

#### 예외 구문의 장단점

- 장점
  - throw를 통해 에러가 발생한 지점을 명확하게 표시
  - 구체적인 에러 정보 제공 가능
  - 에러 발생 위치와 상관없이 중앙 집중식 에러 처리 가능
- 단점
  - 예외처리가 이루어지지 않으면 프로그램이 예기치 않게 종료될 수 있음

```ts
function doException() {
  throw new Error("오류발생");
}

function noException() {
  return true;
}

function callException(type) {
  if (type == "do") {
    doException();
  } else {
    noException();
  }
}

function main() {
  try {
    callException("do");
  } catch (e) {
    console.log(e);
  } finally {
    console.log("done");
  }
}

main();
```
