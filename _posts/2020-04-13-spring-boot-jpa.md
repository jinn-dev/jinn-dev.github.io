---
title: "Spring Boot With JPA - 프로젝트 환경설정(진행중)"
categories:
  - Spring
last_modified_at: 2020-04-13T16s:42:00+09:00
toc_sticky: true
toc: true
comments: true
---

## 프로젝트 생성

Spring Boot 프로젝트는 [http://start.spring.io/](http://start.spring.io/)에서 쉽게 생성 가능하다.

- jsp vs Thymeleaf                   		 
  요즘 jsp는 성능상문제로 최신 Spring boot에서 권장하지 않는다. 그래서 Thymeleaf를 써보기로 한다. 
- Maven vs Gradle    
  차이점은 일단 패스하고.. Gradle을 써보기로

### Lombok 사용하기

lombok은 getter/setter같은 반복된 코드를 어노테이션으로 선언해서 Auto Generation되게 해준다.

[Spring Boot lombok 설치 방법](https://linked2ev.github.io/gitlog/2019/08/25/springboot-mvc-7-스프링부트-Lombok(롬복)-사용하기/)

```java 
import lombok.Getter;
import lombok.Setter;

@Getter @Setter
public class boardDTO {
	
	public String title;
	public Integer idx;
}
```

### 라이브러리 살펴보기

gradle의 라이브러리의 의존관계를 확인하려면 터미널에서 아래 명령어로 볼 수 있다.

```json
$ gradlew dependencies
```

- **핵심 라이브러리**
  
  - 스프링 MVC
  - 스프링 ORM
  - JPA, Hibernate
- 스프링 데이터 JPA
  
- **기타 라이브러리**
  - H2 데이터베이스 클라이언트
  - HikariCP: Spring Boot의 기본 Connection Pool
  - WEB (~~jsp~~/thymeleaf)
  - 로깅 (slf4j라는 로그 인터페이스 모음과 logback 로그 구현체)
  - 테스트 (junit, spring-test, mockito, assertj 등)
  
  

Spring boot는 라이브러리에 대한 Dependency를 관리해주기 때문에 몇 개만 등록해도 하위 Dependency를 자동으로 땡겨와 준다.

- spring-boot-starter-web

  - spring-boot-starter-tomcat: 톰캣(웹서버)
  - spring-webmvc: 스프링 웹 MVC

- spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View)

- spring-boot-starter-test

  - junit: 테스트 프레임워크

  - mockito: 목 라이브러리

  - assertj: 더 편하게 테스트 코드 작성할 수 있는 라이브러리

  - spring-test: 스프링 테스트 통합 지원

    

**참고** 스프링 데이터 JPA는 스프링과 JPA를 먼저 이해하고 사용해야 하는 응용 기술이다.
{: .notice--warning}



## View 환경 설정

[Thymeleaf 템플릿 엔진](https://www.thymeleaf.org/)의 대표적인 장점 중 하나는 **Natural templates**로 마크업을 깨지 않고 그대로 사용할 수 있고, 페이지 소스를 볼 때 깔끔하게 서버사이드 렌더링 되서 나온다.

```html
<table>
  <thead>
    <tr>
      <th th:text="#{msgs.headers.name}">Name</th>
      <th th:text="#{msgs.headers.price}">Price</th>
    </tr>
  </thead>
  <tbody>
    <tr th:each="prod: ${allProducts}">
      <td th:text="${prod.name}">Oranges</td>
      <td th:text="${#numbers.formatDecimal(prod.price, 1, 2)}">0.99</td>
    </tr>
  </tbody>
</table>
```



`spring-boot-starter-thymeleaf`로 세팅이 다 되어 있으므로 따로 해줄 건 없다. 

-  thymeleaf viewName 매핑 방식      
  `resources:templates/`+[ViewName]+`.html`


**java/HelloController.java**

```java
@Controller
public class HelloController {
	
	@GetMapping("hello")
	public String hello(Model model) {
		model.addAttribute("data","hello world");
		return "hello";	//화면 이름
	}
}
```



**resources/templates/hello.html** (템플릿 페이지)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello</title>
</head>
<body>
    <p th:text="'안녕하세요. '+${data}">안녕하세요. 손님</p>
</body>
</html>
```

`<html xmlns:th="http://www.thymeleaf.org">`로 thymeleaf 템플릿을 참조하겠다고 선언한다.



**참고** [Emmet 플러그인](https://github.com/emmetio/emmet-eclipse#readme)을 쓰면 마크업을 더 빨리 할 수있다. (기본 단축키는 `Ctrl+E`) 하지만 인텔리제이가 정신건강에 좋은듯 하다^^
{: .notice--warning}



**resources/static/index.html** (정적인 컨텐츠)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <a href="/hello">hello</a>
</body>
</html>
```



### spring-boot-devtools

기본적으로 resources 하위에 있는 파일 수정 시 서버를 재시작해야 한다는 단점이 있다. 이를 해결하기 위해 라이브러리 하나를 추가하자. 



**bundle.gradle**

```json
implementation 'org.springframework.boot:spring-boot-devtools'
```

reloading, cache 삭제 등 개발 시 유용한 기능이 많은 라이브러리다. `Gradle > Refresh Gradle Project`  하고 서버를 재시작 해서 `restartedMain`로 바뀌었는지 확인한다.

```json
2020-04-13 14:31:05.175  INFO 12168 --- [  restartedMain] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'
2020-04-13 14:31:05.176  INFO 12168 --- [  restartedMain] j.LocalContainerEntityManagerFactoryBean : Closing JPA EntityManagerFactory for persistence unit 'default'
...
```



**application.properties 또는 application.yml**

아래 내용도 추가해주자.

```properties
# properties
spring.thymeleaf.cache = false

# yml
spring.thymeleaf.cache: false
```



설정이 잘 되었다면 소스 수정 후 새로고침하면 변경 사항이 반영됨을 확인 할 수 있다.



## H2 데이터베이스 설치

[H2 데이터베이스](https://h2database.com/)는 개발 및 테스트 용도로 쓰며 웹 콘솔을 제공한다. 

- 1.4.199 버전 다운로드:[https://h2database.com/h2-setup-2019-03-13.exe]( https://h2database.com/h2-setup-2019-03-13.exe)

- 윈도우, 리눅스, MAC: [https://h2database.com/h2-2019-03-13.zip](https://h2database.com/h2-2019-03-13.zip)



zip 파일 압축해제 하고 해당 경로에서 `h2.bat` 파일 실행 (Windows 기준)

```json
D:\dev\h2\bin> h2.bat
```

![주석 2020-04-13 144948](/assets/images/주석 2020-04-13 144948.png)

- http://localhost:8082/ 접속
- `jdbc:h2:~/프로젝트명` (세션 유지한 상태로 진행해야 함)
- `~/프로젝트명.mv.db` 파일 생성
- 이후 부터는 JDBC URL: `jdbc:h2:tcp://localhost/~/프로젝트명` 네트워크 모드로 접속



## JPA와 DB 설정, 동작 확인 

**application.yml**

```yml
spring:
  datasource:
    url: jdbc:h2:tcp://localhost/~/jpashop;MVCC=TRUE
    username: sa 
    password: 
    driver-class-name: org.h2.Driver
```





## Reference

[실전 스프링 부트와 JPA 활용]([https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-JPA-%ED%99%9C%EC%9A%A9-1/dashboard](https://www.inflearn.com/course/스프링부트-JPA-활용-1/dashboard))

[Spring Boot Guide](https://docs.spring.io/spring-boot/docs/2.2.6.RELEASE/reference/html/howto.html#howto)









