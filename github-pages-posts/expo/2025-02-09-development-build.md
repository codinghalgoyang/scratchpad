---
layout: single
title: "[expo] Development build"
categories: expo
---

#### 나의 결론

> development build === 커스텀화된 expo go를 빌드하는 방법

---

#### development build 란?

- expo go의 커스텀 버전
- expo go는 미리 설치된 일반적으로 사용되는 라이브러리만 포함.
- development build는 expo go에 내가 사용할 라이브러리를 설치한 것
- 네이티브 디렉토리 내에서 모든 라이브러리를 통합하거나 코드를 변경할 수 있음.
- 사용하려는 라이브러리가 expo go에서 지원하는지 알아보려면, React Native Directory 사이트에서 검색해서 `Expo Go`에 포함되어있는지 확인할 수 있음.
- notification이나 oAuth인증, 분석 등을 사용할 수 없음
- 앱 별 패키지 이름이나 번들 식별자를 사용하지 않기 때문에 프로덕션과 다른 동작을 경험할 수 있음
- 이런 한계에 도달했을 때, development build를 사용하게 됨.

#### `development build`의 의미는?

- react native app은 2가지 부분으로 이루어짐.
  - 하나, Native app bundle : physical device에 설치되는 bundle
  - 둘, javascript bundle : native app bundle 위에서 돌아가는 bundle
- expo go는 native app bundle 역할을 하는 거임.
- 개발 빌드를 사용하면, native app bundle을 직접 만들 수 있음.
- 다시 얘기하면, 개발 중인 react native 앱을 실행하는데 필요한 네이티브 코드만으로 구성된 커스텀 expo go 임.
- 그래서 development build를 사용하면
  - 모든 네이티브 라이브러리를 사용하면서
  - expo 프로젝트에서 제공되는 개발자 경험을 유지할 수 있음(xcode나 Android studio를 사용하지 않고도 실제 기기에 설치하여 테스트 가능)
