---
title: OS별 버전 정보 확인 명령어
categories:
  - Unix
last_modified_at: 2020-01-06T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
### AIX
```smalltalk
$ oslevel -s / uname -a
```

### HP-UX
```smalltalk
$ uname -a
```
​
### LINUX (openSUSE)
```smalltalk
$ uname -a / Lib 정보 (getconf -a | grep lib, getconf -a | grep LIB)
$ Linux P500S 3.16.6-2-desktop

$ cat /etc/issue*
$ /etc/openSUSE 13.2

$ getconf -a | grep lib, getconf -a | grep LIB
$ 127.0.0.1:lib 2.19
```

### LINUX (Virtual Box RedHat 6.7)
```smalltalk
$ uname-a / Lib정보 (getconf -a | grep lib, getconf - a | grep LIB)
```

​
