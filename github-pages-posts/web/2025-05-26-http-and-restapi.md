---
layout: single
title: "[web] http와 REST API"
categories: web
---

> REST API 의 핵심 : http가 원래 가지고 있는 의미를 그대로 사용하자! (http를 http 답게 사용하자!)

#### http 핵심 요소

- 클라이언트-서버 구조 : 클라이언트가 요청하고, 서버가 응답하는 구조
- URI (Uniform Resource Identifier) : 어떤 리소스(데이터)를 말하는지 식별하기 위해 사용
- 메소드 (PUT, GET, PATCH | PUT, DELTE) : 해당 리소스에 어떤 동작을 할 것인가를 나타내기 위해 사용
- 상태코드 : 클라이언트 요청에 대한 처리 결과를 나타내기 위해 사용

#### REST API

- 위 http 핵심 요소를 그 의도에 맞게 잘 사용하자!
- 기술이 아니라 이런식으로 인터페이스를 구성하세요! 하는 모범사례임.

예를 들어, 서버에서 user 정보를 생성하거나, 가져오는 기능을 제공하는 URI와 메소드를 정의한다고 하면, `/get-user` 또는 `/create-user` 형태로 uri가 resource의 구분 뿐만 아니라 동작을 포함하게 되면, http의 의도를 잘못 사용하고 있는 것임.

> REST API에 대한 내용을 몰랐을 떄, 실제로 잘못된 예처럼 구성했었음.

## REST API 핵심

- Resource의 식별은 URI로 한다.
- 해당 Resource에 대한 동작(CRUD)는 http 메소드의 의도에 맞게 사용한다.
  - create 동작 : http의 post 메소드 사용
  - read 동작 : http의 get 메소드 사용
  - update 동작 : http의 put 메소드 또는 patch 메소드 사용
  - delete 동작 : http의 delete 메소드 사용
- 요청에 대한 결과는 알맞은 http의 status code를 담아준다.

#### put 메소드 vs patch 메소드

- put 메소드는 해당 resource를 아예 교체할 때 사용
- patch 메소드는 해당 resource의 일부 데이터만 교체할 때 사용
- 예) 원래 resource : {id: 0, name: "kim", age : 30}
  - patch {age: 35} 하면, resource는 {id: 0, name: "kim", age: 35} 가 됨
  - put {age: 35} 하면, resource는 {id: 0, age: 35} 가 됨 (id를 제외하고 모든 데이터가 {age: 35}로 교체되어버림, name이 없어짐)

#### URI 설계 방법

```json
// resource
{
  "posts": [
    {
      "id": 1,
      "title": "first"
    },
    {
      "id": 2,
      "title": "second"
    }
  ],
  "comments": [
    {
      "postId": 1,
      "value": "good"
    }
  ]
}
```

- Collection URI (여러 데이터의 모음)의 경우, 복수형을 사용한다.
  - 예) `/posts`
- Element URI (하나의 데이터)의 경우, id나 unique한 값을 사용한다.
  - 예) `/posts/1`
- 관계 표현 : resource간 부모/자식 관계가 있다면, 부모가 되는 resource를 앞쪽에 사용함.
  - 예) `/posts/1/comments`
- 동사보다는 명사를, 대문자보다는 소문자를 사용한다.
- 행위를 포함하지 않는다.
- 마지막에 슬래시(/)를 포함하지 않는다.
- 언더바 대신 하이폰을 사용한다.
- 파일확장자는 URI에 포함하지 않는다. (잘못된 예 : `/photo.png` > 좋은 예 : `/photo`)
