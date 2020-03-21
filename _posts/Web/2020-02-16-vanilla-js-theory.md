---
title: "바닐라 JS 기초 다지기"
categories:
  - JavaScript
last_modified_at: 2020-02-16T22s:00:00+09:00
classes: wide
toc: true
comments: true
---


### let, const, var
변수 초기화&생성할 때 사용        
3년 전까지만 해도 var만 사용 가능했었음    
변수 선언할 때는 우선 const로 필요할 때만 let 사용하기!    
let, var는 변수 값 수정 가능. const는 불가능!    


### Data Type On JS
자료형 따지지 않고 값만 잘 표기하면 됨

```javascript
const a = "hello"; //String
const b = 123;     //Number
const c = true;    //Boolean
const d = 41.1;    //float
```

### Organizing Data With Arrays

```javascript
const daysOfWeek = ["mon", "tue", "wed", "thu", "fri", "sat", "sun", true]; //Array

console.log(daysOfWeek[3]);   //tue
console.log(daysOfWeek[432]); //undefined
```

### Organizing Data With Objects
Array처럼 표현하면 SyntaxError 발생함

```javascript
const personInfo = {"Brandi", 25};

console.log(personInfo); //Unexpected token ','
```

올바른 Object 객체 표현 방식.    
Object 내 자식값은 const임에도 변경 가능하지만 Object 자체는 바꿀 수 없다.    
Array안에 Object를 넣을 수 있다.    

```javascript
const personInfo = {
  name: "Brandi",
  age: 25,
  gender: "Female",
  favMovies: ["NoteBook", "Avengers"],
  favFood: [
    {
      name: "kimchi", fatty: false
    },
    {
      name: "burger", fatty: true
    }
  ]
};

niceInfo = true;

console.log(personInfo.gender); //Female

personInfo.gender = "Maie";

console.log(personInfo.gender); //Male

console.log(personInfo.favMovies);

console.log(personInfo.favFood);
```

### More Function fun
백틱 기호를 사용하면 String 문자열 출력이 좀 더 쉬워진다.
```javascript
const personInfo = {
  name: "Brandi",
  age: 25,
  gender: "Female"
};

function sayHello(name, age){
  console.log("My Name is", name, "And", age, "Years Old!");

  console.log("My Name is "+name+" And "+age+" Years Old!");

  console.log(`Hello ${name} you are ${age} years old`); //백틱 기호 사용
}

sayHello(personInfo.name, personInfo.age);
```

Object에 function 객체를 추가하여 사용 가능하다.
```javascript
const caculator = {
  plus: function (a, b) {
    return a + b;
  },
  minus: function (a, b) {
    return a - b;
  },
  div: function (a, b) {
    return a / b;
  },
  mul: function(a, b) {
    return a * b;
  },
  pow: function(a, b) {
    return a ** b;
  }
}

const plus = caculator.plus(1, 2);
const minus = caculator.minus(1, 2);
const div = caculator.div(1, 2);
const mul = caculator.mul(5, 5);
const pow = caculator.pow(2, 2);

console.log(`${plus} ${minus} ${div} ${mul} ${pow}`);
```

### JS DOM Functions
브라우저에서는 HTML 코드를 DOM(Document Object Model)이라는 객체 형태의 모델로 저장한다. 그리고 JS으로 HTML Document에 접근할 수 있다.
`document.getElementById`로 HTML 태그 ID 정보로 찾을 수도 있고, `document.querySelector()`로 DOM을 주로 찾는다. getElementById의 상위 버전.    

```javascript
//HTML을 DOM 객체로 변경
const title = document.querySelector("#title");
//const title = document.getElementById("title");

console.dir(title);

console.log(title); //<h1 id="title">This works!</h1>

title.innerHTML = "Hi! From JS";
title.style.color = "blue";

document.title = "My Favorit Things";
```

### Events And EventHandlers
자바스크립트는 웹 페이지 내에 작동하는 이벤트에 반응하기 위해 만들어졌다.

```javascript
function handleResize(event) {
  console.log("I have been resized");
}

window.addEventListener("resize", handleResize()); //함수를 바로 호출된다.
window.addEventListener("resize", handleResize); //이벤트 발생 시 호출된다.
```


```javascript
const title = document.querySelector("#title");

function handleClick() {
  title.style.color = "red";
}

title.addEventListener("click", handleClick);
```

### if, else, and, or

`==`와 `===`는 비교한다는 의미로 비슷하지만 깊게 파고 들면 다르다고 한다.<br>    
`==`는 Equal Operator로 `a==b`에서 a와 b의 '값'이 동일하면 true 다르면 false다.<br>
`===`는 Strict Equal Operator로 `a===b`에서 a와 b의 '값'과 '타입'도 동일해야 true 다르면 false다.<br>
ex) `1=="1"`은 true, `1==="1"`은 false
{: .notice--info}


```javascript
const age = prompt("How old are you?");
//참고: prompt 함수는 매우 오래되고 쓰기에 좋지 않음

if(age > 18) {
  console.log('you can drink');
}
else {
  console.log("you can't");
}
```

EventHandler를 적용해보자.
MDN 참조하자.
```javascript
const title = document.querySelector("#title");

const BASE_COLOR = "white";
const OTHER_COLOR = "#c44545";
function handleClick() {
  const currentColor = title.style.color;

  if(currentColor === BASE_COLOR) {
    title.style.color = OTHER_COLOR;
  }
  else {
    title.style.color = BASE_COLOR;
  }
}


function init() {
  title.style.color = BASE_COLOR;
  title.addEventListener("click", handleClick);
}

init();
```
