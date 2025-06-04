## [Next.js] Route Handlers

#### 라우트 핸들러란?

- next.js 서버사이드에서 실행되며, 사용자 정의 request 처리 (http 요청 : 특정 url + method 가 들어왔을 때, 어떻게 처리할 것인가)를 정의하는 부분
- API 엔드 포인트와 유사한 역할 : 클라이언트가 특정 url로 요청을 보내면, 라우트 핸들러가 그 요청을 처리한 뒤, 일반적으론느 json형태의 데이터를 응답으로 돌려줌.
- next.js의 app 폴더 내에 `route.ts` 파일을 정의함으로서 사용할 수 있고, `page.ts` 파일과는 공종할 수 없음
- `page.ts`와 달리 UI 렌더링이 없음
- HTTP 메서드 (GET, POST, PUT, PATCH, DELTETE, HEAD, OPTIONS)을 지원

#### 사용처

- API 생성 : 프론트 엔드에서 데이터를 요청하거나, 백엔드에 데이터를 저장해야할 때 사용
- 데이터베이스 연동 : 서버에서 데이터 베이스에 접근하여 데이터를 조회, 생성, 수정, 삭제 하는 작업
- 서버 사이드 작업 : 서버에서만 수행되어야하는 비동기 작업 처리
- 
-

https://nextjs.org/docs/app/building-your-application/routing/route-handlers

#### Route Handler 정의

app/api/route.ts

```ts
export async function GET(request: Request) {}
```

- Router Handler는 `app`폴더 밑에 `route.ts` 파일에서 정의됨
- `page.ts`나 `layout.ts` 처럼 `app` 폴더 밑에 어디에서나 정의 가능함.

> 다만, `page.ts`와 `route.ts`는 공존할 수는 없음 (둘 중 하나만 가능)

#### 지원되는 HTTP Methods

- GET, POST, PUT, PATCH, DELTETE, HEAD, OPTIONS

#### `NextRequest`와 `NextResponse` APIs

- native Request와 Response APIs를 지원하기 위해, `NextRequest`와 `NextResponse`로 이것들을 extends 함(?)

## Behavior
