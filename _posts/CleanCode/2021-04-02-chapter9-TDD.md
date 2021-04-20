---
title: "클린코드 9장. 단위 테스트"
categories:
  - CleanCode
last_modified_at: 2021-04-02T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true

---

## 제 9장. 단위 테스트

### 📌TDD 법칙 3 가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 마라.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위테스트를 작성
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성

실제 코드와 맞먹을 정도의 테스트 코드를 작성하는건 심각한 관리 문제를 유발한다.



### 📌깨끗한 테스트 코드 유지하기

테스트 코드도 잘 설계하고 주의해서 분리해야 한다. 지저분할 수록 변경하기 어려워진다.

*테스트는 유연성, 유지보수성, 재사용성을 제공한다.*

실제 코드를 점검하는 자동화된 단위 테스트 슈트는 설계와 아키텍처를 최대한 깨끗하게 보존하는 열쇠다.



### 📌깨끗한 테스트 코드(feat. 가독성)

**BUILD-OPERATE-CHECK  패턴**

1. BUILD - 테스트를 위한 기본 설정
2. OPERATE - 실제 테스트 자료 조작
3. CHECK - 조작한 결과가 맞는지 확인

```java
public void testGetDataAsXml() throws Exception {
    //build
    makePageWithContent("TestPageOne", "test page");
    //operate
    submitRequest("TestPageOne", "type:data");
    //check
    assertResponseIsXml();
    assertResponseContains("test page", "<Test");
}
```

*도메인에 특화된 테스트 언어*

API 위에다 함수와 유틸리티를 구현한 후 그 함수와 유틸리티를 사용하여 테스트 코드를 작성하고 읽기 쉬워진다. (ex. makePageWithContent, submitRequest)

이렇게 구현한 함수와 유틸리티는 테스트 코드를 위한 특수 API가 된다.



### 📌테스트 당 assert 하나

함수마다 assert문을 단 하나만 사용해야 한다는 지침은 훌륭하지만 꼭 지킬 필욘 없다. 

단지, 깨끗한 테스트 코드를 위해 함수 내에서 개념 하나만을 테스트 해야 한다.



### 📌F.I.R.S.T

1. Fast 
   - 테스트는 빨리 돌아야 한다.
2. Independent
   - 각 테스트는 독립적이어야 한다. 한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안된다.
3. Repeatable
   - 테스트는 어떠한 환경에서도 반복 가능해야 한다.
4. Self-Validating
   - 테스트는 성공 아니면 실패, bool 값으로 결과를 내야 한다. 
5. Timely
   - 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 작성한다.
     실제 코드를 테스트 가능하도록 구현할 수 있다.

