---
layout: single
title: "[expo/modules] google admob"
categories: expo/modules
---

## 구글 애드몹

- 예전에는 expo 앱에서 expo-ads-admob 라이브러리를 쓸 수 있었지만, Expo SDK 46 부터 지원하지 않는다고 함.
- react-native-google-mobile-ads 사용해야 함.

#### 구글 Admob 가입

[https://admob.google.com/home/](https://admob.google.com/home/)

- 왼쪽 탭 중 지급에서 지급 정보도 추가 필요

#### 구글 Admob ID 추가

- Google Admob
  - 앱추가
    - 플랫폼 : Android
    - 지원되는 앱 스토어에 앱이 등록되어 있나요? 아니요
  - 앱추가
    - 플랫폼 : iOS
    - 지원되는 앱 스토어에 앱이 등록되어 있나요? 아니요

> Admob App ID는 development에 따라 구분하지 않아도 되므로, platform에 따라 2개만 만들어줌

- Admob 에서 패키지 명이 달라도 같은 Id를 사용하는 것을 허락해줌.
- 대신 development 경우 코드 내에서 `__DEV__`인 경우TestID를 사용해야함.

#### 광고 단위 추가 (android, ios 별 진행 필요)

- 사이트 탭 중 앱 > 앱 선택 > 앱 개요 > 광고 단위 추가
- 광고 유형 선택
- 광고 단위 이름 입력
- 파트너 입찰 (X)
- 나머지 기본으로 놓고 광고 단위 만들기

> 배너광고형태를 넣을 꺼면 UI를 커스터마이징할 수 있는 NativeAd를 추천

#### 개념 정리

- AppId 광고를 사용할 앱에 대한 ID로 platform 당 한 개씩 존재함
- 광고 ID 해당 App 안에서 사용할 광고들에 대한 개별 ID
  - 같은 형식의 광고(예. 배너)라도 앱내 위치에 따라 구분해서 만들어주면, 추후 광고 위치에 따른 수익률을 볼 수 있으므로 따로 구별해 주는 것을 추천

#### 패키지 설치

```
npx expo install react-native-google-mobile-ads
npx expo install expo-build-properties
```

#### app.config.ts (구글 Admob App ID 추가)

```ts
export default {
  expo: {
    ios: {
      infoPlist: {
        ITSAppUsesNonExemptEncryption: false,
      },
    },
    plugins: [
      [
        "react-native-google-mobile-ads",
        {
          // Google Admob 사이트 > 앱 > 앱 ID
          androidAppId: "{androidAppId}",
          iosAppId: "{iosAppId}",
        },
      ],
      [
        "expo-build-properties",
        {
          ios: {
            useFrameworks: "static",
          },
        },
      ],
    ],
  },
};
```

#### Build

> react-native-google-mobile-ads 패키지 또한 expo go 앱에 기본 포함 내용이 아니므로, development용으로 재빌드하여 사용이 필요함.

```
eas build --profile development --platform android # android만 빌드
eas build --profile development --platform ios # ios 만 빌드
```

---

#### 유럽 사용자 동의 (optional)

유럽 사용자에게 광고를 제공하기 전에 동의가 필요하고, 아래는 그 동의 받는 코드임.

```tsx
import {
  getTrackingPermissionsAsync,
  PermissionStatus,
  requestTrackingPermissionsAsync,
} from "expo-tracking-transparency";

const { status } = await getTrackingPermissionsAsync();

if (status === PermissionStatus.UNDETERMINED) {
  await requestTrackingPermissionsAsync();
}

const adapterStatuses = await mobileAds().initialize();

// Now ads can be loaded.
```

> 개발 버전에서는 테스트 아이디를 사용하도록 코드가 구성됨

## 광고 유형

#### 배너 광고

- 일반 배너 광고, 앱 UI랑 잘 맞지않아 나는 테스트 용도로만 사용

```tsx
import { Platform, Text, View } from "react-native";
import {
  BannerAd,
  BannerAdSize,
  TestIds,
} from "react-native-google-mobile-ads";

const adUnitId = __DEV__
  ? TestIds.ADAPTIVE_BANNER
  : Platform.OS === "android"
  ? "{android banner ad id}"
  : "{ios banner ad id}";

export default function Index() {
  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        alignItems: "center",
      }}
    >
      <BannerAd
        unitId={adUnitId}
        size={BannerAdSize.ANCHORED_ADAPTIVE_BANNER}
        {% raw %}
        requestOptions={{
          networkExtras: {
            collapsible: "bottom",
          },
        }}
        {% endraw %}
      />
    </View>
  );
}
```

#### 네이티브 광고 : 광고 콘텐츠(제목, 내용, 이미지지를 받아 UI를 커스터마이징할 수 있는 광고

- 코드 : https://docs.page/invertase/react-native-google-mobile-ads/native-ads

#### AppOpen 광고 : 앱 로드 화면에 띄우는 광고

- 광고를 표시하기 전에 미리 로드 필요함. 그래야 사용자가 앱을 열 때 광고가 즉시 표시
- best practice
  - 앱을 몇 번 사용한 후에 오픈 광고를 띄우기
  - 앱이 로드 되는 동안(기다리는 시간)에 오픈 광고를 표시
  - 로딩이 다 되면 광고도 종료하고 매끄럽게 전환

```tsx
import {
  AppOpenAd,
  TestIds,
  AdEventType,
} from "react-native-google-mobile-ads";

const adUnitId = __DEV__
  ? TestIds.APP_OPEN
  : Platform.OS === "ios"
  ? "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy" // ios ad unit id
  : "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy"; // android ad unit id

const appOpenAd = AppOpenAd.createForAdRequest(adUnitId, {
  keywords: ["fashion", "clothing"],
});

// Preload an app open ad
appOpenAd.load();

// Show the app open ad when user brings the app to the foreground.
appOpenAd.show();
```

#### Interstitial 광고 : 전면 광고

- 광고를 표시하기 전에 미리 로드하여 필요시 광고가 표시될 수 있도록 하기
- 게임 앱에서 레벨이 완료된 후나 게임 오버 시점등에 표시
- 광고 로드 Event나 오류 발생 Event를 리스너 메서드로 처리하여, 광고 상태를 모니터링하고, 적절한 시점에 광고를 표시

```tsx
import React, { useEffect, useState } from "react";
import { Button, Platform, StatusBar } from "react-native";
import {
  InterstitialAd,
  AdEventType,
  TestIds,
} from "react-native-google-mobile-ads";

const adUnitId = __DEV__
  ? TestIds.INTERSTITIAL
  : Platform.OS === "ios"
  ? "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy" // ios ad unit id
  : "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy"; // android ad unit id

const interstitial = InterstitialAd.createForAdRequest(adUnitId, {
  keywords: ["fashion", "clothing"],
});

function App() {
  const [loaded, setLoaded] = useState(false);

  useEffect(() => {
    const unsubscribeLoaded = interstitial.addAdEventListener(
      AdEventType.LOADED,
      () => {
        setLoaded(true);
      }
    );

    const unsubscribeOpened = interstitial.addAdEventListener(
      AdEventType.OPENED,
      () => {
        if (Platform.OS === "ios") {
          // Prevent the close button from being unreachable by hiding the status bar on iOS
          StatusBar.setHidden(true);
        }
      }
    );

    const unsubscribeClosed = interstitial.addAdEventListener(
      AdEventType.CLOSED,

      () => {
        if (Platform.OS === "ios") {
          StatusBar.setHidden(false);
        }
      }
    );

    // Start loading the interstitial straight away

    interstitial.load();

    // Unsubscribe from events on unmount

    return () => {
      unsubscribeLoaded();

      unsubscribeOpened();

      unsubscribeClosed();
    };
  }, []);

  // No advert ready to show yet

  if (!loaded) {
    return null;
  }

  return (
    <Button
      title="Show Interstitial"
      onPress={() => {
        interstitial.show();
      }}
    />
  );
}
```

#### Rewarded 광고 : 보상형 광고

```tsx
import React, { useEffect, useState } from "react";

import { Button } from "react-native";

import {
  RewardedAd,
  RewardedAdEventType,
  TestIds,
} from "react-native-google-mobile-ads";

const adUnitId = __DEV__
  ? TestIds.REWARDED
  : Platform.OS === "ios"
  ? "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy" // ios ad unit id
  : "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy"; // android ad unit id

const rewarded = RewardedAd.createForAdRequest(adUnitId, {
  keywords: ["fashion", "clothing"],
});

function App() {
  const [loaded, setLoaded] = useState(false);

  useEffect(() => {
    const unsubscribeLoaded = rewarded.addAdEventListener(
      RewardedAdEventType.LOADED,

      () => {
        setLoaded(true);
      }
    );

    const unsubscribeEarned = rewarded.addAdEventListener(
      RewardedAdEventType.EARNED_REWARD,

      (reward) => {
        console.log("User earned reward of ", reward);
      }
    );

    // Start loading the rewarded ad straight away

    rewarded.load();

    // Unsubscribe from events on unmount

    return () => {
      unsubscribeLoaded();

      unsubscribeEarned();
    };
  }, []);

  // No advert ready to show yet

  if (!loaded) {
    return null;
  }

  return (
    <Button
      title="Show Rewarded Ad"
      onPress={() => {
        rewarded.show();
      }}
    />
  );
}
```

`serverSideVerificationOptions`를 사용하면, 커스텀 데이터를 넣을 수 있음. (나중에 추가적인 분석을 위해서 쓰이는듯?)

```tsx
const rewardedAd = RewardedAd.createForAdRequest(adUnitId, {
  serverSideVerificationOptions: {
    userId: "9999",

    customData: "my-custom-data",
  },
});
```

> RewardedInterstitialAd 라는게 따로 또있음. RewardedAd랑 무슨 차이인지 확인 필요. 자연스러운 중단이나 전환 중에 광고를 시청한 사용자에게 리워드를 제공하는 방식이라고 함. 사용방법은 문서 참고!

#### 참고자료

- https://nonmajor-be-developer.tistory.com/entry/React-Native-Expo%EC%97%90-google-AdMob%EC%9D%84-%EB%84%A3%EC%96%B4%EB%B3%B4%EC%9E%90
- https://www.youtube.com/watch?v=2f0AJ0O_45k
- https://docs.page/invertase/react-native-google-mobile-ads
- https://docs.page/invertase/react-native-google-mobile-ads/displaying-ads
