---
layout: single
title: "[web/supabase] Supabase Authentication"
categories: web/supabase
---

#### Supabase Authentication

- supabase > 프로젝트 > authentication > Sign In / Provider

#### 과정

- 내클라이언트에서 supabase의 signInWithOAuth()로 signIn을 요청하고, redirectTo:로 내 url (http://localhost:8000) 또는 내 웹사이트 url을 같이 넘겨줌
- supabase에서 내 클라이언트 대신 인증 과정 처리를 대신해줌
- supabase에서 oauth에 대한 clientID와 client secret을 가지고 있음.
- 타 서비스에서 인증이 완료되면, supabase 쪽의 callback uri로 리디렉션함.
- supabase에서 처음에 등록한 내 사이트로 redirection 함.

#### 코드

- supabase > API Dos > User Management
-

#### supabase에 내 사이트가 안전하다는걸 알려줘야함

- supabase > Authentication > URL Configuration
  - Redirection URLs > Add URL
    - 개발 용 url (https://localhost:8000) 등록
    - 내 웹사이트 url 등록

#### supabase가 auth 과정에서 나 대신 해주는 일

- 인증 로직 구현 및 인증 서버를 관리할 필요가 없음
- 사용자 정보(이메일, 비밀번호 해시)등을 안전하게 저장하고 있음.
- 세션관리, 인증 시스템 확장성등을 고려할 필요가 없음

#### 코드

```ts
const client = supabase.createClient(supabaseUrl, supabaseKey);

// supabase auth의 signInWitOAuth로 supabase에게 부탁하고 redirect를 하도록함.
const signInWithGithub = async () => {
  const { data, error } = await client.auth.signInWithOAuth({
    provider: "github",
    options: {
      redirectTo: "http://localhost:8000",
    },
  });
};

// supabase에게 현재 세션 정보 확인
const checkLogin = async () => {
  const authInfo = await client.auth.getSession();
  const session = authInfo.data.session;
};

// supabase에게 로그아웃을 부탁
const signOut = async () => {
  const { error } = await client.auth.signOut();
};
```

#### Database & Authentication

- database table에서 column을 만들때, type을 uuid, default 값을 `auth.uid()`를 선택하면, 아이템 추가시 authentication으로 인해 로그인된 사용자의uid가 자동으로 계산되어 들어감
- 또, 링크 버튼을 클릭하면 해당 컬럼이 참고할 테이블을 지정할 수 있음.
  - 이걸 위에서 만든 user id에 대한 컬럼의 내용을 아래와 같이 설정하면
    - shema : auth
    - table : users
    - auth.users to reference to : id
    - Action if referenced row is removed : Cascade
  - user가 탈퇴를 하면(auth/users/id 삭제), 내 테이블의 user id column의 값이 해당 id인 애들도 삭제가 됨.

#### RLS 설정

- owner : 소유자, authencated : 로그인한 사용자, everybody : 모두 별로 각 어떤 권한 (SELECT, INSERT, UPDATE, DELETE, ALL)이 필요한지 고려해서 설정한다.
- supabase > Authentication > Policies
- new poicies에서 해당 권한들을 지정함

#### using expression vs with check expression

- using expression : where의 역할인데, 읽기와 관련된 작업 전 조회할 때 사용 (작업 전 조회)
- with check expression : where의 역할인데, 쓰기와 관련된 작업 후 조회에 사용 (작업 후 조회)
