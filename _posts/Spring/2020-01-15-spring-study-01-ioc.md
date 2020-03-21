---
title: "Spring IoC와 DI"
categories:
  - Spring
last_modified_at: 2020-01-15T22s:00:00+09:00
classes: wide
toc: true
comments: true
---
## Spring IoC 컨테이너
IoC 기능을 제공하는 컨테이너로 Bean 들을 담고 있다.
<br/><br/>
## 스프링 빈(Bean)이란?
객체인데, IoC 컨테이너가 관리하는 객체를 빈이라고 한다. 의존성 주입을 하기 위해서 Bean이 되어야 한다.
<br/><br/>
## Bean Scope
1. SingleTon    
default. 하나의 객체로만 사용
2. ProtoType     
`@Scope("prototype")`을 선언하면 호출할 때마다 매번 다른 객체를 사용
<br/><br/>

## 의존성 주입(DI, Dependency Injection)
<br/><br/>
오로지 Bean만 의존성 주입을 해준다. 객체 자체가 아니라 프레임워크에 의해 객체의 의존성이 주입되는 설계 패턴. 필요한 게 있으면 직접 찾거나 만드는 대신 무엇이 필요하다고 선언만 하면 컨테이너에서 알아서 제공해주겠다는 의미. instance의 생성 및 관리 주체가 사용자(나)가 아니라 컨테이너가 되어 <b>제어의 역전(Inversion Of Control)</b>이 되고 이 방법론 중에 하나로 DI가 있다.
<br/><br/>
## Bean 등록하는 방법
### xml에 Bean 등록하기 (1) Bean을 하나하나 다 등록
```java
package Car;

public class CarRepository {

}
```

```java
package Car;

public class CarService {

	private CarRepository carRepository;

	public void setCarRepository(CarRepository carRepository) {
		this.carRepository = carRepository;
	}
}
```

```java
package Car;

import java.util.Arrays;

public class CarMain {
	public static void main(String[] args) {
		ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
		String[] beanDefName = context.getBeanDefinitionNames();

		System.out.println(Arrays.deepToString(beanDefName));

		CarService carService = (CarService) context.getBean("carService");
		System.out.println(carService!=null);
	}
}
```
<br/>
application.xml이라는 이름으로 <b>Bean 설정 파일(Spring Bean Configuration File)</b>을 생성 후, `CarService`와 `CarRepository`를 Bean으로 등록한다.     
`<bean id="" class=""/>` 형식으로 등록 후, 주입이 필요한 부분은 `<property name="" ref=""/>`을 추가해준다.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
	http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">

	<bean id="carService" class="Car.CarService">
		<property name="carRepository" ref="carRepository"></property>
	</bean>
	<bean id="carRepository" class="Car.CarRepository">
	</bean>

</beans>
```
<br/>
`CarService`가 xml 설정 파일에 의해 `CarRepository` 의존성을 주입 받았는지 확인한다.
```java
package Car;

import java.util.Arrays;

import org.springframework.context.support.ClassPathXmlApplicationContext;

public class CarMain {
	public static void main(String[] args) {
		ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
		String[] beanDefName = context.getBeanDefinitionNames();

		System.out.println(Arrays.deepToString(beanDefName));

		CarService carService = (CarService) context.getBean("carService");
		System.out.println(carService!=null);
	}
}
```
<br/><br/>
### xml에 Bean 등록하기 (2) component-scan
1번은 일일히 하나씩 등록하는 게 번거로워서 잘 안쓰는 방법이다. 또 다른 방법으로는 `component-scan`을 써서 해당 패키지 범위에 있는 Bean을 모두 스캔하여 등록하는 방법이 있다.
설정 xml에 `<context:component-scan base-package="Car"/>`을 추가한다. `src/main/java/Car`를 포함한 하위 패키지를 모두 스캔하여 `@Repository`, `@Service`가 선언된 클래스를 Bean으로 등록한다.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
	http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">
  <!--
    <bean id="carService" class="Car.CarService">
    	<property name="carRepository" ref="carRepository"></property>
    </bean>
    <bean id="carRepository" class="Car.CarRepository">
    </bean>
  -->
  <context:component-scan base-package="Car" />
  </bean>

</beans>
```
<br/>
`CarRepository`에 `@Repository` 선언
```java
package Car;

import org.springframework.stereotype.Repository;

@Repository
public class CarRepository {

}
```
<br/>
`CarService`에 `@Service` 선언해주고 의존성 주입을 하고 싶은 객체에 `@Autowired` 선언해준다.
```java
package Car;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class CarService {

	@Autowired
	private CarRepository carRepository;

	public void setCarRepository(CarRepository carRepository) {
		this.carRepository = carRepository;
	}
}
```
<br/><br/>
### Java 파일로 Bean 등록하기
`@Service`, `@Repository` 등의 어노테이션을 사용 안하고 자바 파일로 Bean을 등록할 수 있다.
`@Configuration`을 선언하여 해당 클래스는 Spring IoC Container에 사용되는 클래스라고 정의할 수 있다.
그리고 `@Bean` 어노테이션을 사용하여 Bean을 정의해준다. 메소드 이름은 xml에서의 bean id, return 되는 값은 실제 객체이다.
```java
package Car;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ApplicationConfig {
	@Bean
	public CarRepository carRepository() {
		return new CarRepository();
	}

	@Bean
	public CarService carService() {
		return new CarService();
	}
}
```
<br/>
`CarService`가 `carRepository` 의존성 주입을 받기 위해 수정해 줘야 한다.    

setter를 선언한 경우, 생성자에 `@Autowired` 선언하여 의존성 주입 가능하다.    
```java
package Car;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class CarService {

	@Autowired
	private CarRepository carRepository;

	public void setCarRepository(CarRepository carRepository) {
		this.carRepository = carRepository;
	}
}
```
<br/>
메소드 이름으로 받아 의존성 주입    
```java
ApplicationConfig.java
	@Bean
	public CarService carService() {
		carService.setCarRepository(carRepository()); //메소드 이름으로 주입
    return carService;
	}
}
```
<br/>
파라미터로 받아 의존성 주입    
```java
ApplicationConfig.java
	@Bean
	public CarService carService(CarRepository carRepository) {
    CarService carService = new CarService();
    carService.setCarRepository(carRepository); //파라미터로 받아서 주입
    return carService;
	}
}
```
<br/>
`AnnotationConfigApplicationContext`를 사용하여 `ApplicationConfig.class`를 선언한다.
```java
package Car;

import java.util.Arrays;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class CarMain {
	public static void main(String[] args) {

		//ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
		ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
		String[] beanDefName = context.getBeanDefinitionNames();

		System.out.println(Arrays.deepToString(beanDefName));

		CarService carService = (CarService) context.getBean("carService");
		System.out.println(carService!=null);
	}
}
```
<br/><br/>
### Component Scan(basePackageClasses = )
`@ComponentScan`에서 basePackageClasses에 클래스를 입력하여 어디부터 스캔할지 알려주고 아래 어노테이션을 찾아 Bean을 등록한다.

* @Component
  * @Repository
  * @Service
  * @Controller
  * @Configuration

```java
package Car;

import org.springframework.context.annotation.ComponentScan;

@ComponentScan(basePackageClasses = CarMain.class)
public class ApplicationConfig {

}
```
<br/>
`CarRepository`에 `@Repository` 선언
```java
package Car;

import org.springframework.stereotype.Repository;

@Repository
public class CarRepository {

}
```
<br/>
`CarService`에 `@Service` 선언해주고 의존성 주입을 하고 싶은 객체에 `@Autowired` 선언해준다.
```java
package Car;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class CarService {

	@Autowired
	private CarRepository carRepository;

	public void setCarRepository(CarRepository carRepository) {
		this.carRepository = carRepository;
	}
}
```
