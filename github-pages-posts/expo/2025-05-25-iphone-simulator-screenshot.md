---
layout: single
title: "[expo] iphone simulator build 및 스크린샷"
categories: expo
---

simulator에서 돌리기 위해서는 아래 설정이 필요함.

eas.json

```json
{
  "build": {
    "development": {
      "ios": {
        "simulator": true
      }
    }
  }
}
```

빌드

```
eas build --profile development --platform ios
```

#### 설치

```
eas build:run -p ios
```

#### 실행

```
# 위 내용 진행하면 실행은 되고, 개발 서버를 열어줘야해서 아래 내용도 함께 실시
npx expo start
```

> 이상한 점은 로그아웃하면 다시 로그인이 안됨. -> 시뮬레이터 > Device > Erase All Content and Settings 하면 다시 되긴 함.
