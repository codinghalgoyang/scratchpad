---
layout: single
title: "[expo] theme 코드 분석"
categories: expo
published: false
---

`npx create-expo-app@latest` 사용시 Theme 관련 코드가 자동 생성되는데 해당 내용을 분석해보자.

> 프로젝트 리셋(`npm run reset-project`) 이후, `app-examples` 폴더로 들어가는 부분

`app/_layout.tsx`

```tsx
import {
  DarkTheme,
  DefaultTheme,
  ThemeProvider,
} from "@react-navigation/native";
...

import { useColorScheme } from "@/hooks/useColorScheme";
...
export default function RootLayout() {
  const colorScheme = useColorScheme();
  ...

  return (
    <ThemeProvider value={colorScheme === "dark" ? DarkTheme : DefaultTheme}>
      ...
    </ThemeProvider>
  );
}
```

DefaultTheme.tsx (from @react-navigation/native)

```tsx
import type { Theme } from "../types";
import { fonts } from "./fonts";

export const DefaultTheme: Theme = {
  dark: false,
  colors: {
    primary: "rgb(0, 122, 255)",
    background: "rgb(242, 242, 242)",
    card: "rgb(255, 255, 255)",
    text: "rgb(28, 28, 30)",
    border: "rgb(216, 216, 216)",
    notification: "rgb(255, 59, 48)",
  },
  fonts,
};
```

- `app/_layout.tsx`에서 ThemeProvider로 하위 컴포넌트들에서 Theme을 사용할 수 있도록 함.
- `ThemeProvider`는 `DarkTheme` 또는 `DefaultTheme`을 사용하는데, 이는 `Theme 타입`의 변수임.
