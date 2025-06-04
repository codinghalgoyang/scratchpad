---
layout: single
title: "[expo/modules] Oauth, google signin, sha-1, google api console 내용 정리"
categories: expo/modules
published: false
---

#### 결론

- expo, eas를 사용해 빌드 및 배포 과정에 필요한 sha-1키를 얻는다(eas credentials)
- firebase에 해당 sha-1키를 등록한다.(프로젝트>앱설정)
- google-service.json, plist 파일 재다운로드 필요
- 구글 플레이 콘솔에 앱을 등록한다. (진짜 되는지 테스트 필요)

#### OAuth란?

- Open Authorization의 약자
- 비밀번호를 공유하지 않고도, 제 3자 애플리케이션이 자신의 정보를 접근할 수 있도록 허용하는 프로토콜
- 제 3자 애플리케이션 개발자 입장
  - 유저한테는 비밀번호 없이 접근 가능한 편의성을 제공
  - 회원가입절차를 생략
  - 해당 OAuth 제공업체의 유저 정보를 사용 가능

#### [클라이언트 인증](https://developers.google.com/android/guides/client-auth?hl=ko)

Google 로그인이나 앱 초대와 같은 특정 Google Play 서비스의 경우, 앱의 OAuth2 클라이언트 및 API키를 만들 수 있도록 서명 인증서의 SHA-1을 제공해야 함.

- eas를 사용하면 빌드 및 배포 과정에서 필요한 인증서와 키를 관리할 수 있다고 함.
- expo에서 내 앱의 configuration에 들어가보면 Android upload keystore라고 해서 SHA-1 키가 있음.
- 이걸 firebase 콘솔에서 앱을 추가할 때 입력하면 인증을 설정할 수 있다고 함. (프로젝트 > 앱설정)
- 나는 위에 과정은 잘 되어있는듯함.
- : Google Play API는 일반적으로 Google Play 서비스에 의해 인증된 앱에서만 사용할 수 있습니다. 강제로 설치한 앱은 이러한 인증을 받지 않기 때문에 API 호출이 실패할 수 있음.
- 그럼 일단 인증이 될떄까지 한 번 기다려 봐야할 것 같다.

#### Firebase에서 Google Authentication을 사용하면, Google API Console을 사용하지 않아도 되는가

- Google OAuth를 사용하려면 원래 Google API console에서 신청을 해야함.
- 그런데 Firebase를 사용하면 내부적으로 그 부분이 따로 진행되는 것 같음. 맞을까?
- Firebase 콘솔에서 Google Authentication을 활성화 하면, Firebase가 필요한 OAuth 클라이언트 ID와 Secret을 자동으로 생성하고 관리해줌.
- 초기 설정을 위해 구굴 API 콘솔에서 프로젝트를 생성하고 Firebase와 연결하는 과정이 필요하다고 답변함...ㅠ
