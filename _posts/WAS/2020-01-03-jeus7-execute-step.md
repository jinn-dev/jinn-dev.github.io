---
title: "JESU7 서버 실행 방법"
categories:
  - WAS
last_modified_at: 2020-01-03T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
### Intro
해당 내용은 아래 환경을 기준으로 작성하였음.    
- 경로 : cd /jeus7/bin/
- 기동 순서 : webtob → domainadmin → nodemanager
- 다운 순서 : nodemanager → domainadmin → nodemanager
- jeusadmin 주소: http://127.0.0.1:9736

### DAS 기동    
```smalltalk
startDomainAdminServer -u administrator -p jeusadmin
```
위 명령어로 `dsboot`이름의 실행스크립트를 만든다.

### DAS 다운
```smalltalk
stopServer -host localhost:9736 -u administrator -p jeusadmin
```
위 명령어로 `dsdown`이름의 실행스크립트를 만든다.

### jeusadmin 접속
```smalltalk
jeusadmin -u administrator -p jeusadmin
```
위 명령어로 `jaboot`이름의 실행스크립트를 만든다.

### 서버 기동
```smalltalk
startManagedServer -server rengine_1 -u administrator -p jeusadmin
```
위 명령어로 `msboot_rengine1`이름의 실행스크립트를 만든다.

### 노드매니저 기동
```smalltalk
startNodeManager
```
위 명령어로 `nmboot`이름의 실행스크립트를 만든다.    

노드매니저 기동을 안하면 서버 구동시 Connection refused 에러 발생
{: .notice--danger}

### webadmin 접속
default server port: 9796     


(1) DataSource 추가    
* 콘솔에서 DataSource 연결 확인
```smalltalk
jeusadmin> testdsconfig renginepool
Configuration is valid. You can use it.
```


(2) Server 추가 후 Start    
* 서버 기동 시 Connection refused error
제우스 설치 후 webadmin 페이지에서 디폴트로 있는 server1을 기동하려 했는데 Connection refused가 나면서 기동이 안됐다. 터미널에서 직접 startManagedServer 해줬더니 기동 잘만됨.. 이유가 뭐지?
<span style="color:red">->노드매니저를 기동해야 함</span>

### Application 추가
(1) webadmin 접속

(2) add-application-repository /home/app/webapps (application 경로)

(3) list-application-repositories

(4) application-info

(5) deploy rengine -servers rengine_1
    deploy rengine -servers rengine_2
    deploy rengine -clusters rengine_cluster

### application 내 설정 파일 수정
* jeus-web-dd.xml 설정 예시​    

```html
<jeus-web-dd xmlns="http://www.tmaxsoft.com/xml/ns/jeus" version="7.0">
<context-path>/examples</context-path>
--> <context-path>/</context-path> 이걸로 변경

<enable-jsp>true</enable-jsp>
<auto-reload>
<enable-reload>true</enable-reload>
<check-on-demand>true</check-on-demand>
</auto-reload>
<added-classpath>
<class-path>/home/user1/libs/lib.jar</class-path>
</added-classpath>
<session-config>
생략
</session-config>

<async-config>
<async-timeout-millis>600000</async-timeout-millis>
<background-thread-pool>
<min>0</min>
<max>10</max>
</background-thread-pool>
<dispatch-thread-pool>
<min>0</min>
<max>10</max>
</dispatch-thread-pool>
</async-config>

<properties>
<property>
<key>jeus.servlet.jsp.modern</key>
<value>true</value>
</property>
</properties>
</jeus-web-dd>
```

### webtob 설정
`DOCROOT = literal WebtoB` 가 Web을 통해 서비스 하는 모든 문서를 포함하는 루트 디렉토리의 경로이다. 즉, WebtoB 는 `DOCROOT`가 지정한 디렉토리를 최상위로 하여 문서를 서비스하게 된다. Client가 요구한 URL은 `DOCROOT`의 경로 뒤에 추가되어 실제 경로명을 이루게 된다. WebtoB는 이 경로를 가지고 파일에 접근하게 된다.

#### webtob 기동 확인 방법
```smalltalk
ps -ef | grep htl
```

#### webtob 기타 참고 링크
* [webtob 연동 매뉴얼](https://technet.tmaxsoft.com/upload/download/online/jeus/pver-20140827-000001/getting-started/chapter_jeus_system_configuration.html)

* [webtob 보안(cf. SSL)](https://technet.tmaxsoft.com/upload/download/online/webtob/pver-20150203-000001/administrator/ch08.html#d4e12450)
