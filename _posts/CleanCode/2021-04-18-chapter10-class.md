---
title: "클린코드 10장. 클래스"
categories:
  - CleanCode
last_modified_at: 2021-04-18T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true


---

## 제 10장. 클래스

### 📌클래스 체계

추상화 단계가 순차적으로 내려간다. 

1. static, public 상수
2. private 변수
3. private 인스턴스 변수
4. public 함수
5. private 함수
   

#### 캡슐화

- 변수와 유틸리티 함수는 가능한 private으로 하는 것이 낫다.
- 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.
  

### 📌클래스는 작아야 한다

함수와 마찬가지로 클래스를 설계할때도 '작게'가 기본 원칙이다.

함수는 물리적인 행 수로 크기를 측정하지만 **클래스는 맡은 책임을 세는 것**이다.

클래스 이름은 해당 클래스 책임을 기술해야 하는데, 간결한 이름이 떠오르지 않으면 클래스 크기가 커서 그런 것이다. 


#### 단일 책임 원칙

SRP, Single Responsibility Priciple은 **클래스나 모듈을 변경할 이유가 하나**여야 한다는 원칙이다. 책임, 즉 변경할 이유를 파악하려 애쓰다 보면 코드를 추상화 하기도 쉬워진다. 

```java
public class SuperDashboard extends JFrame implements MetaDataUser {
    public Component getLastFocusedComponent()
    public void setLastFocused(Component lastFocused)
    public int getMajorVersionNumber()
    public int getMinorVersionNumber()
    public int getBuildNumber() 
}
```

위 코드도 여러 책임이 있어 좋은 코드는 아니다.

1. 소프트웨어 버전이 바뀔 경우 변경 필요
2. 스윙 컴포넌트를 상속받아서 스윙의 버전도 관리해야 함

여기서 버전 정보를 다루는 메소드만 따로 빼서 Version이라는 클래스를 만들어서 책임을 줄이면, 다른 애플리케이션에서 재사용하기 아주 쉬운 구조가 된다.

```java
public class Version {
	public int getMajorVersionNumber()
	public int getMinorVersionNumber()
	public int getBuildNumber()
}
```



#### 응집도

#### 응집도를 유지하면 작은 클래스 여럿이 나온다





### 📌변경하기 쉬운 클래스

#### 변경으로부터 격리



## 

