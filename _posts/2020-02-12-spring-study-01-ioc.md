---
title: "스프링 프레임워크 입문(백기선) - IoC (Inversion Of Control)"
categories:
  - Spring
last_modified_at: 2020-02-12T22s:00:00+09:00
toc: true
---
## 스프링 빈(Bean)
&nbsp;&nbsp;&nbsp;&nbsp;객체인데, IoC 컨테이너가 관리하는 객체를 빈이라고 한다.

### 빈 등록하는 방법

(1) Spring이 알아서 IoC가 만들어질 때 등록함      
&nbsp;&nbsp;&nbsp;&nbsp;Annotation Processor 중에서 Spring IoC Container를 사용하고      
&nbsp;&nbsp;&nbsp;&nbsp;Bean을 등록할 때 사용하는 interface는 life cycle callback이라고 한다.    
&nbsp;&nbsp;&nbsp;&nbsp;@ComponentScan은 어디서 어디까지 Scan할지 알려주고 아래 Annotation을 찾아 Bean을 등록한다.
* @Component
  * @Repository
  * @Service
  * @Controller
  * @Configuration

(2) 또는 일일히 xml이나 자바 파일에 등록 (많이 쓰는 추세)   
&nbsp;&nbsp;&nbsp;&nbsp;@Configuration 으로 정의된 클래스 안에 @Bean으로 정의할 수 있음   

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
&nbsp;&nbsp;&nbsp;&nbsp;오로지 Bean만 의존성 주입을 해준다.
