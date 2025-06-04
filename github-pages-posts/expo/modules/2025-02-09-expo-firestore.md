---
layout: single
title: "[expo/modules] firestore 사용하기"
categories: expo/modules
published: false
---

Firebase 프로젝트를 사용하는 방법은 2가지가 있음.

1. Firebase JS SDK 사용 : 빠른 시작, Firestore, 인증등 기본 동작 지원
2. React Native Firebase 사용 : 추가적인 Firebase Service(Dynamic Links, Crashytics 등), Firebase Anlytics, 좀 더 강력함.

React Native는 2가지 모두 지원함.

> 구글 로그인 인증 사용시 2번 방법을 사용하도록 가이드를 봐서, 2번 방법으로 firestore도 진행하려고 했으나, 빌드시 에러가 발생함.

구글 인증시 2번 방법을 사용했던 이유는 `@react-native-google-signin/google-signin` 라이브러리를 사용하기 위해서였음.

그냥 1번 방법으로 진행!

react native 자료를 보니, 1번 읽을땐 get()을 사용하고, realtime으로 읽고 싶을 땐, onSnapshot()을 사용함.

https://docs.expo.dev/guides/using-firebase/ 참고

#### Firebase firestore 시작하기

> 개발모드로 해야 read, write가 허용됨!

#### SDK 설치

```
npx expo install firebase
```

#### Firebase console에서 Web 추가

- Firebase SDK 관련 내용 복사

#### firebaseConfig.js

```tsx
import { initializeApp } from "firebase/app";

// Optionally import the services that you want to use
// import {...} from 'firebase/auth';
// import {...} from 'firebase/database';
// import {...} from 'firebase/firestore';
// import {...} from 'firebase/functions';
// import {...} from 'firebase/storage';

// Initialize Firebase
const firebaseConfig = {
  apiKey: "api-key",
  authDomain: "project-id.firebaseapp.com",
  databaseURL: "https://project-id.firebaseio.com",
  projectId: "project-id",
  storageBucket: "project-id.appspot.com",
  messagingSenderId: "sender-id",
  appId: "app-id",
  measurementId: "G-measurement-id",
};

const app = initializeApp(firebaseConfig);
// For more information on how to access Firebase in your project,
// see the Firebase documentation: https://firebase.google.com/docs/web/setup#access-firebase
```

#### metro.config.js 파일 수정

- FIrebase 9.7.x 버전 이상 사용시, metro.config.js 파일 수정 필요

```
npx expo customize metro.config.js
```

metro.config.js

```js
const { getDefaultConfig } = require("@expo/metro-config");

const defaultConfig = getDefaultConfig(__dirname);
defaultConfig.resolver.sourceExts.push("cjs");

module.exports = defaultConfig;
```

#### 코드

```tsx
// App.js
import React, { useEffect, useState } from "react";
import { View, Text, Button } from "react-native";
import { db } from "./firebaseConfig";
import { collection, addDoc, getDocs } from "firebase/firestore";

const App = () => {
  const [data, setData] = useState([]);

  const addData = async () => {
    try {
      await addDoc(collection(db, "users"), {
        name: "John Doe",
        age: 30,
      });
      console.log("Document written successfully");
    } catch (e) {
      console.error("Error adding document: ", e);
    }
  };

  const fetchData = async () => {
    const querySnapshot = await getDocs(collection(db, "users"));
    const users = [];
    querySnapshot.forEach((doc) => {
      users.push({ id: doc.id, ...doc.data() });
    });
    setData(users);
  };

  useEffect(() => {
    fetchData();
  }, []);

  return (
    <View style={{ padding: 20 }}>
      <Button title="Add User" onPress={addData} />
      {data.map((user) => (
        <Text key={user.id}>
          {user.name} - {user.age}
        </Text>
      ))}
    </View>
  );
};

export default App;
```
