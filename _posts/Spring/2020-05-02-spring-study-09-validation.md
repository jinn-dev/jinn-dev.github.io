---
title: "Spring Validation 추상화"
categories:
  - Spring
last_modified_at: 2020-05-02T15s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---

## Validation 추상화

애플리케이션에서 사용하는 일반적인 객체 검증용 인터페이스.    
Bean Validation이 제공하는 여러 Validation Annotation을 사용해서 객체의 데이터 검증용으로 사용할 수 있다.    

<br/> 

### EventValidator 인터페이스

- supports(Class clazz)    
  어떤 타입의 객체를 검증할 때 해당 Validation을 사용할 것인지 결정한다.
- validate(Object obj, Errors e)     
  실제 검증 로직 구현 부분

<br/>

**Event.java**

```java
public class Event {
	
	Integer id;
	
	String title;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}
	
}
```

<br/>

**EventValidator.java**

```java
public class EventValidator implements Validator {

	@Override
	public boolean supports(Class<?> clazz) {
		return Event.class.equals(clazz);
	}

	@Override
	public void validate(Object target, Errors errors) {
		ValidationUtils.rejectIfEmpty(errors, "title", "notEmpty", "Empty title is now allowed");
	}

}
```

`ValidationUtils` 대신 아래처럼 직접 Validation 로직을 추가해서 사용할 수 있다.

```java
Event event = (Event) target;

if(event.getTitle() == null) {
    errors.reject("notEmpty", "Empty title is now allowed");
}
```

<br/>

**AppRunner.java**

```java
@Component
public class AppRunner implements ApplicationRunner {
	
    @Override
	public void run(ApplicationArguments args) throws Exception {
		Event event = new Event();
		EventValidator eventValidator = new EventValidator();
		Errors errors = new BeanPropertyBindingResult(event, "event");
		
		eventValidator.validate(event, errors);
		
		System.out.println(errors.hasErrors());
	
		for(ObjectError e : errors.getAllErrors()) {
			System.out.println("====error code====");
			for(String errMsg : e.getCodes()) {
				System.out.println(errMsg);
			}
			System.out.println(e.getDefaultMessage());
		}
	}
    
}
```

<br/>

<br/>

### Validator 인터페이스

<u>스프링 부트 2.0.5 버전</u> 이상 부터는 `LocalValidatorFactoryBean` 빈으로 자동 등록되어 있으므로 `EventValidator` 대신 `Validator`를 의존성 주입 받아서 사용할 수 있다.        

[Validator 인터페이스](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/validation/Validator.html)

**AppRunner.java**    

```java
@Autowired
Validator validator;

@Override
public void run(ApplicationArguments args) throws Exception {

    System.out.println(validator.getClass());

    Event event = new Event();
    
    //임의 Validation error를 발생하기 위해 값 설정
    event.setLimit(-1);
	event.setEmail("abc");
    
    //EventValidator eventValidator = new EventValidator();
    Errors errors = new BeanPropertyBindingResult(event, "event");

    //eventValidator.validate(event, errors);
    validator.validate(event, errors);

    System.out.println(errors.hasErrors());

    for(ObjectError e : errors.getAllErrors()) {
		System.out.println("====error code====");
        for(String errMsg : e.getCodes()) {
            System.out.println(errMsg);
        }
        System.out.println(e.getDefaultMessage());
    }
}
```

<br/>

**Event.java**    
@NotEmpty, @Min, @Max, @Size, @Email 등 다양한 Validation Annotaion을 쓸 수 있다.

```java
public class Event {
	
	Integer id;
	
	@NotEmpty
	String title;
	
	@Min(0)
	Integer limit;
	
	@Email
	String email;
	
	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public Integer getLimit() {
		return limit;
	}

	public void setLimit(Integer limit) {
		this.limit = limit;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}
}
```

<br/>

**Result**

```text
class org.springframework.validation.beanvalidation.LocalValidatorFactoryBean
true
====error code====
Min.event.limit
Min.limit
Min.java.lang.Integer
Min
반드시 0보다 같거나 커야 합니다.
====error code====
NotEmpty.event.title
NotEmpty.title
NotEmpty.java.lang.String
NotEmpty
반드시 값이 존재하고 길이 혹은 크기가 0보다 커야 합니다.
====error code====
Email.event.email
Email.email
Email.java.lang.String
Email
이메일 주소가 유효하지 않습니다.
```



