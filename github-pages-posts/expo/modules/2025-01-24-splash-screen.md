---
layout: single
title: "[expo/modules] Splash Screen"
categories: expo/modules
---

#### 참고자료 : https://docs.expo.dev/versions/latest/sdk/splash-screen/

> 주의1 : splash 이미지 변경은 expo go 앱에서는 확인 불가능함. 이미지 변경 확인을 위해서는 새로 빌드가 필요함
> 주의2 : splash screen은 앱이 실행하면서 처음 나오는 화면으로, 개발모드에서 앱이 로드될 때 나오는 화면과 헷갈리면 안됨. 개발모드의 앱을 처음 실행하면서 나오는 화면이 splash screen이고, 개발모드에서 앱을 로딩하면서 나오는 화면이랑은 다른 개념임

#### expo-splash-screen 설치

```
# 이미 설치되어 있음, package.json 확인시 존재하면 설치된 것임
# npx expo install expo-splash-screen
```

#### splash 이미지(다른 예제를 보니 1024x1024 px 사이즈 사용함)

`assets/images/splash-icon.png` 덮어쓰기

#### app.json 확인 (plugins/expo-splash-screen에서 물고 들어감)

```json
{
  "expo": {
    "plugins": [
      [
        "expo-splash-screen",
        {
          "image": "./assets/images/splah-icon.png",
          "imageWidth": 200,
          "resizeMode": "contain",
          "backgroundColor": "#ffffff"
        }
      ]
    ]
  }
}
```

#### App.tsx 내용 수정

> 아래 내용을 사용해 user session 확인 후, 원하는 screen으로 라우팅

```
import * as SplashScreen from "expo-splash-screen";
import { useEffect } from "react";
// import { router } from "expo-router";

SplashScreen.preventAutoHideAsync();

// Set the animation options. This is optional.
SplashScreen.setOptions({
  duration: 1000,
  fade: true,
});

const doSomething = async () => {};

export default function IndexScreen() {
  useEffect(() => {
    const initialze = async () => {
      doSomething();
      SplashScreen.hide();
      // router.replace("/home");
    };

    initialze();
  }, []);

  return null;
}
```

#### expo go에서 splash screen이 변경 안됨

> development 버전으로 eas build 실행해보니 변경됨!

- 개발시 앱 로딩시 나오는 화면은 splash screen이 아니고, 다른 것 같음. 이거랑 splash screen이랑 헷갈렸음.
- splash screen이 앱이 실행되면서 나오는 화면인데, expo go의 splash screen은 이미 정해져있는 것 같음.

```bash
eas build --profile development --platform android
```
