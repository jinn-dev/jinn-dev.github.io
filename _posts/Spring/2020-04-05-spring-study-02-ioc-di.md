---
title: "Spring에서 Dependency Injection을 하는 방법"
categories:
  - Spring
last_modified_at: 2020-04-27T22s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---
## Overview
어노테이션을 안쓸 경우 생성자를 직접 명시하거나 XML에 bean을 따로 등록해줘야 한다.     

생성자로 바로 주입받는 경우는 어노테이션을 생략할 수 있다.
해당 객체가 생성될 때 반드시 그 객체를 주입받아야 하기 때문이다.    
```java
@Service
public BoardServiceImpl {
  private BoardDAO boardDAO;

  public BoardServiceImpl(BoardDAO boardDAO) {
    this.boardDAO = boardDAO;
  }
}
```

## Autowired
`@Autowired`와 `@Resource`는 둘 다 어노테이션 선언만 해주면 컨테이너가 객체를 찾아서 주입을 시켜준다.      

`@Autowired`는 **객체의 타입>이름** 순으로 확인해서 주입될 클래스를 탐색한다.       
**스프링 프레임워크가 지원** 하는 어노테이션이라서 스프링 프레임워크에 종속적이다.      
필드, 생성자, 입력 파라미터가 여러개인 메소드에 적용 가능하다.       

<br/>

**필드에 @Autowired를 적용한 예시**

```java
@Service
public BoardServiceImpl {
  @Autowired
  private BoardDAO boardDAO;
}
```

<br/>

setter로 주입되는 경우는 생성자처럼 필수적으로 DI가 되는 게 아닌 필요에 의해 DI가 되는 것이다.       
`@Autowired`의 `required()` 기본값이 `true`이기 때문에 에러가 발생한다.     

```java
@Service
public BoardServiceImpl {
  private BoardDAO boardDAO;

  @Autowired
  public setBoardDAO(BoardDAO boardDAO) {
    this.boardDAO = boardDAO;
  }
}
```

이럴 경우는 `required()`를 `false`로 변경해줘야 한다.        

```java
@Service
public BoardServiceImpl {
  private BoardDAO boardDAO;

  @Autowired(required = false)
  public setBoardDAO(BoardDAO boardDAO) {
    this.boardDAO = boardDAO;
  }
}
```

## Autowired의 다형성 문제

1. **@Qualifier**   
    IoC 컨테이너 내에 같은 타입의 빈이 여러 개 검색되었을 경우 `@Qualifier` 어노테이션을 사용하여 구분할 수 있다.  명칭은 스몰 케이스로 시작되는 클래스명이다. (ex. ReviewBoard -> reviewBoard)        

  ```java
  @Service
  public BoardServiceImpl {
    @Autowired
    @Qualifier("reviewBoard")
    private BoardDAO boardDAO;
  }
  ```


2. **@Primary**    
    중복되는 빈 중에 하나를 골라서 `@Primary` 어노테이션을 선언하여 우선순위를 둘 수 있다. (<u>가장 추천하는 방법이다.</u>)     

  ```java
  @Repository
  @Primary
  public BoardDAO {
    ...
  }
  ```


3. **모든 빈 주입 받기**    
    List로 받으면 컨테이너가 발견한 모든 빈이 List에 들어가게 된다.        
    
    ```java
    @Service
      public BoardServiceImpl {
        @Autowired
        private List<BoardDAO> boardDAO;
    
        public void printBoardDao() {
          this.boardDao.forEach(BoardDAO -> System.out.println(BoardDAO));
        }
      }
    ```
    
    
    
4. **이름으로 주입 받기**
     특정 빈을 이름으로 주입 하려면 객체 이름을 해당 클래스명으로 변경하면 된다. (스몰케이스로 시작한다는 것을 명심하자.)      

     ```java
     @Service
     public BoardServiceImpl {
       @Autowired
       private BoardDAO reviewBoard;
     }
     ```

     



## Resource

`@Resource`는 **자바에서 지원** 하는 어노테이션으로 **객체의 이름>타입** 순으로확인해서 어떤 클래스를 의존 주입할 것인지 결정한다. 필드, 생성자, 입력 파라미터가 한 개인 메소드에 적용 가능하다.    

```java
public BoardServiceImpl {
  @Resource(name="boardDAO")
  private BoardDAO boardDAO;
}
```



## Autowired의 동작원리

- BeanPostProcessor  
  - AutowiredAnnotationBeanPostProcessor (이것도 빈으로 등록되어 있음)
    - @Autowired : <u>Bean Initialize 전에 의존성 주입</u>해준다.

