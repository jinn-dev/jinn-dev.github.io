---
title: "마크다운(markdown) 사용법"
categories:
  - Spring
last_modified_at: 2020-02-05T22s:00:00+09:00
toc: true
comments: false
---
내가 보기 위한 마크다운 문법 정리

## 제목과 리스트
### 제목
마크업의 `<h1></h1>..<h2></h2>` 목차태그를 마크다운에서는 이렇게 표현한다.
```smalltalk
# 1
## 2
### 3
#### 4
##### 5
###### 6      
```

<br>
### 리스트
마크업의 `<ul>`, `<ol>` 처럼 마크다운에서도 순차 리스트와 순서가 없는 리스트로 표현할 수 있다.

먼저, 순차 리스트는 다음과 같이 쓸 수 있다.
```smalltalk
1. 음식
1-1. 한식
1-1-1. 김밥
1-2. 양식
1-3. 중식
1-4. 일식
1-5. 야식
```

1. 음식
1-1. 한식       
1-1-1. 김밥        
1-2. 양식        
1-3. 중식       
1-4. 일식      
1-5. 야식          
<br>

순서 없는 목록은 스페이스바 2번 또는 Tab 키로 구분해야 한다.

```smalltalk
* 음식
  * 한식
    * 김밥
      * 참치김밥
  * 양식
  * 중식
  * 일식
  * 야식
```

* 음식
  * 한식
    * 김밥
      * 참치김밥
  * 양식
  * 중식
  * 일식
  * 야식
<br>

두가지 혼합해서도 사용 가능하다. 이상한게 2번째 트리부터는 탭을 2번해야 먹힌다;
```smalltalk
1. 한식
  * 김밥
        * 참치김밥
2. 양식
    * 돈까스
3. 중식
4. 일식
5. 야식
```
1. 한식
    * 김밥
        * 참치김밥
2. 양식
    * 돈까스
3. 중식
4. 일식
5. 야식     

<br>
## 문자강조
```smalltalk
*기울임*
_기울임2_
**두껍게**
__두껍게2__
~~취소선~~
```
*기울임*
_기울임2_
**두껍게**
__두껍게2__
~~취소선~~


<br>
## 인용구와 호라이즌
### 인용구
특히 강조하거나 인용해야 될 문구 있을 때 사용한다.
```smalltalk
> 한식
>> 김밥
>>> 참치김밥
>>>> 먹고싶다.
>>>>> 근데 다이어트중
>>>>>> 그래서 못먹어..
>>>>>>> 어디까지 되는거지;;
```
> 한식
>> 김밥
>>> 참치김밥
>>>> 먹고싶다.
>>>>> 근데 다이어트중
>>>>>> 그래서 못먹어..
>>>>>>> 어디까지 되는거지;;

인용구 문법 안에 다른 문법도 같이 사용할 수 있다.
```smalltalk
> # 오늘의 저녁
> * 메인메뉴
> `닭가슴살..`
```

> # 오늘의 저녁
> * 메인메뉴
> `닭가슴살..`

<br>
### 호라이즌
마크업의 `<hr>`에 해당하는 마크다운 문법은  `*`, `-`, `_` 기호 사용으로 표현 가능하다.
```smalltalk
***
---
___
```

3개 모두 이렇게 똑같이 나온다.
***

<br>
## 링크와 이미지
### 링크
마크업의 `<a>` 하이퍼링크 태그는 마크다운에서 이렇게 사용 가능하다.
```smalltalk
<https://jinn-dev.github.io>
```
<https://jinn-dev.github.io>
<br>
url이 아니라 제목으로 보이게 하려면
```smalltalk
[jinn-dev](https://jinn-dev.github.io)
```
[jinn-dev](https://jinn-dev.github.io)

이렇게 표현할 수 있다.
<br>

### 이미지
```smalltalk
![텍스트](이미지파일경로.jpg)
```
![KakaoTalk_20200213_204220665](/assets/images/KakaoTalk_20200213_204220665.jpg)

`[텍스트]`부분은 `alt` 속성과 동일하며 이미지가 안뜰 때 어떤 텍스트로 대체할지 쓰면 된다.

<br>
## Notice
이건 minimal-mistakes만 해당되는 문법인 것 같음

```smalltalk
**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice}` class.
{: .notice}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--primary}` class.
{: .notice--primary}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--info}` class.
{: .notice--info}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--warning}` class.
{: .notice--warning}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--success}` class.
{: .notice--success}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--danger}` class.
{: .notice--danger}
```
**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice}` class.
{: .notice}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--primary}` class.
{: .notice--primary}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--info}` class.
{: .notice--info}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--warning}` class.
{: .notice--warning}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--success}` class.
{: .notice--success}

**Watch out!** This paragraph of text has been [emphasized](#) with the `{: .notice--danger}` class.
{: .notice--danger}

<br>


## 테이블과 블럭
### 테이블
```smalltalk
| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
```

| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |

<br>
### 블럭 및 코드 강조

```
`블럭강조`
```
`블럭강조`


## references
https://github.com/mmistakes/minimal-mistakes
