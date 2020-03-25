---
title: "Parcel과 PostCSS for CSS4"
categories:
  - CSS
last_modified_at: 2020-03-21T22s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---
### Parcel
 [Parcel](https://parceljs.org/)은 Advanced CSS 코드를 압축 및 컴파일할 수 있는 bundle moduler이다. Advanced CSS는 몇몇 브라우저에서 작동이 안될 수도 있는데 parcel을 통해 예전 버전의 CSS로도 바꿔줄 수 있다.


 1. Terminal에서 **`npm init -y`** 를 입력한다.


 2. 생성된 package.json의 `script` 부분을 일단 지운다.

 3. **`npm install -g parcel -bundler`** 으로 parcel bundler를 설치한다.


 4. package.json의 `script` 내용을 아래와 같이 추가한다.
 <br>
```json
"scripts": {
  "start": "parcel index.html"
}
```

5. Terminal에서 **`npm run start`** 입력하면 아래 결과를 확인할 수 있다.      
> Server running at http://localhost:1234

6. CSS를 수정하면 http://localhost:1234 에서 자동적으로 로딩이 되면서 화면을 확인할 수 있다.

***

### PostCSS
PostCSS는 CSS를 좀 더 현대적으로 바꿔주는 방법이다. 여러 플러그인을 제공하며 그 중 [postcss-preset-env](https://preset-env.cssdb.org/) 플러그인은 PostCSS를 사용하는 대부분의 목적을 해결할 수 있다. 문법은 stage 0~3으로 나눠져 있고 단계별로 사용할 수 있는게 다르다.

1. Terminal에서 **`npm install postcss-preset-env`** 입력하여 설치 진행
2. package.json에서 아래 내용 추가 (property가 지원할 stage를 설정해준다.)
<br>
```json
"postcss": {
  "plugins": {
    "postcss-preset-env": {
      "stage": 0
    }
  }
}
```
3. 파일 변환이 제대로 되는지 확인하기 위해 [Try It Now](https://preset-env.cssdb.org/playground)의 css 내용을 `style.css`로 복사 후 화면 개발자 도구로 변환이 되는지 확인해보자.
![주석 2020-03-23 003610](/assets/images//주석%202020-03-23%20003610.png)
CSS가 컴파일 되서 나오는걸 볼 수 있다.
