---
layout: single
title: "[web] supabase architecture"
categories: web
---

#### Supabase란?

- 할 수 있는 것
  - 관계형 데이터베이스 : 데이터 베이스는 단일 리전을 사용하는듯함.
  - 유저 authentication
  - file storage
  - realtime syncing
  - edge functions : 서버리스 function인데, 추가기능(저지연 등) 지원
  - vector & AI toolkit

## [Architecture](https://supabase.com/docs/guides/getting-started/architecture)

- supabase는 오픈소스임.
- supabase는 firebase와 1:1 매핑되지는 않는다.
- 처음부터 모든걸 개발하는게 아니라 있는 이미 있는 오픈소스를 사용함.
- NoSQL이 아닌 Postgres를 사용함.
- Supabase는 Postgres를 쓰기 쉽게 하는걸 목표로 함. (모든 기능을 몰라도 단순한 기능만 사용할 수 있도록함)

#### 아키텍처

![architecture](https://supabase.com/docs/_next/image?url=%2Fdocs%2Fimg%2Fsupabase-architecture.svg&w=3840&q=75)
supabase 프로젝트는 몇 개의 툴들로 구성되어 있음.

- Postgres (database)
  - Postgres는 Supabase의 core임.
  - Supabase를 사용하더라도 full 권한을 가지고 Postgres database를 사용 가능함.
  - 다만, Firebase처럼 Postgres를 좀더 쉽게 사용할 수 있는 툴들을 제공함.
    - source code : github.com/postgres/postgres (mirror)
- Sutdio (dashboard)
  - database와 서비스를 관리하기 위한 오픈소스 대쉬보드임
    - source code : github.com/supabase/supabase
    - Language : typescript
- GoTrue (Auth) : /auth
  - user와 access tokens 발행을 관리하는 JWT 기반 API
  - PostgreSQL의 RLS(Row Level Security)와 API 서버와 integrate 됨.
    - source code : github.com/supabase/gotrue
- PostgREST (API) : /rest
  - Postgres database를 RESTful API로 바꿔줌
  - GraphQL API를 제공하기 위한 `pg_graphql` extension으로 사용
    - source code : github.com/PostgREST/postgrest
- Realtime (API & multiplayer) : /realtime
  - 사용자 접속상태 관리 : 사용자가 온라인 여부 및 상태등을 관리
  - 메시지브로드캐스팅 : 특정 메시지를 다수의 연결된 사용자에게 동시에 전송
  - 데이터베이스 변경사항을 스트리밍 : 데이터베이스에 발생하는 변경 내용을 감지하여 실시간으로 연결된 클라이언트에게 전달
  - 확장 가능 : 사용자나 트래픽 증가하더라도 안정적임
    - source code : github.com/supabase/realtime
- Storage API (Large file storage): /storage
  - Amazon S3 API와 호환되는 방식을 사용하는 오브젝트 스토리지 서비스
    - source code : github.com/supabase/storage-api
- Deno (Edge Functions) : /functions
  - js나 ts로 작성된 코드를 실행함
  - 사용자에게 물리적으로 가까운 '엣지' 서버에서 코드를 실행
    - source code : https://github.com/denoland/deno
- postgres-meta (database management) : /pg
  - Postgres 데이터베이스 관리를 위한 RESTful API
  - Fetch tables, add rolse, run queries 등
    - source code : github.com/supabase/postgres-meta
- Supavisor
  - 데이터베이스 연결을 미리 생성해두고 요청이 있을 때마다 재사용하여, 연결 생성/종료에 드는 부하를 줄임
  - 클라우드 환경에 최적화됨
  - 여러 고객의 데이터 베이스 연결을 효율적으로 관리
    - source code : https://github.com/supabase/supavisor
- Kong (API gateway)
  - NGINX 기반으로 구축된 클라우드 네이티브 API 게이트웨이
  - NGINX : 고성능 웹서버(고성능 요청 처리, 로드 밸런싱등)
  - API 게이트웨이 : 마이크로서비스 아키텍처 등에서 여러 백엔드 서비스로 향하는 요청을 한곳으로 모아 관리
    - source code : github.com/kong/kong

---

# supabase 원칙

- 대규모 기업이 자체적으로 설계할 만한 아키텍처를 제공
- 개인개발자 및 소규모 팀도 사용하기 쉬운 툴 제공
- 확장성 + 사용성을 모두 보장하기 위해 몇가치 원칙들을 사용함

#### 모든 것은 독립적으로 동작한다

- 각 시스템은 standalone tool로서 동작해야한다.
- '사용자가 Postgres 데이터베이스 외에 아무것도 없이 이 제품을 실행할 수 있는가?'와 같은 질문을 활용해볼 수 있음

#### 모든 것은 확장가능해야한다.

- 새로운 도구를 추가하는 대신, 기존 도구를 확장하는 것을 선호함
- 작지만 더 나은것!을 추구

#### 모든 것은 이식가능해야한다.

- 이식성을 높이기 위해 표준에 따르고, 새표준이 부상하면 기존 방식 지원 중단 및 새 표준에 따름

#### 장기적인 관점에서 접근한다.

- 단기적인 이익을 희생하여 장기적인 이득을 취한다.
- supabase의 고객들만 필요로 하는 추가 기능을 가진 Postgres 포크를 운영하지 않음.
- 이 방식은 이식성과 수명을 보장하는 추가적인 이점도 있음

#### 기존 도구 지원

- 가능한 기존 도구와 커뮤니티를 지원한다.

---

## 용어 정리

- Postgres
  - 오픈소스 관계형데이터 베이스임
  - Oracle DB, MySQL, Microsoft SQL에 이어 4위 사용률을 가짐.
  - MySQL등 경쟁 DB에 비해 성능이 조금 떨어짐.
  - 아직까지는 공유볼륨(?)을 지원하지않아 수평확장(?)에서는 불리하다고 함.
- 웹소켓 엔진이란?
