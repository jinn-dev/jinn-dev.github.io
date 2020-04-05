---
title: "CSS FlexBox"
categories:
  - CSS
last_modified_at: 2020-03-20T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
## Life Before Flexbox
div의 기본값은 `block`이며 옆에 어떤 element도 올 수 없다.   
![주석 2020-04-02 235557](/assets/images//주석%202020-04-02%20235557.png)


`inline-block`은 box를 element 요소로 바꿔주지만 block 속성을 갖고 있어서 너비와 높이를 갖고 있다.   
 ![주석 2020-04-02 2355571](/assets/images//주석%202020-04-02%202355571.png)

하지만 div 간격을 조정하려면 box element 각각 margin을 설정해줘야 하는 노가다가 필요한데.. 이것도 스크린마다 간격이 깨지는 단점이 있다.      

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .box {
      width: 300px;
      height: 300px;
      background-color: blue;
      display: inline-block;
    }

    .box:first-child {
      margin-right: 7%;
    }

    .box:last-child {
      margin-left: 7%;
    }
  </style>
</head>
<body>
  <div class="box"></div>
  <div class="box"></div>
  <div class="box"></div>
</body>
</html>
```

![주석 2020-04-03 001452](/assets/images//주석%202020-04-03%20001452.png)


이걸 해결해주는게 바로 FlexBox다!

## FlexBox
flexbox(flexible box module)는 flexbox 인터페이스 내의 아이템 간 공간 배분과 정렬 기능을 제공하는 1차원 레이아웃 모델로 설계되었다.

부모 element에 `display: flex`를 설정해주면 자식의 위치를 움직일 수 있다. 자식 div에다가 일일히 margin을 설정할 필요가 없어진다!

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .wrapper {
      display: flex;
    }

    .box {
      width: 150px;
      height: 150px;
      background-color: blue;
    }
  </style>
</head>
<body>
  <div class="wrapper">
    <div class="box"></div>
    <div class="box"></div>
    <div class="box"></div>
  </div>
</body>
</html>
```
![주석 2020-04-03 002429](/assets/images//주석%202020-04-03%20002429.png)

## Main Axis and Cross Axis
flex container는 width와 height에 의해 아이템을 정렬한다.
![주석 2020-03-21 233558](/assets/images//주석%202020-03-21%20233558.png)

flexbox는 두 개의 축이 있다. `flex-direction`이 축에 영향을 준다.      
`flex-direction`이 `row`일 경우 main axis는 `justify-content`가 영향을 주는 축이고, cross axis는 `align-items`가 영향을 주는 축이다.

따라서, main axis에 있는 아이템을 움직이려면 `justify-content`를 cross axis에 있는 아이템을 움직이려면 `align-items`를 적용하면 된다.

`flex-direction`이 `column`이면 축은 반대가 된다. (매우 중요하다!!!)

| flex-direction | justify-content | align-items |
|:--------|:--------|:-------:|
| row   | 수평   | 수직   |
| column   | 수직   | 수평   |

더 많은 flexbox에 대한 설명은 [CSS Flex(Flexible Box) 완벽 가이드](https://heropy.blog/2018/11/24/css-flexible-box/) 참고

**flex-direction이 row 혹은 row-inverse일 때**
```css
.father {
  display: flex;
  justify-content: left;      /* main-axis */
  align-items: flex-start;    /* cross-axis */
  flex-direction: row;     
  height: 100vh;
}
```
![Basics1](/assets/images//Basics1.png)

**flex-direction이 column 혹은 column-inverse일 때**
```css
.father {
  display: flex;
  justify-content: center;      /* cross-axis */
  align-items: flex-end;        /* main-axis */
  flex-direction: column;    
  height: 100vh;
}
```
![Basics2](/assets/images//Basics2.png)


## Align Self
flex container가 아닌 flex item에만 영향을 주는 몇 안되는 옵션 중 하나
```css
.box3 {
  align-self: flex-end;
}
```

![주석 2020-03-22 004259](/assets/images//주석%202020-03-22%20004259.png)

## Quiz
- What is the default direction of a flex container?
- By default, the main axis is ?
- How do I move items on the main axis?
- How do I move items on the cross axis?
- How can I align an element individually?
- How can I make the flex-items go to another line instead of shrinking

## Reference
[https://codeburst.io/understanding-basic-concepts-of-css-flexbox-ffa657dc39c1](https://codeburst.io/understanding-basic-concepts-of-css-flexbox-ffa657dc39c1)

[https://developer.mozilla.org/ko/docs/Web/CSS](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_Flexible_Box_Layout/Flexbox%EC%9D%98_%EA%B8%B0%EB%B3%B8_%EA%B0%9C%EB%85%90)

[https://css-tricks.com/snippets/css/a-guide-to-flexbox/](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
