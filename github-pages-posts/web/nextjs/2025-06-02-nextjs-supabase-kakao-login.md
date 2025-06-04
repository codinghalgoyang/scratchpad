---
layout: single
title: "[web/nextjs] Next.js 에서 supabase를 활용한 카카오 로그인"
categories: web/nextjs
---

> 사전 환경 : 'Next.js 프로젝트에서 Supbase 초기 설정' 먼저 진행하기!

## supabase & 카카오 개발자 연동

#### [supabase](https://supabase.com/)의 카카오 콜백 URI 값 확인

- `SUPABASE_KAKAO_CALLBACK_URI` : Authentication > Sign In / Providers > Kakao > Callback URL

#### 카카오 개발자 사이트 프로젝트 생성 및 설정

- 프로젝트 생성 : 앱이름, 회사명, 카테고리 선택 후 저장
- 비즈앱 전환
  - 프로젝트 > 비즈니스 > 앱아이콘 등록 (아무거나)
  - 프로젝트 > 비즈니스 > 개인 개발자 비즈 앱 전환 > 목적 : 이메일 필수 로 해서 신청
- 앱 키 확인
  - `KAKAO_REST_API_KEY` : 프로젝트 > 앱 키 > REST API 키
- 카카오 로그인 설정 :
  - 프로젝트 > 카카오 로그인 > 활성화 상태 On
  - 프로젝트 > 카카오 로그인 > Redirect URI : supabase의 `SUPABASE_KAKAO_CALLBACK_URI`를 등록
  - 프로젝트 > 동의항목 (카카오 로그인)
    - 닉네임, 프로필 사진, 카카오 계정 > 설정 > 필수 동의 > 동의목적 작성 > 저장
  - `KAKAO_CLIENT_SECRET` : 프로젝트 > 보안 (카카오 로그인) > 코드 생성

#### supabase Authentication > Sign In / Providers > Kakao 설정

- Kakao enabled : true
- REST API Key : `KAKAO_REST_API_KEY`
- Client Secret Code : `KAKAO_CLIENT_SECRET`
- Save 클릭

## 코드 작업

#### 패키지 설치

```bash
npm i @supabase/auth-helpers-nextjs @supabase/supabase-js
npm i @supabase/auth-helpers-react
```

#### lib/supbase.ts

```ts
import { createClientComponentClient } from "@supabase/auth-helpers-nextjs";

export const supabase = createClientComponentClient();
```

#### app/page.tsx

```tsx
import { supabase } from "@/lib/supabase";

export default function HomePage() {
  const handleKakaoLogin = async () => {
    const { data, error } = await supabase.auth.signInWithOAuth({
      provider: "kakao",
      options: {
        redirectTo: `${window.location.origin}/auth/callback`,
      },
    });

    if (error) {
      console.error("카카오 로그인 오류:", error);
    }
  };

  return (
    <div className="h-screen flex flex-col justify-center items-center">
      <h1>Home Page</h1>
      <button onClick={handleKakaoLogin}>카카오로 로그인</button>
    </div>
  );
}
```

#### 콜백 처리

```
https://ktnzrtyyyjpwqwkfhfwv.supabase.co/auth/v1/callback
```

#### 참고 자료

- https://lurgi.tistory.com/141
