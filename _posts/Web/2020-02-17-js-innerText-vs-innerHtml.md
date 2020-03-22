---
title: "JavaScript innerText와 innerHTML"
categories:
  - JavaScript
last_modified_at: 2020-02-17T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
※ 간단한 예제 소스
```javascript
var str = '<input type=text" placeholder="What is your name"/>';

document.getElementById("content").innerHTML = str;
document.getElementById("content").innerText = str;

<div id="content"></div>
```

`innerHTML`은 태그 형식이 적용되어 나타나고, `innerText`는 텍스트 그대로 출력된다.
<br>

`innerHTML`이 웹 페이지에 텍스트 삽입하는 데 사용하는 경우가 종종 있다. 이는 사이트 공격 경로가 되어 잠재적인 보안 위험이 발생할 수 있다. (프로젝트 소스 보안 점검 시 보안 취약성에 걸릴 확률 높음)

```javascript
const name = "John";
// assuming 'el' is an HTML DOM element
el.innerHTML = name; // harmless in this case

// ...

name = "<script>alert('I am John in an annoying alert!')</script>";
el.innerHTML = name; // harmless in this case
```

HTML5에서는 `innerHTML`로 삽입된 `<script>` 태그가 실행되지 않도록 지정한다. 하지만 `<script>`외 다른 태그로 자바스크립트를 실행하는 방법이 있기 때문에 `innerHTML`을 사용하여 제어할 수 없는 문자열 설정 시 보안위험이 있다. 따라서 일반 텍스트 삽입 시 `innerHTML`은 사용하지 않는 것이 좋다.
### references
[
https://developer.mozilla.org/ko/docs/Web/API/Element/innerHTML](
https://developer.mozilla.org/ko/docs/Web/API/Element/innerHTML)
