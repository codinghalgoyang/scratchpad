## 카카오 인증 with supabase

## 전체 흐름이 어떻게 될까?

- http의 요청에 대한 응답은 해당 요청을 받은 서버에서만 가능한데, 상세한 흐름이 어떻게 될까?
- 핵심은 브라우저가 next.js server, supbase server, kakao server의 중개기 역할임.

Next.js + Supabase에서 Kakao 로그인의 전체 흐름을 요청-응답 쌍으로 자세히 설명해드리겠습니다:

1. 로그인 시작 단계
   요청: 사용자 브라우저 → Next.js 앱
   • GET /login (로그인 페이지 접근)
   응답: Next.js 앱 → 사용자 브라우저
   • 로그인 페이지 HTML + “카카오 로그인” 버튼
2. OAuth 인증 시작
   요청: 사용자 브라우저 → Supabase
   • POST /auth/v1/authorize (signInWithOAuth 호출)
   • body: { provider: 'kakao', options: { redirectTo: 'http://localhost:3000/auth/callback' } }
   응답: Supabase → 사용자 브라우저
   • 302 Redirect to https://kauth.kakao.com/oauth/authorize?client_id=...&redirect_uri=...&response_type=code
3. 카카오 인증
   요청: 사용자 브라우저 → 카카오 인증 서버
   • GET https://kauth.kakao.com/oauth/authorize?...
   응답: 카카오 인증 서버 → 사용자 브라우저
   • 카카오 로그인 페이지 HTML
   요청: 사용자 브라우저 → 카카오 인증 서버
   • POST https://kauth.kakao.com/oauth/authorize (사용자가 로그인 정보 입력)
   응답: 카카오 인증 서버 → 사용자 브라우저
   • 302 Redirect to https://your-supabase-project.supabase.co/auth/v1/callback?code=...&state=...
4. Supabase 콜백 처리
   요청: 사용자 브라우저 → Supabase
   • GET https://your-supabase-project.supabase.co/auth/v1/callback?code=...
   내부 처리: Supabase가 카카오와 토큰 교환
   • Supabase → 카카오: authorization code를 access token으로 교환
   • Supabase → 카카오: access token으로 사용자 정보 조회
   응답: Supabase → 사용자 브라우저
   • 302 Redirect to http://localhost:3000/auth/callback#access_token=...&refresh_token=...
5. Next.js 콜백 처리
   요청: 사용자 브라우저 → Next.js 앱
   • GET /auth/callback#access_token=... (fragment로 토큰 전달)
   응답: Next.js 앱 → 사용자 브라우저
   • 콜백 페이지 HTML + JavaScript (토큰을 추출하고 세션 설정)
6. 세션 확인 및 리다이렉트
   요청: Next.js 클라이언트 → Supabase (JavaScript SDK)
   • POST /auth/v1/token (토큰 검증 및 사용자 정보 조회)
   응답: Supabase → Next.js 클라이언트
   • 사용자 세션 정보 JSON
   요청: 사용자 브라우저 → Next.js 앱
   • GET /dashboard (로그인 후 리다이렉트)
   응답: Next.js 앱 → 사용자 브라우저
   • 대시보드 페이지 HTML
   핵심 포인트: 1. 서버 간 직접 통신 불가: 브라우저가 중개 역할을 하며, 각 서버는 자신이 받은 요청에만 응답합니다. 2. 리다이렉트 체인: OAuth는 여러 서버 간의 리다이렉트 체인으로 구성됩니다.
   • Next.js → Supabase → 카카오 → Supabase → Next.js 3. 토큰 전달 방식:
   • Authorization code는 URL parameter로
   • Access token은 URL fragment(#)로 전달되어 서버에 노출되지 않음 4. 상태 관리: 각 단계에서 state 파라미터로 CSRF 방지 및 상태 추적
   이런 복잡한 흐름이 필요한 이유는 보안상 서버 간 직접 통신 없이도 안전하게 인증을 처리하기 위함입니다.​​​​​​​​​​​​​​​​

---

## supabase 환경설정

```bash
npm install @supabase/supabase-js
```

---

> supabase에서 나 대신 인증을 하고(supabse <-> kakao), 내 사이트를 redirection url로 해서 인증 결과를 받고, 인증 관련 database등은 supabase에서 관리해줌.

자료 : https://euni8917.tistory.com/m/575

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

---
