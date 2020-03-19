---
title: "아톰 단축키 및 패키지 설치 이력 정리"
categories:
  - Tool
last_modified_at: 2020-03-15T22s:00:00+09:00
toc: true
comments: false
---
## 아톰이란?
오픈소스로 관리되고 100% 무료인 코딩 에디팅 툴

코드 개발과 소스 관리에 특화된 Github가 지원하고 있음

## 아톰 단축키 정리

`ctrl + /` : 현재 줄을 주석 처리/해제

`alt + 1` : 1번 탭 선택

`alt + 2` : 2번 탭 선택

`ctrl + left` : 단어의 맨 처음으로 이동

`ctrl + right` : 단어의 맨 끝으로 이동

`ctrl + l` : 한줄 전체 선택

`ctrl + shift + d` : 현재 줄 또는 선택한 줄 복사

`ctrl + shift + k` : 줄 삭제

`ctrl + home` : 파일 맨 처음으로 이동

`ctrl + end` : 파일 맨 마지막으로 이동

`ctrl + \` : 트리 보이기/감추기

`ctrl + shift`
`ctrl + shift + 9` : Toggle Github

`ctrl + alt + f2` : 북마크 설정/헤제

## 패키지 설치 이력

### [markdown-preview-enhanced](https://atom.io/packages/markdown-preview-enhanced)
마크다운 페이지 작성 미리볼 수 있는 패키지

`ctrl + shift + m` : Toggle preview

### [Color Picker](https://atom.io/packages/color-picker)
CSS 스타일 색상 선택할 때 색상 팔레트에서 직접 선택할 수 있다.

`ctrl + alt + c` : Open color picker


### [Emmet](https://atom.io/packages/emmet)
특정 Syntax로 반복적인 코드를 쉽게 작성할 수 있다.
```html
div>ul>li
```

```html
<div>
    <ul>
        <li></li>
    </ul>
</div>
```
Reference: [Emmet Syntax Doc](https://docs.emmet.io/abbreviations/syntax/)

### [Linter](https://atom.io/packages/linter)
HTML, CSS, JavaScript 등의 코드를 분석해서 문법과 유효성 검증해준다.

### [Sublime Style Column Selection](https://atom.io/packages/Sublime-Style-Column-Selection)
`alt` 클릭한 상태에서 드래그하면 세로로 선택 가능하다. 열 편집을 할 수 있게 만들어 주는 플러그인

### [platformio-ide-terminal](https://atom.io/packages/platformio-ide-terminal)
`ctrl+백틱` 아톰 내에서 터미널을 사용할 수 있다.
