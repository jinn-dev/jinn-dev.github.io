---
title: "minimal mistakes 테마로 github.io 블로그 생성하기"
categories:
  - Github
last_modified_at: 2020-02-11T22s:00:00+09:00
toc: true
comments: false
---

에버노트, 네이버 블로그만 쓰다가 github.io를 알게 되었고 재밌을거 같아
시작해봤는데.. 생각보다 엄청 헤맴ㅎㅎ..
업무에선 cvs에서 svn만 써봤지, github는 개인적으로만 접해와서 알아야 할게 생각보다 많았다. 나중에 또 까먹을 경우를 대비해서 기록해야지.

### 루비 설치하기
난 가장 최신버전인 2.7.0 버전을 설치했다. [link](https://rubyinstaller.org/downloads/)

### 블로그 테마 적용하기
가장 유명한 것 같은 "Minimal Mistakes" 테마로 선택했다.    

* Minimal Mistakes Github [link](https://github.com/mmistakes/minimal-mistakes)
* Minimal Mistakes Demo Page [link](https://mmistakes.github.io/minimal-mistakes/)

적용하는 방법을 찾아보니 해당 테마 Repository를 fork, clone, zip 하는 방법이 있는데
다 시도해본 결과 zip으로 설치하는게 가장 쉬웠다. (나한테는ㅎㅎ) fork 하는 방법이 쉽다는데 git 넘 어려웡.. 아래 사이트에서 release 된 버전을 로컬에 다운로드 받는다. 폴더명을 username.github.io로 변경하고, 내 레파지토리로 올리기 전에 불필요한 파일은 삭제하자. Atom 기준으로 Git Toggle에서 해당 폴더를 Initialize and publish 해주자.     
[https://github.com/mmistakes/minimal-mistakes/releases](https://github.com/mmistakes/minimal-mistakes/releases)

### Gemfile 수정
빌드 전 라이브러리를 읽는 것처럼 `Gemfile`을 읽어 루비 라이브러리인 `gem`을 등록하는 것 같다. 아래 내용으로 수정했다.
```javascript
source "https://rubygems.org"

gem "jekyll", "~> 3.6"
gem 'jekyll-include-cache'
gem "minimal-mistakes-jekyll"
```

그리고 터미널에서 `Gemfile`이 있는 경로 이동 후 아래 명령어를 실행하자.    
```javascript
$ bundle
```

### _config.yml 수정
`_config.yml`은 테마 기본 설정 파일인데 주로 Site Settings, minimal_mistakes_skin, Site Author, Search 및 Comments 기능 활성화 등을 수정한 것 같다.

### _posts, _pages 폴더 생성
`_posts`, `_pages` 폴더가 없다면 생성하자. `_posts`는 포스트 파일들이 담길 곳이고 `_pages`는 단순 페이지 파일이 담길 곳이다.

### Navigation 설정
헤더 부분에 표시될 메뉴를 설정하는 파일은 `_data/navigation.yml`이다. 난 아래 내용으로 수정했다.
```javascript
# main links
main:
  - title: "About"
    url: /about/
  - title: "Post"
    url: /year-archive/
  - title: "Category"
    url: /categories/
```

그리고 `_pages` 폴더에 아래 파일을 추가한다.

`category-archive.md`
```javascript
---
title: "Posts by Category"
layout: categories
permalink: /categories/
author_profile: true
---
```

`year-archive.md`
```javascript
---
title: "Posts by Year"
permalink: /year-archive/
layout: posts
author_profile: true
---
```



### 포스팅 하는 방법
`_posts` 폴더에 `YYYY-MM-DD-제목(영어로).md` 형식으로 파일 생성 후 파일 헤더에 아래 내용을 입력하고 마크다운 형식으로 글을 작성한다. 마크다운 문법도 처음 써보는데 완전 어색하다..    

```javascript
---
title: "minimal mistakes 테마로 github.io 블로그 생성하기"
categories:
  - Github
last_modified_at: 2020-02-12T22s:00:00+09:00
toc: true
---
내용입력하긔
```

### 로컬에서 테스트
터미널에서 테마 소스 최상위 경로로 이동 후 아래 명령어 실행하면 로컬에서 띄어볼 수 있다.    
```javascript
$ bundle exec jekyll serve
```

`http://localhost:4000`으로 접속할 수 있다. 포스트 수정은 실시간으로 볼 수 있지만 _config.yml 파일 수정 시 재기동 해야한다.    

### 원격 레파지토리에 적용하기
commit까지 했다면, 정상적으로 push 후 fetch까지 하면 원격 레파지토리에 적용 완료:)
