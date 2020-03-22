---
title: "Github 블로그 404 에러"
categories:
  - Github
last_modified_at: 2020-03-16T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
github blog 저장소를 private으로 돌렸는데 404 에러가 발생했다. 다시 public으로 변경해도 접속이 안됨ㅠㅠ

알아보니 빈 커밋을 해주면 해결된다고 한다. 통상적으로 빈 커밋은  허용이 안되는 거지만.. 트리거 시스템 테스트 시 더미 파일을 추가하지 않고 커밋할 때 사용한다고 한다.

```
$ git commit -m "This is a blank commit" --allow-empty
$ git push
```


### reference
[https://riptutorial.com/git/example/4170/creating-an-empty-commit](https://riptutorial.com/git/example/4170/creating-an-empty-commit)
