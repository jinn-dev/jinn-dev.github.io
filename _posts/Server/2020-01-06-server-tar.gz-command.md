---
title: OS별 tar.gz 압축 및 압축 해제 명령어 (+zip)
categories:
  - Unix
last_modified_at: 2020-01-06T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
tar.gz 배포할때 많이 쓰는데 OS마다 차이가 있다보니 자꾸 까먹어서 메모..    
tar로 여러 파일을 한번에 묶은 다음 , gzip 명령어로 압축

### tar.gz 압축
1. AIX
```smalltalk
$ tar cvf - 압축할폴더명 | gzip -c > 파일명.tar.gz
```

2. HP-UX
```smalltalk
$ tar cvf - 압축할폴더명 | gzip -c > 파일명.tar.gz
```
​
3. LINUX
```smalltalk
$ tar -zcvf 파일명.tar.gz 압축할폴더명
```
​
### tar.gz 압축 해제
1. AIX
```smalltalk
$ gzip -dc 파일명.tar.gz | tar xvf -
```
​
2. HP-UX
```smalltalk
$ gzip -dc 파일명.tar.gz | tar xvf -
```
​
3. LINUX
```smalltalk
$ tar -zxvf 파일명.tar.gz
```

### 압축 파일을 암호화 하려면 zip 사용
1. zip 으로 암호화해서 압축하기
```smalltalk
$ zip -P password -r 파일명.zip  압축할파일명 또는 경로
ex) zip -P jeun123 -r test.zip /home/jeun/test/*
```

2. unzip으로 압축 풀기
```smalltalk
$) unzip 파일명.zip -> 패스워드 입력하라고 나옴
$) unzip -P password 파일명.zip
```
