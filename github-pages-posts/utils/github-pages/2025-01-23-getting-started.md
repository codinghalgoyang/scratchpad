---
layout: single
title: "[utils/github-pages] minimal mistakes theme으로 github pages 시작하기"
categories: utils/github-pages
---

#### 생성방법

- [minimal-mistakes repository](https://github.com/mmistakes/minimal-mistakes) fork 하기
- fork 한 repository에서 settings에서 "{id}.gihub.io"
- `_config.yml` 파일 수정
  - `주석처리된 url 부분의 주석을 해제한 후, “https://{id}.github.io“로 수정후 커밋`
- `{id}.github.io` 가 그 주소가 됨.

#### 포스팅

- `_posts` 라는 폴더 밑에 md 파일 생성
- `2021-01-13-{url}.md` 형태로 이름 생성

> 저 부분이 url이 되면, 카테고리를 url에 붙여줘야 할까? No!

- 폴더 별로 url이 한 번 더 들어가기 때문에 상관없음
  - `예) url/utils/github-pages/위부분url`

```
___
layout: single
title: ‘첫 포스팅’
published : false # 이건 포스팅 비공개시에 사용
___

내용작성
```

#### Configuration

`_config.yml` 파일수정

```yml
locale: "ko-KR" # UI 텍스트 데이터에서 지역화된 텍스트와 연관짓는데 사용함.
title: "" # 사이트의 이름, <title> 태그에 사용
title_seperator: "|" # 페이지의 title을 생성하는데 사용
description: "" # 사이트의 description에 사용, SEO 향상에 도움
url: "{github_id}.github.io" # 사이트의 url
repository: "{usernmame}/{repo_name}" # repository 경로
teaser: /assets/images/500x300.png # 사이트의 default teaser 이미지 지정
logo: "/assets/images/88x88.png" # 로고 이미지
enable_copy_code_button: true # 코드 블록 복사 가능 여부 설정
search: true # 사이트에서 post 검색 가능 여부 설정
author: # 저자 설정
	name: ""
	avatar: "/assets/images/avatar.png"
	bio: "" # 저자에 대한 description, 경력, 관심사, 전문 분야등을 설명
	email: ""
	location: "Suwon, Korea"
	links:
		- label: "Github"
		  icon: "fab fa-fw fa-github"
		  url: "https://github.com/codinghalgoyang"
timezone: JP, AU
```

#### favicon 수정 방법

minimal mistake theme 기준

`_include/head/custom.html` 에 아래 내용 추가

```html
<link rel="icon" href="/assets/favicon.ico" type="image/x-icon" />
```
