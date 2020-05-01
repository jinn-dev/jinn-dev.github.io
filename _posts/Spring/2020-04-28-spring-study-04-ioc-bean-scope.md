---
title: "Spring 빈의 스코프(@Scope)"
categories:
  - Spring
last_modified_at: 2020-04-28T13s:50:00+09:00
toc: true
toc_sticky: true
comments: true
---

## 빈의 스코프

빈이 참조하는 스코프는 크게 2가지가 있다.    

1. 싱글톤
2. 프로토타입

대부분의 경우에 사용하는 스코프이자 디폴트 값인 싱글톤은 초기화 시 생성한 객체와 인스턴스만 참조하는 것이다.  

극히 일부에서 사용되는 프로토타입은 **참조할 때마다 매번 새로운 객체와 인스턴스를 생성하는 스코프**이다.        

**Single.java**

```java
@Component
public class Single {

	@Autowired
	Proto proto;
	
	public Proto getProto() {
		return proto;
	}
}
```

<br/>

**Proto.java**    

```java
@Component @Scope("prototype")
public class Proto {

}

```

<br/>

**ScopeRunner.java**

```java
@Component
public class ScopeRunner implements ApplicationRunner {

	@Autowired
	Single single;
	
	@Autowired
	Proto proto;
	
	@Override
	public void run(ApplicationArguments args) throws Exception {
		System.out.println(single);
		System.out.println(single);
		System.out.println(single);
		
		System.out.println(proto);
		System.out.println(proto);
		System.out.println(proto);
		
		System.out.println(single.getProto());
		System.out.println(single.getProto());
		System.out.println(single.getProto());
	}
}
```

<br/>

**Result**

```text
com.example.demo3.Single@46b695ec
com.example.demo3.Single@46b695ec
com.example.demo3.Single@46b695ec
com.example.demo3.Proto@408613cc
com.example.demo3.Proto@408613cc
com.example.demo3.Proto@408613cc
com.example.demo3.Proto@325f7fa9
com.example.demo3.Proto@325f7fa9
com.example.demo3.Proto@325f7fa9

```

<br/>

싱글톤 빈은 항상 동일한 객체를 참조하고, 프로토타입 빈은 매번 다른 객체를 참조한다.     

하지만 **싱글톤 빈에서 참조하는 프로토타입 빈은 항상 동일한 객체를 가져오는 것**을 알 수 있다. (의도되지 않은 현상)

이를 고치기 위해 싱글톤 빈이 프로토타입 빈을 직접 참조하게 만들지 않고, **싱글톤 빈 → 프록시 → 프로토타입 빈** 형태로 참조하게 만들면 된다.  ([프록시 패턴 참고](https://en.wikipedia.org/wiki/Proxy_pattern))

@Scope 어노테이션에 `proxyMode = ScopedProxyMode.TARGET_CLASS`를 추가하면 된다.    

**Proto.java**

```java
@Component @Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class Proto {

}
```

<br/>

프록시를 사용하지 않고 코드를 변경하는 방법도 있다. 

**Single.java**

```java
@Component
public class Single {

	@Autowired
	ObjectProvider<Proto> proto;
	
	public Proto getProto() {
		return proto.getIfAvailable();
	}
}
```



## 싱글톤 빈 사용 시 주의할 점

- 멀티쓰레드 환경에서는 프로퍼티가 공유되기 때문에 Thread-Safe하게 싱글톤 객체를 생성하는 것이 중요하다.

- ApplicationContext 초기 구동 시 인스턴스가 생성되기 때문에 Application 시 구동 속도에 영향을 줄 수 있다.

