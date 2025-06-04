---
layout: single
title: "[web/nextjs] Next.js 시작하기"
categories: web/nextjs
---

## next.js 프로젝트

자료 : https://nextjs-ko.org/docs

- react 구조 뿐만 아니라, 서버의 역할도 함.

#### 요구사항

- Node.js 18.18 이상 (`node -v`로 확인인)

#### 프로젝트 생성

```bash
npx create-next-app@latest
√ What is your project named? ... {app_name}
√ Would you like to use TypeScript? ... Yes
√ Would you like to use ESLint? ... Yes
√ Would you like to use Tailwind CSS? ... Yes
√ Would you like your code inside a `src/` directory? ... No
√ Would you like to use App Router? (recommended) ... Yes
√ Would you like to use Turbopack for `next dev`? ... Yes
√ Would you like to customize the import alias (`@/*` by default)? ... No
```

- `src/ directory? No`
  - src 관련 폴더 및 파일을 root 디렉토리에 놓을 것인가? src 폴더 안에 넣을 것이냐?
  - 작은 프로젝트의 경우, 크게 상관없으나 프로젝트가 커지면 src 폴더를 따로 만들어 관리하는게 좋다고 함.
- `use App Router? Yes`
  - Next.js에는 Page Router(default: CSR)과 App Router(default: SSR)가 존재함.
  - 기존에는 Page Router만 있었고, App Router를 새로 만든거임.
  - 둘 다 CSR, SSR 구현이 가능하긴함.
  - Next.js가 추구하는 방향은 App Router임.
- `use Turbopack for next dev? Yes`
  - 현재는 dev 모드에서만 지원하고, rust 언어로 빌드를 빠르게해 개발시 피드백이 빨라짐
- `customize the import alias (@/* by default)? No`
  - import시 `../../../components/Button` 대신 `@components/Button` 형태를 쓰게 되는데, 이 `@`대신 다른걸 쓸건지 물어보는거임. 특별한 이유가 아니면 굳이 바꿀 필요가 없음.

#### 로컬 실행

```bash
npm run dev # package.json : "next dev --turbopack"
```

## [Vercel](https://vercel.com/)

#### Vercel의 장점

- git에 push 하면 자동으로 변경사항을 감지하고, 자동으로 빌드 및 배포가 이루어짐.
- CDN을 통해 전세계적으로 콘텐츠를 빠르게 제공함
- 서버리스 함수를 지원하여 백엔드 로직을 구현할 수 있게 해줌
- secret code등도 환경변수로 서버에 넣어서 사용 가능

#### Vercel의 단점

- 광고를 달기 위해서는 plan에 가입해야함 (상업적사용)

#### Vercel 초기 설정

- signup시, Continue with GitHub 선택
- 홈 > Import Git Repository > Install
  - Only select repositories
    - repository 선택
  - Install 클릭
- 홈 > Import Git Repository > 프로젝트 > Import
  - Environment Variables : API 키등 환경변수 등록
  - Deploy 클릭

> 나중에 새로운 repository를 추가할 때는 다른 화면으로 들어가야할 것처럼 보임. (바로 보이진않음)

#### 도메인 확인

- Vercel > 프로필 > Dashboard > 프로젝트 선택 > Domains
