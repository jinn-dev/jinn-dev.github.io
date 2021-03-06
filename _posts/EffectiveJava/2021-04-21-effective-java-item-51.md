---
title: "아이템51. 메서드 시그니처를 신중히 설계하라"
categories:
  - EffectiveJava
last_modified_at: 2021-04-21T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---

## 아이템51. 메서드 시그니처를 신중히 설계하라

🙎‍♀ 클린코드 3장 메서드와 유사한 내용이라 같이 참고하면 좋을듯 하다.

### 📌메서드 이름을 신중히 짓자

표준 명명 규칙(아이템 68)을 따르자. 같은 패키지에 속한 다른 이름들과 일관되게 짓는게 최우선이다. 그 다음 목표는 개발자  커뮤니티에서 널리 받아들여지는 이름을 사용하는 것이다.



### 📌편의 메서드를 너무 많이 만들지 말자

메서드가 너무 많은 클래스는 관리하기 힘들다. 클래스나 인터페이스는 자신의 각 기능을 완벽히 수행하는 메서드로 제공해야 한다. **아주 자주 쓰일 경우만 별도 약칭 메서드를 두고** 확신이 서지 않으면 만들지 말자.

🙎‍♀클린코드에서 메서드는 작게 만드는게 좋다고 그랬는데, 작게 쪼갤 수록 메서드가 많아질 수 있어서 넘모 어렵네; 적당한 선을 지키는게 뭐든 중요하겠다.



### 📌매개변수 목록은 짧게 유지하자

4개 이하가 좋다. 또한, 같은 타입의 변수 여러 개가 연달아 나오는 경우를 주의하자. 

#### 과하게 긴 매개변수 목록을 짧게 줄이는 방법

- 여러 메서드로 쪼개고, 쪼개진 메서드 각각은 원래 매개변수 목록의 부분집합을 받도록 한다. 
  
  ex. List에서 지정된 범위의 부분 리스트에서부터 원소를 찾는 경우? 

  3개 필요 - 부분 리스트 시작, 부분 리스트 끝, 찾을 원소 > subList, indexOf
  
- 매개변수 여러 개를 묶어주는 도우미 클래스를 만든다. 일반적으로 이런 도우미 클래스는 정적 멤버 클래스로 둔다. 특히 잇따른 매개변수 몇 개를 독립된 하나의 개념으로 볼 수 있을 때 추천하는 기법이다. (아이템 24)

- 앞서의 두 기법을 혼합한 것으로, 객체 생성에 사용한 빌더 패턴을 메서드 호출에 응용한다. 이 기법은 매개변수가 많고, 그중 일부는 생략해도 괜찮을 때 좋다. (아이템 2)



### 📌매개변수의 타입으로는 클래스보다는 인터페이스가 더 낫다

매개변수로 적합한 인터페이스가 있다면 (이를 구현한 클래스가 아닌) 그 인터페이스를 직접 사용하는 것이 좋다. 메서드에 HashMap 대신 Map을 넘기면, HashMap 뿐만 아니라 TreeMap의 부분맵,  TreeMap 등 어떤 Map 구현체든 인수로 넘기는 게 가능하다. 인터페이스 대신 클래스를 사용하면 클라이언트에게 특정 구현체만 사용하도록 제한하여, 다른 구현체를 넘길 때 복사 비용이 발생한다.



### 📌 boolean보다는 원소 2개짜리 열거 타입이 낫다

매개변수로 boolean을 넘기기 보다는 여러 개의 선택지가 있는 Enum을 넘기는 게 가독성과 확장성이 용이하다.



### 참고자료 

http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEa&Kc= 