---
layout: single
title: "[web/nextjs] 페이지 라우팅"
categories: web/nextjs
---

## 라우팅이란?

- 사용자가 특정 URL을 요청했을 때, 어떤 페이지를 보여줄지(또는 어떤 데이터를 가져와서 보여줄지)를 연결해주는 역할
- 예전에는 서버가 모든 라우팅을 담당했으나, SPA(Single Page Application)의 경우, 사용자가 페이지를 이동할 때 전체 페이지를 새로고침하지 않고, javascript를 사용하여 필요한 부분만 동적으로 업데이트 하는 방식을 취함

## Next.js의 라우팅

- Next.js는 파일 시스템 기반의 라우팅을 제공함.
  - 폴더를 만들고 파일을 생성하는 것만으로도 라우팅이 자동으로 생성됨
- 크게 두 가지 방식을 제공함 (동작 방식, 파일 이름등에서 차이를 보임)
  - Page Router : Next.js 12 까지 사용되는 라우터
  - App Router : Next.js 13 부터 사용되는 라우터

## App Router

> Next.js에서 `App Router` 사용을 권장하므로, `App Router`를 중심으로 설명

#### 기본 라우팅

- `app` 디렉토리 내 폴더를 생성하고, 그 안에 `page.tsx` 파일을 생성
- 폴더 안에 폴더를 또 생성해서 중첩하는 것도 가능함
- 예1) `app/page.tsx` -> `/`
- 예2) `app/about/page.tsx` -> `/about`
- 예3) `app/dashboard/settings/page.tsx` -> `/dashboard/settings`

#### 동적 라우팅

- 폴더 이름에 대괄호 `[]`를 사용해서 동적 경로를 정의
- 동적 페이지 생성 : `app/blog/[slug]/page.tsx`
- 해당 url들 : `app/blog/first-post`, `app/blog/second-post`, ..
- 코드 : `first-post`, `second-post`는 페이지 컴포넌트의 `params` prop으로 전달됨

```tsx
// app/blog/[slug]/page.tsx
export default function BlogPost({ params }) {
  return <h1>블로그 게시물: {params.slug}</h1>;
}
```

#### `layout.tsx` 파일

- 공통된 부분을 일괄적으로 제공하기 위한 파일
- 특정 경로에 layout 파일을 생성하면 해당 경로 아래 자식들을 감싸는 역할을 하게 됨
- 예) `app/dashboard/layout.tsx` 는 `app/dashboard/` 아래 모든 자식들의 `page.tsx`를 감싸는 역할을 함

```tsx
export default function DashboardLayout({ children }) {
  return (
    <section>
      <nav>네비게이션 메뉴</nav>
      {children} {/* 자식 페이지나 레이아웃이 여기에 렌더링됩니다 */}
    </section>
  );
}
```

#### `loading.tsx` 파일

- 특정 경로의 콘텐츠를 로드하는 동안 보여질 페이지 정의
- 예) `app/dashboard/loading.tsx` 는 `/app/dashboard`가 로드되는동안 보여질 화면임.
- 내가 isLoading 처럼 처리할 필요가 없어짐

#### `error.tsx` 파일

- 특정 경로의 콘텐츠 로드에 실패했을 때 보여질 페이지 정의
- 예) `app/dashboard/error.tsx` 는 `/app/dashboard`가 로드되는동안 보여질 화면임.

## 페이지 이동 방법

Next.js에서 페이지를 이동하는 크게 2가지 방식임

- `<Link>` 컴포넌트 사용 방식 (권장)
- `useRouter` 훅을 사용한 프로그래밍 방식

#### `<Link>` 컴포넌트 사용 방식

```tsx
<Link href="/about">about</Link>
```

- `<a>`태그와 유사하지만, Next.js의 내장된 라우팅 기능을 활용하여 클라이언트 사이드 탐색을 제공함(페이지 이동시 전체 페이지를 새로고침하지 않고, 필요한 부분만 빠르게 업데이트)

#### `useRouter` 훅을 사용한 프로그래밍 방식

```tsx
// app/dashboard/profile/page.js (예시 클라이언트 컴포넌트)
"use client"; // useRouter를 사용하기 위해 클라이언트 컴포넌트로 선언

import { useRouter } from "next/navigation"; // App Router에서 useRouter import

export default function UserProfilePage() {
  const router = useRouter(); // useRouter 훅 초기화

  const handleSaveProfile = () => {
    // 프로필 저장 로직 (예: API 호출)
    console.log("프로필 저장 중...");

    // 저장 성공 후 대시보드 페이지로 이동
    router.push("/dashboard"); // /dashboard 경로로 이동
  };

  const handleGoBack = () => {
    // 이전 페이지로 이동
    router.back();
  };

  return (
    <div>
      <h1>사용자 프로필</h1>
      <button onClick={handleSaveProfile}>
        프로필 저장 및 대시보드로 이동
      </button>
      <button onClick={handleGoBack}>뒤로 가기</button>
      {/*
        router.replace('/some-path') : 현재 히스토리 스택을 대체하여 이동 (뒤로 가기 시 해당 페이지 건너뜀)
        router.refresh() : 현재 경로를 새로고침 (페이지 데이터를 다시 불러옴)
        router.prefetch('/another-path') : 해당 경로의 데이터를 미리 가져옴 (Link 컴포넌트처럼)
      */}
    </div>
  );
}
```
