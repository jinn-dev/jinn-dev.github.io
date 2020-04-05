---
title: "HTML5의 시맨틱 요소"
categories:
  - HTML
last_modified_at: 2020-04-01T22s:00:00+09:00
toc: true
toc_sticky: true
comments: true
share: true
related: true
read_time: true
---
## Overview
HTML을 공부하면서 항상 궁금했던 점이 HTML의 적절한 시맨틱 요소 사용이다. 화면이 디자인되서 이미지로 나오면 HTML로 구조화 하는 작업이 필요하다. `header`, `nav` 등의 확실한 목적을 가진 엘리먼트는 이걸 사용해야 되겠다고 쉽게 판단이 가능하지만 `section`, `main`, `article` 등 어떨 때 사용해야 하는 지 파악이 힘든 엘리먼트도 있었다. 추가로 `div`와 `span`의 차이점도 정리해보자.    

![HTML Layout](https://media.vlpt.us/images/kimu2370/post/34a633a0-4543-4fdc-a7a5-b579a3e2279b/image.png)    
![HTML Layout2](https://patrickhlauke.github.io/aria/presentation/pictures/blog-structure-after-html5.png)

## header and hgroup      
`<header>`는 섹셔닝 요소에서 도입부와 네비게이션 컨텐츠에 사용된다. heading(h1~h6 또는 hgroup)을 포함하지만 관련 로고, 검색 폼, 테이블 컨텐츠, 링크 항목 또는 nav 요소 같은 컨텐츠를 담을 수 있다.

이 요소의 위치를 설명하는 것보다 도입부, 네비게이션의 지원을 담고자 함이다. 일반적으로 섹션의 맨 위쪽에 위치하지만 필수는 아니고 어디든지 올 수 있다.

```html
<article>
  <header>
    <h1>인터넷 익스플로러 9</h1>
    <p><time pubdate datetime="2011-03-15"></time></p>
  </header>
  <p>윈도우 인터넷 익스플로러 9(간단히 IE9)은 2011년 3월 15일 21:00에 릴리즈되었다...</p>
</article>
```

`<hgroup>`은 전문화된 header의 한 형식이지만, HTML5에서는 쓸모 없게 되어 명세에서 삭제되었다. 사용할 경우 유효성 검사에서 에러 메세지가 발생된다.

## footer
페이지의 top 링크, copyright 데이터, 관련 문서로의 링크 항목, 이글의 저자 등 추가 정보를 나타내는 데 사용된다. 주로 컨텐츠의 마지막 부분에 나타낸다. footer안에 footer를 담을 수 없다.

`<footer>`는 여러 번 사용이 가능하며 일반적으로 섹션 아래쪽에 위치하지만 필수는 아니고 어디든지 올 수 있다.

## h1~h6
HTML4의 heading 요소를 물려 받은 형식

## aside
주요한 내용을 담는게 아닌 부차적인 내용을 담는 태그로 article 요소 안쪽 또는 바깥쪽에 올 수 있다.
article 안에 있을 때는 article 본문과 관련된 내용이어야 하고, 바깥쪽에 있을 때는 전체 웹 페이지와 관계있는 내용이어야 한다. (div id="sidebar"와 같은 역할)

## section and article
`<section>`은 콘텐츠의 장, 절 구분에 사용되고 스타일, 스크립트 적용 금지(적용이 필요하다면 하위에 div 요소를 사용한다.)    
`<section>`에 제목을 표시하는 HTML5 heading(ex. h1~h6)을 1개 이상 넣자.    

```html
<section>
  <h1>제목</h1>
  <p>다람쥐 헌 쳇바퀴에 타고파‎. 다람쥐 헌 쳇바퀴에 타고파‎. 다람쥐 헌 쳇바퀴에 타고파‎.</p>
  <p>다람쥐 헌 쳇바퀴에 타고파‎. 다람쥐 헌 쳇바퀴에 타고파‎. 다람쥐 헌 쳇바퀴에 타고파‎.</p>
</section>
```

`<article>`은 블로그 글, 신문 기사와 같은 독립적 컨텐츠를 담는 태그

```html
<article>
  <h1>안녕 친구들</h1>
  <p>우리나라의 말이 중국과 달라 문자가 서로 통하지 않는데 이런 이유로 어리석은 백성이 말하고자 하는 바가 있어도 마침내 제 뜻을 능히 펴지 못하는 사람이 많다. 내가 이를 불쌍히 여겨 새로 스물여덟 자를 만드니 사람마다 하여금 쉽게 익혀 매일 쓰기에 편안하게 하고자 할 따름이다.</p>
</article>
```

## main
문서의 주요 내용을 담는 태그다. <div id="main">과 같은 역할이고 문서에서 `<main>`은 하나 이상 있어선 안되고 페이지 당 한번만 사용 가능하다.    
또한 `<main>`은 article, aside, footer, header, nav의 자손이어선 안된다.     
해당 요소는 문서 영역을 구분 짓는 용도로 사용하지 않기 때문이다.


## div and span
`<div>`는 HTML 문서의 구획 요소 중 가장 흔히 사용하는 태그로 `<a>`태그를 감싸서 링크를 만들 수도 있다.

`<span>`은 텍스트 일부분에 글자색/폰트/배경색 등을 지정할 때 사용한다.

```html
<p>여기는 <span style="color: blue">zetawiki</span>입니다.</p>

<p>여기는 <span style="background-color: gold">zetawiki</span>입니다.</p>

<p>제가 좋아하는 폰트는 <span style="font-family: Impact">Impact</span>입니다.</p>
```

2개 다 영역을 설정하는 건 동일하지만 3가지 차이점이 있다.    
1. 줄바꿈
`<div>`는 줄바꿈이 되지만 `<span>`은 한줄로 붙는다.

2. 영역 지정 방식 차이
텍스트 표현 시 `<div>`는 사각형 박스로 구역을 정하지만 `<span>`은 문장 단위로 지정한다.

3. margin 방향
`<div>`는 4방향 모두 적용되고 위 아래 겹쳐지는 여백은 상쇄된다.    
`<span>`은 margin은 양 옆으로만 적용되며 겹쳐지지 않는다.


## Reference
[https://mainia.tistory.com/3289](https://mainia.tistory.com/3289)
[https://zetawiki.com/wiki/HTML_%ED%83%9C%EA%B7%B8](https://zetawiki.com/wiki/HTML_%ED%83%9C%EA%B7%B8)
