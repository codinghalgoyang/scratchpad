---
layout: single
title: "[expo] 프로젝트 시작하기"
categories: expo
---

#### expo-cli 설치

```
npm install --global expo-cli
```

#### 프로젝트 생성

```
npx create-expo-app@latest # expo-router, 폴더 구조등 기본 설정이 되어있음
```

- `expo init 프로젝트명`으로 시작하는 방법도 있지만, 아래 명령어를 사용하면 expo-router, 기본 폴더 구조등이 잘 잡혀있음.

#### 프로젝트 Reset

```
npm run reset-project
```

> 위 명령어를 실행하면, 기존 코드는 app-exmaple 폴더 내에 들어가고, 새로운 app 폴더와 \_layout.tsx, index.tsx가 만들어짐.

기존 상태에서는 새로운 화면을 추가하려고 할 때나 splash 화면을 변경할 때, 작동이 잘 안되는 부분이 있었는데, 다시 작동이 잘되는지 확인해봐야할듯!

#### 프로젝트 실행

```
npm run start

# 또는

npx expo start # 그냥 expo start를 하면, 버전 확인하라는 문구가 발생하는 듯. (npx expo 랑 그냥 expo와 버전차이가 있는 듯함.)
```
