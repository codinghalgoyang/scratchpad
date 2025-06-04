---
layout: single
title: "[expo/modules] Screen 추가하기"
categories: exexpo/modules
---

- app 폴더에 스크린 컴포넌트 작성
- \_layout.tsx의 Stack 안에 아래처럼 내용 추가
- options를 활용하지 않으면 안해도 될 것 같지만, headerShown을 false로 사용할꺼라서 항상 필요해보임.

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
