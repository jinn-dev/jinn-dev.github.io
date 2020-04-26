---
title: "Spring의 @Component, Component Scan"
categories:
  - Spring
last_modified_at: 2020-04-27T01s:50:00+09:00
toc: true
toc_sticky: true
comments: true
---

## Component의 기능

- 해당 어노테이션은 <u>Spring 3.1</u>부터 도입 되었디.

- `basePackageClasses` 기준으로 Component Scan을 시작한다.

- 필터로 특정 어노테이션을 스캔 범위에서 제외할 수 있다.
- @Component 기반의 어노테이션을 빈으로 등록해준다. 
  - @Repository
  - @Service
  - @Controller
  - @Configuration



아래 처럼 패키지 범위가 다른 클래스를 의존성 주입하려고 하면 해당 클래스 빈을 찾을 수 없다는 오류가 뜰 것이다. 

```java
package com.example.demo2;
..
import com.example.test.TestService;

@SpringBootApplication
public class Demo2Application {

	@Autowired
	TestService testService;
	
	public static void main(String[] args) {

		SpringApplication.run(Demo2Application.class, args);
	}
}
```

```text
Description:

Field testService in com.example.demo2.Demo2Application required a bean of type 'com.example.test.TestService' that could not be found.

The injection point has the following annotations:
	- @org.springframework.beans.factory.annotation.Autowired(required=true)


Action:

Consider defining a bean of type 'com.example.test.TestService' in your configuration.
```



## function을 사용한 빈 등록

@Component 대신 function을 사용하여 직접 빈을 등록해줄 수 있다.
리플렉션, CG 라이브러리(프록시 등)을 안쓰기 때문에 애플리케이션 구동 타임의 성능상 이점이 있고, 프로그램 내에서 로직을 컨트롤 할 때 용이한 장점이 있다.       

<u>하지만 @Component를 대체하기 보다는 @Bean으로 등록하는 것들을 대체하는 게 더 효율적인 것 같다.</u>      

```java
package com.example.demo2;
..
import com.example.test.TestService;

@SpringBootApplication
public class Demo2Application {

	@Autowired
	TestService testService;
	
	public static void main(String[] args) {

		new SpringApplicationBuilder().sources(Demo2Application.class)
				.initializers((ApplicationContextInitializer<GenericApplicationContext>) applicationContext -> {
					applicationContext.registerBean(TestService.class);
				}).run(args);
		
	}
}
```

## Conponent의 동작 원리

- BeanFactoryPostProcessor
  - ConfigurationClassPostProcessor         
    다른 모든 빈을(직접 등록하는 것들) 만들기 전에 실행한다. 