---
layout: single
title: "[expo] 개발 앱/양산 앱 분리 및 빌드"
categories: expo
published: false
---

## 배경

- 앱을 개발하다 보니 양산에 영향을 미치지 않으면서, 이런 저런 테스트를 해야할 경우가 많다는 걸 알게됨.
- 개발은 expo go로 해도 되는거 아닌가? 생각도 들지만, 앞으로 사용하게될 Google Signin이나 Google Admob은 기존 expo go 앱에는 포함되어있지않아, 나만의 expo go 앱을 만들어야 함. 참고로 이 [커스텀된 나만의 expo go 앱 빌드](https://velog.io/@gyukyue/expo-development-build)를 `development build`라고 부름)

## 할 일

#### 개발 앱 / 양산 앱 분리

- 개발앱과 양산앱이 같은 패키지명(예. `com.{company}.{appname}`)을 사용하게 되면, 앱이 이 패키지명 기준으로 폰에 설치되기 때문에 개발앱/양산앱 둘 중 하나만 사용해야하는 상황이 됨.
- 그럼 개발 중 양산앱을 사용하고 싶다거나, 양산앱 사용중 개발앱을 사용하고 싶다면 다시 해당 버전을 찾아 설치를 다시해야하는 번거로움이 발생함.

#### 개발 앱 환경 / 양산 앱 환경 분리

- 앱 자체를 분리했다해도 사용하는 환경(데이터베이스등)이 같으면 개발 도중 양산 앱 환경을 건드리게 됨. 그래서 환경 분리가 필요하고 이건 각 환경(데이터베이스, Google Signin등)에 따라 달라지므로, 각 부분 내에서 환경 분리 내용과 함께 작성 예정

## 개발 앱 / 양산 앱 분리

- 나는 (로컬 빌드가 아닌) eas 환경 변수 및 빌드를 사용함. (eas : 온라인 빌드 및 제출 가능)
- [eas 환경 변수](https://velog.io/@gyukyue/expo-eas-%ED%99%98%EA%B2%BD%EB%B3%80%EC%88%98)를 설정을 통한 개발 앱 / 양산 앱 분리에 대한 내용임
- 요약하면, 온라인에 환경변수를 등록해놓고, 온라인 환경에서 빌드하겠다는 내용임.

#### Step 1. eas 설치 및 로그인

```
npm install -g eas-cli # eas-cli 설치
eas login # eas 로그인
eas whoami # 현재 로그인된 계정 확인
```

#### Step 2. 프로젝트에 대한 eas 초기화

온라인 프로젝트 생성 및 연결

```bash
eas init
```

위 명령어 실행 뒤, `app.json` 파일을 보면 `expo/plugins/extra/eas/projectId`가 추가된 것을 확인할 수 있음.

- expo 사이트에서 프로젝트가 생성되었고, 해당 projectId가 app.json 에 설정된 것임.
- 다른 말로, expo(eas)에서 관리하는 프로젝트를 생성하고 이걸 내 프로젝트(로컬)을 연결시킨 것임.

```bash
eas build:configure
# Select All
```

위 명령어를 실행하면, `eas.json` 파일이 생성됨.

- eas는 온라인 빌드 및 앱 제출을 도와주는 툴임
- eas에서는 기본적으로 "development", "preview", "production"이라는 `profile`을 제공함.
- 이 profile 별로 환경변수와 빌드 방법, 제출 환경 설정을 다르게 설정 할 수 있음.

위 명령어 실행을 통해 성생된 `eas.json`의 내용을 잠깐 살펴보자

```json
{
  "cli": {
    "version": ">= 16.3.3",
    "appVersionSource": "remote"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    },
    "preview": {
      "distribution": "internal"
    },
    "production": {
      "autoIncrement": true
    }
  },
  "submit": {
    "production": {}
  }
}
```

- `"build"` : profile에 따른 빌드 설정
  - `"development"` : development 프로파일을 의미
    - `"developmentClient : true` : development build (custom expo go) 형태로 앱을 빌드, 앱 코드 내에서 `__DEV__` 변수가 true가 됨
    - `"distribution" : "internal"` : 앱스토어(Apple App Store, Google play store등)을 거치지 않고 내부적으로 배포하기 위한 목적, expo 사이트에서 빌드된 application을 바로 다운로드하여 사용 가능하도록 빌드함
  - "preview" : preview 프로파일을 의미
    - `"distribution" : "internal"` : 위와 내용 같음
    - developmentClient가 설정되어있지않으므로, expo go 형태가 아닌 그냥 일반 앱으로 빌드됨. 앱 코드 내에서 `__DEV__` 변수가 false가 됨
  - `"production"`
    - `"autoIncrement" : true` : 버전정보를 내부적으로 증가시켜 반영함.
    - distribution이 설정되어 있지 않으므로, 앱스토어에 올리기 위한 파일로 빌드됨.
    - developmentClient가 설정되어있지않으므로, expo go 형태가 아닌 그냥 일반 앱으로 빌드됨. 앱 코드 내에서 `__DEV__` 변수가 false가 됨
- "submit" : profile에 따른 제출 설정, `eas submit`을 사용할 경우, 특정 앱스토어에 앱을 제출하기 위한 내용이 들어가게 됨

정리하면,

- "development" profile은 expo go 형태의 앱으로 빌드되어 빠른 디버깅이 가능, expo 사이트에서 바로 다운로드하여 사용 가능한 형태로 빌드됨 -> **개발 및 디버깅 목적**
- "preview" profile은 일반 앱 형태로 빌드되고, expo 사이트에서 바로 다운로드하여 사용 가능한 형태로 빌드됨 -> **개발된 앱을 바로 다운 및 설치하여 일반 앱 형태로 확인 목적 (코드 내 **DEV**가 false가 되므로 양산앱 환경(데이터베이스)을 사용하게 됨)**
- "production" profile은 일반 앱 형태로 빌드되고, 앱스토어 제출용으로 빌드됨(expo 사이트에서 다운로드는 가능하나 바로 설치는 불가능한 포맷임, 매 빌드마다 내부적으로 관리하는 버전이 올라감 ) -> **앱스토어 제출 목적**

#### Step 3. app.json 변경

- 개발앱/양산앱 분리를 위해 패키지명과 앱이름을 개발환경인지 아닌지에 따라 다르게 설정할 것임
- 패키지명은 앞서 말한 것처럼 폰 OS (안드로이드, ios)에서 앱을 구분하는 기준이고 이걸 구분해줘야 앱이 2개가 생기기 때문에 다르게 설정함.
- 앱이름은 우리가 앱을 봤을 때, 개발용 앱인지 양산용 앱인지 이름을 보고 쉽게 알 수 있도록 다르게 설정함.
- 패키지명과 앱이름은 `app.json` 파일 안에 정의 되어있음.
- 그런데, `json` 파일은 코드적 요소 (개발환경일 땐 이거, 아닐 땐 저거)를 설정할 수 없음.
- expo는 `app.json` 말고도 `app.config.js` 또는 `app.config.ts` 파일을 지원함.
- `app.json` 파일을`app.config.ts`로 바꾸고 내용을 살짝만 바꿔주면 됨.

`app.json` -> `app.config.ts`

```ts
export default {
...
```

- (vscode extension인 prettier 사용시)맨 앞에 export default만 붙여주고 저장하면, 나머지는 typescript 형태로 저장됨.
- prettier를 사용하지 않는다면, 일일이 ts의 객체 형태로 json을 바꿔주는 방법도 있지만, pretter 를 설정해서 사용하는 것이 나중에 코드 작성시에도 도움이 됨.

#### Step 4. profile에 따른 패키지명과 앱이름 구분

- preview 도 사용할 수는 있겠지만, preview 앱도 패키명을 구분해버리면, 나중에 패키지명을 기반한 외부 서비스를 등록할 때도 관리해야할 파일들이 늘어남.
- preview는 production과 같은 패키지명과 앱이름을 쓰도록하고, 필요한 경우에만 사용
- 또, preview를 사용하지 않더라도, 시간은 좀 더 걸리지만 production으로 빌드후 appstore에 올린후 다운로드 하거나, 파일 변환(안드로이드의 경우, aab -> apk 파일 변환)을 통해서도 설치할 수는 있긴 함.

[eas 환경 변수](https://velog.io/@gyukyue/expo-eas-%ED%99%98%EA%B2%BD%EB%B3%80%EC%88%98)에 `EXPO_PUBLIC_APP_VARIANT` 생성

- https://expo.dev > 프로젝트 선택 > Environment variables
  - Add variables
    - name : EXPO_PUBLIC_APP_VARIANT
    - Value : development
    - Visibility : Plain text
    - Environment : development
  - Add variables
    - name : EXPO_PUBLIC_APP_VARIANT
    - Value : production
    - Visibility : Plain text
    - Environment : preview, production

#### Step 5. app.confg.ts 설정

아래 내용을 app.config.ts에 추가

```ts
const IS_DEV = process.env.EXPO_PUBLIC_APP_VARIANT === "development";
const name = IS_DEV ? "Appname (dev)" : "Appname";
const appId = IS_DEV
        ? "com.{company}.{appname}.dev"
        : "com.{company}.{appname}";

export default {
  expo: {
    name: name,
    ios: {
      bundleIdentifier: appId,
    },
    android: {
      package: appId,
    },
};
```

#### Step 6. 빌드

```
eas build --platform andriod --profile development
```

- 위 명령어의 --platform, --profile 옵션을 주어 빌드
- platform : android, ios
- profile : development, production
