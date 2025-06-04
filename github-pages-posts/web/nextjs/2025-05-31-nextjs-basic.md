---
layout: single
title: "[web/nextjs] Next.js 기초 (NextJS 12 vs NextJS 13)"
categories: web/nextjs
---

> Next.js 12와 Next.js 13은 큰 차이를 가지고 있옴. Next.js 12 먼저 설명 후 13에서는 어떤식으로 변경되는지 설명

#### Next JS란?

결론 : react의 SSR (Server side rendering)을 쉽게 구현할 수 있게 도와주는 프레임워크

- React는 SPA(Single Page Application)을 이용해 CSR(Client Side Rendering)을 하기 때문에 좋은 점도 있지만 단점은 SEO(검색엔진최적화화임.
- CSR를 하면 첫페이지에서 빈 html을 가져오기 때문에 포털 검색에 노출될 일이 없음
- Next.js는 pre-rendering을 통해 페이지를 미리 렌더링하여 완성된 HTML을 가져오기 때문에 검색 엔진 크롤러에게 바로 렌더링된 페이지를 전달할 수 있음
- 리액트에서 SSR(Sever Side Rendering)을 지원하면 구현이 복잡해지기 때문에, Next.js를 통해 이 문제를 해결

#### SSR

- 클라이언트 대신 서버에서 페이지를 준비
- 사용자와 검색 엔진 크롤러에게 바로 렌더링된 페이지를 전달할 수 있음

#### NextJS 기본 파일 구조 (NextJS 12)

- pages
  - 이 폴더 안에 페이지들을 생성
  - `index.tsx`가 처음 “/” 페이지 임.
  - `_app.tsx`는 공통 레이아웃. 모든 페이지에 공통으로 들어가는 것을 여기에 작성.
  - about 페이지를 만들고 싶은 경우, `about.tsx`를 생성
- public
  - 이미지 같은 정적(static) 에셋들 저장
- styles
  - css 파일 저장
  - globals.css : 전체
  - 모듈이름.module.css : 모듈에 대한 css, module이 붙어야함.ㄹ
- next.config.js
  - next에 관한 설정 파일
  - nextjs는 웹팩을 기본 번들러로 사용하며, 이것에 관한 설정을 해줌킺
- package.json : 패키지 관리
- tsconfig.json : 타입스크립트 관련 설정

#### Pre-rendering

- NextJS는 모든 페이지를 pre-render함.
- pre-render : Client 사이드에서 js로 처리하기 전 미리 생성

#### pre render 테스트

- js를 disable 시켜서 pre-render된 화면을 확인하자!
- js를 disable 시키는 방법 : developer.chrome.com/docs/devtools/javascript/disable

## Data Fetching

next.js에서 데이터를 가져오는 방법은 여러가지이고, 사용 용도에 따라 다른 방법을 사용하면 됨.

- react에서 데이터를 가져올 땐 useEffect()안에서 가져오는데, next.js에서는 다른 방법을 사용함.

#### 1. getStaticProps

static generation으로 빌드(build)할 때 데이터를 불러옴. (미리 만들어줌)

- getStaticProps()를 async로 export 하면, getStaticProps에서 리턴되는 props를 가지고 빌드타임 때, 페이지를 pre-render함.
- getStaticProps를 사용해야할 때
  - 사용자의 요청보다 먼저 build시간에 필요한 데이터를 가져올 때
  - 데이터는 headless cmd에서 데이터를 가져올 때??
  - 데이터를 공개적으로 캐시할 수 있을 때???
  - 페이지는 미리 렌더링되어야하고(SEO의 경우) 매우 빨라야할 때, (getStaticProps는 성능을 위해 CDN에서 캐시할 수 있는 HTML 및 JSON 파일을 생성???)

```js
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the paget component as props
  };
}
```

```js
// `posts`는 빌드타임 때, getStaticProps()에 의해 생성됨.
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((posts) => (
        <li>{post.title}</li>
      ))}
    </ul>
  );
}

// getStaticProps()는 빌드 타임때 서버 사이드에서 호출됨.
// client 사이드에서 호출되지 않음. 그래서 database 쿼리를 다이렉트로 날려도 됨.
export async function getStaticProps() {
  // get posts를 하기 위한 외부 API를 여기서 호출
  // 아무 데이터 패칭 라이브러리 사용 가능함.
  const res = await fetch("https://.../posts/");
  const posts = await res.json();

  // { props: { posts } }를 리턴해서, Blog component는 posts를 props로 받을 수 있게됨.
  return {
    props: {
      posts,
    },
  };
}

export default Blog;
```

#### 2. getStaticPaths : 동적 라우팅

static generation으로 데이터에 기반하여 pre-render시 특정한 “동적 라우팅 구현(pages/post/[id].js)”

- 동적 라우팅이 필요할 때 getStaticPath로 경로 리스트를 정의하고, HTML에 build 시간에 렌더링됨.
- nextjs는 pre-render에서 정적으로 getStaticPaths에서 호출하는 경로들을 가져옴.

```js
export async function getStaticPaths() {
	return {
		paths: [
			{ params: { ... }}
		],
		fallback: true // false or 'blocking'
	};
}
```

#### paths

- 어떠한 경로가 pre-render 될지를 결정
- 만약, pages/posts/[id].js 라는 이름의 동적 라우팅을 사용하는 페이지가 있다면 아래와 같이 정의

```js
return {
	paths: [
		{ params: { id: '1' } },
		{ params: { id: '2' } }
	],
	fallback: ...
}
```

- 빌드하는 동안 /posts/1과 /posts/2를 생성하게 됨.

#### params

- 페이지의 이름이 pages/posts/[postID]/[commentId] 라면, params는 postId와 commmentId임.
- 만약 페이지 이름이 pages/[...slug]와 같이 모든 경로를 사용한다면, params는 slug가 담긴 배열이어야함. ['postId', 'commentId'] ????

#### fallback

- false라면, getStaticPaths로 리턴되지 않는 것(params에 없는 페이지 접근시), 404 페이지로 띄워줌.
- true라면, getStaticPaths로 리턴되지 않는 것(params에 없는 페이지 접근시, fallback 페이지를 띄워줌.

```js
// 페이지가 생성되지 않았다면 보여지는 페이지
// getStaticProps()가 끝날 때 까지 보여짐???
if (router.isFallback) {
  return <div>Loading...</div>;
}
```

```js
// pages/posts/[id].js

function Post({ post }) {
  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  // posts를 얻기 위한 외부 API를 호출
  const res = await fetch("https://.../posts");
  const posts = await res.json();

  // 얻어진 posts 기반으로 pre-render할 paths 정의
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }));

  // 이 paths로 된 것들만 pre-render됨.
  // { fallback: false }는 paths 정의된 것 외 다른 route는 404 페이지를 띄우겠다임.
  return { paths, fallback: false };
}

// 이 부분도 빌드 타임때 호출됨
// { params }는 getStaticPaths() 코드 내 paths 안에 들어가 있는 것을 받은 것임.
// 그럼 여기 getStaticProps는 paths의 각 path마다 한 번씩 불리는 것 같음.
export async function getStaticProps({ params }) {
  // 그러
  const res = await fetch(`https://.../posts/${params.id}`);
  const posts = await res.json();

  // { props: { posts } }를 리턴해서, Blog component는 posts를 props로 받을 수 있게됨.
  return {
    props: {
      posts,
    },
  };
}

export default Post;
```

#### 3. getServerSideProps

server side rendering으로 요청이 있을 때 데이터를 불러옴

- getServerSiceProps 함수를 async로 export 하면, Next는 각 요청마다 리턴되는 데이터를 getServerSideProps로 pre-render 함.

```js
export async function getServerSideProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  };
}
```

```js
function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch("https://.../data");
  const data = await res.json();

  // Pass data to the page via props
  return { props: { data } };
}

export default Page;
```

- getServerSideProps를 사용해야 할 때
  - 요청할 때 데이터를 가져와야하는 페이지를 미리 렌더해야할 때 사용?
  - 서버가 모든 요청에 대한 결과를 계산하고, 추가 구성없이 CDN에 의해 결과를 캐시할 수 없기 때문에 첫번때 바이트까지의 시간은 getStaticProps보다 느림?
- 리퀘스트를 보낼 때마다 새롭게 데이터를 가져올 때 사용한다???

## 메인 페이지 UI 만들기 (마크다운 파일 생성)

...

## NextJS App Router (nextjs 13)

- routing 방식
  - nextjs 12에서는 pages라는 폴더 안에 페이지를 넣고 파일시스템라우팅을 함.
  - nextjs 13에서는 app 폴더 안에 서 파일시스템 라우팅을 함.
    - home/ 디렉토리 생성, page.js => abc.com/home
    - [slug]/ 디렉토리 생성, page.js => abc.com/{slug}
    - (group)/ 디렉토리 생성 => abc.com/ just for grouping (경로에 포함되지 않음? 그냥 폴더 그루핑만 하는 애이고 경로에는 실제 포함되지 않음.
      - app
        - layout.js : 기본 layout을 위한 파일로 예약된 이름
        - (marketing)
          - about/page.js => abc.com/abouut : page.js는 기본 파일로 예약된 이름
          - blog/page.js => abc.com/blog (abc.com/marketing/blog가 아님)
- 예약된 파일 이름들
  - page.js(page.tsx) : 12의 index.js 와 같은 거임. 해당 폴더의 기본 페이지
  - layout.js(layout.tsx) : app폴도 바로 아래 생성도 되고, 특정 페이지 폴더 안에도 생성됨. 기본 레이아웃
  - loading.tsx : 로딩 부분 처리
  - error.tsx : throw Error에서 가장 가까이 있는 error.tsx가 활성화됨. (해당 컴포넌트 부분만 에러로 표시)
- nextjs 13에서 아무 표시도 없으면 서버컴포넌트가 됨. `use client`라는 지시자를 넣어주어야 client 컴포넌트가 됨.
- 서버컴포넌트란? react 18 버전부터 생긴 개념이고, 이전까지는 다 client 컴포넌트였다고 함.
  - nextjs 13에서는 서버컴포넌트를 기본으로 함.
  - 서버컴포넌트 vs 클라이언트컴포넌트 비교 표를 봐볼것!
- fetch시 옵션에 cache: ‘no-store'를 주면, cache된 내용을 사용하지 않고 cache 여부에 상관없이 모두 새로 fetch를 다시 해오는데, 이게 getSErverSideProps와 유사하다고 함?
- generateStaticParams는 getStaticPath와 유사? revalidate 간격으로 동작
- useRouter()의 refresh()를 사용하면, 현재 state를 초기화하지 않고 가능함?

## Server Action?

- server action을 쓰면, 수동으로 API endpoint를 만들 필요는 없음?
- 비동기 서버 함수를 정의하고, 컴포넌트에서 직접 호출해서 사용!
- 서버 컴포넌트에서 정의되고, 클라이언트 컴포넌트에서 호출
- 강의에서는 아직 베타 버전이라고 함..
