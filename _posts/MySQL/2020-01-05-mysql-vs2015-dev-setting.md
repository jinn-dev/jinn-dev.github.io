---
title: "MySQL VS2015 개발 환경 설정 정리"
categories:
  - MySQL
last_modified_at: 2020-01-05T22s:00:00+09:00
toc: true
comments: false
---

### Intro
- MySQL 설치경로: `C:\Program Files\MySQL\MySQL Server 5.7`
- Visual Studio 2015 설치 버전은 Professional 영문판 기준

### 프로젝트 속성 설정
1. Property Pages(속성 페이지) ▶ VC++ Directories(VC++ 디렉터리) ▶ Include Directories(포함 디렉터리) 항목
```smalltalk
C:\Program Files\MySQL\MySQL Server 5.7\include 추가    
```

2. Property Pages(속성 페이지) ▶ VC++ Directories(VC++ 디렉터리) ▶ Library Directories(라이브러리 디렉터리) 항목
```smalltalk
C:\Program Files\MySQL\MySQL Server 5.7\lib 추가    
```

3. Property Pages(속성 페이지) ▶ Linker(링커) ▶ Input(입력) ▶ Additional Dependencies(추가종속성) 항목    
```smalltalk
libmysql.lib 추가
```
