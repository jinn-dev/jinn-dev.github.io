---
title: "Spring 이벤트 프로그래밍에 유용한 인터페이스인 ApplicationEventPublisher"
categories:
  - Spring
last_modified_at: 2020-04-30T14s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---

## ApplicationEventPublisher

스프링의 ApplicationEventPublisher는 옵저버 패턴 기반으로 이벤트 프로그래밍을 할 때 필요한 인터페이스를 제공한다.



### 이벤트 생성

<u>Spring 4.2 이하 버전</u>은 ApplicationEvent 클래스를 상속 받아서 이벤트를 만들 수 있다.    



아래는 데이터를 받는 이벤트 생성 예제이다.    

**MyEvent.java**

이벤트 객체는 **ApplicationEvent 클래스를 상속** 받아 생성하고 빈으로 등록할 필요는 없다.    

```java
public class MyEvent extends ApplicationEvent {
	
	private int data;
	private String str;
	
	public MyEvent(Object source) {
		super(source);
	}
	
	public MyEvent(Object source, int data, String str) {
		super(source);
		this.data = data;
		this.str = str;
		
	}
	
	public int getData() {
		return data;
	}
	
	public String getString() {
		return str;
	}
}
```

<br/>

### 이벤트 처리

**MyEventHandler.java**

- 이벤트 수신 핸들러를 ApplicationListener 인터페이스를 상속받아 생성
- 빈으로 등록

```java
@Component
public class MyEventHandler implements ApplicationListener<MyEvent>{

	@Override
	public void onApplicationEvent(MyEvent event) {
		System.out.println("My Event Handler:  "+event.getData()+", "+event.getString());
	}
	
}
```

<br/>

**AppRunner.java**

- publishEvent로 이벤트를 발생시킨다.

- ApplicationEventPublisher는 ApplicationContext 안에 속하는 IoC 컨테이너 기능 중 하나이므로 ApplicationContext로 사용 가능

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired
	ApplicationEventPublisher publishEvent;
	//ApplicationContext applicationContext;
	
	
	@Override
	public void run(ApplicationArguments args) throws Exception {
		publishEvent.publishEvent(new MyEvent(this, 100, "User"));
		//applicationContext.publishEvent(new MyEvent(this, 100, "User"));

	}
}
```

<br/>

### 이벤트 생성 및 처리(Spring 4.2 이상)

<u>Spring 4.2 버전</u> 부터 코드 내에 프레임워크 소스가 없는 구조인 POJO 형식으로 프로그래밍이 가능해졌다.

**MyEvent.java**

-  ApplicationEvent를 상속하지 않아도 된다.

```java
public class MyEvent {
	
	private int data;
	private String str;
	
	private Object source;
	
	public MyEvent(Object source, int data, String str) {
		this.source = source;
		this.data = data;
		this.str = str;
		
	}
	
	public int getData() {
		return data;
	}
	
	public String getString() {
		return str;
	}
	
	public Object getSource() {
		return source;
	}
}
```

<br/>

**MyEventHandler.java**

- ApplicationListener를 상속하지 않아도 된다.
- 메소드명을 임의로 수정할 수 있다.
- **@EventListener** 어노테이션 사용

```java
@Component
public class MyEventHandler {

	@EventListener
	public void handleMyEvent(MyEvent event) {
		System.out.println("My Event Handler:  "+event.getData()+", "+event.getString());
	}
	
}

```

<br/>

### 다수의 핸들러 처리

- 여러개의 핸들러가 있을 때 임의로 순차적 실행이 된다.

- 순서를 정할 땐 **@Order** 어노테이션을 같이 사용한다.

  **AnoterHandler.java**

  ```java
  @Component
  public class AnotherHandler {
  	
  	@EventListener
  	@Order(Ordered.HIGHEST_PRECEDENCE)
  	public void handle(MyEvent myEvent) {
  		System.out.println("Another Event Handler: "+myEvent.getData());
  	}
  }
  
  ```

  

- 비동기적으로 실행할 땐 **@Async** 어노테이션을 사용한다. (메인 소스에 **@EnableAsync** 어노테이션을 같이 사용해야 한다.)

- 비동기 실행 시 순서는 보장 안된다.

  **MyHandler.java**

  ```java
  @Component
  public class MyEventHandler {
  
  	@EventListener
  	@Async
  	public void handleMyEvent(MyEvent event) {
  		System.out.println("My Event Handler: "+event.getData()+", "+event.getString());
  	}
  	
  }
  ```

  <br/>

  **AnoterHandler.java**

  ```java
  @Component
  public class AnotherHandler {
  	
  	@EventListener
  	@Async
  	public void handle(MyEvent myEvent) {
  		System.out.println("Another Event Handler: "+myEvent.getData());
  	}
  }
  ```
  <br/>

  **ProjcetApplication.java**

  ```java
  @SpringBootApplication
  @EnableAsync
  public class Demo4Application {
  
  	public static void main(String[] args) {
  		SpringApplication.run(Demo4Application.class, args);
  	}
  
  }
  ```

  

### 스프링 기본 이벤트

- **ContextRefreshedEvent**     
  ApplicationContext가 초기화 및 리프레시 할 때 발생한다.

  ```java
  	@EventListener
  	@Async
  	public void handleMyEvent(ContextRefreshedEvent event) {
  		System.out.println("ContextRefreshedEvent");
  	}
  	
  ```

- **ContextStartedEvent**        
  
ApplicationContext가 start되어 라이프사이클 빈들이 시작신호를 받는 시점에 발생
  
- **ContextStoppedEvent**    

  ApplicationContext가 stop되어 라이프사이클 빈들이 정지신호를 받는 시점에 발생

- **ContextClosedEvent**    
  ApplicationContext가 close되고 빈 소멸되는 시점에 발생한다.

  ```java
  	@EventListener
  	@Async
  	public void handleMyEvent(ContextClosedEvent event) {
  		System.out.println("ContextClosedEvent");
  	}
  ```

- **RequestHandledEvent**    
  HTTP 요청을 처리했을 때 발생한다.
  
  

## Reference

[ApplicationEventPublisher 인터페이스](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEventPublisher.html)

[@EventListener Annotation](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/context/event/EventListener.html)

