---
layout: single
title: "[web/nextjs] Next.js 프로젝트에서 Supbase 초기 설정"
categories: web/nextjs
---

문서 : https://supabase.com/docs/guides/auth/quickstarts/nextjs

#### [supabase](https://supabase.com/) 프로젝트 생성 및 값 확인

- 생성후, 아래 키 값들 위치 확인
- `NEXT_PUBLIC_SUPABASE_URL` : Settings > Data API > Project URL
- `NEXT_PUBLIC_SUPABASE_ANON_KEY` : Settings > API Keys > anon/public

#### 환경 변수 설정

.env.local

```
NEXT_PUBLIC_SUPABASE_URL=your-project-url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
```

- `NEXT_PUBLIC_`이 붙어야 클라이언트 사이드에서 환경 변수가 사용 가능해서 붙인듯함.
- 로컬 개발 환경일 경우, .env.local에 넣고, vercel에 올릴땐 해당 내용을 vercel의 환경 변수에 등록해줘야 함.
- supabase url과 supabase anon key는 보여져도 괜찮은 키라고 함.
