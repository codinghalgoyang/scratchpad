---
layout: single
title: "[expo/modules] 하이브리드앱을 위한 Web View"
categories: expo/modules
---

#### 패키지 설치

```bash
npx expo install react-native-webview
npx expo install react-native-safe-area-context
```

#### 코드

> `_layout.tsx` : Stack.Screen의 options를 설정해, header를 보이지 않게 한다

```tsx
import { Stack } from "expo-router";

export default function RootLayout() {
  return (
    <Stack>
      <Stack.Screen
        name="index"
        options={{
          headerShown: false,
        }}
      />
    </Stack>
  );
}
```

> `index.tsx` : WebView를 전체화면으로 보이게 하고, 유저경험을 위한 사항들을 추가한다.

- 안드로이드에서는 이전버튼을 눌렀을 때 앱종료 대신 webview에서 이전화면으로 돌아갈 수 있어야함.
- ios 사용자경험을 위해 스와이프 제스처를 통해 앞으로/뒤로 이동을 지원할 수 있어야함.
- 페이지가 로딩중일 때, 페이지를 찾지못했을 때 처리가 필요함.

```tsx
import { StatusBar } from "expo-status-bar";
import React, { useEffect, useRef, useState } from "react";
import {
  ActivityIndicator,
  BackHandler,
  Platform,
  StyleSheet,
} from "react-native";
import { SafeAreaProvider, SafeAreaView } from "react-native-safe-area-context";
import { WebView } from "react-native-webview";

export default function Index() {
  const webViewRef = useRef<WebView>(null);
  const [canGoBack, setCanGoBack] = useState(false);

  const onAndroidBackPress = () => {
    if (webViewRef.current) {
      if (canGoBack) {
        webViewRef.current.goBack();
        return true; // prevent default behavior (exit app)
      }
    }

    return false;
  };

  useEffect(() => {
    if (Platform.OS === "android") {
      const backHandler = BackHandler.addEventListener(
        "hardwareBackPress",
        onAndroidBackPress
      );

      return () => {
        backHandler.remove();
      };
    }
  }, [canGoBack]);

  return (
    <SafeAreaProvider>
      <StatusBar style="auto" />
      <SafeAreaView
        style={{
          flex: 1,
        }}
      >
        <WebView
          ref={webViewRef}
          source={{ uri: "https://www.naver.com" }}
          style={styles.webview}
          // 로딩 중 표시
          renderLoading={() => (
            <ActivityIndicator
              color="#0000ff"
              size="large"
              style={styles.loadingIndicator}
            />
          )}
          startInLoadingState={true} // 초기 로딩 시 `renderLoading`을 호출하도록 설정
          // 오류 처리
          onError={(syntheticEvent) => {
            const { nativeEvent } = syntheticEvent;
            console.warn("WebView error: ", nativeEvent);
            // 사용자에게 오류 메시지를 보여주거나 다른 페이지로 리디렉션하는 로직 추가
            alert("웹페이지 로딩 중 오류가 발생했습니다. 다시 시도해주세요.");
          }}
          // 네비게이션 상태 변경 시 (뒤로 가기, 앞으로 가기 등)
          onNavigationStateChange={(navState) => {
            console.log("Navigation State Changed:", navState);
            setCanGoBack(navState.canGoBack);
          }}
          // ios 스와이프 탐색(앞으로/뒤로) 제스처 허용
          allowsBackForwardNavigationGestures={true}

          // 자바스크립트 주입 (필요한 경우)
          // injectedJavaScript={`
          //   window.ReactNativeWebView.postMessage("Hello from WebView");
          // `}
          // onMessage={(event) => {
          //   console.log("Message from WebView:", event.nativeEvent.data);
          // }}
        />
      </SafeAreaView>
    </SafeAreaProvider>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  webview: {
    flex: 1,
  },
  loadingIndicator: {
    position: "absolute",
    left: 0,
    right: 0,
    top: 0,
    bottom: 0,
    justifyContent: "center",
    alignItems: "center",
  },
});
```

#### 주의사항

- container 스타일에 `justifyContent: "center"`와 `alignItems: "center"`가 들어가 있을 땐, webview가 쪼그라들어서 보이지 않았음
- `StatusBar`가 react-native 것이 아니라, expo-status-bar 의 것이고, 이게 자동으로 인식이 안되서 수동으로 넣어줬음.
- `SafeaAreaView` 도 react-native 것이 아니라, react-native-safe-area-context의 것이고, `SafeAreaProvider`로 감싸주는 것이 필요함.

> 안드로이드, ios 사용자 고려한 추가 내용 필요! 예) 앞으로, 뒤로 버튼에 대한 제스쳐 또는 버튼에 대한 처리가 필요함, 페이지가 안떳을 때도 처리가 필요함

- allowsBackForwardNavigationGestures은 ios의 스와이프 제스처를 통한 앞으로/뒤로 페이지 이동
- BackHandler를 사용한 부분은 안드로이드의 이전 버튼에 대한 ba

#### 참고자료

- https://velog.io/@posinity/react-native-expo%EB%A1%9C-%EC%9B%B9%EB%B7%B0-%EC%95%B1-%EA%B0%9C%EB%B0%9C%ED%95%B4%EB%B3%B4%EA%B8%B0
