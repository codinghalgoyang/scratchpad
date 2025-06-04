---
layout: single
title: "[expo/modules] Google Signin with supabase"
categories: expo/modules
published: false
---

> Google Signin with supabase는 ios에서는 현재 지원하지 않는다! (2025-05-23 기준)

이 포스트는 앞서 google signin과 supbase 초기 설정이 완료되었다고 가정함.

## google login이 이미 되었지만, supbase 로그인이 필요한 이유

- supabase와 구글 클라우드 콘솔은 연동되어있지 않음
- 다시 말해 `GoogleSignin()`을 했다고 하더라도, 구글 클라우드 콘솔에 로그인을 한것이지 supabase에는 로그인 정보가 전달된 것이 아님
- supabase에서 데이터 베이스를 사용할 때 아무나 접근하지 못하게 하기위해 데이터베이스를 접근하려는 사용자가 로그인을 했는지 등을 규칙을 정함.
- GoogleSignin으로 로그인한 정보(idToken)을 통해, supabase에 다시 구글 로그인을 해줘야함.
- firebase를 사용하는 경우에는 구글 클라우드 콘솔과 파이어베이스가 연동되어있어, GoogleSignin()만 사용하면 됨.

#### 전체 과정

- react-native-google-signin을 통해 구글 클라우드 콘솔(firebase)의 구글 인증에 로그인
- 로그인한 정보(idToken)을 활용해 다시 supabase의 구글 authentication에 로그인

## supabase & 구글 로그인 연동

#### 웹용 구글 인증 클라이언트 내용 업데이트

- expo 프로젝트에서 supabase의 구글 로그인을 위해서는 **웹용 구글 인증 클라이언트**를 사용함. (안드로이드의 경우, GoogleSigin의 OneTapSignin을 사용하면 웹용 클라이언트가 따로필요하진 않다고는 하나, 난 ios도 같이 할꺼고, OneTapSignin도 잘 되지 않아 기존 방법으로 진행함.)
- 앞선 과정을 통해 firebase에서 만든 dev용 프로젝트와 production용 프로젝트를 만들었고 구글 인증을 활성화 했다면, 구글 클라우드 콘솔 해당 프로젝트가 자동 생성되어있음.
- 구글 클라우드 콘솔 > 프로젝트 선택 (안보이면 전체 탭 선택하면 보임) > 모든 제품 보기 > 구글 인증 > 클라이언트 > Web client 선택
- 승인된 리디렉션 URI 추가 : supabase > 프로젝트(dev,production) 선택 > Authentication > Sign in / Provider > Google 클릭 > Callback URL (for OAuth) 복사해서 붙여넣기
  - 어플리케이션 유형 : 웹 어플리케이션
  - 이름 : {appname} Web for supabase (dev) 또는 {appname} Web for supabase
  - 승인된 리디렉션 URI : supabase > 프로젝트 > Authentication > Sigin In / Provider > Auth Providers 중 Google 선택 > Callback URL (for OAuth)
  - 저장

#### supabase 인증 > 구글 provider 설정

- supabase > 프로젝트 > Authentication > Sigin In / Provider > Auth Providers 중 Google 선택
  - Enable Sign in with Google : true
  - Client IDs : 구글 클라우드 콘솔 > 프로젝트 > 모든 제품 보기 > 구글 인증 > 클라이언트 > 웹 용 클라이언트 선택({appname} Web for supabase ) > 클라이언트 ID
  - Client Secret (for OAuth) : 구글 클라우드 콘솔 > 프로젝트 > 모든 제품 보기 > 구글 인증 > 클라이언트 > 웹 용 클라이언트 선택({appname} Web for supabase ) > 클라이언트 보안 비밀번호
  - Save

#### supabse.ts 내용 추가

```javascript
...
import { AppState } from "react-native";

export const supabase = createClient(supabaseUrl, supabaseAnonKey, {
  auth: {
    storage: AsyncStorage,
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: false,
  },
});

// 이 부분 추가!
AppState.addEventListener('change', (state) => {
  if (state === 'active') {
    supabase.auth.startAutoRefresh()
  } else {
    supabase.auth.stopAutoRefresh()
  }
})
```

#### 코드

```ts
const signinWithGoogle = async () => {
  try {
    await GoogleSignin.hasPlayServices();
    const googleUser = (await GoogleSignin.signIn()).data;
    console.log("googleUser : ", googleUser);
    if (googleUser?.idToken) {
      const { data, error } = await supabase.auth.signInWithIdToken({
        provider: "google",
        token: googleUser.idToken,
      });
      console.log("supabase error : ", error);
      console.log("supabase data : ", data);
    }
  } catch (error) {
    console.error(error);
  }
};

export default function Index() {
  useEffect(() => {
    const iosClientId = __DEV__
      ? "{iosClientId (dev)}"
      : "{iosClientId}";
    const webClientId = __DEV__
      ? "{webClientId (dev)}"
      : "{webClientId}";
     
    GoogleSignin.configure({
      iosClientId: iosClientId,
      webClientId: webClientId,
      scopes: ["https://www.googleapis.com/auth/drive.readonly"],
    });
  }, []);
```

android에서는 위에 부분이 동작하는데 ios에서는 동작하지 않음. 왜 안될까?
-> https://readwriteexercise.com/posts/google-signin-expo-supabase/

> Supabse는 IOS에서 구글 로그인하는걸 지원하지 않는다고 함.
