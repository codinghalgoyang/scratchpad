---
layout: single
title: "[web/typescript] 비동기함수와 Promise"
categories: web/typescript
---

## Promise와 비동기 함수

김민태의 프론트엔드 아카데미 - Javascript & Typescript Essential 내용 + 개인 학습 내용 정리

#### 주요 내용

> - 비동기 코드는 앞의 코드가 완료되지 않고도 다음 코드를 실행할 수 있는 코드임
> - Promise는 비동기코드가 많아졌을 때 복잡성을 해결하기 위해 나온 객체이고, 자세한 내용은 아래 참고 필요!

#### 동기 코드 vs 비동기 코드

- 동기 코드 : 앞의 코드가 완료되기 전에 다음 코드를 실행할 수 없는 코드. (코드가 순차적으로 실행됨)
- 비동기 코드 : 앞의 코드가 완료되지 않아도 그 다음 코드를 실행할 수 있는 코드. (코드가 동시에 실행됨)ß

```ts
// 동기 코드
const square(x) {
	return x * x;
}
const result = square(10);
console.log(result);

// 비동기 코드
const delaySquare(x, cb) {
	setTimeout(()=>{
		cb(x*x);
	}, 1000);
}
dealySquare(10, (result)=>{ console.log(result); });
```

#### Promise를 사용하여 delaySquare 구현

```ts
function dealySquare(x) {
  const p = new Promise((resolve, reject) => {
    setTimeout(() => {
      const result = x * x;
      resolve(result);
      // reject(new Error("실패함"));
    }, 2000);
  });
  return p;
}

delaySquare(10)
  .then((result) => {
    console.log(result); // 10 * 10 출력
  })
  .catch((error) => {
    console.log(error); // error 출력
  });
```

- 비동기 코드는 callback 함수를 넘겨주고, 작업이 완료되면 callback 함수를 호출해주는 형태로 동작하는데, 비동기 코드 여러 개를 순차적으로 실행한다거나, 여러 개가 엃혀있다면 코드가 오히 복잡해지게됨 그래서 이걸 좀 더 명확하고 쉽게 다루기위해 나온게 Promise 객체임.
- 주로 Promise 객체를 함수의 return으로 하는 패턴이 사용됨.
- 예. Promise 객체를 return 하는 delaySquare(x) 함수 구현 및 사용
- Promise 객체의 동작
  - Promise 객체는 생성시 함수를 하나 전달 받음.
  - 이 함수 안에서 비동기 작업을 진행함.
  - 이 함수는 resolve와 reject 라는 함수 인자를 가짐.
  - 이 함수가 실행하려는 비동기 작업이 성공적으로 마쳐지면 resolve() 함수를 호출해주고, resolve 함수 안에 결과도 인자로 넘겨줄 수 있음. 예. `resolve(result)`
  - 반대로 이 함수가 실행하려던 비동기 작업이 실패하면 reject() 함수를 호출하고, 실패 내용에 대한 부분도 인자로 넘겨줄 수 있음. 예. `reject(new Error("실패함"))`
  - Promise 객체는 then() 과 catch() 메소드를 가짐.
  - then()과 catch() 메소드는 callback 함수를 인자로 받음.
  - Promise 객체에서 resolve() 함수를 호출하면 then() 메서드를 통해 등록된 callback 함수를 호출하게 됨. 다르게 생각해보면, resolve() 함수에 넘기는 인자와 then()에 등록할 callback 함수의 인자가 일치되어야함. (실제 동작은 resolve()함수 안에서 callback 함수를 직접호출하는 구조는 아님. -> 아래 동작 시점 관련 내용 정리 참고)
  - Promise 객체에서 reject() 함수를를호출하면 catch() 메서드를 통해 등록된 callback 함수를 호출하게 됨. reject() 함수는 인자를 여러개 받을 수 있지만, 관례적으로 Error 객체 하나만 사용하는 것이 관례이며 권장됨. 예. `reject(new Error("실패함"))`

#### Promise 함수 실행 시점 및 callback 함수 등록 시점

- Promise의 resolve()함수에서 직접 then() 메서드를 통해 등록된 콜백함수를 직접 호출한다고 생각했었음.
- 그럼, 순서가 then()을 통해 콜백함수를 먼저 등록하고 나서, Promise 생성시 등록된 함수가 호출되어야 하는데, 함수 실행 시점이 좀 애매해짐.
- 정답은
  - Promise에 전달되는 함수 실행 시점은 Promise 객체가 생성되는 즉시임.
  - resolve(), reject()는 결과에 대한 내용을 큐에 넣어놓음
  - then(), catch()를 통해 콜백함수가 등록되면 큐의 내용을 처리함
  - Promise 객체 내에 실제 큐가 있는건 아니고, 자바스크립트 자체에서 관리하는 매크로태스크 큐를 사용한다고 함.

#### Simple Promise 객체

실제로 본건 아니고, 이런식으로 구성이 되어있을 꺼임이라고 내부구조를 예상해봄

```ts
// 실제 Promise는 이것보다 더 복잡하지만, 간단하게 나타냄.
class Promise {
  // execFunc : Promise 객체가 실제 실행할 함수
  // execFunc의 형태 : (resolve, reject) => {}
  constructor(execFunc) {
    // this.state : execFunc의 실행상태(실행전,성공,실패)를 나타내는 필드
    this.state = "pending";
    // this.result : execFunc 성공시 결과를 저장하기 위한 필드
    this.result = undefined;
    // this.error : execFunc 실패시 Error를 저장하기 위한 필드
    this.error = undefined;
    // funcOnSuccess, funcOnError
    // execFunc 실행후, 사용자가 resolve 또는 reject 함수를 호출해주게 됨.
    // this.funcOnSuccess : resolve 함수가 호출된 경우, this.result와 함께 호출할 함수
    // this.funcOnError : reject 함수가 호출된 경우, this.error와 함께 호출할 함수
    // this.state가 'success' 또는 'error'로 바뀌게 되고
    this.funcOnSuccess = undefined;
    this.funcOnError = undefined;

    // execFunc 실행 후, 실행 결과에 따라 funcOnSucces() 또는 funcOnError()를 실행
    // execFunc는 Promise 객체가 생성되는 순간, 실행됨.
    // execFunc 실행이 끝났음에도 아직 then(), catch()를 사용한 funcOnSuccess, funcOnError가 등록되어있지 않을 수 있음.
    // JSTaskQueue(뭉뜽그린 JS의 TaskQueue를 나타냄)를 사용하여 funcOnSuccess, funcOnError이 등록이 되었는지 확인하고, 그때 완료처리를 하도록 함.
    // this.task : funcOnSucess, funcOnError가 등록되고 state가 변경될때까지 JSTaskQueue에서 호출해줄 callback 함수
    this.task = () => {
      if (this.state == "success" && this.funcOnSuccess) {
        this.funcOnSuccess(this.value);
      } else if (this.state == "fail" && this.funcOnError) {
        this.funcOnError(this.error);
      } else {
        queueJSTaskQueue(this.task);
      }
    };

    // resolve : execFunc가 성공했을 때, execFunc 안에서 호출해줄 함수
    // resolve 함수의 인자는 execFunc 내용에 따라 달라질 수 있음.
    // 작업이 성공했을 때 호출해주는 함수이므로 this.state를 성공으로 변경함
    const resolve = (result) => {
      this.state = "success";
      this.value = result;
      queueJSTaskQueue(this.task);
    };
    // reject : execFunc가 실패했을 때, execFunc 안에서 호출해줄 함수
    // reject 함수의 인자는 관례적으로 Error 타입 하나임.
    const reject = (error) => {
      this.state = "error";
      this.error = error;
      queueJSTaskQueue(this.task);
    };
  }

  // resolve가 호출 되었을 때, 실행시켜줄 콜백 함수(funcOnSuccess)를 등록하는 메서드
  then(funcOnSuccess) {
    this.funcOnSuccess = funcOnSuccess;
  }

  // reject가 호출 되었을 때, 실행시켜줄 콜백 함수(funcOnError)를 등록하는 메서드
  catch(funcOnError) {
    this.funcOnError = funcOnError;
  }

  // then과 catch 메서드는 체이닝을 위해 return new Promise()를 하는데 요건 생략함.
}
```

- Promise 객체는 3명의 개발자 입장에서 바라본다면?
  - Promise class를 설계한 개발자
    - 현재 작업 상태(pendiing, success, error)를 관리
    - resolve 함수 호출 -> then으로 등록된 콜백함수 funcOnSuccess를 연결
    - reject 함수 호출-> catch로 등록된 콜백한수 funcOnError로 연결
  - Promise 인스턴스를 만들어서 특정 기능을 제공하는 개발자
    - execFunc 함수 `(resolve, reject) => {}`를 정의함.
  - 위에서 만들어진 인스턴스를 사용하는 개발자
    - then()와 catch()를 통해 함수 성공여부에 따라 불릴 콜백함수를 등록함
