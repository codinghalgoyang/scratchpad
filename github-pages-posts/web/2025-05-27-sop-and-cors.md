---
layout: single
title: "[web] SOP & CORS"
categories: web
---

#### SOP (동일 출처 정책)

- 웹은 기본적으로 “동일 출처 정책“ 이라는 강력한 보안 원칙을 따름
- 한 출처(origin)에서 얻어온 문서(주로 html)이나 스크립트(주로 javascript)는 다른 출처의 리소스에 접근하지 못하게 막는 것
- 각 웹사이트의 콘텐츠를 독립적인 샌드박스(‘리소스 접근 환경 격리‘)로 만듦

#### SOP가 없다면?

- 어떤 사용자가 bank.com에 로그인하여 금융 거래를 하고 있음. (이때, bank.com은 사용자의 로그인 세션을 유지하기 위해 쿠키에 세션 ID를 저장함)
- 해당 사용자가 다른 탭에서 악의적인 사이트인 evil.com에도 방문함.
- evil.com의 자바스크립트가 사용자의 세션 쿠키를 활용해서 bank.com으로 특정 요청을 보낼 수 있게됨.
- SOP가 있기 때문에, evil.com은 bank.com으로 접근이 불가함.

#### 출처의 정의

- 여기에서 출처는 아래 3가지가 모두 동일할 때, 동일한 출처라고 함.
  - 프로토콜 : 예) .http 또는 https
  - 호스트(도메인) : 예) www.naver.com
  - 포트 : 예) 80(http용), 443(https)등

![CORS](https://i.namu.wiki/i/HNHtNnVxe6OlxHSft71vsuagkwNOfQWX5DiFNjm_Xyjrgerr0ogwIQ0XD_q1CVZzeXWd8FNtTnA2brR83AR2JMVIhO2KtFwvgORwlQuwsq_ZQon_bk4TOVjR43eCQRYoO75kTlVZBzBCOOihPoCqPw.webp)

#### CORS

- SOP는 강력한 보안 메커니즘 이지만, 현대 웹 애플리케이션은 서로 다른 출처 간에 리소스를 공유해야하는 일이 많음.
- 예1) 프론트 엔드(예. react)와 백엔드는 서로 다른 출처에서 실행됨. 이 경우, SOP는 프론트엔드에서 백엔드 API로 요청을 보내는 것을 기본적으로 차단함
- 예2) 서드파티 API 사용 : 지도 서비스, 결제 서비스, 소셜 로그인 등 다양한 외부 API를 웹 애플리케이션에서 직접 호출

#### CORS 동작 원리 (HTTP 헤더 기반)

- 클라이언트(브라우저)의 요청
  - `https://app.example.com`에서 받은 문서 또는 스크립트가 `https://api.example.com/data`로 API 요청을 보냄
  - 웹 브라우저는 이 요청이 다른 출처로 가는 것임을 인지하고, 요청 헤더에 현재 웹페이지의 출처를 나타내는 `Origin : https://app.example.com` 추가하여 보냄
- 서버의 응답
  - `https://api.example.com`서버는 해당 Origin에서 온 요청을 허용할지 여부를 판단하고, 허용하는 경우, 응답 헤더에 `Access-Control-Allow-Origin: https://app.example.com` 또는 `Access-Control-Allow-Origin: *`을 추가하여 이 응답은 허용된 출처라는 것을 알려줌
- 클라이언트(브라우저)의 최종 결정
  - 응답 헤더의 `Access-Control-Allow-Origin` 내용을 확인하고, 이 응답을 최종적으로 수행할지 결정한 뒤, 안전하다고 판단되는 경우 문서 또는 스크립트에게 response를 전달함.
  - 안전하지 않다고 판단되는 경우, 문서 또는 스크립트에 response를 전달하지 않고, 개발자 도구 콘설에 CORS 에러를 표시함.

#### 궁금한 점

- 차단이 어디에서 이루어지는건가?
  - 서버에서 1차적으로 본인이 허락하지 않는 Origin인 경우, 차단할 수 있음. (서버 자신 보호)
  - 클라이언트(브라우저)에서 응답 헤더의 (Access-Control-Allow-Origin)의 내용을 보고 응답이 요청을 보낸 문서나 스크립트로 넘어가지 않도록 차단할 수 있음. (클라이언트 자신 보호)
  - 서버가 Origin에 상관없이 요청을 처리하되, Access-Control-Allow-Origin은 설정하는 구조라면, CORS 정책을 따르는 웹브라우저는 CORS 에러를 발생시키겠지만, curl이나 postman 같은 클라이언트는 CORS 정책을 무시하기 때문에 별일 없이 요청이 처리되는것을 볼 수 있음.
- 모바일 앱의 경우, origin은 무엇인가?
  - Origin 헤더는 웹 브라우저가 SOP 및 CORS를 구현하기 위해 만들어진 것임.
  - 모바일 앱은 브라우저 환경에서 동작하지 않으므로 이러한 보안 제약 사항에 구속되지 않음.
  - 앱 요청 : 보통 Origin 헤더를 포함하지 않고 요청함.
  - 서버의 처리 : 서버는 Origin 헤더가 없는 요청을 받게되고, 서버에 따라 Origin 헤더가 없는 요청을 처리할지 말지를 결정함.
  - 모바일 앱 환경에서의 인증 및 보안 메커니즘은 따로 있고, 이건 따로 다시 정리하기로 함.
