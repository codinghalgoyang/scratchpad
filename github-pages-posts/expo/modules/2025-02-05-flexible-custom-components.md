---
layout: single
title: "[expo/modules] 유연한 custom components 작성"
categories: expo/modules
published: false
---

아래는 custom components를 만들 때, 해당 component 스타일에 추가적인 props와 style을 변경할 수 있도록 한 custom component 예제임.

```tsx
import { Colors } from "@/contants/Colors";
import { StyleSheet, View, ViewStyle } from "react-native";

interface ContainerProps extends React.ComponentProps<typeof View> {
  // 이 부분은 아래 컴포넌트에 따라 바뀜
  children: React.ReactNode;
  style?: ViewStyle; // 이 부분은 아래 컴포넌트에 따라 바뀜
}

export default function Container({
  children,
  style,
  ...props
}: ContainerProps) {
  return (
    <View style={StyleSheet.flatten([styles.container, style])} {...props}>
        {children}
    </View>
  );
}

// 기본 스타일
const styles = StyleSheet.create({
  container: {
    backgroundColor: Colors.background,
  },
});
```

- `React.ComponentProps<typeof View>`를 통해 해당 컴포넌트의 props에 `View`의 모든 props를 확장시킴.
- component의 기본 스타일에, 전달 받은 style을 덮어씀. (순서는 기본 스타일 먼저, 이후 전달 받은 style)
- `...props`를 통해 추가 props를 받도록 함.
- `children`을 통해 자식 컴포넌트를 전달 받을 수 있도록 함.
