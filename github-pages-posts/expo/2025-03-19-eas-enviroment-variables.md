---
layout: single
title: "[expo] eas 환경변수 내용 정리"
categories: expo
---

## 나의 결론

- 내가 local에 환경변수를 굳이 가지고 있을 필요가 없음.
- 환경변수는 코드 내 `__DEV__`가 false가 되는 `preview`, `production`는 함께 사용하고 `__DEV__`가 true가 되는 `development`는 따로 구분하자.
- plain text로 구분되어야할 환경 변수들
  - 가독성을 위해 사용자 앱에서 사용할 환경변수는 EXPO*PUBLIC*을 붙이자.
  - app config 에서 사용할 환경 변수는 EXPO*PUBLIC*을 붙이지 말자.
- sensitive
  - API키등을 위한 환경변수들
- secret
  - 빌드시에 사용할 애들(예. 구글서비스json 파일)

---

## [환경변수 - Expo Doc](https://docs.expo.dev/eas/environment-variables/) 문서 정리

환경 변수들을 EAS에서 어떻게 사용하고 관리하는가에 대한 내용임.

- Expo CLI는 코드에서 접두사가 있는 변수를 해당 환경 변수값으로 대체함.
- 예) `process.env.EXPO_PUBLIC_VARNAME`
- EAS Build와 Workflow는 원격 서버에서 진행되기 때문에, `.env` 파일을 사용할 수 없음
- `.env` 파일은 보통 `.gitignore`에 등록되어 버전관리에 포함되지 않음.
- 그래서 EAS는 환경변수를 관리하기 위한 별도의 메커니즘을 제공함.
- 이 메커니즘은 EAS 서버에서 환경 변수를 저장하고 관리하며, 이를 로컬 개발과 동기화하는데 중점을 두고 있음.

#### EAS 안의 환경변수들

- EAS는 환경변수를 위한 3가지 환경을 제공함.
- 3가지 환경은 `development`, `preview`, `production`임.
- 이 환경을 사용해서 `development`, `production`의 환경변수를 구분하거나, 서로 다른 App Store 릴리즈를 위해 다른 번들 식별자를 사용할 수 있음(?)
- EAS 빌드는 사용가능한 환경 중 하나의 환경변수를 사용하여 실행됨.
- Project-wide 환경 변수 : 하나의 EAS 프로젝트에서 어느 jobs(아마 `development, preview, production`을 의미하는듯)에서나 사용가능
- Account-wide 환경 변수 : 나의 EAS 계정 모두에서 사용가능 (project-wide 환경변수와 함께 사용 가능함)
- visibility 세팅
  - Plain text : expo 웹사이트와 EAS CLI, logs에서 (감춰져있지 않고) 그냥 그대로 다 보임
  - Sensitive : 빌드와 워크플로우 잡 로그에서 볼 수 없음(난독화되어있음). 웹사이트에서는 보게 만들 수 있음. EAS CLI에서도 볼 수 있음.
  - Secret : EAS 서버 밖에서 볼 수 없음. (빌드와 워크플로우 잡 뿐만아니라, 웹사이트, EAS CLI에서 확인 불가) 최종적으로 앱에 포함되는 값의 추가 보안이 지원되는 개념은 아님
- visibility가 허락하면 로컬 개발환경에 땡겨올 수 있음.

#### 환경변수의 노출 문제

> 클라이언트 측 코드에 포함된 모든 것은 공개적이며 앱을 실행할 수 있는 누가나 읽을 수 있다고 항상 기억해야함. 앱을 디컴파일하거나 네트워크 요청을 분석하는 방법으로 쉽게 얻을 수 있음.

해결방법 1. 서버 사이드 프록시 사용

- 클라이언트 앱에서 직접 API에 접근하는 대신, 서버를 통해 요청을 전달하는 방법. 요청시 google signin 정보를 같이 보내서 정당한지 확인하는 법이 있을까?
- Firestore에 접근할 때 Google Sign-In 정보를 사용하여 보안 규칙을 설정하면, 인증된 사용자만 특정 데이터에 접근할 수 있도록 제한할 수 있습니다. 이를 통해 데이터의 보안성을 높이고, 사용자 경험을 개선할 수 있습니다. 추가적인 질문이 있거나 더 알고 싶은 내용이 있으면 언제든지 말씀해 주세요!
  해결방법 2. API 모니터링 밑 사용제한

#### 환경 변수 만들고 사용하기

이 후 설명에서는 아래의 환경변수를 사용함.

- `EXPO_PUBLIC_API_URL`(plain, text) : API 서버의 URL이 들어있는 `EXPO_PUBLIC_` 변수
- `APP_VARIANT`(plain, text) : app variant(?)를 선택하기 위한 변수
- `GOOGLE_SERVICE_JSON`(secret, file) : `.gitignore`에 등록되어있는 `google-services.json` 파일을 빌드할 때 사용하기 위한 파일
- `SENTRY_AUTH_TOKEN`(sensitive variable) : 빌드와 업데이트 이후 소스맵을 업데이트 하기위해 사용되는 Sentry를 위한 authentication token

#### 코드에서 환경변수 사용하기

###### `EXPO_PUBLIC_` prefix 변수

- `EXPO_PUBLIC_` prefix가 붙은 환경 변수는 **내 앱(클라이언트 사이드)에서** `process.env.variables`로 사용 가능함.
- `EXPO_PUBLIC_`을 붙이고 내앱(클라이언트 사이드)에서 아무곳에서나 사용한다.

> `EXPO_PUBLIC_` 변수는 컴파일된 앱에 plain-text로 보이기 때문에 private 키등은 이걸로 설정하지 말아야 한다.

###### `EXPO_PUBLIC_` prefix 가 **아닌** 변수

- app config 해석 중에사용.
- 예로 `development, preview, production`에 따라 앱의 bundleID와 앱의 이름을 지정할 때 사용

```js
const IS_DEV = process.env.APP_VARIANT === "development";
const IS_PREVIEW = process.env.APP_VARIANT === "preview";

const getUniqueIdentifier = () => {
  if (IS_DEV) {
    return "com.yourname.stickersmash.dev";
  }

  if (IS_PREVIEW) {
    return "com.yourname.stickersmash.preview";
  }

  return "com.yourname.stickersmash";
};

const getAppName = () => {
  if (IS_DEV) {
    return "StickerSmash (Dev)";
  }

  if (IS_PREVIEW) {
    return "StickerSmash (Preview)";
  }

  return "StickerSmash: Emoji Stickers";
};

export default {
  name: getAppName(),
  ios: {
    bundleIdentifier: getUniqueIdentifier(),
  },
  android: {
    package: getUniqueIdentifier(),
  },
};
```

#### EAS Build시 어떤 환경을 쓸지 지정하는 방법

eas.json 의 "build"안에 이름 및 environment를 지정

```
{
  "build": {
    "development": {
      "environment": "development"
    },
    "preview": {
      "environment": "preview"
    },
    "production": {
      "environment": "production"
    },
    "my-profile": {
      "environment": "production"
    }
  }
}

```

- secret 타입의 환경 변수는 빌드 **config 해석에서 사용될 수 없음.**

#### EAS Update

- expo 앱의 업데이트를 배포할때 사용됨.
- 간편하게 앱을 업데이트 할 수 있음
- 이 명령어를 사용하면 사용자는 앱을 다시 설치할 필요없이 최신 변경 사항을 즉시 사용할 수 있음?
- 짐작해보면, eas 서버에 환경변수를 사용자앱에서 직접 가져다 쓸 수 있는 개념인것 같다...

```
# production 배포
eas update --environment production
```

`--environment` 플래그를 사용하면, 지정하지 않으면 `.env` 파일의 내용으로 업데이트가 되는데, 그러면 문제가 생길수 있으니, `--environment` 플래그를 잘 사용하자!

#### 몇가지 의문

- 각기 다른 앱스토어 릴리즈를 위해 다른 번들 식벽자를 사용할 수 있다는 말이 `development, preview, production` 안에서도 또 나누어서 환경변수를 사용할 수 있다는 얘기인가?
  - 그런듯. 그런데 애플과 안드로이드까지 구분할 필요는 없을듯. 예제에서도 그렇게하지 않고 더 복잡할 것 같다.
- visibility가 허락하면 로컬 개발환경에 땡겨올 수 있다? 어디까지 허락하는건가? EAS CLI가 기준인가?
  - 일단 로컬에 환경변수를 굳이 둘 필요가 없기 때문에 pass
- sentry가 뭔가?
  - 앱에서 발생하는 오류나 예외를 실시간으로 수집하여 개발자에게 알려, 문제를 신속하게 파악하고 해결 할 수 있음, 이벤트와 스택 트레이스를 기록하기도 해서 분석을 편하게 함.
  - 성능 분석하여 느린 요청이나 병목 현상을 찾아낼 수 있음
- expo, eas, 환경변수에서 EXPO*PUBLIC*를 붙였을 때 좋은점이 뭔가?
  - 코드 가독성 : 명시적으로 공개할 변수를 구분할 수 있다 (`EXPO_PUBLIC_API_URL` vs `API_SECRET`)
  - 자동 노출(?) : 환경 변수를 수동으로 설정할 필요가 없다?
  - process.env는 클라이언트 애플리케이션에서 직접 접근할 수 없다? 서버 사이드쪽에사 사용된다?
  - 왜 process.env를 서버사이드에서만 사용가능함?
  - process.env는 Node.js의 런타임 환경에서 사용되는 객체로 서버의 환경변수를 읽는데 사용됨. 클라이언트 앱은 사용자의 브라우저나 모바일장치에서 실행되는데, 이 환경에서는 process.env가 정의되어있지 않거나 사용할수 없음?
- eas에서 secret으로 설정된 API 키 환경변수는 앱이 빌드되면 plain text로 안보이나?
  - EAS(Expo Application Services)에서 secret으로 설정된 API 키 환경변수는 앱이 빌드될 때 plain text로 노출되지 않습니다. 이 환경변수는 빌드 프로세스 중에 안전하게 처리되며, 실제 앱 코드나 리소스에 포함되지 않습니다. 따라서, 앱이 설치된 후에는 해당 키를 직접적으로 확인할 수 없습니다.
  - 클라이언트쪽에 포함되면 결국 보이는건 맞음.
  - secret 환경 변수는 EAS Build나 워크플로우 잡에서 쓰이기 위해 만들어진거임. (예. `NPM_TOKEN` : npm으로 private package설치에 사용, `Sentry API key` 소스맵을 업로드하거나 릴리즈를 생성시 사용)
  - 최종적으로 앱 자체에 포함되는 값에 대한 추가 보안을 제공하지 않음.
- 그럼 local에서 환경변수를 굳이 가지고 있을 필요가 있을까?
  - 꼭 필요하지는 않음. 개발 중에 필요한 환경변수는 로컬에 설정해두는게 유용할 수 있음. 이런게 있나?
- sensitive로 구분되어야 하는 환경변수의 예가 뭐가 있나?
  - API 키
