---
layout: single
title: "[web] http 개념 및 headers 주요 내용"
categories: web
---

#### 인터넷이란?
- 수많은 기기들을 연결해놓은 네트워크를 의미
- 물리적인 하드웨어와 인프라 자체를 의미

#### 인터넷을 통해 사용자에게 제공되는 서비스
- 웹 서비스 (정보 공유 서비스)
- 파일 공유 서비스
- 이메일 서비스
- 원격 접속 서비스
- 도메인 이름 IP 주소 변환 서비스 (예. `www.naver.com` -> `125.209.222.141`) 

#### 클라이언트와 서버
- 클라이언트 : 서비스(정보공유(웹), 파일공유, 이메일, 원격접속, 도메인 변환등)를 요청하는 주체
- 서버 : 서비스(정보, 파일공유, 이메일, 원격접속, 도메인 변환등)를 제공하는 주체

#### 프로토콜
- 클라이언트와 서버간 통신 규약
- 각 서비스별 사용하는 프로토콜의 예
	- 웹서비스 : http, https
	- 파일공유 : ftp
	- 이메일 : smtp, pop3
	- 원격접속 : ssh
	- 도메인 이름 IP 주소 변환 시스템 : dns
	
#### HTTP (HyperText Transfer Protocol)
- 정보공유를 목적으로 만들어진 프로토콜
- Request와 Response의 짝으로 이루어짐.

#### 웹브라우저와 웹서버
- 정보공유를 목적으로 하는 웹서비스를 위한 클라이언트와 서버
- 웹브라우저 : 사용자가 요청하는 정보를 세분화하여 웹서버에 request를 날리고, response 받은 내용을 조합하여 보여주는 역할
- 웹서버 : request로 들어온 애들에 대한 response를 만들어주는 역할

#### Chrome > Inspect > Network
- 클라이언트(chrome)과 서버가 통신하는 내용을 볼 수 있음
- 간단한 웹페이지 로드 과정
	- html을 요청하고 받음
	- html 안에 추가 콘텐츠(예. img 태그)가 있다면 콘텐츠들을 하나씩 요청하고 받음으
- 해당 요소(html, 이미지등)를 클릭하고, 탭 Headers를 의 Vew Sources를 클릭하면 해당 요소를 요청하고 받을 때, 사용한 request, response의 header 내용을 자세히 살펴볼 수 있음

#### HTTP Request/Response Headers
![request response header](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F413e2f10-ceb7-4479-9c6a-a8ec7691bb64_800x988.jpeg)
#### Request Headers
- request line :
	- Method : GET, POST등
	- path(웹서버에 요청하는 정보) : /1.html
	- http 버전 (protocol) : http/1.1
- request headers
	- Host : 웹서버의 주소 및 포트 번호, `Host: localhost:8080` 
	- User-Agent : 나의 운영체제정보, 웹브라우저정보의이 들어감.
	- Accept-Encoding : 웹브라우저가 압축을 풀 수 있는 포맷들 정보 (콘텐츠의 양이 크면 웹서버에서 압축을 해서 보낼 수 있음): gzip, deflate, br
	- If-Modified-Since : 웹브라우저가 다운로드했던 날짜를 웹서버한테 알려줘서, 재 다운로드를 막을때 사용
- blank line to seperate
- request message body

#### Response Headers
- status line : 
	- http 버전 (protocol) : http/1.1
	- status code : 2xx(성공), 3xx(Redirection : 웹서버가 웹브라우저한테 다른 곳으로 이동하게 요청), 4xx(클라이언트 에러 : 404 not found가 유명함, 403 Forbidden 여기는 너가 접속하면 안되요 에러), 5xx(서버 에러)
	- phrase : status 메시지(OK)
- response headers
	- Content-Type : response의 내용 타입, text/html, image/jpeg, 
	- Content-Length : 콘텐트의 크기
	- Content-Encoding : 콘텐트를 압축한 방식, gzip등
	- Last-Modified: 이 정보가 언제 수정되었는지에 대한 시간 정보