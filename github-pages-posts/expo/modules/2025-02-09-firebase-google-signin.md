---
layout: single
title: "[expo/modules] firebase google signin"
categories: expo/modules
published: false
---

#### Firebase 프로젝트 만들기

- ios 앱 추가
  - Apple bundle ID : com.codinghalgoyang.{appname}
  - App nickname : {appname}
  - Download GoogleService-info.txt
  - 나머지는 넘기기
- android 앱 추가
  - Android package name : com.codinghalgoyang.{appname}
  - App nickname : {appname}
  - 아래 내용에 따로 SHA1 복사하여 붙여넣기
  - Download google-services.json
  - 나머지 넘기기

```
eas build:configure
# EAS build for All

# eas(expo-dev-client 문서) 내용을 진행하여
# app.json에 android package와 apple의 bundleID가 들어갈수 있도록 하고 아래 과정 진행

eas credentials
# Select platform > Android
# profile > development # 이 부분은 나중에 production으로 변경 필요
# 여기에서 나오는 SHA1 복사하여 android쪽에 추가
# What do you want to do? > Exit
```

#### google-signin 설치

```
npx expo install @react-native-google-signin/google-signin
```

app.json 수정

```json
"expo": {
	"plugins": [
		"@react-native-google-signin/google-signin"
	]
}
```

#### FIrebase 콘솔 > Authentication

- 구글 제공업체 추가
  - Enable 후 Save

#### 업로드 secret file to eas

```
eas secret:create --scope project --name GOOGLE_SERVICES_JSON --type file --value {google-services.json 경로}

eas secret:create --scope project --name GOOGLE_SERVICES_INFOPLIST --type file --value {GoogleService-Info.plist 경로}
```

app.json 파일 이름 수정 > app.config.js
app.config.js 내용을 js 형태(`export default`)로 바꿔주고, 아래 내용 추가

```js
export default {
	expo: {
		ios: {
			googleServiceFile: process.env.GOOGLE_SERVICES_INFOPLIST,
		}
		android: {
			googleServiceFile: process.env.GOOGLE_SERVICES_JSON,
		}
	}
}
```

#### development build

```
eas build -p android --profile development
```

index.tsx 내용 추가

```jsx
import {
  GoogleSignin,
  GoogleSigninButton,
  SignInResponse,
} from "@react-native-google-signin/google-signin";
import { useEffect, useState } from "react";
import { Button, StyleSheet, Text, View } from "react-native";

export default function IndexScreen() {
  const [error, setError] = useState<Error | null>(null);
  const [userInfo, setUserInfo] = useState<User | null>(null);

  const checkUserSession = async () => {
    try {
      const userInfo = await GoogleSignin.getCurrentUser();
      if (userInfo) {
        console.log("사용자가 로그인 상태입니다:", userInfo);
        setUserInfo(userInfo);
      } else {
        console.log("사용자가 로그인하지 않았습니다.");
      }
    } catch (error) {
      console.error("세션 확인 중 오류 발생:", error);
    }
  };

  const signin = async () => {
    try {
      await GoogleSignin.hasPlayServices();
      const user = await GoogleSignin.signIn();
      setUserInfo(user);
      setError(null);
    } catch (e) {
      setError(e as Error);
    }
  };

  const logout = () => {
    setUserInfo(null);
    GoogleSignin.revokeAccess();
    GoogleSignin.signOut();
  };

  useEffect(() => {
    GoogleSignin.configure({
      // TODO: 이 부분은 나중에 환경변수 같은걸로 뺴야할듯?
      webClientId: "{firebase 콘솔 > athentication > 로그인방법 > 웹SDK구성 > 웹 클라이언트 ID}"
    });
    checkUserSession();
  }, []);

  return (
    <View style={styles.container}>
      {error && <Text>error : {JSON.stringify(error)}</Text>}
      {userInfo && <Text>{JSON.stringify(userInfo.data?.user)}</Text>}
      {userInfo ? (
        <Button title="Logout" onPress={logout} />
      ) : (
        <GoogleSigninButton
          size={GoogleSigninButton.Size.Standard}
          color={GoogleSigninButton.Color.Dark}
          onPress={signin}
        />
      )}
      <Text>Index Screen</Text>
    </View>
  );
}



const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
  },
});
```
