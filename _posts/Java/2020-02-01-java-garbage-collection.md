---
title: "Java Garbage Collection"
categories:
  - Java
last_modified_at: 2020-02-01T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
줄여서 GC라 불리우는 Garbage Collection은 그저 나 대신 사용하지 않는 메모리를 정리해주고 재할당해주는 개념이라고만 알고 있었지, 깊게 생각해보진 않았다. 하지만 C++ 기반의 엔진 모듈을 디버깅해야 하는 상황이 생기면서 주로 Java를 접했던 나에게 수동 메모리 할당 개념은 생소하게 다가왔다. 이때 GC의 소중함을 느꼈고.. 성능 테스트를 하거나 서버 세팅 시 알맞은 GC 방식을 알아두면 좋다고 하여 정리해본다.

### stop-the-world
GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것을 "stop-the-world"라고 부른다고 한다.
대부분의 GC 튜닝은 "stop-the-world" 시간을 단축하는 것을 의미한다.

### JVM의 개념
JVM(Java Virtual Machine)은 물리적인 형태가 아닌 소프트웨어로서 하나의 개념으로 존재한다. 정확한 설계도는 존재하지 않고 표준화된 정의로만 존재하여 벤더들은 표준에 맞춰서 각자의 JVM을 별도로 구현하여 사용한다. <u>JVM의 역할은 Java 또는 Web Application Server 등을 구별하지 않고 Java 프로그램 범주에 들어가는 모든 것들을 실행시키는 기본 데몬</u>이라고 할 수 있다.

### JVM Heap
Java 메모리 이슈의 장이자 GC가 깊게 연관되어 있는 영역인 Heap은 Instance, Array 객체 2가지 종류만 저장되는 공간이다. 또한, 모든 Thread가 공유되는 영역으로 공유된 Heap Data 이용 시 동기화 문제가 발생할 수 있다.

JVM Heap은 Memory를 할당하는 Instruction만 존재하고 매모리 해제를 위한 Java Code, Bytecode도 존재하지 않는다. Heap의 메모리 할당 및 해제는 오로지 GC로만 수행되므로 개발자가 직접 메모리 컨트롤 할 일은 없다.

JVM Heap은 Young, Old, Perm 3가지 영역으로 나뉜다.    
- <b>Young 영역(Young Generation)</b>: 새롭게 생성한 객체 대부분이 여기에 위치함. 대부분의 객체는 금방 접근 불가능한 상태가 되므로 Young 영역에 생성되었다 사라진다. 이 영역에서 객체가 사라질 때 <b>Minor GC</b>가 발생한다고 말한다.

- <b>Old 영역(Old Generation)</b>: 접근 불가능한 상태로 되지 않아 Young 영역에서 살아남은 객체는 Old 영역으로 복사된다. 대부분 Young 영역보다 크게 할당하며, 그만큼 GC는 적게 발생한다. 이 영역에서 객체가 사라질 때 <b>Major GC</b>가 발생한다고 말한다.

- <b>Perm 영역(Permenent)</b>: Class나 Method의 Meta 정보, Static 변수와 상수 정보들이 저장되는 공간으로 메타데이터 저장영역이라고 부른다. 이 영역에서 GC가 발생할 수 있는데 여기서 GC가 발생해도 <b>Major GC</b>의 횟수에 포함된다.

### Young 영역의 Minor GC
Young 영역은 Eden, Survivor 2개로 총 3개의 영역으로 나뉜다. 아래는 GC가 이루어지는 일반적인 절차로 Serial GC라고 부른다.

- 새로운 객체는 Eden 영역에 위치된다.
- Eden 영역에서 GC가 발생하면 Survivor 영역 중 하나로 이동되고 객체가 계속 쌓인다.
- 하나의 Survivor 영역이 가득차면 그 중에서 살아남은 객체는 다른 Survivor 영역으로 이동하고, 가득 찼던 Survivor 영역은 빈 공간으로 남게 된다.
- 이 과정을 반복하다가 계속해서 사용중인 객체는 Old 영역으로 이동된다.

### Old 영역의 Major GC
Old 영역은 데이터가 가득찰 때 GC를 실행한다. JDK7 기준으로 지원되는 GC 방식은 5가지가 있다.

1. <b>Serial GC</b>  
Young 영역과 Old 영역이 Serial하게 처리되며 하나의 CPU를 사용한다. "stop-the-world"일 때 애플리케이션 수행이 정지 되므로 성능이 매우 떨어진다. 메모리 적고 코어 개수 적을 때 사용한다.
> -XX:+UseSerialGC

2. <b>Parallel GC</b>    
Serial GC와 기본적인 알고리즘은 같지만 Serial은 처리 쓰레드가 1개고 Parallel은 여러 개이다. 그래서 Serial GC보다 빠르게 객체를 처리할 수 있다. 메모리 충분하고 코어의 개수가 많을 때 유리하다.
> -XX:+UseParallelGC

3. <b>Parallel Old GC(Parallel Compacting GC)</b>    
Parallel GC에서 Old 영역의 알고리즘만 다르다.  

      이 방식은 <b>Mark-Summary-Compaction</b> 단계를 거친다.
      - Mark: 살아 있는 객체를 식별하여 표시하는 단계
      - Sweep: 이전에 GC를 수행하여 컴팩션된 영역에 살아 있는 객체의 위치를 조사하는 단계
      - Compaction: 필요 없는 객체들을 지우고 살아 있는 객체들을 한 곳으로 모으는 컴팩션을 수행하는 단계. 수행 이후에는 컴팩션된 영역과 비어 있는 영역으로 나눔


      > -XX:+UseParallelOldGC

4. <b>Concurrent Mark & Sweep GC(이하 CMS)</b>    
Young 영역에 대한 GC는 Parallel GC와 동일하지만 Old 영역의 GC는 다음 단계를 거친다.
      - Mark: 클래스 로더에서 가장 까운 객체 중 살아 있는 객체만 찾는 단계. 대기 시간 매우 짧음
      - Concurrent Mark: 방금 살아있다고 확인한 객체에서 참조하고 있는 객체들을 따라가면서 확인한다. 다른 스레드가 실행중인 상태에서 동시에 진행된다.
      - Remark: Concurrent 표시 단계에서 표시하는 동안 변경된 객체에 대해 다시 표시하는 단계
      - Concurrent Sweep: 표시되어 있는  쓰레기를 정리하는 단계        

      응답 속도가 매우 중요할 때 CMS GC를 사용하지만, 2가지 단점이 있다.
      - 다른 GC 방식보다 메모리와 CPU를 많이 사용
      - Compaction 단계가 제공되지 않음

      >-XX:+UseConcMarkSweepGC

5. <b>G1(Garbage First) GC</b>    

### JVM의 메모리 누수
GC는 참조하지 않는 접근 불가능한 상태의 객체를 제거 대상으로 간주하기 때문에, 사용은 하지 않지만 계속해서 참조를 하는 경우의 객체가 있으면 메모리 누수가 발생할 수 있다. 예를 들어 JDBC 드라이버의 데이터 커넥션 풀을 사용하는 경우가 있다. close() 메소드를 호출하면서 관련된 Statement와 ResultSet 객체를 닫고 GC를 수행한다. 만약 close() 메소드를 호출하지 않으면 Statement, ResultSet은 GC되지 않고 커넥션 풀 안의 데이터베이스 연결에 붙어서 계속 남아 메모리 누수가 발생할 것이다.  


### Reference
[https://12bme.tistory.com/382](https://12bme.tistory.com/382)     
[https://d2.naver.com/helloworld/1329](https://d2.naver.com/helloworld/1329)
