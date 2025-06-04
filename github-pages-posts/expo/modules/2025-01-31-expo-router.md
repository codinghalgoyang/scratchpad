---
layout: single
title: "[expo/modules] expo-router"
categories: expo/modules
---

파일 기반 라우터 expo-router 사용하기

#### 시작

```
npx create-expo-app@latest
```

이렇게 프로젝트를 시작하면 자동으로 설치 및 설정이 완료됨.

#### page 만들기

`app` 디렉토리에 파일을 생성하면 자동 route 됨

```
- app
	- index.tsx # '/' 페이지
	- home.tsx # '/home' 페이지
	- [user].tsx # '/{user}' 페이지, 동적 path
	- settings
		- index.tsx # `/settings` 페이지
```

> `expo-router`는 `app` 폴더 내 파일을 기반으로 routing을 하기 때문에, routing 페이지만 들어가야함. 그 외 파일들(예. components, hooks)등은 따로 떼어내서 관리가 필요.

#### 플랫폼 별 layout

> expo router 3.5.x 버전 이상 지원

`.ios.tsx, .web.tsx, .native.tsx` 파일등을 지원

```
- app
	- _layout.tsx
	- _layout.web.tsx
	- index.tsx
	- about.tsx
	- about.web.tsx
```

#### 동적 라우팅

- `app/blog/[slug].tsx` 는 `/blog/{slug}` 경로

router paramter 사용방법

```tsx
import { useLocalSearchParams } from "expo-router";
import { Text } from "react-native";

export default function Page() {
  const { slug } = useLocalSearchParams();

  return <Text>Blog post: {slug}</Text>;
}
```

> `screen`, `params`, `key`는 Reserved keywords로 동적 라우트 파라미터로 사용할 수 없음.

#### 페이지 이동

`Link`를 사용하여 페이지 이동 (web의 `a` 태그와 `href` 속성과 유사함.)

페이지 구조

```
- app
	- index.tsx
	- about.tsx
	- user
		- [id].tsx
```

`app/index.tsx`

```tsx
import { View } from "react-native";
import { Link } from "expo-router";

export default function Page() {
  return (
    <View>
      <Link href="/about">About</Link>
      {/* ...other links */}
      <Link href="/user/bacon">View user</Link>
    </View>
  );
}
```

버튼 형태로 `Link` 사용하기

- `asChild` prop을 사용하여 자식 컴포넌트를 포워딩함.

```tsx
import { Pressable, Text } from "react-native";
import { Link } from "expo-router";

export default function Page() {
  return (
    <Link href="/other" asChild>
      <Pressable>
        <Text>Home</Text>
      </Pressable>
    </Link>
  );
}
```

#### Route

Link 사용

```tsx
<Link push href="/login">Login</Link> // push가 default 임
<Link replace href="/login">Login</Link>

// params 활용
// pathname 에는 /user/[id] 가 들어가야하나, markdown에서 블로그 글로 변경하면서 syntax 에러가 발생해 아래 부분에서는 []를 제외함. 실제로는 [] 필요!
<Link
  href={{ pathname: '/user/id', params: { id: 'bacon' }}}>
  View user
</Link>
```

router 사용

```tsx
import { router } from "expo-router";

router.replace("/login");
router.push("/login");
router.back();
```

#### Layout routes

> 네이티브 앱에서는 사용자들이 헤더와 탭 바와 같은 공유 요소가 페이지간 지속되길 바람. 라우트간 이동은 애니메이션 없이 전체 페이지 전환이 이루어짐. 이런건 Layout Route를 사용해 사용자 경험을 높일 수 있도록 해야함

- 파일 이름은 `_layout.tsx` 으로 생성함
- 각 위치에 `_layout.tsx`를 생성하면 됨

#### Group

- URL segments 없이 레이아웃을 추가하려면그룹 syntax인 `()`를 사용
- app의 section을 구분하는데도 좋음

예

- `app/root/home.tsx` > `/root/home`
- `app/(root)/home.tsx` > `/home`

#### Native Layouts

native UI 컴포넌트를 사용할 수 있음.

- 성능면에서 좋음
- 각 플랫폼에 맞는 UX 제공할 수 있음
- 플랫폼 특화기능(ios 내비게인션바 or Android drawer) 쉽게 구현

Stack Navigation : 앱에서 경로 간 탐색을 위한 기본적인 방법

- android에서는 stack 된 경로가 현재 화면 위에 애니메이션으로 나타남
- ios 에서는 스택된 경로가 오른쪽에서 애니메이션으로 나타남

```tsx
import { Stack } from 'expo-router';

export default function HomeLayout() {
  return (
    <Stack screenOptions={{ ... }} />
  );
}
```

#### `npx create-expo-app`시 expo-router가 기본설치되는데 해당 내용 분석

폴더구조

```
- app
  - _layout.tsx
  - +not-found.tsx
  - (tabs)
    - _layout.tsx
    - index.tsx
    - home.tsx
  -
```

`app/_layout.tsx`

```tsx
export default function RootLayout() {
  ...
  return (
    <Stack>
      <Stack.Screen name="(tabs)" options={{ headerShown: false }} />
      <Stack.Screen name="+not-found" />
     </Stack>
  );
}
```

- Stack 안에는 Stack 에서 사용할 screen들을 명시해놓는듯함. (진짜 Stack에 들어간 것은 아니고, 나중에 쓰기위해 정의만 해놓은 것임)
- `(tabs)`는 그룹이므로, 앱 실행시 가장 처음 실행되는 경로인 "/"와 매칭되는 것은 `app/(tabs)/index.tsx`가 됨.

#### 참고자료

- https://docs.expo.dev/router/create-pages/
- https://onemask514.tistory.com/43
