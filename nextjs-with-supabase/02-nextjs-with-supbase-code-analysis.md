# Next.js with supbase 코드 분석

`npx create-next-app -e with-supabase` 명령을 사용해서 생성되는 프로젝트의 주요 코드 분석

## lib/supabase

- lib/supabase 아래에는 파일 3개가 생성됨
- `client.ts`, `server.ts`, `middleware.ts`
- 이 파일들은 모두 Supabase의 public key와 anon key를 가지고 supabase 클라이언트를 생성하는 역할을 함.
- supabase 클라이언트는 supabase 기능을 사용하기 위해 사용되는 객체임.

> 예제에서는 클라이언트 사이드 (브라우저)용 supabase 클라이언트를 생성하는 `client.ts`의 `createClient()`만 사용하고 있음. 추가적으로 서버 사이드 클라이언트가 필요한 경우에 다시 살펴보면 될듯

#### `client.ts`

- 클라이언트 사이드(브라우저)에서 supabase 클라이언트를 생성하는 역할
- 사용자 UI에서 직접 데이터 접근 및 인증 처리등에 사용 (로그인, 회원가입, 사용자 데이터 표시, 클라이언트 컴포넌트)
- supabase Key/인증 : Public Key

```ts
import { createBrowserClient } from "@supabase/ssr";

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  );
}
```

#### `server.ts`

- 서버 사이드 (Next.js 또는 Node.js)에서 supbase 클라이언트를 생성하는 역할
- 서버에서 데이터 패치, 민감한 작업등에 사용 (서버 컴포넌트, 라우트 핸들러, 서버 액션)
- supabase Key/인증 : Public Key, JWT

```ts
import { createServerClient } from "@supabase/ssr";
import { cookies } from "next/headers";

export async function createClient() {
  const cookieStore = await cookies();

  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return cookieStore.getAll();
        },
        setAll(cookiesToSet) {
          try {
            cookiesToSet.forEach(({ name, value, options }) =>
              cookieStore.set(name, value, options)
            );
          } catch {
            // The `setAll` method was called from a Server Component.
            // This can be ignored if you have middleware refreshing
            // user sessions.
          }
        },
      },
    }
  );
}
```

#### `middleware.ts`

- 미들웨어 사이드(Edge Runtime)에서 supbase 클라이언트를 생성하는 역할
- 미들웨어에서 요청 전 인증 확인, 세션 관리등에 사용 (인증 기반 리다이렉션, 전체 앱의 인증 플로우 관리)
- supabase Key/인증 : Public Key, JWT

```ts
import { createServerClient } from "@supabase/ssr";
import { NextResponse, type NextRequest } from "next/server";
import { hasEnvVars } from "../utils";

export async function updateSession(request: NextRequest) {
  let supabaseResponse = NextResponse.next({
    request,
  });

  // If the env vars are not set, skip middleware check. You can remove this once you setup the project.
  if (!hasEnvVars) {
    return supabaseResponse;
  }

  const supabase = createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return request.cookies.getAll();
        },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value }) =>
            request.cookies.set(name, value)
          );
          supabaseResponse = NextResponse.next({
            request,
          });
          cookiesToSet.forEach(({ name, value, options }) =>
            supabaseResponse.cookies.set(name, value, options)
          );
        },
      },
    }
  );

  // Do not run code between createServerClient and
  // supabase.auth.getUser(). A simple mistake could make it very hard to debug
  // issues with users being randomly logged out.

  // IMPORTANT: DO NOT REMOVE auth.getUser()

  const {
    data: { user },
  } = await supabase.auth.getUser();

  if (
    request.nextUrl.pathname !== "/" &&
    !user &&
    !request.nextUrl.pathname.startsWith("/login") &&
    !request.nextUrl.pathname.startsWith("/auth")
  ) {
    // no user, potentially respond by redirecting the user to the login page
    const url = request.nextUrl.clone();
    url.pathname = "/auth/login";
    return NextResponse.redirect(url);
  }

  // IMPORTANT: You *must* return the supabaseResponse object as it is.
  // If you're creating a new response object with NextResponse.next() make sure to:
  // 1. Pass the request in it, like so:
  //    const myNewResponse = NextResponse.next({ request })
  // 2. Copy over the cookies, like so:
  //    myNewResponse.cookies.setAll(supabaseResponse.cookies.getAll())
  // 3. Change the myNewResponse object to fit your needs, but avoid changing
  //    the cookies!
  // 4. Finally:
  //    return myNewResponse
  // If this is not done, you may be causing the browser and server to go out
  // of sync and terminate the user's session prematurely!

  return supabaseResponse;
}
```
