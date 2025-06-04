---
layout: single
title: "[expo/modules] AsyncStorage (Local Storage) 사용하기"
categories: expo/modules
---

> Expo SDK 41 이상에서는 `@react-native-async-storage/async-storage`를 사용해야함.

#### 설치

```
npx expo install @react-native-async-storage/async-storage
```

#### 빌드

위 패키지 설치 후 재빌드가 필요함.

- 따로 plugin을 설정해줄 필요는 없었음.

```sh
npx eas-cli build --profile development
#eas build --profile development --platform android
#eas build --profile development --platform ios
```

#### 예제 코드

```typescript
import React, { useEffect } from "react";
import { View, Text, Button } from "react-native";
import AsyncStorage from "@react-native-async-storage/async-storage";

const App = () => {
  const storeData = async (value) => {
    try {
      await AsyncStorage.setItem("@storage_Key", value);
    } catch (e) {
      // 저장 실패
      console.error(e);
    }
  };

  const getData = async () => {
    try {
      const value = await AsyncStorage.getItem("@storage_Key");
      if (value !== null) {
        console.log(value); // 저장된 데이터 출력
      }
    } catch (e) {
      // 읽기 실패
      console.error(e);
    }
  };

  useEffect(() => {
    // 컴포넌트가 마운트될 때 데이터 불러오기
    getData();
  }, []);

  return (
    <View>
      <Text>AsyncStorage 예제</Text>
      <Button title="데이터 저장" onPress={() => storeData("Hello World")} />
    </View>
  );
};

export default App;
```
