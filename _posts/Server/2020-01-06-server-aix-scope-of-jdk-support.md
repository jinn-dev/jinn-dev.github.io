---
title: AIX 버전별 JDK 지원 범위 확인 및 설치 방법
categories:
  - Unix
last_modified_at: 2020-01-06T22s:00:00+09:00
toc: true
comments: false
---
AIX JAVA JDK는 RISC 형태라 JAVA 공식홈에서 설치본 제공 안하므로 IBM에서 지원하는 SDK 설치해야 함

### JDK 지원 범위    
AIX 5.3에서 JAVA 1.7 지원안하는데 설치하려고 뻘짓함^^ 버전 지원 유무 먼저 확인
![image](/assets/images/image.png)
### 설치 방법

1. 아래 링크에서 IBM 로그인

2. JDK 설치 파일 선택 후 다운로드 디렉터 또는 http 다운로드 클릭 하여 설치본 다운로드(난 http가 편해서 그걸로 함)

3. `root`로 터미널 접속

4. `/usr`에 설치본 sftp로 옮김 (smitty 이용할 경우 tmp 폴더 따로 만들어서 설치하는게 편함)

5. 확장자가 `tar.gz` 일 경우 해당 명령어로 압축 해제
```smalltalk
$ gzip -dc Java7r1_64.sdk.7.1.0.445.tar.gz | tar xvf -  
```

6. `smitty` 명령어 사용
```smalltalk
$ smitty install_latest
```
    1. 설치 경로 입력 후 엔터
    2. 설치할 소프트웨어 `all_latest`
    3. F4로 설치할 리스트 확인 후 진행하면 설치됨

7. `installp` 명령어 사용
```smalltalk
$ installp -a -Y -d '.' Java71_64.sdk
```
    1. 삭제할 땐 `-a`를 `-u`로 변경 후 사용
