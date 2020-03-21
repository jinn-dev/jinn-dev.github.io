---
title: "core 파일 분석 방법 - gdb, dbx"
categories:
  - Unix
last_modified_at: 2020-01-06T22s:00:00+09:00
classes: wide
toc: true
comments: false
---

### Linux - gdb
1. core 파일을 남기도록 하자     
```smalltalk
$ unlimit -a
```

2. core file size가 0이면 파일이 안생기므로 따로 설정해야 함
```smalltalk
$ unlimit -a unlimited
```
해당 명령어는 root만 가능하고 현재 터미널만 적용되므로 영구적으로 적용시키려면 따로 설정을 해줘야 함
​
3. 컴파일 할때 디버깅 옵션 추가
`-g`와 `-ggdb` 옵션 추가함

4. core 파일 분석
```smalltalk
$ gdb ./프로세스명 ./core파일명
$ backtrace  : 죽기 직전까지 호출되었던 스택 정보 확인
$ where : 문제 시점에 해당 프로세스가 한 작업 확인
```

### AIX - dbx
dbx로 Running Process 디버깅하기
[https://www.ibm.com/support/knowledgecenter/ko/ssw_aix_71/com.ibm.aix.cmds2/dbx.htm](https://www.ibm.com/support/knowledgecenter/ko/ssw_aix_71/com.ibm.aix.cmds2/dbx.htm)


1. 필요 시 파일 바이너리 확인    
```smalltalk
$ file core이름
​```

2. 프로세스 실행 후 프로세스 아이디 체크하기    

3. dbx로 디버깅 시작  
```smalltalk
$ dbx -d<depth> -a <pid> 또는 $ dbx -a <pid>
또는
$ dbx [바이너리경로] [코어파일경로]
```
​
4. dbx로 thread 확인
```smalltalk
$ thread
```
​
5. 현재 소스 경로 확인
```smalltalk
$ file
```
​
6. 특정 소스로 이동
```smalltalk
$ file [디렉토리 경로]
```
​
8. 소스 확인
```smalltalk
$ list [라인번호]
```
​
9. 중단점 찍기
```smalltalk
$ stopi [라인번호]
```
​
8. 트레이스 로그 옵션 활성화
```smalltalk
$ trace
```
