---
title: "Mybatis와 Spring Transactional"
categories:
  - Spring
last_modified_at: 2020-03-27T22s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---
## Overview
업무 중 CRUD의 비즈니스 로직 처리를 할 때 Spring 기반이지만 자사 커스텀 프레임워크를 같이 사용하면서 MyBatis와 Spring의 조합을 제대로 사용하지 않았다. 지금 생각해보면 상당히 귀찮았던 방식이었던 것 같다.(바꾼다고 했어도 바쁜 일정과 실시간으로 문의 전화 오는걸 생각하면 일을 벌리는 거라고 컨펌도 안해주셨을 거 같지만^^) 최근에 본 면접 중 Spring의 Transactional 질문을 받게 되면서 사실대로 안써봤다고 말한게 찝찝했다. 이 기회에 정리를 하고 넘어가기로 하자:)

## MyBatis의 정의 및 특징
MyBatis는 반복적이고 지루한 JDBC 프로그래밍을 단순화하기 위해 클린턴 비긴이 만든 작은 라이브러리에서 시작되었다.

- MyBatis의 핵심
  - 개발과 유지보수가 쉽도록 소스 코드에 박혀있는 **SQL을 별도의 파일로 분리** 하는 것
  - 단순하고 반복적인 **JDBC 코드를 캡슐화** 하여 데이터베이스 프로그래밍을 간결하게 만드는 것    

## MyBatis 프레임워크의 핵심 컴포넌트
### SqlSessionFactoryBuilder  
MyBatis 설정 파일 내용을 토대로 SqlSessionFactory를 생성한다.

### SqlSessionFactory      
JDBC의 DataSource를 대신하여 SqlSession 객체를 생성한다.
SqlSessionFactory를 생성하려면 SqlSessionFactoryBuilder를 build() 할 때 MyBatis 설정 파일을 참고해서 만든다.

SqlSessionFactoryBuilder를 건설사에 비유하고 SqlSessionFactory를 공장에 비유하자면 건설사가 공장을 만들 때 설계도(MyBatis 설정 파일)가 필요하다.

이런 식의 객체 생성 패턴을 **빌더 패턴(Builder Pattern)** 이라고 한다.

```java
  String resource = "spms/dao/mybatis-config.xml";
  Resources.setCharset(Charset.forName("utf-8"));
  Reader reader = Resources.getResourceAsReader(resource);
  SqlSessionFactory sqlSessionFactory = (new SqlSessionFactoryBuilder()).build(reader);
```

### MyBatis 설정 파일   
데이터베이스 연결정보, 트랜잭션 정보, MyBatis 제어 정보 등 설정 내용을 포함한다. SqlSessionFactoryBuilder가 SqlSessionFactory 객체를 생성하기 위해 필요한 설계 도면이다.

**mybatis-config.xml**
```xml
<configuration>
  <!-- 프로퍼티 경로 -->
  <properties resource="spms/dao/db.properties"/>

  <!-- 자바 클래스 이름에 대한 별칭 설정 -->
  <typeAliases>
  <typeAlias type="spms.vo.Project" alias="project"/>
  </typeAliases>

  <!-- 프레임워크에서 사용할 데이터베이스 정보 -->
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <!--
      <dataSource type="POOLED">
        <property  name="driver" value="${driver}"/>
        <property  name="url" value="${url}"/>
        <property  name="username" value="${username}"/>
        <property  name="password" value="${password}"/>
      </dataSource>
      -->
      <dataSource type="JNDI">
        <property name="data_source" value="java:comp/env/jdbc/studydb"/>
      </dataSource>
    </environment>
  </environments>

  <!-- SQL 맵퍼 파일들이 있는 경로 설정 -->
  <mappers>
    <mapper resource="spms/dao/MySqlProjectDao.xml"/>
  </mappers>
</configuration>
```
여기서 **transactionManager** 로 트랜잭션 관리 유형을 설정할 수 있다.
- `JDBC` 직접 JDBC의 커밋, 롤백 기능을 이용하여 MyBatis 자체에서 트랜잭션을 관리
- `MANAGED` 서버의 트랜잭션 관리 기능을 이용. Java EE 애플리케이션 서버나 서블릿 컨테이너에서 트랜잭션을 관리

MyBatis에서 사용 가능한 **dataSource** 의 type은 3가지가 있다.
- `UNPOOLED` DB 커넥션 요청할 때마다 매번 커넥션 객체를 생성한다.
- `POOLED` 미리 DB 커넥션 객체 생성해두고 요청하면 즉시 반환한다. 연결을 초기화하고 사용자 인증하는 과정이 없어 속도가 빠르다.
- `JNDI` Java EE 애플리케이션 서버나 서블릿 컨테이너에서 제공하는 데이터 소스 사용 **-> 톰캣 서버 정보를 참고하므로 db.properties 필요 없음**


**db.properties**
```smalltalk
driver=com.mysql.jdbc.driver
url=jdbc:mysql://localhost/studydb
username=study
password=study
```

### SQL 맵퍼 파일          
SQL 문을 담고 있는 파일로 SqlSession이 참조한다.
```xml
<mapper namespace="spms.dao.PrjectDao">
  <select id="selectList" resultType="project">
    select PNO, PNAME, STA_DATE, END_DATE, STA_DATE
    from PROJECTS
    order by PNO desc
  </select>
</mapper>
```

### SqlSession      
실제 SQL을 실행하는 객체
```java
SqlSession sqlSession = SqlSessionFactory.openSession();
```

```java
try {
  return sqlSession.selectList("spms.dao.PrjectDao.selectList");
} finally {
  sqlSession.close();
}
```
SqlSession 사용 후에는 반드시 `close()`를 해야 한다.
{: .notice--warning}


## Spring Transactional
지금까지는 **MyBatis로 프로그램에서 직접 트랜잭션 처리** 하는 과정이었다. Spring과 연동해서 트랜잭션 관리를 하면 더 쉽게 트랜잭션을 처리할 수 있다.

### AOP를 이용한 트랜잭션 설정 방법
AOP는 제 3자의 관점에서 핵심 기능을 봤을 때 공통되는 부분을 묶어서 부가기능으로 관리하자는 개념이다. 핵심 로직 구현할 때 트랜잭션 적용, 로그, 보안 검사 등의 공통 기능을 처리하는 로직을 핵심 로직 코드 내에 쓸 필요가 없어진다.    
![Cross cutting concerns](https://www.codejava.net/images/articles/frameworks/spring/Understanding%20Spring%20AOP/CrossCuttingConcersn.png)

**AOP 용어 정리**
- `Target` 부가기능을 부여할 대상 클래스        
- `Advice` 언제 공통 관심 기능을 핵심 로직(Target)에 적용할 지 정의하는 것이다. (ex. 핵심 로직 실행 전, 정상 종료 후, 비정상 종료 후 등)        
- `JoinPoint` 공통관심사항(Advice)이 적용 될 수 있는 시점 (ex. 메소드 호출 시, 객체 생성 시 등)     
- `PointCut` 여러 개의 JoinPoint를 하나로 묶은 것을 PointCut이라 한다.     
- `Aspect` 여러 객체에 공통으로 적용되는 공통 관심 사항. Advice + PointCut      
- `Proxy` 타켓을 감싸서 타켓의 요청을 대신 받아주는 Wrapping Object이다. 클라이언트가 타켓을 호출하면 프록시가 요청을 먼저 받아서 타겟 메소드 실행 전에 선처리, 실행 후에는 후처리를 하도록 구성된다.     

XML 설정 파일에 **aop, tx 네임스페이스를 추가** 하고 스프링에서 제공하는 **DataSourceTransactionManager** 클래스를 빈으로 등록한다.

**mybatis-config.xml**
```xml
<!-- Transaction 설정 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <property name="dataSource" ref="dataSource"/>
</bean>

<!-- Transaction을 위한 AOP 설정 -->
<aop:config proxy-target-class="true">
  <aop:pointcut id="servicePublicMethod" expression="execution(public * com.freehoon.web.board..*(int))" />
  <aop:advisor advice-ref="txAdvice" pointcut-ref="servicePublicMethod" />
</aop:config>

<!-- 선언적 Transaction 설정  -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
  <tx:attributes>
    <tx:method name="getBoardContent" rollback-for="Exception" />
  </tx:attributes>    
</tx:advice> 	
```
[AOP를 이용한 트랜잭션 참고](https://freehoon.tistory.com/110)

### @Transactional 어노테이션      
AOP를 위해 XML 설정을 많이 해줬던 것과 달리 선언적 트랜잭션은 설정 파일에 `DataSourceTransactionManager` 빈과 `tx:annotation-driven` 을 설정하고, 서비스 클래스에서 다수개의 SQL문장을 호출하는 메소드에 `@Transactional` 을 선언해주면 된다. `@Transactional` 으로 선언된 메소드 안에서 어떠한 오류가 발생할 경우에는 그 메소드 안에서 호출된 모든 SQL문장은 모두 취소되고 오류가 없다면 모두 실행된다.

**mybatis-config.xml**
```xml
<!-- Transaction 설정 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <property name="dataSource" ref="dataSource"/>
</bean>

<!-- Transaction을 위한 AOP 설정 -->
<!-- 주석처리
<aop:config proxy-target-class="true">
  <aop:pointcut id="servicePublicMethod" expression="execution(public * com.freehoon.web.board..*(int))" />
  <aop:advisor advice-ref="txAdvice" pointcut-ref="servicePublicMethod" />
</aop:config>
-->

<!-- 선언적 Transaction 설정  -->
<!-- 주석처리
<tx:advice id="txAdvice" transaction-manager="transactionManager">
  <tx:attributes>
    <tx:method name="getBoardContent" rollback-for="Exception" />
  </tx:attributes>    
</tx:advice> 	 -->
<tx:annotation-driven/>
```

```java
  @Transactional
  @Override
	public void updateBoard(Map<String, Object> map, HttpServletRequest request) throws Exception {
		sampleDAO.updateBoard(map);

		sampleDAO.deleteFileList(map);

		List<Map<String, Object>> list = fileUtils.parseInsertFileInfo(map, request);

		Map<String, Object> tempMap = null;
		int size = list.size();
		for(int i=0; i<size; i++) {
			tempMap = list.get(i);
			if(tempMap.get("IS_NEW").equals("Y")) {
				sampleDAO.insertFile(tempMap);
			}
			else {
				sampleDAO.updateFile(tempMap);
			}
		}
	}
```

아 맞다.. 그리고 면접 중에 업무에서 쓴 스프링 버전을 질문 받았는데 검색해보면서 왜 물어봤는지 알 것 같았다.
업무에선 주로 2.x, 그나마 최근에서야 3.x 대의 버전을 사용했었는데 High Level에선 Spring이 모든 클래스를 만들 때 규칙에 `@Transactional`이 추가되어 있다고 한다.    

그래서 Unchecked Exception(Runtime Exception) 발생 시 Transaction은 Rollback을 발생시킨다.
만약 사용자 예외 처리 전에 Runtime Exception이 발생한다면 롤백이 되버려서 겉보기엔 왜 사용자 예외처리를 안타고 롤백이 되버리지? 라는 의문이 생길 수도 있다. 이럴 땐 `rollbackOn, dontRollbackOn` 속성으로 특정 Exception 발생 시 rollback 유무를 제어할 수 있다.
관련 정보는 [Spring Boot의 Transactional](https://dev.overnodes.com/entry/Spring-Boot-Transactional-%EC%9D%B4%EB%9E%80-Transactional%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC)에 더 자세히 나와있다.
스프링은 역시 쉬운 프레임워크가 아니다 😂
