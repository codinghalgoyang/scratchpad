---
layout: single
title: "[expo] development & production 환경 분리 내용 정리"
categories: expo
published: false
---

## 결론

- development, preview, production을 모두 사용한다.
- development는 expo의 developmentClient를 위한 것
- preview는 app의 테스트 환경(DB등)을 사용하기 위해 development와 환경변수를 모두 공유한다.
- production은 따로 환경을 사용한다.

#### EAS credentials (development/production 구분 & android/ios 구분 필요)

- 빌드 및 배포 과정에 필요한 sha-1 를 eas credentials를 사용해 생성 가능
- eas credentials는 development / production 및 android / ios 를 구분하여 총 4개를 생성
- 각 생성된 SHA-1 finger print를 firebase development / production 프로젝트의 android / ios에 각각 넣어준다
- 아마 새로 google service json, plist 파일 받는게 필요해보임.

```bash
eas credentials
# android / ios 선택
# development / production 선택
# 일단 확인해보니 android의 development와 production은 같은 sha-1을 뱉는다.
> build를 할 때 package 명이 달라지니 credentials도 새로 만든다. 이걸 사용하자
```

> build를 할 때 package 명이 달라지니 credentials도 새로 만든다. 이걸 사용하자

파일을 다운로드 후, 업로드를 해준다. (이미 환경변수가 있을 경우, 명령어 입력후 Overwrite가 띄면 Y 선택 )

```bash
# GOOGLE_SERVICES_JSON : {firebase > development | production 프로젝트 > 프로젝트설정 > Android 앱 > google-services.json}
eas env:create --name GOOGLE_SERVICES_JSON --environment development --environment preview --scope project --type file --visibility secret --value google-services-dev.json

eas env:create --name GOOGLE_SERVICES_JSON --environment production --scope project --type file --visibility secret --value google-services.json

# GOOGLE_SERVICES_INFOPLIST : {firebase > development | production 프로젝트 > 프로젝트설정 > Apple 앱 > GoogleService-Info.plist}
eas env:create --name GOOGLE_SERVICES_INFOPLIST --environment development --environment preview --scope project --type file --visibility secret --value GoogleService-Info-dev.plist

eas env:create --name GOOGLE_SERVICES_INFOPLIST --environment production --scope project --type file --visibility secret --value GoogleService-Info.plist
```

- GOOGLE_SERVICES_JSON, GOOGLE_SERVICES_INFOPLIST는 app.config.js 에서 사용

#### EAS 환경변수

###### Plain Text (보여져도 되는 부분)

- development / production 구분 필요
  - APP_NAME : app.config.js에서 사용할 앱 이름
  - APP_PACKAGE_NAME : app.config.js에서 사용할 앱 package 이름

```
# APP_NAME : "appname (dev)" / "appname"
eas env:create --scope project --environment development --environment preview --type string --visibility plaintext --name APP_NAME --value "{appname} (dev)"
eas env:create --scope project --environment production --type string --visibility plaintext --name APP_NAME --value "{appname}"

# APP_PACKAGE_NAME : "com.company.appname.dev" / "com.company.appname"
eas env:create --scope project --environment development --environment preview --type string --visibility plaintext --name APP_PACKAGE_NAME --value "com.{companyname}.{appname}.dev"
eas env:create --scope project --environment production --type string --visibility plaintext --name APP_PACKAGE_NAME --value "com.{companyname}.{appname}"
```

###### Sensitive (Client 코드에서 사용되면 결국 노출 되는 부분)

- ADMOB 관련 (android, ios 구분은 있지만, development, production 공통 사용)
  - ADMOB_ANDROID_APP_ID : admob에서 만든 안드로이드 프로젝트의 앱 ID"
  - ADMOB_IOS_APP_ID : admob에서 만든 ios 프로젝트의 앱 ID
  - ADMOB_ANDROID_xxx_AD_ID : admob에서 만든 안드로이드 프로젝트의 광고 단위의 ID (클라이언트 코드에서 사용됨)
  - ADMOB_IOS_xxx_AD_ID : admob에서 만든 ios 프로젝트의 광고 단위의 ID (클라이언트 코드에서 사용됨)

```
# ADMOB_ANDROID_APP_ID
eas env:create --scope project --environment development --environment preview --environment production --type string --visibility sensitive --name ADMOB_ANDROID_APP_ID --value "{admob>안드로이드프로젝트>앱설정>앱ID}"

# ADMOB_IOS_APP_ID
eas env:create --scope project --environment development --environment preview --environment production --type string --visibility sensitive --name ADMOB_IOS_APP_ID --value "{admob>ios프로젝트>앱설정>앱ID}"
```

- Firebase 관련 (development / production 구분 필요) : firebase web의 firebaseConfig 내용으로 채워넣음.
- FIREBASE*CONFIG*\* : web app인 나의 expo app에서 web형식으로 firebase를 사용하기 위한 정보
  - FIREBASE_CONFIG_API_KEY
  - FIREBASE_CONFIG_AUTH_DOMAIN
  - FIREBASE_CONFIG_PROJECT_ID
  - FIREBASE_CONFIG_STORAGE_BUCKET
  - FIREBASE_CONFIG_MESSAGING_SENDER_ID
  - FIREBASE_CONFIG_APP_ID
  - FIREBASE_CONFIG_MEASUREMENT_ID

```
# FIREBASE_CONFIG_ : (firebase development | production 프로젝트> 프로젝트 설정> 웹앱 > SDK 설정참고)
eas env:create --scope project --environment development --environment preview --type string --visibility sensitive --name FIREBASE_CONFIG_API_KEY --value "{}"
eas env:create --scope project --environment development --environment preview --type string --visibility sensitive --name FIREBASE_CONFIG_AUTH_DOMAIN --value "{}"
eas env:create --scope project --environment development --environment preview --type string --visibility sensitive --name FIREBASE_CONFIG_PROJECT_ID --value "{}"
eas env:create --scope project --environment development --environment preview --type string --visibility sensitive --name FIREBASE_CONFIG_STORAGE_BUCKET --value "{}"
eas env:create --scope project --environment development --environment preview --type string --visibility sensitive --name FIREBASE_CONFIG_MESSAGING_SENDER_ID --value "{}"
eas env:create --scope project --environment development --environment preview --type string --visibility sensitive --name FIREBASE_CONFIG_APP_ID --value "{}"
eas env:create --scope project --environment development --environment preview --type string --visibility sensitive --name FIREBASE_CONFIG_MEASUREMENT_ID --value "{}"

eas env:create --scope project --environment production --type string --visibility sensitive --name FIREBASE_CONFIG_API_KEY --value "{}"
eas env:create --scope project --environment production --type string --visibility sensitive --name FIREBASE_CONFIG_AUTH_DOMAIN --value "{}"
eas env:create --scope project --environment production --type string --visibility sensitive --name FIREBASE_CONFIG_PROJECT_ID --value "{}"
eas env:create --scope project --environment production --type string --visibility sensitive --name FIREBASE_CONFIG_STORAGE_BUCKET --value "{}"
eas env:create --scope project --environment production --type string --visibility sensitive --name FIREBASE_CONFIG_MESSAGING_SENDER_ID --value "{}"
eas env:create --scope project --environment production --type string --visibility sensitive --name FIREBASE_CONFIG_APP_ID --value "{}"
eas env:create --scope project --environment production --type string --visibility sensitive --name FIREBASE_CONFIG_MEASUREMENT_ID --value "{}"
```

###### secret (노출 되면 안되는것, appconfig에서만 사용할 것들)

- google service 관련 파일 (development/production 구분 필요) - GOOGLE_SERVICES_INFO_PLIST : Apple 앱을 위한 구글 서비스 관련 정보 - GOOGLE_SERVICES_JSON : Android 앱을 위한 구글 서비스 관련 정보 - GOOGLE_SIGN_IN_CLIENT_ID : google 로그인 위한 웹 클라이언트 ID
  > GOOGLE_SERVICES_INFO_PLIST, GOOGLE_SERVICES_JSON 생성 명령어는 이미 위에 EAS credentials 파트에서 써놓음.

```
# GOOGLE_SIGN_IN_CLIENT_ID : {firebase > development | production 프로젝트 > authentication>로그인방법>Google > 웹SDK구성 > 웹클라이언트ID}
eas env:create --scope project --environment development --environment preview --type string --visibility secret --name GOOGLE_SIGN_IN_CLIENT_ID --value "{}"
eas env:create --scope project --environment production --type string --visibility secret --name GOOGLE_SIGN_IN_CLIENT_ID --value "{}"
```

#### 환경구성

- firebase : “development", "production" 용 프로젝트 구분 생성 필요. 내부적으로 ios, android 시작
- admob : "android", ”ios" 프로젝트만 필요(dev, production 구분 안함)
- eas credentials : "development", "production" 구분 및 그 안에서 "android", "ios" 구분

#### ADMOB 관련 환경변수를 공통으로 사용하는 이유

- development에서는 광고 단위의 ID 대신 testIds를 사용하여 실제 광고가 나오지 않게 함.
- 공통으로 사용하게 되니, ADMOB에서 development, production 환경을 나눠줄 필요가 없어짐. (Android 프로젝트와 ios 프로젝트를 구분해서 세팅은 원래 필요함)

#### firebase에서 왜 ios, android 말고 web을 만들었을까?

> 실제 동작은 web 코드의 firebase를 사용해 돌아감.

- expo 프로젝트가 js를 사용하기 때문에 firebase 를 사용하기 위해 web으로 만든 내용이 실제 동작이 됨. firebaseConfig.ts 내용을 보면 initializeApp, getFirestore 등을 web 기준으로 하고 있음.
- 그럼 반대로 왜 ios, android 가 필요할까?
- 앱을 구성할 때, 구글 서비스(구글 signin, 앱초대등)에 대한 정보를 들고 가는데 이게 ios, andriod 안에 들어있음.
- 이를 생각해보면 firebaseConfig 내용은 어쩔 수 없이 client 코드에 들어가서 노출이 되지만, 노출이 되지 않는 구글 서비스를 활용하여 보안 부분을 강화할 수 있을듯!

#### build

```
eas build --profile development --platform android
eas build --profile production --platform android
```

#### development 환경변수 사용하면서 developmentClient가 아니게 빌드 할 수 있나?

> No! `eas.json`의 build > development > developmentClient를 없애보려고 했으나 실패함.

- development는 진짜 expo의 developmentClient를 위한 것 같다.
- 테스트용 DB를 따로 구분할 필요가 있긴함. -> preview 환경을 이용하자
- build는 preview로 하고 development의 환경을 공유해서 development앱과 preview앱이 같은 테스트 환경을 쓰도록 하자!

#### `FIREBASE_CONFIG_*`와 `ADMOB_*_APP_ID`는 undefined로 나온다.

- EXPO_PUBLIC_APP_VIRANT를 사용하고 생문자열을 넣어보자!
- eas environment 문서에 보면 "The environment variables with the `EXPO_PUBLIC_` prefix are available as process.env variables in your app's code. This means you can use them to dynamically configure your app behavior based on the values from environment variables."라고 나옴.
- app.config.js 가 아닌 앱 내에서 사용할 변수는 모두 `EXPO_PUBLIC_`을 사용해야하는 것으로 보임.
- const IS*DEV = process.env.EXPO_PUBLIC_APP_VARIANT === "development"; EXPO_PUBLIC*도 왜 undefined로 나올까?
- google signin webClientId 설정은 app.config.js에서 이미 해주니깐 굳이 안해줘도 되나? -> Yes, Configure 하는 동작에서 webClientId 직접 안넣어줘도 됨!
- 일단, 지금까지 결과로는 빌드할 때, app.config.js에서 `process.env.`는 동작하나 나머지 코드에서는 동작하지 않는 것 같다.
- 그냥 코드에서는 `__DEV__` 로 development 구분이 가능한 것 같다. 그럼 그냥 코드에서는 `__DEV__`로 구분하자!

> 정리하면, eas environment는 app.config.js에서만 읽을 수 있고, 안읽히는 항목도 존재한다.(ADMOB_APP_ID)
> 클라이언트 코드에서 development 구분이 필요할땐, `__DEV__`를 사용한다.
> 기존에 production에서 안된 이유는 google-service.json을 업데이트 안하면서 sha-1 키가 development꺼로 들어가면서 firebase 접근이 안된것 같다.

- 다시 정리, `__DEV__`를 사용하면 preview 환경도 development가 아니기 때문에 production의 환경을 사용하게 된다.
- 다른게 있는지 확인해봤으나, 없다.
- 그런데, development와 preview 환경이 합쳐지는게 맞는지도 의문이 든다.
- development로 만들어놓은 앱의 장점은 hotreload인데 preview로 덮어쓰면 development를 다시 빌드하거나 예전껄 받아야하니깐 이것도 이상하다.

> 결론. 그냥 development랑 production만 나눈다. (preview 노 사용)
> 테스트가 필요할 경우, production에서 특정 DB만 직접 골라 삭제하는 방식을 사용한다!

env가 필요한경우, EXPO*PUBLIC* 방법말고 고전 방법(expo-constants)이 존재하고, 그걸 사용하자
