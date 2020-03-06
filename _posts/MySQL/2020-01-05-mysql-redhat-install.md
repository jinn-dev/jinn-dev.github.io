---
title: "RedHat에 MySQL 설치하는 방법"
categories:
  - MySQL
last_modified_at: 2020-01-05T22s:00:00+09:00
toc: true
comments: false
---
해당 내용은 MySQL 5.7, RedHat6.7 버전 기준으로 작성하였음.

### tar 풀기
```smalltalk
$ tar –xvf mysql-5.7.26-1.el6.x86_64.rpm-bundle.tar
mysql-community-server-5.7.26-1.el6.x86_64.rpm
mysql-community-libs-compat-5.7.26-1.el6.x86_64.rpm
mysql-community-client-5.7.26-1.el6.x86_64.rpm
mysql-community-devel-5.7.26-1.el6.x86_64.rpm
mysql-community-common-5.7.26-1.el6.x86_64.rpm
mysql-community-test-5.7.26-1.el6.x86_64.rpm
mysql-community-embedded-5.7.26-1.el6.x86_64.rpm
mysql-community-libs-5.7.26-1.el6.x86_64.rpm
mysql-community-embedded-devel-5.7.26-1.el6.x86_64.rpm
```

### rpm 설치
아래 순서로 설치한다.    
1. common
2. libs
3. libs-compat
4. embedded
5. embedded-devel
6. client
7. server

```smalltalk
$ rpm -Uvh mysql-community-common-5.7.13-1.el6.i686.rpm
```



설치 중에 라이브러리 충돌로 인한 설치 실패 관련 오류 메시지(<span style="color:red">conflicts with file from package mysql-libs-5.1.73-5.el6_6.x86_64</span>)가 발생하면 이미 설치되어 있는 MySQL 관련 라이브러리가 충돌 난 것이므로 해당 라이브러리를 먼저 삭제한다.

```smalltalk
$ yum remove mysql-libs-5.1.73-5.el6_6.x86_64
```


삭제가 완료되었다면 다시 mysql-community-common rpm을 다시 설치한다. 설치가 완료되면 아래의 순서대로 계속해서 rpm을 설치한다.

```smalltalk
$ rpm -Uvh mysql-community-common-5.7.13-1.el6.i686.rpm
$ rpm -Uvh mysql-community-libs-5.7.13-1.el6.i686.rpm
$ rpm -Uvh mysql-community-libs-compat-5.7.13-1.el6.i686.rpm
$ rpm -Uvh mysql-community-embedded-5.7.13-1.el6.i686.rpm
$ rpm -Uvh mysql-community-embedded-devel-5.7.13-1.el6.i686.rpm
$ rpm -Uvh mysql-community-client-5.7.13-1.el6.i686.rpm
$ rpm -Uvh mysql-community-server-5.7.13-1.el6.i686.rpm
```


MySQL용 C 프로그램 개발 환경(컴파일 및 빌드)를 위해서는 추가로 mysql-community-devel rpm을 추가로 설치해야 한다.

```smalltalk
mysql-community-devel-5.7.26-1.el6.x86_64.rpm
```
