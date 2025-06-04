## 카카오 인증 with supabace

> supabase에서 나 대신 인증을 하고(supabse <-> kakao), 내 사이트를 redirection url로 해서 인증 결과를 받고, 인증 관련 database등은 supabase에서 관리해줌.

자료 : https://euni8917.tistory.com/m/575

```mermaid
sequenceDiagram
    participant User as 사용자
    participant SignIn as SignIn 컴포넌트
    participant Supabase as Supabase Client
    participant KakaoAuth as Kakao OAuth
    participant AuthCallback as /auth/callback
    participant HomePage as 홈페이지

    Note over User, HomePage: 사전 설정 완료 (Kakao Developer + Supabase Provider 설정)

    Note over User, HomePage: 1. 로그인 시작
    User->>SignIn: 카카오 로그인 버튼 클릭
    SignIn->>Supabase: supabase.auth.signInWithOAuth({<br/>  provider: 'kakao',<br/>  options: {<br/>    redirectTo: `${origin}/auth/callback`<br/>  }<br/>})

    Note over User, HomePage: 2. 카카오 인증 페이지 이동
    Supabase->>User: 카카오 OAuth URL로 리다이렉트
    User->>KakaoAuth: 카카오 로그인 페이지 접근

    Note over User, HomePage: 3. 사용자 인증 및 동의
    KakaoAuth->>User: 동의 항목 화면 표시<br/>(닉네임, 프로필사진, 이메일 등)
    User->>KakaoAuth: 동의 및 로그인 완료

    Note over User, HomePage: 4. 콜백 처리
    KakaoAuth->>AuthCallback: authorization code와 함께<br/>/auth/callback으로 리다이렉트
    AuthCallback->>Supabase: supabase.auth.exchangeCodeForSession(code)
    Supabase->>KakaoAuth: Access Token 요청
    KakaoAuth->>Supabase: Access Token 및 사용자 정보 반환

    Note over User, HomePage: 5. 세션 생성 및 사용자 정보 저장
    Supabase->>Supabase: 사용자 계정 생성/업데이트<br/>(users.auth 테이블)
    Supabase->>AuthCallback: 세션 정보 반환
    AuthCallback->>HomePage: 성공 시 홈페이지로 리다이렉트

    Note over User, HomePage: 6. 로그인 완료
    HomePage->>Supabase: 현재 세션 확인<br/>(supabase.auth.getSession())
    Supabase->>HomePage: 사용자 세션 정보 반환
    HomePage->>User: 로그인된 상태로 홈페이지 표시

    Note over User, HomePage: 7. 후속 요청 (인증 상태 유지)
    User->>HomePage: 페이지 새로고침 또는 재방문
    HomePage->>Supabase: 세션 확인
    Supabase->>HomePage: 유효한 세션 정보 반환
    HomePage->>User: 로그인 상태 유지하여 페이지 표시
```

```tsx
async function signInWithKakao() {
  const { data, error } = await supabase.auth.signInWithOAuth({
    provider: "kakao",
    options: {
      redirectTo: process.env.NEXT_PUBLIC_VERCEL_URL
        ? `https://${process.env.NEXT_PUBLIC_VERCEL_URL}/auth/callback`
        : "http://localhost:3000/auth/callback",
    },
  });
}
```

#### redirect 주소 설정

1. supabse redirection URL 등록

- supabase > 프로젝트 > Authentication > URL Configuration > Redirect URLs 여기에 추가가 필요한듯.
- Site URL에는 기본적으로 http://localhost:8000은 등록이 되어있는 것 같고, 그게 아니라면 production용 환경 URL은 등록해줘야하는 것 같음
  (이기에 등록된 url이 아니면 redirection이 불가능한 것으로 보임)

2. supabaseWithOAuth 메으드의 redirectionTo 옵션 지정

- app/auth/callback/route.ts

```ts
import { cookies } from "next/headers";
import { NextResponse } from "next/server";
import { type CookieOptions, createServerClient } from "@supabase/ssr";
import { createServerSupabaseClient } from "utils/supabase/server";

export async function GET(request: Request) {
  const { searchParams, origin } = new URL(request.url);
  const code = searchParams.get("code"); // if "next" is in param, use it as the redirect URL
  const next = searchParams.get("next") ?? "/";

  if (code) {
    const supabase = await createServerSupabaseClient();
    const { error } = await supabase.auth.exchangeCodeForSession(code);
    if (!error) {
      return NextResponse.redirect(`${origin}${next}`);
    }
  } // return the user to an error page with instructions

  return NextResponse.redirect(`${origin}/auth/auth-code-error`);
}
```
