---
layout: single
title: "[expo/modules] supabase 초기 설정"
categories: expo/modules
published: false
---

#### supabase 프로젝트 만들기

- https://supabase.com/
- Start your project > New project (dev용 하나, production용 하나)

#### 패키지 설치

```bash
npx expo install @supabase/supabase-js @react-native-async-storage/async-storage react-native-url-polyfill
```

#### 빌드

```bash
eas build --profile development --platform android
eas build --profile development --platform ios
```

#### supabase.ts 생성

```ts
import AsyncStorage from "@react-native-async-storage/async-storage";
import { createClient } from "@supabase/supabase-js";
import "react-native-url-polyfill/auto";

// Get from supabase > project > Project Settings > Data API
const supabaseUrl = __DEV__ ? "{supabaseUrl (dev)}" : "{supabaseUrl}";
const supabaseAnonKey = __DEV__ ? "{anonKey (dev)}" : "{anonKey}";

export const supabase = createClient(supabaseUrl, supabaseAnonKey, {
  auth: {
    storage: AsyncStorage,
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: false,
  },
});

/ Tells Supabase Auth to continuously refresh the session automatically
// if the app is in the foreground. When this is added, you will continue
// to receive `onAuthStateChange` events with the `TOKEN_REFRESHED` or
// `SIGNED_OUT` event if the user's session is terminated. This should
// only be registered once.
AppState.addEventListener('change', (state) => {
  if (state === 'active') {
    supabase.auth.startAutoRefresh()
  } else {
    supabase.auth.stopAutoRefresh()
  }
})
```

#### supabase-js Expo 호환성 문제

```
Android Bundling failed 1355ms node_modules\expo-router\entry.js (1171 modules)
The package at "node_modules\ws\lib\stream.js" attempted to import the Node standard library module "stream".
It failed because the native React runtime does not include the Node standard library.
Learn more: https://docs.expo.dev/workflow/using-libraries/#using-third-party-libraries
```

- 결론 : 일단 supabase-js 버전을 "2.49.5-next.5" 를 사용하자. (package.json에서 버전 수정 후, `"@supabase/supabase-js": "2.49.5-next.5",` npx expo install 수행)
- 원인 : supabase-js가 내부적으로 사용하는 ws가 React Native에서는 사용할 수 없는 Node.js의 내장 모듈인 stream을 필요로 해서 발생함. (supabase-js는 웹에서도 react-native에서도 같은 코드를 사용하는 것으로 보임)
- supabase-js 버전(2.49.5, 2.49.7, 2.49.8)에서 발생하고 있고, 2.49.6에서 고쳤으나 다른 문제가 발생해서 2.49.7 버전에서 고친 부분을 뺏다고 함.
- Expo SDK 53의 dependency도 "2.49.5-next.5"를 사용하도록 최근(25년 5월 23일)에 수정함
- 참고 : https://github.com/supabase/supabase-js/issues/1400
- 추후 이 문제는 업데이트가 진행 되면 사라질 것으로 보임.

#### 참고자료

- https://docs.expo.dev/guides/using-supabase/
