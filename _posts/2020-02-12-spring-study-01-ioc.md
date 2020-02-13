---
title: "스프링 프레임워크 입문(백기선) - IoC (Inversion Of Control)"
categories:
  - Spring
last_modified_at: 2020-02-12T22s:00:00+09:00
toc: true
comments: false
---
## 스프링 빈(Bean)
객체인데, IoC 컨테이너가 관리하는 객체를 빈이라고 한다.

### 빈 등록하는 방법

(1) Spring이 알아서 IoC가 만들어질 때 등록함      
Annotation Processor 중에서 Spring IoC Container를 사용하고      
Bean을 등록할 때 사용하는 interface는 life cycle callback이라고 한다.    
@ComponentScan은 어디서 어디까지 Scan할지 알려주고 아래 Annotation을 찾아 Bean을 등록한다.
* @Component
  * @Repository
  * @Service
  * @Controller
  * @Configuration

(2) 또는 일일히 xml이나 자바 파일에 등록 (많이 쓰는 추세)   
@Configuration 으로 정의된 클래스 안에 @Bean으로 정의할 수 있음   

```java
package org.springframework.samples.petclinic.sample;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SampleConfig {
	@Bean
	public SampleController sampleController() {
		return new SampleController();
	}
}
```

## 의존성 주입(Dependeny Injection)
오로지 Bean만 의존성 주입을 해준다. 객체 자체가 아니라 Framework에 의해 객체의 의존성이 주입되는 설계 패턴. 필요한 게 있으면 직접 찾거나 만드는 대신 무엇이 필요하다고 선언만 하면 컨테이너에서 알아서 제공해주겠다는 의미. instance의 생성 및 관리 주체가 사용자(나)가 아니라 컨테이너가 되어 제어의 역전(Inversion Of Control)이 되고 이 방법론 중에 하나로 DI가 있음. 의존성 주입할 객체 선언한 부분에 @Autowired를 달아준다.  


* 필드           
```java
@Controller
class OwnerController {
    @Autowired
    private OwnerRepository owners;
}
```


* 생성자    
```java
@Controller
class OwnerController {

    private OwnerRepository owners;

    public OwnerController(OwnerRepository clinicService) {
        this.owners = clinicService;
    }
}
```

* setter    
```java    
@Controller
class OwnerController {

    private OwnerRepository owners;

    public OwnerController(OwnerRepository clinicService) {
        this.owners = clinicService;
    }
}
```
