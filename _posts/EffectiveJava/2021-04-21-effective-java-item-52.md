---
title: "아이템52. 다중정의는 신중히 사용하라"
categories:
  - EffectiveJava
last_modified_at: 2021-04-21T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---

## 아이템52. 다중정의는 신중히 사용하라

### 📌 정적으로 선택되는 다중정의 매서드

```java
public class CollectionClassifier {
    public static String classify(Set<?> s) {
        return "집합";
    }

    public static String classify(List<?> lst) {
        return "리스트";
    }

    public static String classify(Collection<?> c) {
        return "그 외";
    }

    public static void main(String[] args) {
        Collection<?>[] collections = {
                new HashSet<String>(),
                new ArrayList<BigInteger>(),
                new HashMap<String, String>().values()
        };

        for (Collection<?> c : collections)
            System.out.println(classify(c));
    }
}
```

```java
그 외
그 외
그 외

Process finished with exit code 0
```

다중 정의는 컴파일 타임에 어느 메서드를 호출할 지 결정한다. 컴파일 타임의 for문의 c는 항상 Collection<?> 타입이다. 따라서 타입에 상관없이 세 번째 메서드만 호출된다.


### 📌 동적으로 선택되는 재정의 메서드

```java
class Wine {
    String name() { return "포도주"; }
}

class SparklingWine extends Wine {
    @Override
    String name() {
        return "발포성 포도주";
    }
}

class Champagne extends SparklingWine {
    @Override
    String name() {
        return "샴페인";
    }
}

public class Overriding {
    public static void main(String[] args) {
        List<Wine> wineList = List.of(
                new Wine(), new SparklingWine(), new Champagne());

        for (Wine wine : wineList)
            System.out.println(wine.name());
    }
}
```

```
포도주
발포성 포도주
샴페인

Process finished with exit code 0
```

재정의한 메서드는 동적(런타임)으로 선택된다. 메서드 재정의란 상위 클래스가 정의한 것과 같이 똑같은 시그니처 메서드를 하위 클래스에서 다시 정의하는 것을 말한다.

CollectionClassifier의 classifier 원래 의도에 맞게 런타임에 적절한 다중정의 메서드로 자동 분배하고 싶다면 인자를 받아 instanceof 로 검사하면 된다.

```java
public class FixedCollectionClassifier {
    //수정된 컬렉션 분류기 p.314
    public static String classify(Collection<?> c) {
        return c instanceof Set ? "집합" :
                c instanceof List ? "리스트" : "그 외";
    }

    public static void main(String[] args) {
        Collection<?>[] collections = {
                new HashSet<String>(),
                new ArrayList<BigInteger>(),
                new HashMap<String, String>().values()
        };

        for (Collection<?> c : collections)
            System.out.println(classify(c));
    }
}
```



### 📌다중정의가 혼돈을 일으키는 상황을 피하자

classifier처럼 의도하지 않은 동작이 발생할 수 있다. 안전하고 보수적으로 가려면 매개변수 수가 같은 다중정의는 만들지 말자. 

1. 다중정의하는 대신 메서드 이름을 다르게 지어주는 방법도 있다. 
   (ex. ObjectInputStream의 write 메서드)

2. 생성자는 이름을 다르게 지을 수 없으니 두 번째 생성자부터는 무조건 다중 정의가 된다.
   하지만 정적 팩터리라는 대안이 있어 적절히 사용하자.

3. 매개변수 수가 같은 다중정의 메서드가 많더라도 어떤 매개변수 집합을 처리할 지 명확히 구분된다면 헷갈릴 필요가 없다.  하지만 오토박싱과 제네릭이 나오면서 명확히 구분되는 경계선이 모호해졌다. 

   ```java
   public class SetList {
       public static void main(String[] args) {
           Set<Integer> set = new TreeSet<>();
           List<Integer> list = new ArrayList<>();
   
           for (int i = -3; i < 3; i++) {
               set.add(i); -3, -2, -1, 0, 1, 2
               list.add(i);
           }
           for (int i = 0; i < 3; i++) {
               set.remove(i);
               list.remove(i);
           }
           System.out.println(set + " " + list);
       }
   }
   ```

   ```
   [-3, -2, -1] [-2, 0, 2]
   
   Process finished with exit code 0
   ```

   ArrayList에서 remove를 다중정의 했기 때문에 예상했던 결과와 다르게 나온다.

   `set.remove(i)`의 시그니처는 `remove(Object)`로 다중 정의된 메서드가 없어 정상적인 결과가 나온다. 
   `list.remove(i)`는 다중정의된 `remove(int index)`를 선택하여 index 위치에 따른 원소를 제거하기 때문에 결과가 다르게 나온다. 이 문제는 인수를 형변환하여 해결 가능하다.

   ```java
   public class SetList {
       public static void main(String[] args) {
           Set<Integer> set = new TreeSet<>();
           List<Integer> list = new ArrayList<>();
   
           for (int i = -3; i < 3; i++) {
               set.add(i);
               list.add(i);
           }
           for (int i = 0; i < 3; i++) {
               set.remove(i);
               list.remove((Integer) i);
           }
           System.out.println(set + " " + list);
       }
   }
   ```

   ```
   [-3, -2, -1] [-3, -2, -1]
   
   Process finished with exit code 0
   ```

   

### 📌메서드 다중정의 할 때, 서로 다른 함수형 인터페이스도 같은 위치의 인수로 받으면 안된다.

람다와 메서드 참조도 다중정의 혼란을 일으켰다. 
1, 2번 모두 인수도 동일하고 Runnable을 받는 형제 메서드를 다중정의하고 있다.

```java
public class Lamda {
    public static void main(String[] args) {
        //1번 Thread 생성자 호출
        new Thread(System.out::println).start();

        //2번 ExecutorService의 submit 메서드 호출
        ExecutorService es = Executors.newCachedThreadPool();

        es.submit(System.out::println);

    }
}
```

2번에서 컴파일 에러 발생

```
D:\repo\codeTestJDK8\src\main\java\com\github\sejoung\codetest\methods\overloading\Lamda.java
Error:(13, 11) java: reference to submit is ambiguous
  both method <T>submit(java.util.concurrent.Callable<T>) in java.util.concurrent.ExecutorService and method submit(java.lang.Runnable) in java.util.concurrent.ExecutorService match
Error:(13, 18) java: incompatible types: cannot infer type-variable(s) T
    (argument mismatch; bad return type in method reference
      void cannot be converted to T)
```

원인은 submit 다중정의 메서드 중에 Callable<T>를 받는 메서드가 있고, println도 다중정의된 메서드라는 데 있다.

`System.out::println` 같은 부정확한 메서드 참조나 암시적 타입 람다식 의 인수 표현식은 목표 타입이 선택 되기 전에는 그 의미가 정해지지 않기 때문에 적용성 테스트 때 무시된다. ❓

컴파일할 때 명령줄 스위치로 `-Xlint:overloads`를 지정하면 이런 종류의 다중정의를 경고해 줄 것이다.

String 클래스를 보면 contentEquals 메소드는 forward ❓시켜 버리는 방법을 선택했다. foward는 상대적으로 더 특수한 다중정의 메서드에서 덜 특수한(일반적인) 다중정의 메서드로 일을 넘겨 버리는 것. 

아래는 같은 객체를 입력하면 동일한 기능을 수행한다. 

```java
public boolean contentEquals(StringBuffer sb) {
    return contentEquals((CharSequence)sb);
}
```


아래는 같은 객체를 넘겨도 다른 작업을 한다.

```java
public static String valueOf(Object obj) {
    return (obj == null) ? "null" : obj.toString();
}

public static String valueOf(char data[]) {
    return new String(data);
}
```



### 참고자료 

http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9788966262281&orderClick=LEa&Kc= 