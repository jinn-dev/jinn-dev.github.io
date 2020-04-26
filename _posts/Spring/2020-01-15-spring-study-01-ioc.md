---

title: "Spring ApplicationContext와 다양한 Bean 설정 방법"
categories:
  - Spring
last_modified_at: 2020-04-26T22s:00:00+09:00
toc_sticky: true
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

## xml 설정파일로 Bean 등록
### 1. 하나씩 직접 등록

<br/>

**CarRepository.java**   

```java
package Car;

public class CarRepository {

}
```

<br/>

**CarService.java**  

```java
package Car;

public class CarService {

	private CarRepository carRepository;

	public void setCarRepository(CarRepository carRepository) {
		this.carRepository = carRepository;
	}
}
```

<br/>

**application.xml** 

<b>bean 설정 파일(Spring Bean Configuration File)</b>을 생성 후, `CarService`와 `CarRepository`를 Bean 으로 등록한다.     
`<bean>` 을 등록 후, 주입이 필요한 부분은 `<property>` 를 추가해준다. name은 class에서 선언된 객체 이름이고, ref는 해당 bean의 이름이다.

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

**CarMain.java**

`CarService`가 xml 설정 파일에 의해 `CarRepository` 의존성을 주입 받았는지 확인한다.

```java
package Car;

import java.util.Arrays;

import org.springframework.context.ApplicationContext;

public class CarMain {
	public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
		String[] beanDefName = context.getBeanDefinitionNames();

		System.out.println(Arrays.deepToString(beanDefName));

		CarService carService = (CarService) context.getBean("carService");
		System.out.println(carService!=null);
	}
}
```
<br/><br/>

### 2. Component Scan
<u>첫번째 방법은 하나씩 등록하는 게 번거로워서 잘 안쓰는 방법이다.</u> xml에서 빈을 설정해주는 두번째 방법은 `component-scan`을 써서 해당 패키지 범위에 있는 Bean을 모두 스캔하여 등록하는 것이다.

<br/>

**application.xml** 

application.xml에 `<context:component-scan/>`을 추가한다. `src/main/java/Car`를 포함한 하위 패키지를 모두 스캔하여 `@Repository`, `@Service`가 선언된 클래스를 bean으로 등록한다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
	http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd">

  <context:component-scan base-package="Car" />
  </bean>

</beans>
```
<br/>

**CarRepository.java**

- `CarRepository`에 `@Repository` 선언

```java
package Car;

import org.springframework.stereotype.Repository;

@Repository
public class CarRepository {

}
```
<br/>

**CarService.java**

- `CarService`에 `@Service` 선언

- 의존성 주입을 하고 싶은 객체에 `@Autowired` 선언해준다.

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





## Java 설정파일로 Bean 등록

###  1. 하나씩 직접 등록

xml 설정 파일이 아닌 java  class 파일로 Bean을 등록할 수 있다.    

**ApplicationConfig.java**

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

**CarService.java**

`CarService`가 `carRepository`를 의존성 주입을 받기 위해 생성자에 `@Autowired` 선언하여 의존성 주입 가능하다.     

```java
package Car;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class CarService {

	@Autowired
	private CarRepository carRepository;

}
```
<br/>

**ApplicationConfig.java** 

setter를 선언한 경우,  메소드 이름으로 받아  설정 파일에서 <u>직접 의존성 주입</u>을 해줄 수 있다.

```java
	/*
	@Bean
	public CarService carService() {
		return new CarService();
	}
	*/

	@Bean
	public CarService carService() {
        CarService carService = new CarService();
		carService.setCarRepository(carRepository());
    	return carService;
	}
}
```
<br/>

**CarMain.java**

`AnnotationConfigApplicationContext`를 사용하여 `ApplicationConfig.class`를 선언한다.

```java
package Car;

import java.util.Arrays;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class CarMain {
	public static void main(String[] args) {
		/*
		ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
		*/
        
		ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
		
        String[] beanDefName = context.getBeanDefinitionNames();

		System.out.println(Arrays.deepToString(beanDefName));

		CarService carService = (CarService) context.getBean("carService");
		System.out.println(carService!=null);
	}
}
```
<br/><br/>

### 2. Component Scan
`@ComponentScan`에서 basePackageClasses에 클래스를 입력하여 어디부터 스캔할지 알려주고 아래 어노테이션을 찾아 Bean을 등록한다.

* @Component
  * @Repository
  * @Service
  * @Controller
  * @Configuration



**ApplicationConfig.java** 

`@ComponentScan`을 선언하고 CarMain.class부터 스캔하도록 지정해준다.

```java
package Car;

import org.springframework.context.annotation.ComponentScan;

@ComponentScan(basePackageClasses = CarMain.class)
public class ApplicationConfig {

}
```
<br/>

**CarRepository.java**  

- `CarRepository`에 `@Repository` 선언

```java
package Car;

import org.springframework.stereotype.Repository;

@Repository
public class CarRepository {

}
```
<br/>

**CarService.java** 

- `CarService`에 `@Service` 선언

- 의존성 주입을 하고 싶은 객체에 `@Autowired` 선언해준다.

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
