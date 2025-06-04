---
layout: single
title: "[web/typescript] 속성과 메서드"
categories: web/typescript
---

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 정리

#### 주요 내용

> - 객체의 메소드를 정의시, function 키워드를 생략한 형태로 많이 쓰임
> - class 내부의 값을 보호하면서 setting할 때는 setter를 사용함
> - class 내부의 값을 변형하면서 보여줄 때는 getter를 사용함
> - class 의 속성과 메소드에 대한 추가 설정(readonly, delete 불가)은 객체 리터럴로 할 수 있음.
> - typescript에선 타입 정의만으로 추가 설정(readonly, delete 불가) 기능을 구현할 수 있음.

#### 기본 구조

- 속성 : 객체 안에 들어있는 순수한 데이터
- 메소드 : 객체 안에 들어있는 함수
- 메소드 선언시 `function` 키워드를 축약하는 형태로 많이 쓰임.

```ts
class Person {
  // bloodType 함수와 같은 이름을 사용할 수 없어 `_`를 붙임
  bloodType: string;

  //	constructor: function(bloodType: string) {
  //		this.bloodType = bloodType;
  //	}

  // function 키워드를 생략한 형태
  constructor(bloodType: string) {
    this.bloodType = bloodType;
  }
}

const p1 = new Person("B");
p1.bloodType = "A";
console.log(`${p1.bloodType} 형`); // B 형
```

#### getter/setter

- class 내부의 어떤 객체의 값을 보호하면서, 외부에는 사용하는 편의성을 그대로 유지시켜줄 수 있는 방법

```ts
class Person {
  // bloodType 함수와 같은 이름을 사용할 수 없어 `_`를 붙임
  _bloodType: string;

  constructor(bloodType: string) {
    this._bloodType = bloodType;
  }

  // bloodType은 함수라서 사용시 p1.bloodType() 형태로 써야함.
  // set 키워드를 사용하면 p1.bloodType = 'B' 형태로 사용 가능
  set bloodType(bloodType: string) {
    if (bloodType === "A" || bloodType === "B") {
      this._bloodType = bloodType;
    }
  }

  get bloodType() {
    return `${this._bloodType} 형`;
  }
}

const p1 = new Person("B");
p1.bloodType = "C"; // 'C'형은 못넣게 불가능하게 만들고 싶음
console.log(p1.bloodType); // B 형
```

#### 객체 리터럴

- 객체를 생성하는 방법 중 하나이고, 속성과 메소드에 대한 추가 설정등을 할 수 있음.
  - readonly 속성을 주고 싶은 경우, writable을 false로 만듦
  - 속성 delete를 못하게 하고 싶은 경우, configurable을 false로 만듦
- ts는 타입정의시, readonly를 주거나, ?를 안해주는걸로도 같은 처리가 가능함.

```js
// 부모객체 : null > 추후 프로토타입때 좀 더 자세히
const myObj = Object.create(
	null,
	name : {
		value: 'gu',
		writable: false, // read only
		configurable: false, // delete 못하게 만들기
	}
);
```
