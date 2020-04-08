---
title: "알고리즘 공부 - 완주하지 못한 선수"
categories:
  - Algorithm
last_modified_at: 2020-04-07T22s:00:00+09:00
toc: true
toc_sticky: true
comments: true
---
### Overview
문제 링크: [https://programmers.co.kr/learn/courses/30/lessons/42576](https://programmers.co.kr/learn/courses/30/lessons/42576)

## 나의 풀이

```java
import java.util.HashMap;
import java.util.Map;

public class Main {
	public static void main(String[] args) throws Exception {

		String[] participant = {"leo", "kiki", "eden"};
		String[] completion = {"eden", "kiki"};
		String answer = "";
		HashMap<String, Integer> map = new HashMap<String, Integer>();

		for(int i=0; i<participant.length; i++) {
			if(map.containsKey(participant[i])) {
				int val = map.get(participant[i])+1;
				map.put(participant[i], val);
			}
			else {
				map.put(participant[i], 1);
			}
		}

		for(int i=0; i<completion.length; i++) {
			if(map.containsKey(completion[i])) {
				int val = map.get(completion[i])-1;
				map.put(completion[i], val);
			}
		}

		for (Map.Entry<String, Integer> el : map.entrySet()) {
			if(el.getValue()!=0) {
				answer = el.getKey();
			}
		}
		System.out.println(answer);
	}
}
```

## 다른 사람 코드
participant를 HashMap에 담을 때 `getOrDefault`를 쓰면 `containsKey`를 안써도 된다.
`forEach`까지 쓰면 깔-끔😂    

**default V getOrDefault(Object key, V defaultValue)**      
찾는 키가 존재한다면 찾는 키의 값을 반환하고 없다면 기본 값을 반환한다.

```java
import java.util.HashMap;
import java.util.Map;

public class Main {
	public static void main(String[] args) throws Exception {

		String[] participant = {"leo", "kiki", "eden"};
		String[] completion = {"eden", "kiki"};
		String answer = "";
		HashMap<String, Integer> map = new HashMap<String, Integer>();

    for(String part : participant) {
        map.put(part, map.getOrDefault(part, 0)+1);
    }

    for(String comp : completion) {
      map.put(comp, map.get(comp)-1);
    }

		for (Map.Entry<String, Integer> el : map.entrySet()) {
			if(el.getValue()!=0) {
				answer = el.getKey();
			}
		}
		System.out.println(answer);
	}
}
```
