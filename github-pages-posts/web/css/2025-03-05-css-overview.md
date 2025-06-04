---
layout: single
title: "[web/css] CSS Overview"
categories: web/css
---

## 주요 내용

- 대부분의 글자 관련 스타일 속성들(font-style, font-weight, line-height, color, text-align등)은 상속되고, 상속이 안되는 항목을 상속받으려면 inherit를 사용하면 된다.
- 선택자는 우선순위 점수가 있고, 같으면 마지막에 선언된 애가 적용된다.
- display 속성은 크게 block(상자요소: width/height를 가짐), inline(글자요소: width/height 조정 안됨)로 구분된다.
- flex에 부여하는 속성은 container에 적용되는 속성과 item에 적용되는 속성으로 나누어짐
- box-sizing에 따라 요소의 실제 크기를 계산하는 방법이 달라진다. (기본적으로 padding과 border가 포함되지 않음)
  - width, height를 설정하면 padding과 border를 제와한 content의 값만임.
  - 이를 바꾸고 싶다면, box-sizing을 border-box로 변경 필요

## CSS 기본 형태

- element를 선택하는 `선택자`와 `속성:값` 쌍으로 이루어짐

```css
/* 주석 */
선택자 {
  속성1: 값1;
  속성2: 값2;
}
```

## 선언방식

- 내장 방식 : html안에 `<style></style>` 안에 작성
- 링크 방식 : 요소의 stype 속성에 직접 스타일을 작성. `<div style="color: red;"></div>`
- 인라인 방식 : css파일을 따로 두고 link로 파일을 가져옴. `<link rel="stylesheet" href="./css/main.css>`, 병렬로 css가 load되고
- @import 방식 : 하나의 css 파일에서 다른 css를 가져옴. `@import url("./box.css)`, 직렬로 css가 load됨. (import를 따라가면서 하나씩 차례 차례 import 하게 됨.)

## 선택자

#### 기본 선택자

```css
* {
} /* 전체 선택자 */
태그명 {
} /* 태그 선택자 */
.클래스명 {
} /* 클래스 선택자 */
#아이디 {
} /* 아이디 선택자 */
```

#### 복합 선택자

```
선택자1선택자2 {} /* 일치 선택자 : 선택자1과 선택자2를 모두 만족하는 요소 선택 */
선택자1 > 선택자2 {} /* 자식 선택자 : 선택자1 1차 자식 중 선택자2 선택 */
선택자1 선택자2 {} /* 하위 선택자 : 선택자1 아래 모든 선택자2 선택 */
선택자1 + 선택자2 {} /* 인접 형제 선택자 : 선택자1의 형제 중 바로 다음 형제 하나 선택 */
선택자1 ~ 선택자2 {} /* 일반 형제 선택자 : 선택자1읠 모든 형제 선택 */
```

#### 가상 클래스 선택자

동작을 나타내는 가상 클래스 선택자

```
선택자:hover {} /* 해당 선택자에 마우스가 올라가 있을 때 선택 */
선택자:active {} /* 해당 선택자가 마우스로 누르고 있을 때 선택 */
선택자:focus {} /* 해당 선택자가 활성화 되었을 때 선택, input 같은 입력받는 요소들만 가능 */
```

- focus가 안되는 요소를 focus 되게 만드려면, tabindex="-1" 속성을 넣어주면 됨. 예. `<div tabindex="-1"/>`

```
.box {
    width: 100px;
    height: 100px;
    background-color: orange;
    transition: 1s;
}

.box:hover {
	width: 300px;
}
```

- 선택자:가상클래스선택자 형태로 작성
- 어떤 동작을 했을 때 그떄 선택이되는 css를 작성하는데 사용 (기본적으로 css는 동작을 나타내진 않으나, 얘가 예외 케이스임)
- 선택자에 transition 속성을 넣으면, css 속성 변경시 애니메이션이 들어감.

```
선택자:first-child {} /* 형제 요소 중 첫 째라면 선택 */
선택자:last-child {} /* 형제 요소 중 마지막이라면 선택 */
선택자:nth-child(2) {} /* 형제 요소 중 2번째라면 선택 */
선택자:nth-child(2n) {} /* 형제 요소 중 0, 2, 4, 8.. 번째 선택 */
선택자:nth-child(2n+1) {} /* 형제 요소 중 1, 3, 5, 7... 번째 선택 */
선택자:nth-child(n+2) {} /* 형제 요소 중 2번째 이상 선택 */

선택자1:not(선택자2) /* 선택자2가 아닌 선택자1 */
```

#### 가상 요소 선택자 : 해당 요소의 content의 앞 또는 뒤에 내용을 삽입

```
선택자::before {
	content: "Hi";
} /* 해당 선택자의 content의 앞에 “Hi”를 삽입 */

선택자::after {
	content: "Hi";
} /* 해당 선택자의 content의 뒤에 “Hi”를 삽입 */
```

#### 속성 선택자

```
[속성] {} /* 해당 속성을 가진 요소를 선택 */

/* 예 */
[disabled] {} /* disabled 된 요소 선택 */
[type="password"] {} /* type이 "password"인 요소 선택 */
```

## 스타일 상속

> 스타일은 상속된다. 모두 상속 되는건 아니고, 대부분의 글자 관련 속성들(font-style, font-weight, line-height, color, text-align등)이 있다.

```
.animal {
	color: red;
}

<div class="animal">동물
	<div>호랑이</div>
	<div>코끼리</div>
	<div>사자</div>
</div>

/* 동물만 color red로 했지만, 호랑이, 코끼리, 사자도 color: red 속성을 갖음 */
```

#### 강제 상속 (inherit)

```
<div class="parent">
	<div class="child"/>
</div>

.parent {
	width: 300px;
	height: 200px;
}

.child {
	width: 200px;
	height: inherit; /* height는 기본 상속되지 않는 속성이지만, inherit을 통해 상속 */
}
```

## 선택자 우선순위

같은 요소에 여러 선언이 되어있을 때, 우선순위가 높은 css속성을 적용하고, 순위가 같다면 마지막에 선언된 css속성을 적용함. 우선순위는 점수로 매김

- !important : 무한대 점수
- 인라인 style 선언 : 1000점
- id 선택자 : 100점
- class 선택자 : 10점
- 태그 선택자 : 1점
- 전체 선택자 : 0점
- 상속된 속성 : X (점수를 계산하지 않음)

# CSS 속성

html의 요소는 사각형임

```
# width, height : 가로, 세로 너비
- auto(기본값) : 브라우저가 너비를 계산(인라인요소인지 블록요소인지에 따라 다르게 계산)
	- span : 인라인 요소임. 포함한 컨텐츠의 크기만큼 가로, 세로 너비가 줄어듬.
		- width, height가 안먹힘. 인라인 요소는 가로 세로 사이즈를 구현할 수 없음.
		- 인라인 요소는 레이아웃을 작업하는 용도가 아니고, 글자를 제어하는 용도로 만들어졌기 때문임.
	- div : 블록 요소임. 가로는 부모요소의 크기만큼 늘어남, 세로 너비는 최대한 줄어듬
- 단위(px, em, vw등) : 보통은 px를 많이 사용함.

# max-width, max-height : 요소가 커질 수 있는 최대 가로, 세로 너비
- none(기본값) : 최대 너비 제한 없음
- 단위(px, em, vw등)

# min-width, min-height : 요소가 작아질 수 있는 최소 가로/, 세로 너비
- 0(기본값) : 최소 너비 제한 없음, 0은 따로 단위가 안붙음
- 단위(px, em, vw등)

# margin : 요소의 외부 여백을 지정하는 단축 속성
- 0(기본값) : 외부 여백 없음
- auto : 브라우저가 여백을 계산, 가로(세로) **너비가 있는 요소**의 가운데 정렬에 사용
- 단위(px, em, vw등) : 음수도 사용 가능함
- 단축 속성(값의 개수에 따라 적용 범위가 달라짐)
	- 값을 하나만 쓰면, top + bottom + left + right에 모두 적용
	- 값을 두 개 쓰면, top + bottom, left + right에 각각 적용
	- 값을 세 개 쓰면, top, left + right, bottom에 각각 적용
	- 값을 네 개 쓰면, top, right, bottom, left에 각각 적용
- margin-방향(top, bottom, left, right)도 있음

# padding : 요소의 내부 여백을 지정하는 단축 속성
- 0(기본값) : 내부 여백 없음
- 단위(px, em, vw 등)
- % : 부모 요소의 가로 너비에 대한 비율로 지정
- 요소의 크기가 커지는 특징이 있음
- 단축 속성(값의 개수에 따라 적용 범위가 달라짐)
- padding-방향(top, bottom, left, right)도 있음

# border : 요소의 테두리 선을 지정하는 단축 속성, border-width, border-style, border-color가 합쳐진 단축 속성임
- border: 선-두께 선-종류 선-색상;
- 예) border: 10px solid orange;
- 요소의 크기가 커지는 특징이 있음

# border-width : 개별 속성이자 단축 속성임. top, bottom, left, right를 설정 가능
- 단위(px, em, %등)

# border-style // 개별 속성이자 단축 속성임. top, bottom, left, right를 설정 가능
- none(기본값)
- solid : 실선
- dashed : dashed 선

# border-color // 개별 속성이자 단축 속성임. top, bottom, left, right를 설정 가능
- black(기본값)
- 색상 : tomato, #F23918, rgba(255, 255, 255, 0.5)등
- transparent : 투명

# border-방향-속성 : 방향(top, bottom, left, right)과 속성(width, style, color)로 상세지정 가능
- 예) border-top, border-right-width, border-left-color, ...

# border-radius : 요소의 모서리를 둥글게 깍음, 각 모서리에 radius만큼의 원으로 깍은 형태
- 0(기본값) : 둥글게 없음
- 단위(px, em, vw등)
- 단축 속성 : border-radius: 0 10px 0 0; 하면 우상단 모서리만 깍음

# box-sizing : 요소의 크기 계산 기준을 지정
- content-box(기본값) : 요소의 내용(content)으로 크기 계산, content만 width, height가 됨
- border-box : 요소의 내용 + padding + border로 크기 계산, content + padding + border가 width, height가 됨
- width, height를 설정해도, padding과 border로 인해 실제 요소의 크기가 바뀜. width, height에 명시한대로 요소 크기를 사용하고 싶을 땐, border-box를 사용하면 됨!

# overflow : 요소의 크기 이상으로 content가 넘쳤을 때, 보여짐을 제어하는 단축 속성
- visible(기본값) : 넘친 내용을 그대로 보여줌
- hidden : 넘친 내용을 잘라냄
- auto : 넘친 내용이 있는 경우에만 잘라내고 스크롤바 생성
- scroll : auto와 비슷한데 넘친 방향외 다른 방향도 스크롤바가 생성됨
- overflow-x, overflow-y 형태로도 제어 가능

# display : 요소의 화면 출력(보여짐) 특성
- block(상자(레이아웃) 요소의 기본값)
- inline(글자 요소의 기본값)
- inline-block(inline-block 요소의 기본값)
- flex : 플렉스 박스 (1차원 레이아웃), 수직정렬이든 수평정렬이든 축 하나에 대해 정렬
- grid : 그리드 (2차원 레이아웃)
- none : 보여짐 특성 없음, 화면에서 사라짐
- 기타 : table, table-row, table-cell 등...
- 예를 들어, inline 요소의 display 속성을 block으로 바꾸면 width, height를 먹일 수 있음.

# opacity : 요소 투명도
- 1(기본값) : 불투명
- 0 ~ 1 사이의 값 : 0에 가까울 수록 투명, 소수점 단위에서 0은 뺴도 됨. 예) .5

# font-style : 글자의 기울기
- normal(기본값) : 기울기 없음
- italic : 이텔릭체

# font-weight : 글자의 두께(가중치)
- normal, 400(기본값) : 기본 두께
- bold, 700 : 두껍게
- 100~900 : 100 단위의 숫자 9개, normal과 bold 이외 두께

# font-size : 글자의 크기
- 16px(기본값)
- 단위(px, em, rem등)

# line-height : 한 줄의 높이, 행간과 유사함
- 1(reset css 사용시 기본값)
- 숫자 : 요소의 글꼴 크기의 배수로 지정. 1.4라면 글자 크기의 1.4배
- 줄 높이는 행간 자체가 아니라, 글자가 그 높이 사이에 가운데 정렬되는 거임.

# font-family : 글꼴1, “글꼴2”, 글꼴 계열;
- 글꼴 계열을 필수로 작성
- 브라우저가 후보 중 사용가능한 것을 사용함
- 후보 중 쓸 수 있는것이 없다면 브라우저가 가지고 있는 글꼴계열을 가져다 씀
- 띄어쓰기가 있다면 쌍따옴표로 묶어야함.
- sans-serif : 고딕체 계열, 대부분 웹에서 볼 수 있음
- monospace : 고정너비(가로폴이 동등) 글꼴 계열, 코드 에디터에서 주로 쓰임

# color : 글자의 색상, font-color가 아니라 color임!
- rgb(0,0,0) (기본값)
- 색상

# text-align : 문자의 정렬방식
- left(기본값)
- right, center

# text-decoration : 문자의 장식(선)
- none(기본값)
- underline : 밑줄, a 태그는 underline이 기본값임
- line-through : 중앙선(취소선)

# text-indent : 문장 첫 줄의 들여쓰기
- 0 (기본값) : 들여쓰기 없음
- 단위(px, em, rem등)

# background-color : 요소의 배경 색상
- transparent(기본값)
- 색상

# background-image : 요소의 배경 이미지 삽입
- none(기본값)
- url("경로“)

# background-repeat : 요소의 배경 이미지 반복
- repeat(기본값) : 이미지를 수직, 수평 반복 => 바둑판식 반복
- repeat-x : 이미지를 수평 반복
- repeat-y : 이미지를 수직 반복
- no-repeat : 반복하지 않음

# background-position : 요소의 배경이미지 위치
- 0% 0% (기본값) : 좌상단
- top, bottom, left, right, center
- 단위(px, em, rem등)

# background-size : 요소의 배경 이미지의 크기
- auto(기본값) : 이미지의 실제 크기
- 단위(px, em, rem등)
- cover : 비율을 유지, 요소의 더 넓은 너비에 맞춤
- contain : 비율을 유지, 요소의 더 짧은 너비에 맞춤

# background-attachment : 요소의 배경 이미지 스크롤 특성
- scroll(기본값) : 이미지가 요소를 따라서 같이 스크롤
- fixed : 이미지가 뷰포트에 고정, 스크롤X

// 요소의 위치를 설정할 땐, 기준을 설정하고 위치에 대한 값을 조정함!
# position : 요소의 위치 지정 **기준**
- static(기본값) : 기준 없음
- relative : **요소 자신**을 기준
	- 원래 자기 자신의 위치를 기준으로 해서 위치를 변경함.
	- 자기 자신의 원래 위치는 빈공간으로 남아 있기 떄문에, 다른 요소들 사이에 빈공간이 발생함.
	- 다른 요소들 사이에 빈공간이 발생하는 것이 make sense 하지는 않음.
	- relative + 위치조정(top, bottom, left, right) 방법을 권장하지 않음.
	- 하위 요소들의 위치상 부모요소가 되기 위해서만 사용!
- absolute : 위치 상 **부모 요소**를 기준
	- 다른 요소들 사이에 상호작용하지 않아 다른 요소들 사이에 빈공간이 생기지 않음.
	- 그냥 했을 때는 위치상의 부모요소는 뷰포트가 되어버림.
	- 상위 요소에서 position: relative를 가진 애가 위치상 부모요소가 됨.
	- 상위 요소 중 position: relative를 가진 애를 기준으로 top, bottom, left, right 작동
	- 상위 요소 중 position: relative를 가진 애가 없다면 결국 뷰포트가 위치상 부모요소가 됨
	- 대부분의 경우에는 부모요소를 위치상 부모요소로 만듦
- fixed : 뷰포트(브라우저)를 기준
	- 다른 요소들 사이에 상호작용 하지 않음.
> position 속성 값이 absolute, fixed인 경우, display 속성이 block으로 변경됨!!!!!

# top, bottom, left, right : 기준(position)에 대한 위치, 요소의 각 방향별 거리 지정
- auto(기본값) : 브라우저가 계산
- 단위(px, em, rem등)

# z-index
- auto(기본값) : 0과 같음
- 숫자 : 숫자가 높을 수록 위에 쌓임
- z-index가 높더라도 요소의 쌓임순서에 의해 position 속성값이 먼저 비교가 되므로 주의 필요함!
- z-index가 작동하지 않는다면, position 값을 static이 아닌 다른 값으로 지정이 필요함!
- 하나씩 늘려가며 숫자가 너무 커지지 않도록 하는 것이 관리가 편리함

# display
- flex : 블록 요소와 같이 flex Container 정의
	- 블록 요소처럼 가로너비가 최대한 늘어나려고 함.
- inline-flex : 인라인 요소와 같이 flex Container 정의, 때에 따라 인라인 요소처럼 쓰고 싶을 때 사용
	- 인라인 요소처럼 content에 맞게 줄어드려고 함.

# flex container 속성
## flex-direction : flex container 속성, 정렬할 주 축을 설정
- row(기본값) : 좌->우 기준
- column : 위->아래 기준, div가 원래 수직 정렬이라 자주 안쓰인다고 함.
## flex-wrap : flex items 묶음(줄 바꿈) 여부
- nowrap(기본값) : 묶음(줄 바꿈) 없음
	- items 사이즈를 줄여서라도 하나의 줄에 여러개를 끼워넣으려 함.
- wrap : 여러 줄로 묶음, 칸이 모자라서 넘치려고 하면 줄바꿈을 해라
## justify-content : 주 축의 정렬 방법
- flex-start(기본값) : flex items를 시작점으로 정렬
- flex-end : flex items를 끝 점으로 정렬
- center : flex items를 가운데 정렬
## align-content : 교차 축의 정렬 방법
- stretch(기본값) : flex items를 시작점으로 정렬
- flex item의 height가 auto(기본값) 이라면, flex container에 맞게 height를 늘려버림. flex의 특징임!
- flex-start(기본값) : flex items를 시작점으로 정렬
- flex-end : flex items를 끝 점으로 정렬
- center : flex items를 가운데 정렬
- align-content는 flex-wrap이 wrap인 상태에서, flex items가 여러줄이 된 경우 작동
## align-items : 교차축의 한 줄 정렬 방법
- stretch(기본값) : flex items를 교차축으로 늘림
- flex-start(기본값) : flex items를 각 줄의 시작점으로 정렬
- flex-end : flex items를 각 줄의 끝 점으로 정렬
- center : flex items를 각 줄의 가운데 정렬
> align-content vs align-items : 여러 줄 제어는 content, 한 줄 제어는 items

# flex items 속성
## order : flex item의 순서
- 0(기본값) : 순서 없음
- 숫자 : 숫자가 작을 수록 먼저
## flex-grow : flex item의 증가 너비 비율
- 0(기본값) : 증가 비율 없음
- 숫자 : 증가 비율, 크기의 개념이 아닌, 나머지 영역을 몇 대 몇으로 나눌 것인가 하는 개념임
## flex-shrink : flex item의 감소 너비 비율
- 1(기본값) : flex container의 너비에 따라 감소 비율 적용
- 숫자 : 감소 비율
- 컨테이너의 크기가 작으면, 아이템의 크기도 줄어드는데, flex-shrink:0이면 아이템의 크기는 줄어들지 않고 container에서 넘치게 됨.
- 1이라는 의미는 컨테이너가 줄어들면, 각 item들이 1:1:1 비율로 줄어든다를 의미함
## flex-basis : flex item 공간 배분 전 기본 너비
- auto(기본값) : 요소의 Content 너비
- 단위(px, em, rem등)
- flex-grow를 1:1:2로 해도 실제 요소가 1:1:2가 안되는 이유는, flex-grow가 flex-basis를 제외한 나머지 영역에 대한 비율을 나누기 때문임. flex-basis가 0이라면 나머지 공간 == container가 되기 때문에 실제 요소가 1:1:2의 비율을 가지게 됨.

# 전환 : 가상클래스로 요소의 전상태, 후상태가 정의 되어있을 때 전환할 때 효과를 정의
## transition: 요소의 전환(시작과 끝) 효과를 지정하는 단축 속성
- transition-property, transition-duration, transition-timing-function, transition-delay가 합쳐진 단축 속성임
- 쉼표를 통해서 여러 단축 속성을 나열 가능
- transition-property : 전환 효과를 사용할 속성 이름 지정
	- all(기본값) : 모든 속성에 적용
	- 속성이름 : 전환 효과를 사용할 속성 이름 명시
- transition-duration : 전환 효과가 일어나는 지속 시간
	- 0s(기본값) : 전환 효과 없음, 0초
	- 시간: 지속시간s로 지정
- transition-timing-function : 전환 효과 함수 지정
	- ease(기본값) : 느리게 > 빠르게 > 느리게
	- linear : 일정하게
	- ease-in : 느리게 > 빠르게
	- ease-out : 빠르게 > 느리게
	- ease-in-out : 느리게 > 빠르게 > 느리게, ease랑 비슷한데 기울기가 좀 다른듯
	- easing functions를 검색해보면 다양한 function을 볼 수 있음
- transition-delay : 전환 효과가 몇 초 뒤에 시작할지 지정
	- 0s(기본값)
	- 시간: 대기시간s로 지정

# 변환 : 요소들을 2D, 3D 형태로 크기, 회전, 기울이거나, 이동하거나, 원근법을 적용하는 것
- transform: 변환함수1 변환함수2 변환함수3 ...;
- transform: 원근법 이동 크기 회전 기울임;
## 2D 변환
- translate(x,y) : x축 + y축 이동
- translateX(x): x축 이동
- translateY(y): y축 이동
- scale(x, y) : x축 + y축 크기, 하나만 입력하면 x,y 모두에 적용
- rotate(degree) : 회전, 45deg라고 쓰면 45도
- skewX(x) : x축 기울임
- skewY(y) : y축 기울임
## 3D 변환
- rotateX(x) : x축 회전
- rotateY(y) : y축 회전
- perspective(n) : 원근법(거리), transform에 들어갈 때 가장 앞에 들어가야함!, n은 나와의 거리라고 생각하면되고 단위(px, em, rem등)을 사용함. 여기서 말하는건 perspective() 함수이고, perspective 속성이 따로 있음. perspective 속성은 관찰 대상의 부모가 적용 대상이고, perspective() 함수는 관찰 대상이 적용 대상임. 보통은 부모 요소에 perspective를 넣는다고 함.
- rotateX, rotateY는 원근법이 적용되어야 진짜 3D 회전처럼 느껴짐.

# backface-visibility : 3D 변환으로 회전된 요소의 뒷면 숨김 여부
- visible(기본값): 뒷면 보임
- hidden : 뒷면 보이지 않음, 요소가 뒤집어질때 해당 요소를 보이지 않게 함
```

#### css 단위

- px : 픽셀
- % : 상대적 백분율 (부모요소 기준 백분율)
- em : 요소의 글꼴 크기 (상위요소에서 내려온 해당요소의 글꼴 크기 기준)
- rem : 루트 요소(html)의 글꼴 크기 (글꼴 크기가 16px이라면 1rem == 16px, html { font-size: 16px })
- vw : 뷰포트 가로 너비의 백분율 (1vw는 뷰포트 가로의 1/100 만큼임)
- vh : 뷰포트 세로 너비의 백분율 (1vh는 뷰포트 세로의 1/100 만큼임)

#### css 색상 표현

- 색상이름 : 브라우저에서 제공하는 색상 이름
  - 예) red, tomato, royalblue
- hex 색상 코드 : 16진수 색상
  - 예) #000, #FFFFFF
- RGB
  - rgb(255, 255, 255)
- RGBA
  - rgba(0, 0, 0, 0.5)
  - 투명도는 0~1 사이

#### 요소의 쌓임순서 (stack order) : 어떤 요소가 더 위에 쌓이는지 결정

1. 요소에 position 속성의 값(static제외)이 있는 경우 위에 쌓임.
2. 1번 조건이 같은 경우, z-index 속성의 숫자 값이 높을 수록 위에 쌓임.
3. 1번 & 2번 조건이 같은 경우, HTML의 다음 구조일 수록 위에 쌓임.

#### flex 기본 개념

- 1차원 정렬에 사용, main-axis와 cross-axis, 시작점, 끝점 개념을 가짐.
- flex container & flex items
  - display: flex 인 요소가 flex container가 되고, 그 자식 요소들이 flex items가 됨
- flex에 부여하는 속성은 container에 적용되는 속성과 item에 적용되는 속성으로 나누어짐
- container 속성 : display, flex-direction, flex-wrap, justify-content, align-items 등
- item 속성 : order, flex-grow, flex-shrink, flex-basis 등

> 특정 요소에 display:flex를 부여할 수 있고, 부여된 요소는 flex container, 그 자식 요소는 flex items가 되며, container와 item에 부여할 수 있는 속성들이 따로 있다.

## BEM?

- CSS 제작 방법론 중 하나임.
- Block\_\_Element--Modifier 형태로 네이밍
