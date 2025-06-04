---
layout: single
title: "[utils/github-pages] category navigation 사용하기"
categories: utils/github-pages
---

#### 구조

- 사이드바 : 페이지 왼쪽에 위치한 부분
- 네비게이션 : 사이드바 아래쪽에 위치한 부분으로 누르면 특정 url (예. /test)로 이동
- 카테고리 페이지 : 네비게이션 각 링크를 눌렀을 때 나오는 페이지로, 해당 카테고리를 가지고 있는 글을 묶어서 보여주는 역할

#### Step 1. 카테고리를 눌렀을 때 나오는 페이지들 생성

루트 디렉토리에 `_pages` 폴더를 만들고, 그 안에 카테고리별 markdown 파일(예. git.md)을 만들어 아래 내용 작성.

```plaintext
---
title: "Github Pages 관련 글 모음"
layout: archive
permalink: /category/github-pages
---

{% raw %}
{% assign posts = site.categories.github-pages %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
{% endraw %}
```

- `title: “Github Pages 관련 글 모음”`은 해당 페이지에서 보여지는 title을 정의
- `layout: archive` 는 해당 페이지가 다른 페이지를 묶어서 요약하는 페이지의 레이아웃인 archive를 사용하겠다는 의미
- `permalink: /category/github-pages`은 해당 페이지의 url임.
  - `/github-pages` 처럼 `category`를 안 붙여도 상관없으나, 카테고리 관련 페이지들을 `/category` 안에 두고 싶어서 이렇게 작성함.

> permalink와 나중에 navigation의 url이 일치해야함.

> 내가 실수한 부분은 여기에는 permalink는 `/category/github-pages/`으로 써놓고, navigation url에는 마지막 `/category/github-pages`으로 마지막 `/`를 붙이지 않아서 not found 페이지가 보이게됨. 마지막 `/`를 둘 다 없애는 것으로 해결함.

- `assign posts = site.categories.github-pages` : 포스트한 페이지 중 `categories`가 `github-pages`인 애들을 `posts`라는 변수에 넣은 것
- `include archive-single.html type=page.entries_layout` : 위에서 변수 `posts`를 하나씩 순회해가면서 `archive-single.html` 형태(제목, 내용)로 페이지 내용을 생성하는 부분

```

> archive-single.html 대신 다른걸 쓰면 다른 형태로 페이지 내용을 생성도 가능할듯.

> 카테고리명에 띄어쓰기가 있는 경우, `site.categories['category name']` 이런식으로 작성한다고 하는데, 나는 잘안되서 그냥 -를 사용하기로 함.

#### Step 2. 위 카테고리별 페이지의 url 링크를 가진 nav 변수 정의

`_data/navigation.yml`

```

category_nav: - title: etc
children: - title: "github pages"
url: /category/github-pages - title: "tools"
url: /category/tools - title: web
children: - title: "javascript"
url: /category/javascript

```

- category_nav : 변수명
	- title: 네비게이션 그룹의 이름
	- children :
		- title : 링크에 표시될 이름
		- url : 링크될 url

#### Step 3.  nav 변수를 sidebar에 등록

위에서 만든 nav 변수를 필요한 곳에 sidebar에 등록 해줘야함.

```

---

sidebar:
nav: category_nav

---

```

위의 내용이 들어가야할 곳.
1. index.html : 가장 처음에 나오는 페이지, `---` 안에 넣어주면 됨.
2. 각 포스트의 `---`안에 또는 `_config.yml`의 `defaults/values` 안에!(각 포스트의 기본 값을 설정하는 부분)

`_config.yml` : 각 posts에 기본 값에 sidebar: nav: category_nav를 넣겠다는 의미

```

defalults: # \_posts
values:
sidebar:
nav: category_nav

```

#### Step 4. 각 포스트별 categories 등록

포스팅시 머릿말에 categories 추가
```

---

## categories: github-pages

```

> _posts 아래 카테고리별로 포스트를 나누어써도 괜찮나? 아마 OK? 대신 따로 연결되거나 하는 부분은 없고, 이걸 하려면 코드를 수정하는 방법도 있긴한듯.


#### 다 잘했는데 안바뀌는 것 같을때?
캐시 되어있는 페이지가 나오는 경우가 있음. -> 새로고침 해보기!
- 일반새로고침 (일부 캐시를 사용할 수 있음)
	- windows : F5
	- mac : cmd + r
- 강력한 새로고침 (캐시 사용 없음)
	- windows : ctrl + F5
	- mac : cmd + shift + r

#### Step 5. 카테고리별 post 파일 관리 (optional)

꼭 필요한건 아니지만, `_posts` 아래 폴더를 만들고 그 안에서 마크다운 파일을 작성해도 이전과 동일하게 동작함.
- 각 파일을 카테고리별로 관리하고 싶다면 폴더별로 만들어 관리해도 좋을듯!

#### 관련 코드

- [Navigation category commit](https://github.com/codinghalgoyang/codinghalgoyang.github.io/commit/ca1ef8a9b074130cdfa97dcf7f75bd07d6610b92)

```
