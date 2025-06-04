---
layout: single
title: "[web] redirection"
categories: web
---

#### redirection이란?

사용자가 특정 웹 주소 (URL)로 접속 했을 때, 자동으로 다른 URL로 이동하는 기술

#### 동작 방식 (2가지)

- 서버측 리디렉션
  - 웹브라우저가 웹서버에 특정 url을 요청하면, 웹서버가 redirection 코드인 3xx와 함께 헤더에 새로운 url을 포함하여 응답하면, 웹브라우저가 해당 url로 다시 접속해 요청하는 방식
- 클라이언트측 리디렉션
  - html 메타 태그 사용 방법
    - `<meta http-equiv="refresh" content="0; url=http://example.com/new-page">`
  - 자바스크립트 방법 (href 또는 replace 사용)
    - `window.location.href = "http://example.com/new-page";`
    - `window.location.replace("http://example.com/new-page");`

#### 사용처

- url이 변경되었을 때, 기존 url에 접속하면 변경된 url에 접속하도록 함.
- 일부 사이트의 경우, 모바일 기기와 데스크톱 기기에 따라 다른 버전의 사이트를 가지고 있어, 적절한 버전의 페이지로 리디렉션하도록 함
- http에서 https url로 리디렉션
