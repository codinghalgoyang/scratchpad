---
layout: single
title: "[web/typescript] Type vs Interface (from Typescript cheatsheet)"
categories: web/typescript
---

#### 결론 : 언제 type을 쓰고, 언제 interface를 쓸까?

- **객체의 구조(객체 안에 뭐가 들어있는지)**를 설명할 때는 interface를 사용하자!
- 그 외 타입의 정의(유니온 타입, 기본 타입등)은 type을 사용하자! (인터페이스로는 정의가 안됨)

#### Interfaces can only describe object shapes

> 결론 : 인터페이스는 **객체**가 가져야하는 속성과 메서드 타입은 명시할 수 있지만, 다른 형태의 타입을 표현할 수 없음. (객체에 대한 것만 정의라는 게 중요한 개념인듯)

인터페이스로 정의할 수 없는 것들

```
// 1. Union Type
type StringOrNumber = string | number;

// 2. Tuple
type StringNumberTuple = [string, number];

// 3. Array Type
type NumberArray = number[];

// 4. Primitive type (기본 타입)
type MyString = string;
type MyBoolean = boolean;

// 5. Advanced Union Type (복합 유니온 타입)
type ComplexType = {id: number} | {name: string};

// 6. Generic Type
type Box<T> = { value: T };
```

#### Interfaces can be extended by declaring it multiple times

> 결론 : 인터페이스는 같은 이름으로 여러번 정의되면서 확장 가능함.

```ts
// interface는 같은 이름으로 중복 선언하여 확장함.
interface User {
  name: string;
}
interface User {
  age: number;
}
// 위 interface User는 아래와 같이 병합됨.
// interface User {
//	name: string;
//	age: number;
// }

// type은 한 번에 써야함
type UserType = {
  name: string;
  age: number;
};
```

###### 한 번에 정의하면 안되나? 왜 여러번을 나눠쓸까?

- 각각 모듈에서 필요한 속성을 추가하고, 특정 기능에 대한 수정이 필요할 때 해당 모듈만 수정
- 새로운 속성이 필요하면 새로운 모듈을 추가하여 인터페이스를 확장할 수 있음

```ts
// 모듈 1: 기본 사용자 정보
interface User {
  id: number;
  name: string;
}

// 모듈 2: 연락처 정보 추가
interface User {
  email: string;
  phoneNumber: string;
}

// 모듈 3: 주소 정보 추가
interface User {
  address: {
    street: string;
    city: string;
    country: string;
  };
}

// 이제 User 인터페이스는 다음과 같이 확장됩니다.
const user: User = {
  id: 1,
  name: "홍길동",
  email: "hong@example.com",
  phoneNumber: "010-1234-5678",
  address: {
    street: "가로수길 123",
    city: "서울",
    country: "대한민국",
  },
};

// 사용자 정보를 출력하는 함수
function printUserInfo(user: User) {
  console.log(`ID: ${user.id}`);
  console.log(`이름: ${user.name}`);
  console.log(`이메일: ${user.email}`);
  console.log(`전화번호: ${user.phoneNumber}`);
  console.log(
    `주소: ${user.address.street}, ${user.address.city}, ${user.address.country}`
  );
}

// 사용자 정보 출력
printUserInfo(user);
```

#### In performance critical types interface comparison checks can be faster.

> 결론 : 성능이 중요한 상황에서는 interface를 사용하는 것이 더 빠를 수 있음. type은 복잡한 타입 조합을 지원하지만, 이로 인한 성능에 영향을 미칠 수 있음.

## 추가 내용

#### type이 있는데, 왜 interface를 만들게 됐을까?

- 선언 병합 : 같은 이름의 인터페이스를 선언해서 자동 병합하는데, 이건 API 통합시 유용하다고 함.
- 명시적인 의도 : 인터페이스는 주로 객체를 설명하기 위해 사용됨. 이 객체는 뭐하는거야 하고 객체를 묘사하기 위한 의도로 만들어졌기 때문에 인터페이스를 사용하면 아 객체 모양이 어떻게 생겼는지 설명하려고 하는구나 알 수 있다.

#### 구조적 타이핑

- 동일한 구조를 가진 객체끼리는 타입을 명시적으로 바꿔주지 않아도 됨.
- 인터페이스와 타입의 차이에 관해 찾아보던 도중 알게된 정보임.
- 인터페이스와 타입에서 모두 지원함.

```ts
interface Person {
  name: string;
  age: number;
}

const user = {
  name: "홍길동",
  age: 30,
};

// user는 명시적 타입은 없지만 구조적으로 {name:string, age: number} 타입임.
// 그래서 구조적인 타입이 같은 Person 타입으로 대입이 가능합니다.
const anotherUser: Person = user;
```

#### 문법의 차이가 존재함

- 정의할 때, interface는 `=`을 사용하지 않고, type은 `=`을 사용함.
- 확장시, interface는 `extends`를, type은 `&`를 사용함.
