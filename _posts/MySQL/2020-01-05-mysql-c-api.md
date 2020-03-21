---
title: "MySQL for C API 정리"
categories:
  - MySQL
last_modified_at: 2020-01-05T22s:00:00+09:00
classes: wide
toc: true
comments: true
---

### 객체선언
1. MYSQL &#42;connection;

2. MYSQL_STMT &#42;sqlstmt;    
* `prepared statement handler`로 `mysql_stmt_init()`을 호출하여 생성된다.

3. MYSQL_BIND &#42;bind;       
* 입력의 경우: `MYSQL_BIND`는 `mysql_stmt_execute()`가 사용할 수 있도록 파라미터 값을 버퍼에 넣기 위해 `mysql_stmt_bind_param()`을 함께 사용한다.
* 출력의 경우: `MYSQL_BIND`는 `mysql_stmt_fetch()`에서 열을 fetch할 때 사용하기 위한 결과 셋 버퍼를 넣기 위해 `mysql_stmt_bind_restult()`와 함께 사용한다.
* `MYSQL_BIND` 구조를 사용하기 위해서는 0으로 초기화 시킨 후에 적당한 멤버로 설정해야 한다.

### 헤더파일

```cpp
#include <my_global.h>
#include <WinSock2.h>
#include <mysql.h>
```
### DB connection
1. mysql_init()
```cpp
MYSQL *mysql_init(MYSQL *mysql)
```
- MySQL이 필요로하는 일부 전역 변수를 초기화한다.
- 쓰레드에서 `my_thread_init()`을 호출한다.
- `mysql_library_init()`, `mysql_server_init()`, `mysql_connect()`를 호출한다.
- 해당 프로토타입에 액세스 하려면 `my_init()` 프로그램에 위 헤더 파일을 포함해야 한다.

2. mysql_real_connect()      
```cpp
MYSQL *mysql_real_connect(MYSQL *mysql, const char *host, const char *user, const char *passwd, const char *db, unsigned int port, const char *unix_socket, unsigned long client_flag)
```
- 실행중인 MySQL 데이터베이스 엔진에 대한 연결을 설정한다.
- `port`는 기본값 0이고, 사용하려면 TCP/IP Connection을 위한 포트번호를 지정할 수 있다.
- `unix_socket` 기본값 `NULL`이며, 특정 파이프 이름 또는 소켓 이름을 지정할 수 있다.
- `client_flag`은 보통 0 이지만 특정 기능을 사용하려면 다음 플래그를 조합하여 설정할 수 있다.


### 쿼리실행
```cpp
int mysql_real_query(MYSQL *mysql, const char *stmt_str, unsigned long length)
```

- `mysql_query()`는 바이너리 데이터를 사용할 수 없으므로 대신, `mysql_real_query()`를 사용해야 한다.


### reference
[https://dev.mysql.com/doc/refman/5.7/en/](https://dev.mysql.com/doc/refman/5.7/en/)
