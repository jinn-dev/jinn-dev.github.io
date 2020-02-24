---
title: "웹 프로젝트 폴더 구조"
categories:
  - Web
last_modified_at: 2020-02-12T22s:00:00+09:00
toc: true
comments: false
---
### 웹 프로젝트 폴더 구조
- WebContent
.html, .css, .js, JSP, image 등 웹 콘텐츠 파일을 두는 폴더로 웹 애플리케이션을 서버에 배치할 때 이 폴더의 내용물이 그대로 복사된다.

- WebContent/WEB-INF
웹 애플리케이션의 설정과 관련된 파일을 두는 폴더다. 이 폴더에 있는 파일은 클라이언트에서 요청할 수 없다.

- WebContent/WEB-INF/web.xml
웹 애플리케이션 배치 설명서 파일(Deployment Descriptor)이다. 줄여서 DD파일이라고도 한다. 서블릿, 필터, 리스너, 매개변수, 기본 웹 페이지 등 웹 애플리케이션 컴포넌트들의 배치 정보를 이 파일에 작성한다. 서블릿 컨테이너는 클라이언트의 요청을 처리할 때 이 파일의 정보를 참고하여 서블릿 클래서를 찾거나 필터를 실행하는 등의 작업을 수행한다.

- WebContent/WEB-INF/lib
자바 아카이브 파일(.jar)을 두는 폴더이다. 
