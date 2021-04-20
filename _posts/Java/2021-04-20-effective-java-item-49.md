---
title: "아이템 49. 매개변수가 유효한지 검사하라"
categories:
  - Java
last_modified_at: 2021-04-20T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---

## 아이템49. 매개변수가 유효한지 검사하라

### 📌오류는 가능한 한 빨리 (발생한 곳에서) 잡아야 한다.

메서드 몸체가 실행되기 전에 매개변수를 검사한다면, 잘못된 값이 넘어왔을 경우 깔끔한 예외를 던질 수 있다.



### 📌매개변수 검사에 실패했을 때 발생하는 문제점?

1. 메서드 수행되는 중간에 모호한 예외를 던지며 실패 처리

2. 메서드 수행되었지만 잘못된 결과 발생

3. 메서드 정상 수행, 객체의 오염으로 미래의 알 수 없는 시점에서 메서드와 관련 없는 오류 발생 
   → 실패 원자성 (아이템 76)

   

### 📌공개 API는 반드시 문서화 해라.

`public`과 `protected`는 매개변수의 제약, 매개변수 값이 잘못되었을 때 던지는 예외를 문서화 해야 한다. 
→ `@thorws` 자바독 태그 사용(아이템 74) 

- IllegalArgumentException
  호출자가 인수로 부적절한 값을 넘길 때 예외 처리, 가장 많이 재사용되는 예외(아이템 49)
- IndexOutOfBoundsException
  리스트, 배열 사이즈보다 참조를 시도한 인덱스가 더 클 경우 발생
- NullPointerException
  NULL 값을 허용하지 않는 메서드에 NULL을 건네면 관례상 예외 던지는 예외 처리

아래는 문서화의 예시로, 개별 메서드가 아니라 BigInteger 클래스 수준에서 예외 처리 정보를 기술했다.

```java
    /**
     * Returns a BigInteger whose value is {@code (this mod m}).  This method
     * differs from {@code remainder} in that it always returns a
     * <i>non-negative</i> BigInteger.
     *
     * @param  m the modulus.
     * @return {@code this mod m}
     * @throws ArithmeticException {@code m} &le; 0
     * @see    #remainder
     */
    public BigInteger mod(BigInteger m) {
        if (m.signum <= 0)
            throw new ArithmeticException("BigInteger: modulus not positive");

        BigInteger result = this.remainder(m);
        return (result.signum >= 0 ? result : result.add(m));
    }
```



### 📌매개변수 NULL 체크에 유용한 방법

```java
this.strategy = Objects.requireNonNull(strategy, "ErrorMessage");
```

자바 9는 Obejcts에 범위 검사 기능도 더해졌지만, requireNonNull 처럼 유연하지 않다. 
(checkFromIndexSize, checkFromToIndex, checkIndex)

1. 예외 메시지 지정 불가
2. 리스트와 배열 전용으로 설계되어 다른 컬렉션 사용 불가
3. 닫힌 범위❓를 다루지 못함
   



### 📌단언문(assert)으로 매개변수 유효성 검증하기

private 메서드는 개발자가 메서드 호출되는 상황을 통제할 수 있다. 

```java
private static void sort(long a[], int offset, int length) {
    assert a != null;
    assert offset >= 0 && offset <= a.length;
    assert length >= 0 && length <= a.length - offset;
    //계산 수행 ...
}
```

#### 일반적인 유효성 검사와 다른 점?

1. 실패하면 AssertionError를 던진다.
2. 런타임에 아무런 효과도, 아무런 성능 저하 없음
   (단, java를 실행할 때 명령줄에서 -ea 혹은 --enableassertions 플래그를 설정하면 런타임에 영향을 준다).



### 📌 메서드 실행 전 매개변수 검사 규칙의 예외 상황

유효성 검사 비용이 지나치게 높거나, 실용적이지 않은 경우, 
혹은 **계산 과정에서 암묵적으로 검사가 수행되는 경우**가 있다.

Collections.sort(List)는 정렬 과정에서 객체 비교가 자연스럽게 이루어진다.
예외처리로 ClassCastException을 던지기 때문에 비교 전에 검사를 안해도 된다.

하지만 암묵적인 검사에 의존할 경우 **실패 원자성**을 해칠 수 있으니 주의하자.

또한, 매개변수에 제약을 두는 것보다 메서드는 최대한 범용적으로 설계하는 것을 추구하자.



### 참고자료 

http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEa&Kc= 

