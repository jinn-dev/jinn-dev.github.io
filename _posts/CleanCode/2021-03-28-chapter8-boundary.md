---
title: "클린코드 8장. 경계(작성중)"
categories:
  - CleanCode
last_modified_at: 2021-03-28T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
## 제 8장. 경계

### 📌외부 코드 사용하기

Map은 다양한 기능을 제공하는 인터페이스이지만, 자유도 만큼 위험도가 크다.

- ex. Map.clear() 메서드로 누구든 지울 권한이 있다.

- ex. 설계 시 특정 유형만 Map에 저장하기로 결정 → 하지만 Map은 객체 유형을 제한하지 않아 어떤 유형이든 추가 가능


Sensor라는 객체를 담는 Map을 만들려면 다음과 같이 Map을 생성한다. Map이 반환하는 Object를 올바른 유형으로 변환할 책임은 클라이언트에 있다.

```java
//bad
Map sensors = new HashMap();
Sensor s = (Sensor)sensors.get(sensorId);
```



제네릭스를 사용하여 가독성을 높인 코드이다. 하지만 사용자에게 필요없는 Map의 기능을 제공한다는건 변함 없다.

Map<String, Sensor> 인스턴스를 여기저기 넘긴다면, Map의 인터페이스가 변한 경우 수정할 코드가 많아진다.

```java
//soso
Map<String, Sensor> sensors = new HashMap<Sensor>();
Sensor s = sensors.get(sensorId);
```



다음은 Map을 좀 더 깔끔하게 사용한 코드다. 제네릭스 사용 여부는 Sensors 안에서 결정되서 사용자가 알 필요가 없어진다.

경계 인터페이스인 Map을 Sensors 안으로 숨긴다.  Map 인터페이스가 변하더라도 나머지 프로그램에는 영향을 주지 않는다.

```java
//good
public class Sensors = {
    private Map sensors = new HashMap();

    public Sensor getById(String id) {
        return (Sensor) sensors.get(id);
    }
}
```


Map 클래스를 사용할 때마다 캡슐화를 하라는 게 아니고,  Map 또는 유사한 인터페이스를 여기저기 넘기지 말라는 뜻이다.

Map과 같은 경계 인터페이스를 사용할 때는 이를 이용하는 클래스나 클래스 계열 밖으로 노출되지 않도록 주의한다.

Map 인스턴스를 공개 API의 인수로 넘기거나 반환값으로 사용하지 않는다.



### 📌경계 살피고 익히기

외부 코드 사용 전, 우리 쪽 코드를 먼저 작성하자.  간단한 테스트 케이스를 만들어 외부 코드를 익히는 것이고 이를 **학습 테스트**라고 한다.

학습 테스트는 API를 사용하려는 목적에 초점을 맞춘다.

#### log4j 익히기

1. 경계 살피고 익히기의 예시로 아파치의 log4 패키지를 사용한다고 가정한다. 문서를 자세히 읽기 전 첫 번째 테스트 케이스를 작성한다.

   ```java
   @Test
   public void testLogCreate() {
       Logger logger = Logger.getLogger("MyLogger");
       logger.info("hello");
   }
   ```



2. 테스트 케이스를 돌리면 Appender라는 뭔가가 필요하다는 오류가 발생한다.  
   문서엔 ConsoleAppender라는 클래스가 있어 ConsoleAppender를 생성한 후 테스트 케이스를 다시 돌린다.

   ```java
   @Test
   public void testLogCreate() {
       Logger logger = Logger.getLogger("MyLogger");
       ConsoleAppender appender = new ConsoleAppender();
       logger.addAppender(appender);
       logger.info("hello");
   }
   ```



3. 이번에는 Appender에 출력 스트림이 없다는 사실을 발견한다. 구글 검색 후 다음과 같이 시도한다.

   ```java
   @Test
   public void testLogCreate() {
       Logger logger = Logger.getLogger("MyLogger");
       logger.removeAllAppenders();
       logger.addAppender(new ConsoleAppender(
         new PatternLayout("%p %t %m%n"), ConsoleAppender.SYSTEM_OUT
       ));
       logger.info("hello");
   }
   ```



4. 테스트 케이스를 여러 번 짜면서 log4 동작을 이해하고 다음과 같은 단위 테스트 케이스를 만들 수 있다.

   ```java
    public class LogTest {
        private Logger logger;

        @Before
        public void initialize() {
            logger = Logger.getLogger("logger");
            logger.removeAllAppenders();
            Logger.getRootLogger().removeAllAppenders();
        }

        @Test
        public void basicLogger() {
            BasicConfigurator.configure();
            logger.info("basicLogger");
        }

        @Test
        public void addAppenderWithStream() {
            logger.addAppender(new ConsoleAppender(
                new PatternLayout("%p %t %m%n"),
                ConsoleAppender.SYSTEM_OUT));
            logger.info("addAppenderWithStream");
        }

        @Test
        public void addAppenderWithoutStream() {
            logger.addAppender(new ConsoleAppender(
                new PatternLayout("%p %t %m%n")));
            logger.info("addAppenderWithoutStream");
        }
    }
   ```





### 📌학습 테스트는 공짜 이상이다

학습 테스트는 투자하는 노력보다 얻는 성과가 크다. 패키지가 예상대로 도는지 검증하며 실제 코드와 동일한 방식으로 인터페이스를 사용하는 테스트 케이스 작성으로 버전업이 쉬워진다.

### 📌아직 존재하지 않는 코드를 사용하기

아직 정의도 되지 않은 외부 API를 사용해야 할 때 **어댑터 패턴** 적용



### 📌깨끗한 경계

경계에 위치하는 코드는 깔끔히 분리하고, 가능한 줄여서 경계를 관리하자. Map에서 봤듯이 새로운 클래스로 경계를 감싸거나, 아니면 어댑터 패턴을 사용한다. 



