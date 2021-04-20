---
title: "아이템50. 적시에 방어적 복사본을 만들라"
categories:
  - EffectiveJava
last_modified_at: 2021-04-20T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---

## 아이템50. 적시에 방어적 복사본을 만들라

### 📌자바는 안전한 언어

네이티브 메서드를 사용하지 않아 메모리 충돌 오류에서 안전하다.



### 📌생성자의 방어적 복사

- 악의적 의도를 가진 클라이언트 요청으로 불변식이 깨질 수 있다.
- 개발자 실수로 클래스가 오작동할 수 있다. 



기간을 표현하는 클래스로 불변식을 추구하는 것 처럼 보인다. 

```java
import java.util.Date;

public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        if (start.compareTo(end) > 0)
            throw new IllegalArgumentException(
                    start + " after " + end);
        this.start = start;
        this.end = end;
    }

    public Date getStart() {
        return start;
    }

    public Date getEnd() {
        return end;
    }
}
```



하지만 Data는 가변으로 쉽게 불변식을 깨뜨릴 수 있다.

```java
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);
end.setYear(78); //p의 내부를 수정했다.
```

Date는 불변성을 해치기 때문에 되도록 사용하지 말고, 자바 8 환경이라면 Instant나 LocalDateTime, ZonedDateTime을 사용하자.
{: .notice--info}


Period 인스턴스 내부를 보호를 위해 생성자에서 받은 가변 매개변수 각각을 방어적으로 복사하는 방법도 있다.

```java
import java.util.Date;

public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        this.start = new Date(start.getTime());
        this.end = new Date(end.getTime());

        if (start.compareTo(end) > 0)
            throw new IllegalArgumentException(
                    start + " after " + end);
    }
}
```

매개변수 유효성 검사하기 전에(아이템 49) 방어적 복사본을 만든 이유는
**멀티 스레딩 환경에서의 다른 스레드가 원본 객체를 수정할 위험을 줄이기 위해서다**. 
(그 찰나를 줄여서 누구보다 빠르게 원복 객체를 복사하는 것임) 

또한, 복사할 때 clone을 사용하지 않은 점도 주목해야 한다. 
Date는 가변이기 때문에 하위 클래스의 인스턴스를 반환할 수 있다. 이 하위 클래스가 start와 end 필드의 참조를 private 정적 리스트에 담아두면 공격자가 이 리스트에 접근할 수 있다. **매개변수가 제3자에 의해 확장될 수 있는 타입이면 방어적 복사본을 만들 때 clone을 사용하면 안된다.**



### 📌가변 필드의 방어적 복사본 반환

접근자 메서드가 내부의 가변 정보를 직접 드러내서 계속 Period를 바꿀 수 있다.

```java
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);
p.end().setYear(78);
```


이를 막기 위해 접근자가 가변 필드의 방어적 복사본을 반환하도록 하자.

```java
public Date getStart() {
return new Date(start.getTime());
}

public Date getEnd() {
return new Date(end.getTime());
}
```

생성자와 달리 접근자 메서드에서는 방어적 복사에 clone을 사용해도 된다. Period의 Date는 java.util.Date가 확실하기 때문이다. 그래도 인스턴스 복사는 생성자나 정적 팩토리 사용을 추천한다.

클래스가 불변이든, 가변이든 가변인 내부 객체를 반환 할 때 안심할 수 없는 상황이면 방어적 복사본을 반환해야 한다. 길이 1 이상인 배열은 무조건 가변으로 항상 방어적 복사를 수행하자. 혹은 **배열의 불변 뷰를 반환**하는 대안도 있다. (아이템 15)



### 📌되도록 불변 객체를 조합해 객체를 구성

*되도록 불변 객체들을 조합해 객체를 구성해야 방어적 복사를 할 일이 줄어든다*

Period 예제의 경우, 자바 8이상으로 개발해도 된다면 Instant, LocalDateTime 또는 ZonedDateTime을 사용하는 것이 좋다. 이전 버전의 자바를 사용한다면 Date 참조대신 Date.getTime()이 반환하는 long 정수를 사용하는 방법을 써도 된다.



### 📌방어적 복사는 필요에 따라 쓰자

클라이언트 ↔ 클래스로 주고 받는 구성요소가 가변이면 반드시 방어적 복사를 해야 한다.  
하지만, 방어적 복사는 성능 저하가 따르고, **같은 패키지에 속하는**❓ 등의 이유로 사용 불가능 할 수 있다. 호출자가 컴포넌트 내부를 수정하지 않는다는 확신이 있으면 문서화를 통해 생략 가능하기도 하다. 



### 참고자료 

http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEa&Kc= 

