---
title: "Java Collection Framework"
categories:
  - Java
last_modified_at: 2020-03-17T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
## Java Collection

List
- 순서 유지하고 저장
- 중복 저장 가능

Set
- 순서 유지하지 않고 저장
- 중복 허용 안됨

### List Collection (feat. Array vs ArrayList vs LinkedList)
Array(일반 배열)과 ArrayList는 배열에 객체를 저장해서 인덱스로 관리한다는 점은 동일하다.
하지만 **Array** 는 생성할 때 크기가 고정되고 사용 중에 크기를 변경할 수 없지만,
**ArrayList** 는 저장 용량 초과한 객체가 들어오면 용량이 늘어난다.

Array와 ArrayList는 찾고자 하는 인덱스 값을 알고 있으면 검색할 때 O(1)에 해당 원소로 접근할 수 있다.
추가/삭제 시에는 O(n)의 비용이 발생한다. 빈 공간을 확보하거나 채우기 위해 index를 shift 해줘야 하기 때문이다.

추가/삭제가 빈번한 경우는 LinkedList를 사용하는게 바람직하다.


인덱스 검색이나 맨 마지막에 객체를 추가하는 경우는 ArrayList가 더 좋은 성능을 발휘한다.
{: .notice--info}

**LinkedList** 는 인접 참조를 링크해서 체인처럼 관리한다. 각각의 원소는 자기 자신 다음에 어떤원소인지만 기억하면 된다.
만약 삭제가 되면 삭제된 객체 앞뒤 링크만 변경된다.   

따라서, 순차적으로 추가/삭제 할때 ArrayList를, 중간에 추가/삭제가 많을 땐 LinkedList를 쓰는게 효율적이다.

**Vector** 는 ArrayList와 유사하지만 동기화 메소드가 구성되어 있어 동시에 실행할 수 없고 Thread-Safe하다.

### Set Collection
Set은 List와 다르게 저장 순서가 유지되지 않는다. 그리고 객체를 중복해서 저장할 수 없고, 하나의 null만 저장할 수 있다.
전체 객체를 대상으로 한번씩 반복해서 가져오는 반복자(Iterator)를 사용한다.

**HashSet** 은 객체를 순서 없이 저장하고 중복을 허용하지 않는다.
동일한 객체란 꼭 같은 인스턴스를 의미하는 게 아니며 객체들의 해시코드로 비교한다.
같은 문자열을 갖는 String은 동등한 객체로 간주되는 데 String 클래스가 hashCode(), equals() 메소드를 재정의해서
같은 문자열일 경우 hashCode()의 리턴값을 같게, equals()의 리턴값을 true가 나오도록 했기 때문이다.

### Map Collection
List와 Set이 순서나 집합적인 개념의 인터페이스라면 Map은 검색의 개념이 가미된 인터페이스다.
Map 인터페이스는 데이터를 삽입할 때 Key와 Value의 형태로 삽입되며, Key를 이용해서 Value를 얻을 수 있다.

keySet() 메소드로 모든 키를 Set 컬렉션으로 얻은 다음, 반복자를 통해 키를 하나씩 얻고 get() 메소드로 값을 얻으면 된다.

HashTable은 HashMap과 동일한 내부구조를 가지고 있지만 동기화 메소드로 구성되어 있어 Thread-Safe하다.

### Q. Hash에 대해 설명할 수 있나요?
자료구조의 대표적인 질문 Hash, 평균 시간 복잡도가 O(1)로 매우 빠르기 때문에 실무에서 자주 쓰이는 대표적인 자료구조이다.
따라서 기술면접이라면 자료구조 파트에서 나올 수 있는 대표적인 질문이다.
각 아이템의 Hash값을 통해 해당 값에 바로 접근할 수 있는 자료구조이며 Key-Value 한 쌍으로 구성된다.

### Q. Hash가 왜 좋은가?
값을 거의 바로 찾을 수 있다. 32라는 값을 찾기 위해 해쉬 자료구조가 아니라면
처음부터 끝까지(최악의 경우에) 값을 비교해보면서 찾던가, 가장 우수하게 찾더라도 정렬되 있음이 전제가 되야 하지만
해쉬는 정렬이 되어 있지 않더라도 Hash함수가 만들어준 인덱스로 바로 접근하기때문에 바로 접근이 가능 거의 원샷원킬이다.

### Q. 그렇다면 Hash는 만능인가?
그럴리가... 만약 Hash함수를 거지같이 짜서 배열에 중복이 많이 발생한다면(이를 해쉬충돌이라고 한다, 파생질문 참조) 저장하는데도, 찾는데도 문제가 발생한다.
해쉬의 성능은 해쉬함수의 영향을 많이 받는다.

### Q. 해시충돌이 무엇이고 해결책을 알고 있나요?
서로 다른 키 값이 해시함수의 잘못된 설계로 같은 해시값이 나오는 것이다. 배열에 중복이 많이 발생한다면 추가와 검색하는데 문제가 발생한다.

이를 처리하는 방법은 크게 2가지가 있다.

**Open Addressing** : 충돌이 일어나면 다른 인덱스에 저장하도록 조정한다.  (선형탐사, 제곱탐사, 이중해쉬 등)

**Close Addressing** : 해쉬값과 실제 저장된 위치는 다를 수 없다. 충돌이 일어나도 해당 위치에 저장하되 배열로 쌓거나 연결리스트로 연결한다.

[Hash 질문 참고 링크](https://stdin2stdout.tistory.com/category/%EB%A9%B4%EC%A0%91%EC%A7%88%EB%AC%B8)
