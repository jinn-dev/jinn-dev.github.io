---
title: "MySQL의 Transaction Isolation Level"
categories:
  - MySQL
last_modified_at: 2020-02-12T22s:00:00+09:00
toc: true
comments: false
---
여러 명이서 Oracle -> MySQL DB 컨버전 작업을 진행하면서 겪었던 일이었다. 단건 `update`를 실행하려 해도 계속 lock이 걸리거나 무한 대기중인 상황이 수시로 발생했다. 나는 솔루션 모듈 변환 작업 진행중이라 처음엔 상관 없었지만 실제로 디비에 붙어서 테스트 했을때 해당 문제 때문에 어려움을 겪었다. 작업자 모두가 여태까지 Oracle DB를 사용했기 때문에 이런 문제가 왜 생기는지조차 원인을 찾는 것도 힘들었다.

늦었지만 MySQL 작업 내용을 되돌아보면서 알게 된게 MySQL의 Transaction Isolation Level이다.

기본적으로 <b>Transaction Level</b>은 아래 4가지로 나눈다.
1. `READ UNCOMMITTED`
2. `READ COMMITTED`
3. `REPEATABLE READ`
4. `SERIALIZABLE`

그리고 <b>트랜잭션 비일관성 특징 3가지</b>를 알아야 한다.    
1. `dirty read` : 신뢰할 수 없는 데이터가 조회됨
2. `non-repeatable read` : 동일한 `select` 쿼리의 결과가 다름
3. `phantom read` : 기존에 없던 데이터 `row`가 조회되거나 새로운 `row`가 조회됨

### READ UNCOMMITTED
`commit`이 안된 데이터를 읽어올 수 있을까? `READ UNCOMMITTED` level에서는 가능하다.
1. B가 user 테이블을 조회
2. A가 user 테이블에 `insert`, `update`를 하고 `commit`을 하지 않음
3. B가 user 테이블을 읽을 때 A가 수정한 데이터가 조회됨
4. A가 수정한 데이터를 `rollback`
5. B가 user 테이블을 다시 조회하면 3번에서 조회된 결과와 다르게 나옴

 `READ UNCOMMITTED`은 <b>트랜잭션 비일관성 특징 3가지</b>가 모두 해당이 된다.
 - `dirty read`    
 ex) 3번에서 `commit`되지 않은 데이터가 조회됨
 - `non-repeatable read`  
 ex) 1번, 3번, 5번의 동일 쿼리 조회 결과가 다름
 - `phantom read`    
 ex) 1번에 없던 `row`가 3번에서 조회됨

이 level은 데이터 정합성 문제가 너무 심하므로 쓰는 경우를 본적이 없다. ~~(근데 왜 있는거지?)~~

### READ COMMITTED
Oracle 기준으로 작업했던 나같은 초보 개발자는 당연히 "Commit된 데이터만 조회할 수 있는거 아닌가?""라는 생각을 할 수도 있다. 이런 의미를 가진 `READ COMMITED`는 Oracle DBMS에서 기본적으로 채택된 격리 수준이며, 대부분 사이트에서 가장 많이 쓰이는 level이다. `select`쿼리 실행 시 Shared lock이 걸리고 조회가 끝나는 동시에 Shared lock을 해제한다.

1. B가 user 테이블을 조회
2. A가 user 테이블에 `insert`, `update`를 하고 `commit`을 하지 않음
3. B가 user 테이블을 조회하면 1번 조회 결과와 동일함
4. A가 수정한 데이터를 `commit`
5. B가 user 테이블을 다시 조회하면 A가 수정한 데이터로 조회됨

하지만 해당 level도 <b>트랜잭션 비일관성 특징 3가지 중 2가지</b>에 해당이 된다.
- `non-repeatable read`    
ex) 3번과 5번에서 조회 결과가 다름
- `phantom read`   
ex) 1번, 3번에 없던 `row`가 5번에서 조회됨

따라서 해당 level도 실시간 데이터 처리에 민감한 트랜잭션의 경우 구성에

### REPEATABLE READ
MySQL은 `REPEATABLE READ`를 default level로 채택하고 있다.

1. B가 user 테이블을 조회
2. A가 user 테이블에 `insert`, `update`를 하고 `commit`을 하지 않음
3. B가 user 테이블을 조회하면 1번 조회 결과와 동일함
4. A가 수정한 데이터를 `commit`
5. B가 user 테이블을 다시 조회하면 A가 수정한 데이터로 조회되지 않고 1번 조회 결과와 동일함

나처럼 `READ COMMITED`에 익숙한 개발자는 1번, 3번, 5번의 결과가 왜 동일한가에 의문을 품을 것이다.

`REPEATABLE READ`은 한 트랜잭션에서 "최초" 쿼리 조회 시 하나의 snapshot을 구축하고 거기 데이터를 읽어온다. 이후 같은 쿼리를 조회할 때 "최초" 쿼리 조회 시 구축된 snapshot에서 데이터를 읽어오는 것이다. "최초" 쿼리 조회를 했던 1번에서 이미 구축된 snapshot에서 항상 읽어오기 때문에 조회 결과는 동일했고 <b>따라서 트랜잭션 비일관성 특징 3가지 모두 해당이 안된다.</b>

또한, 글 서두에 말한 lock이 걸리는 현상은 해당 level때문에 발생했던 것이었다.        
Oracle에서 MySQL DB로 데이터를 옮기면서 `insert into select` 또는 `create table as select`로 전체 테이블 참조 쿼리 실행을 할 때가 많았다. 이 때 다른 트랜잭션에서 해당 쿼리가 실행되고 있는 테이블 데이터 변경 작업을 하려고 하면 대기 상태 또는 lock이 걸리는 것이다.
<span style="color:red">
snapshot 구축 시점으로 인해 해당 데이터에 관해서 암묵적으로 lock과 비슷한 효과가 나타나며, `select` 작업이 종료될 때 까지 해당 데이터 변경 작업이 불가능하게 되는 것이다.
</span>

### SERIALIZABLE
가장 고립된 격리 수준으로 `select` 쿼리가 사용하는 모든 데이터에 Shared lock이 걸리고 다른 트랜잭션에서는 해당 영역에 관한 데이터 변경과 입력도 불가능하다.
```sql
1. [A-1] select email from user where user_id='jinny';
2. [B-1] select email from user where user_id='jinny';
3. [A-2] update user set email='jinny123@naver.com' where user_id='jinny';
4. [A-3] COMMIT;
5. [B-2] update user set email='jinny456@naver.com' where user_id='jinny';
6. [B-3] COMMIT;
```

트랜잭션 A와 B가 `select`를 할 때 해당 `row`는 각각 Shared lock이 걸리게 된다.    
연이어 각 트랜잭션에서 `update`를 실행을 하면 해당 `row`에 Exclusive lcok이 걸려고 시도할 것이다. 하지만 이미 해당 `row`는 Shared lock이 걸려있으므로 두 트랜잭션 모두 deadlock 오류가 발생하거나 timeout으로 `update`쿼리가 실행이 안될 것이다.

`SERIALIZABLE`은 한 트랜잭션에서 `select`쿼리를 실행하면 그 트랜잭션이 `commit`되기 전까지는 다른 트랜잭션에서는 DML 작업(`insert`, `update`, `delete`)이 불가능하므로 <b>트랜잭션 비일관성 특징 3가지 모두 해당이 안된다.</b>
### reference
[https://jupiny.com/2018/11/30/mysql-transaction-isolation-levels/](https://jupiny.com/2018/11/30/mysql-transaction-isolation-levels/)
[https://gywn.net/2012/05/mysql-transaction-isolation-level/](https://gywn.net/2012/05/mysql-transaction-isolation-level/)
