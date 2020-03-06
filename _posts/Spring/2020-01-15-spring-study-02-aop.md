---
title: "스프링 프레임워크 입문(백기선) - AOP"
categories:
  - Spring
last_modified_at: 2020-01-15T22s:00:00+09:00
toc: true
comments: false
---



### 다양한 AOP 구현 방법
OOP를 적용하여도 핵심기능에서 중복되는 부가기능을 분리하는게 쉽지 않을 때가 있는데 이런 문제점을 AOP가 해결해준다.

1. 컴파일을 이용하는 방법
A.java -> (AOP) -> A.class

2. 바이트코드 조작
A.java -> A.class -> (AOP) - 메모리(AspectJ)

3. 프록시패턴 (스프링 AOP가 사용하는 방법)

**Aspect란?** 부가기능을 정의한 코드인 Advice와 Advice를 어디에 적용할 지 결정하는 PointCut을 합친 개념이다. AOP 개념을 적용하면 핵심기능에서 사용된 부가기능을 독립적인 Aspect로 구분해 낼 수 있다.
{: .notice--info}
