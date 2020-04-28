---
title: "Spring 국제화 서비스를 위한 메세지소스(MessageSource) 적용"
categories:
  - Spring
last_modified_at: 2020-04-28T14s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---

## MessageSource

ApplicationContext의 기능 중 하나인 MessageSource는 **다국어를 지원해주는 인터페이스**이다.

**messages.properties**

```properties
greeting=Hello {0}
```

<br/>

**messages_ko_KR.properties**

```properties
greeting=안녕하세요. 반갑습니다. {0}
```

<br/>

**AppRunner.java**

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired 
	MessageSource messageSource;
	
	@Override
	public void run(ApplicationArguments args) throws Exception {
			System.out.println(messageSource.getMessage("greeting", new String[] {"지니"}, Locale.KOREA));
			System.out.println(messageSource.getMessage("greeting", new String[] {"Jinny"}, Locale.getDefault()));
	}	
}
```

<br/>

(+) 서버 기동중에 메세지 수정할 수 있는 **리로딩 기능**도 사용할 수 있다.

관련 인터페이스는 `ReloadableResourceBundleMessageSource`이다.

**AppApplication.java**

```java
@SpringBootApplication
@PropertySource("classpath:/app.properties")
public class Demo3Application {

	public static void main(String[] args) {
		SpringApplication.run(Demo3Application.class, args);
	}
	
	@Bean
	public MessageSource messageSource() {
		ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource();
		messageSource.setBasename("classpath:/messages");
		messageSource.setDefaultEncoding("UTF-8");
		messageSource.setCacheMillis(3);
		return messageSource;
	}
}
```

<br/>

**AppRunner.java**

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired 
	MessageSource messageSource;
	
	
	@Override
	public void run(ApplicationArguments args) throws Exception {
		while(true) {
			System.out.println(messageSource.getMessage("greeting", new String[] {"지니"}, Locale.KOREA));
			System.out.println(messageSource.getMessage("greeting", new String[] {"Jinny"}, Locale.getDefault()));
			Thread.sleep(1000);
		}
	}	
}
```

서버 기동 후 properties 파일을 수정 후 빌드하면 메세지가 변경됨을 확인할 수 있다.