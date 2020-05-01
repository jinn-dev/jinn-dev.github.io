---
title: "Spring Resource 및 ResourceLoader"
categories:
  - Spring
last_modified_at: 2020-05-01T15s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---

## ResourceLoader

Spring은 리소스 읽어오는 기능을 제공하는 인터페이스인 ResourceLoader를 제공한다.     ApplicationContext에서 제공되는 인터페이스이기 때문에 ResourceLoader 대신 ApplicationContext 객체를 사용해도 된다.   

```java
@Component
public class AppRunner implements ApplicationRunner {

	@Autowired
	ResourceLoader resourceLoader;

	@Override
	public void run(ApplicationArguments args) throws Exception {
		Resource resource = resourceLoader.getResource("classpath:test.txt");
		System.out.println(resource.exists());
		
	}	
}
```



## Resource의 추상화

스프링 내부에서 가장 많이 사용하는 인터페이스로 java.net.URL을 추상화 한 것이다. (org.springframework.core.io.Resource)    

<br/>

리소스를 읽어오는 방법은 2가지이다. 

- Resource의 타입은 location 문자열과 **ApplicationContext 타입에 따라 결정**된다.
  - ClassPathXmlApplicationContext → ClassPathResource
  - FileSystemXmlApplicationContext → FileSystemResource
  - WebApplicationContext → ServletContextResource
- (추천) ApplicationContext 타입에 상관없이 리소스 타입을 강제하려면 **java.net.URL 접두어 중 하나를 사용**할 수 있다. 

  - classpath: → ClassPathResource
  - file: → FileSystemResource


<br/>

아래 코드로 ApplicationContext 타입이 처음에 어떻게 설정되어 있는지와(`ResourceLoader.getClass`) 접두어를 사용해서 Resource 구현체를 강제해주었을 때 어떤 타입으로 바꼈는지(`resource.getClass`) 확인할 수 있다.

```java
	@Autowired
	ResourceLoader resourceLoader;

	@Override
	public void run(ApplicationArguments args) throws Exception {
		System.out.println(resourceLoader.getClass());
		
		Resource resource = resourceLoader.getResource("classpath:test.txt");
		System.out.println(resource.getClass());
		
		System.out.println(resource.exists());
		System.out.println(resource.getDescription()); 
        System.out.println(resource.getURI().getPath());
	}
```

```text
class org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext
class org.springframework.core.io.ClassPathResource
true
class path resource [test.txt]
/D:/dev/workspace-spring-tool-suite-4-4.6.0.RELEASE/demo4/target/classes/test.txt
```

`ClassPathResource`가 리졸빙 된것을 확인할 수 있다.

<br/>

위 코드에서 `classpath:` 접두어를 안쓰고 파일명만 주고 실행을 해보면

```text
2020-05-01 14:44:40.238  INFO 17320 --- [           main] com.example.demo4.Demo4Application       : Starting Demo4Application on DESKTOP-JOQCK4O with PID 17320 (D:\dev\workspace-spring-tool-suite-4-4.6.0.RELEASE\demo4\target\classes started by jekwak in D:\dev\workspace-spring-tool-suite-4-4.6.0.RELEASE\demo4)
2020-05-01 14:44:40.242  INFO 17320 --- [           main] com.example.demo4.Demo4Application       : No active profile set, falling back to default profiles: default
2020-05-01 14:44:41.205  INFO 17320 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2020-05-01 14:44:41.214  INFO 17320 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2020-05-01 14:44:41.215  INFO 17320 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.33]
2020-05-01 14:44:41.319  INFO 17320 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2020-05-01 14:44:41.319  INFO 17320 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1026 ms
2020-05-01 14:44:41.501  INFO 17320 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2020-05-01 14:44:41.663  INFO 17320 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-05-01 14:44:41.666  INFO 17320 --- [           main] com.example.demo4.Demo4Application       : Started Demo4Application in 1.792 seconds (JVM running for 2.271)

class org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext
class org.springframework.web.context.support.ServletContextResource
false
ServletContext resource [/test.txt]
2020-05-01 14:44:41.670  INFO 17320 --- [           main] ConditionEvaluationReportLoggingListener : 

Error starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
2020-05-01 14:44:41.675 ERROR 17320 --- [           main] o.s.boot.SpringApplication               : Application run failed

java.lang.IllegalStateException: Failed to execute ApplicationRunner
```

Resource 타입이 `ServletContextResource`로 리졸빙 되고 **Class Path 가 아닌 Context Path부터** 파일을 찾게 된다.    

스프링 부트의 내장형 톰캣에는 Context Path가 기본적으로 지정이 되있지 않으므로 실제 파일을 참조하려고 하면 에러가 발생한다. (`resource.getURI().getPath()` 부분)

Spring Boot 기반 프로젝트를 개발할 땐 classpath 기준으로 하기 때문에 되도록이면 접두어를 붙여서 사용하자.



## Reference

  [Resource 인터페이스](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/Resource.html)

  [ResourceLoader 인터페이스](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ResourceLoader.html)

