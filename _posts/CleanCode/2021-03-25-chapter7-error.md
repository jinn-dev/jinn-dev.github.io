---
title: "클린코드 7장. 오류 처리"
categories:
  - CleanCode
last_modified_at: 2021-03-25T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
## 제 7장. 오류 처리

### 📌오류 코드보다 예외를 사용하라

오류 코드 사용은 호출자 코드가 복잡해진다.

오류가 발생하면 예외를 던지는게 낫다.

```java
public void sendShutDown() {
	try {
		tryToShutDown();
	} catch (DeviceShutDownError e) {
		logger.log(e);
	}
}

private void tryToShutDown() throws DeviceShutDownError {
		DeviceHandle handle = getHandle(DEV1);
		DeviceRecord record = retrieveDeviceRecord(handle);
		pauseDevice(handle);
		clearDeviceWorkQueue(handle);
		closeDevice(handle);
}

private DeviceHandle getHandle(DeviceID id) {
    ...
	throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    ...
}
```



### 📌 Try-Catch-Finally 부터 작성하라

try 블록은 트랜잭션과 비슷하다. try 블록에서 무슨 일이 생겨도 catch 블록은 프로그램 상태를 일관성 있게 유지해야 한다.

TDD 방식으로 메소드 구현

1. 단위 테스트 만들기

   ```java
    @Test(expected = StorageException.class)
    public void retrieveSectionShouldThrowOnInvalidFileName() {
        sectionStore.retrieveSection("invalid - file");
    }
   ```



2. 단위 테스트에 맞춰 코드 구현

   ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
        // 실제로 구현할 때까지 비어 있는 더미를 반환한다.
        return new ArrayList<RecordedGrip>();
    }
   ```



3. 파일 접근 시도하도록 코드 수정

   ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
        try {
            FileInputStream stream = new FileInputStream(sectionName);
        } catch (Exception e) {
            throw new StorageException("retrieval error", e);
        }
        return new ArrayList<RecordedGrip>();
    }
   ```



4. 리팩터링 가능

   ```java
    public List<RecordedGrip> retrieveSection(String sectionName) {
        try {
            FileInputStream stream = new FileInputStream(sectionName);
            stream.close();
        } catch (FileNotFoundException e) {
            throw new StorageException("retrieval error", e);
        }
        return new ArrayList<RecordedGrip>();
    }
   ```

   TDD를 사용해 필요한 나머지 논리를 추가한다. 나머지 논리는 FileInputStream을 생성하는 코드와 closes 호출문 사이에 넣으며, 오류나 예외가 전혀 발생하지 않는다고 가정한다.


아래처럼 하면 try 블록의 트랜잭션 범위부터 구현하게 되어 범위 내에서 트랜잭션 본질을 유지하기  쉬워진다.

- 먼저 강제로 예외를 일으키는 테스트 케이스를 작성
- 테스트를 통과하게 코드를 작성



### 📌 미확인 예외(unchecked)를 사용하라

최하위 함수를 변경해 확인된 예외를 던지면 catch 블록에서 새로운 예외를 처리하거나, 선언부에 throw 절을 추가해야 한다. 단계별로 연쇄적인 수정이 일어나고 throw 경로에 위치하는 모든 함수가 최하위 함수에서 던지는 예외를 알아야 하므로 캡슐화가 깨진다.

**Checked Exception**

- 반드시 예외 처리 해야 함
- Rollback 안됨
- IOException, SQLException

```java
  private final ObjectMapper objectMapper = new ObjectMapper();

  // 예외처리를 throws를 통해서 위임하고 있습니다.
  public String writeValueAsString(Object object) throws JsonProcessingException {
    return objectMapper.writeValueAsString(object);
  }

  // 예외처리를 throws를 통해서 위임하고 있습니다.
  public <T> T readValue(String json, Class<T> clazz) throws IOException {
    return objectMapper.readValue(json, clazz);
  }

```



**UnChecked Exception**

- 예외 처리 안해도 됨
- Rollback 진행
- NullPointerexception, IllegalArgumentException
- <u>**RuntimeException 상속**</u>

```java
public String writeValueAsString(Object object) {
    try {
      return objectMapper.writeValueAsString(object);
    } catch (JsonProcessingException e) {
      throw new JsonSerializeFailed(e.getMessage());
    }
  }

  public <T> T readValue(String json, Class<T> clazz) {
    try {
      return objectMapper.readValue(json, clazz);
    } catch (IOException e) {
      throw new JsonDeserializeFailed(e.getMessage());
    }
  }
```



**Checked Exception은 왜 Rollback이 안될까?**

Checked Exception은 복구가 가능하다는 매커니즘을 가지고 있음

하지만 Exception으로 처리하는 것보다 코드 흐름으로 제어하는게 더 적합하다.

유니크한 값이 중복되서 SQLException이 발생하는 경우는 난수로 소스 내에서 insert 처리하면 되겠지만, 현실적인 방법은 DuplicateEmailException(RuntimeException)을 발생시키고 입력을 다시 유도하는 것이다.

**Checked Exception을 만나면 더 구체적인 Unchecked Exception을 발생시켜 정확한 정보 전달 및 로직 흐름을 제어해야 한다.**

참고: https://cheese10yun.github.io/checked-exception/



### 📌 예외에 의미를 제공하라

예외를 던질 때는 전후 상황을 충분히 덧붙인다. 그러면 오류가 발생한 원인과 위치를 찾기 쉬워진다.

즉, 오류 메시지에 정보를 담아 예외와 함께 던진다. (실패한 연산 이름 및 실패 유형 언급)




### 📌 호출자를 고려해 예외 클래스를 정의하라

애플리케이션에서 오류 정의할 때 가장 중요한 관심사는 **오류를 잡아내는 방법**이 되야 한다.

**오류를 형편없이 분류한 사례**

```java
 ACMEPort port = new ACMEPort(12);

 try {
     port.open();
 } catch (DeviceResponseException e) {
     reportPortError(e);
     logger.log("Device response exception", e);
 } catch (ATM1212UnlockedException e) {
     reportPortError(e);
     logger.log("Unlock exception", e);
 } catch (GMXError e) {
     reportPortError(e);
     logger.log("Device response exception");
 } finally {
     ...
 }
```


**개선**
호출되는 라이브러리 API를 감싸면서 예외 유형 하나를 반환하면 된다. LocalPort는 ACMEPort 클래스가 던지는 예외를 잡아 변환하는 Wrapper 클래스이다.

```java
LocalPort port = new LocalPort(12);
 try {
     port.open();
 } catch (PortDeviceFailure e) {
     reportError(e);
     logger.log(e.getMessage(), e);
 } finally {
     ...
 }


public class LocalPort {
     private ACMEPort innerPort;

     public LocalPort(int portNumber) {
         innerPort = new ACMEPort(portNumber);
     }

     public void open() {
         try {
             innerPort.open();
         } catch (DeviceResponseException e) {
             throw new PortDeviceFailure(e);
         } catch (ATM1212UnlockedException e) {
             throw new PortDeviceFailure(e);
         } catch (GMXError e) {
             throw new PortDeviceFailure(e);
         }
     }
     ...
}
```

외부 API를 사용할때 감싸기 방법이 최선이다.

- 외부 라이브러리와 프로그램 사이에서 의존성이 크게 줄어든다.
- 다른 라이브러리로 갈아타도 비용이 적다.
- 프로그램 테스트가 쉬워진다.
- 외부 API 설계 방식에 의존하지 않아도 됨



### 📌 정상 흐름을 정의하라

클래스나 객체가 예외적인 상황을 캡슐화 처리해서 클라이언트 코드가 예외적인 상황을 처리할 필요가 없도록 할 수 있다.

특수 사례 객체를 반환하는 특수 사례 패턴의 예시
청구한 식비를 총계에 더하고, 청구한 식비가 없으면 일일 기본 식비를 더한다.

```java
try {
     MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
     m_total += expenses.getTotal();
 } catch(MealExpencesNotFound e) {
     m_total += getMealPerDiem();
}
```

청구한 식비가 없다면 일일 기본 식비를 반환하는 MealExpense 객체를 반환한다.

```java
public class PerDiemMealExpenses implements MealExpenses {
   public int getTotal() {
       // 기본값으로 일일 기본 식비를 반환한다.
       // (예외가 아닌)
   }
}

MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
 m_total += expenses.getTotal();
```



### 📌 null을 반환하지 마라

다음은 한줄마다 null을 확인하는 코드이다.

```java
public void registerItem(Item item) {
	if (item != null) {
		ItemRegistry registry = peristentStore.getItemRegistry();
		if (registry != null) {
			Item existing = registry.getItem(item.getID());
			if (existing.getBillingPeriod().hasRetailOwner()) {
				existing.register(item);
			}
		}
	}
}
```

null을 반환하는 코드는 일거리도 늘리고, 호출자에게 문제를 떠넘긴다.  둘째 행에 peristentStore null 체크가 빠졌는데 NPE가 발생될 수 있다.

사용하려는 외부 API가 null을 반환한다면 메서드를 구현해 예외를 던지거나 특수 사례 객체를 반환하는 방식을 고려한다.

대부분의 경우 **특수 사례 객체**가 손쉬운 해결책이다.

```java
// bad
List<Employee> employees = getEmployees();
if(employees != null) {
	for(Employee e : employees) {
		totalPay += e.getPay();
	}
}

// good
List<Employee> employees = getEmployees();
for(Employee e : employees) {
	totalPay += e.getPay();
}

public List<Employee> getEmployees() {
	if (..직원이 없다면..)
		return Collections.emptyList();
}
```



NULL 객체 패턴 참고 - https://johngrib.github.io/wiki/null-object-pattern/

Optional 사용 - https://www.daleseo.com/java8-optional-after/



### 📌 null을 전달하지 마라

정상적인 인수로 null을 기대하는 API가 아니라면 메서드로 null을 전달하는 코드는 최대한 피한다.

```java
// bad - Parameter에 null값이 들어오면 NullPointerException 발생
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        return (p2.x – p1.x) * 1.5;
    }
}

// bad - InvalidArgumentException 사용: 위 방법보단 낫다.. 하지만 좋은 방법은 아님
public class MetricsCalculator {
    public double xProjection(Point p1, Point p2) {
        if (p1 == null || p2 == null) {
            throw InvalidArgumentException("Invalid argument...");
        }
        return (p2.x – p1.x) * 1.5;
    }
}

// bad - 가독성 개선, 첫번째 예시와 같이 NullPointerException 문제를 해결하지 못한다.
public class MetricsCalculator {
  public double xProjection(Point p1, Point p2) {
    assert p1 != null : "p1 should not be null";
    assert p2 != null : "p2 should not be null";

    return (p2.x – p1.x) * 1.5;
  }
}
```

그냥 null을 넘기지 못하도록 금지하는 정책이 합리적이다.
