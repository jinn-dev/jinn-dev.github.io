---
title: "이클립스 Spring Maven Dependency Add 검색 안될 때"
categories:
  - Spring
last_modified_at: 2020-02-15T22s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---
Maven에서 Dependency를 처음 추가하려고 하는데 검색이 안되는 경우는 다음과 같은 설정을 해줘야 한다.     
`Windows > Preferences > Maven`에서 3가지 옵션 체크 후 이클립스 재시작한다.         
![주석 2020-02-26 215407](/assets/images/주석%202020-02-26%20215407_jklr8b755.png)





프로젝트 오른쪽 마우스 클릭하여 `Maven > Add Dependency` 검색이 잘됨!    
![주석 2020-02-26 215408](/assets/images/주석%202020-02-26%20215408_xezv5cygt.png)


(+) 추가 후 오른쪽 마우스 클릭 `Maven > Update Project` 해주기
