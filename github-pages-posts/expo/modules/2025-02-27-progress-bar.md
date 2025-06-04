---
layout: single
title: "[expo/modules] Progress Bar, Circle 등"
categories: expo/modules
---

#### 설치

```
npx expo install react-native-progress
```

#### 빌드

> eas 빌드 재 필요

```
eas build --profile development --platform android
```

#### 예제 코드

```tsx
import * as Progress from 'react-native-progress';

<Progress.Bar progress={0.3} width={200} />
<Progress.Pie progress={0.4} size={50} />
<Progress.Circle size={30} indeterminate={true} />
<Progress.CircleSnail color={['red', 'green', 'blue']} />
```
