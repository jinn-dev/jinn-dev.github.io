---
title: "Spring Environment 프로파일(@Profile), 프로터피(@PropertySource)"
categories:
  - Spring
last_modified_at: 2020-04-28T14s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---

## 프로파일

프로파일은 **빈의 묶음**으로 이럴 때 사용한다.

- 특정 환경마다 쓰는 빈이 다르다. (테스트 환경에서는 A라는 빈, 배포 환경에서는 B라는 빈을 사용)
- 특정 환경에서만 빈을 등록하고 싶다. (모니터링 용 빈은 테스트할때만 등록)



### 프로파일 정의

프로파일 정의는 2개에 정의할 수 있다.    

**1. 클래스 2. 메소드**     

아래는 클래스에 정의하는 방법이다.    



**TestConfiguration.java**

자바설정파일의 프로파일명을 test라고 등록한다. ``@Profile("test")``    

```java
@Configuration
@Profile("test")
public class TestConfiguration {
	
	@Bean
	public BookRepository bookeRepository() {
		return new TestBookRepository();
	}
}

```

<br/>

**AppRunner.java**

BookRepository를 의존성 주입해준다.

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired
	ApplicationContext ctx;
	
	@Autowired
	BookRepository bookRepository;
	
	@Override
	public void run(ApplicationArguments args) throws Exception {
		Environment env = ctx.getEnvironment();
			System.out.println(Arrays.toString(env.getActiveProfiles()));
	}	
}
```

<br/>

**Result**

BookRepository 빈을 찾을 수 없다고 나온다.

```text
Description:

Field bookRepository in com.example.demo3.AppRunner required a bean of type 'com.example.demo3.BookRepository' that could not be found.

The injection point has the following annotations:
	- @org.springframework.beans.factory.annotation.Autowired(required=true)


Action:

Consider defining a bean of type 'com.example.demo3.BookRepository' in your configuration.

```

<br/>

### 프로파일 사용 방법

해당 프로파일을 사용하려면 JVM Option에 다음을 추가해준다.

`-Dspring.profiles.active="test"`

<br/>



## 프로퍼티

프로퍼티는 사용자 정의 설정 값이다. Enviroment는 프로퍼티 소스를 설정하고 프로퍼티 값을 가져오는 역할을 한다.    

### 프로퍼티 사용 방법

JVM Option으로 간단하게 추가할 수 있다.

`-Dapp.about="spring-environment"`

Environment를 통해 프로퍼티를 추가하는 방법은 다음과 같다.

**app.properties**

```properties
app.name=spring-study
```

<br/>

**AppApplication.java**

@PropertySource 어노테이션을 추가해준다.

```java
@SpringBootApplication
@PropertySource("classpath:/app.properties")
public class Demo3Application {

	public static void main(String[] args) {
		SpringApplication.run(Demo3Application.class, args);
	}
}
```

<br/>

**AppRunner.java**

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired
	ApplicationContext ctx;

	@Override
	public void run(ApplicationArguments args) throws Exception {
		Environment env = ctx.getEnvironment();
        System.out.println(env.getProperty("app.name")); //Properties
		System.out.println(env.getProperty("app.about")); //JVM 시스템 변수
	}	
}
```

<br/>

### 프로퍼티 우선순위

둘 다 동일한 프로터티명의 다른 값을 주면 어떻게 될지 고려해봐야 한다.

JVM Option을 변경한다.     

`-Dapp.name="spring-environment"`

<br/>

**app.properties**

```properties
app.name=spring-study
```

<br/>

**AppRunner.java**

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired
	ApplicationContext ctx;

	@Override
	public void run(ApplicationArguments args) throws Exception {
		Environment env = ctx.getEnvironment();
        System.out.println(env.getProperty("app.name"));
	}	
}
```

<br/>

**Result**

```text
spring-environment
```

<br/>

우선순위가 1. JVM 옵션 2. 프로퍼티 파일 순으로 있는 것을 알 수 있다.

