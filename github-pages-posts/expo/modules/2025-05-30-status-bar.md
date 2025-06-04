---
layout: single
title: "[expo/modules] StatusBar"
categories: expo/modules
---

> 주의 : `react-native`가 아닌 `expo-status-bar`의 StatusBar를 import 한다.

#### 코드

```tsx
import { StatusBar } from "expo-status-bar";

export default function Index() {
  return (
    <View>
      <StatusBar style="auto" />
    </View>
  );
}
```

- StatusBar는 실제로 보이는 부분은 아니고, 위의 StatusBar를 제어하는 부분임
- `style` 속성에는 `export type StatusBarStyle = 'auto' | 'inverted' | 'light' | 'dark';` 가 들어갈 수 있는 것으로 보임
