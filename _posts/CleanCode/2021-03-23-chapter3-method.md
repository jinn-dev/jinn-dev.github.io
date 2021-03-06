---
title: "클린코드 3장. 함수"
categories:
  - CleanCode
last_modified_at: 2021-03-23T22s:00:00+09:00
toc_sticky: true
toc: true
comments: true
---
## 제 3장. 함수

### 📌작게 만들어라

if/else 문 while문 등에 들어가는 블록은 한 줄 정도가 적당하다.

중첩 구조가 생길만큼 함수가 커져서는 안된다.



### 📌한 가지만 해라

한 가지만 하는지 판단하는 방법

- 특정 부분이 의미 있는 이름으로 다른 함수를 만들 수 있다면 그 함수는 여러 작업을 하는 것이다.



### 📌함수 당 추상화 수준은 하나로

함수가 한 가지 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.

**내려가기** 규칙

- 위에서 아래로 이야기처럼 읽혀야 좋다.

- 아래로 내려갈 수록 함수 추상화 수준이 낮아진다.



### 📌Switch문

switch문은 작게 만들기 어렵고, 한 가지 작업만 하는 switch문을 만들기 어렵다.

**다형성** 객체 생성 코드로 개선할 수 있다.



switch문을 abstract factory에 숨겨서 다형성 객체를 생성하는 코드 안에서만

switch 문을 사용하도록 한다.

```java
//기존 코드
public Money calculatePay(Employee e) throws InvalidEmployeeType {
	switch (e.type) {
		case COMMISSIONED:
			return calculateCommissionedPay(e);
		case HOURLY:
			return calculateHourlyPay(e);
		case SALARIED:
			return calculateSalariedPay(e);
		default:
			throw new InvalidEmployeeType(e.type);
	}
}
```



```java
//개선 코드
public abstract class Employee {
	public abstract boolean isPayday();
	public abstract Money calculatePay();
	public abstract void deliverPay(Money pay);
}

public interface EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}

public class EmployeeFactoryImpl implements EmployeeFactory {
	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
		switch (r.type) {
			case COMMISSIONED:
				return new CommissionedEmployee(r) ;
			case HOURLY:
				return new HourlyEmployee(r);
			case SALARIED:
				return new SalariedEmploye(r);
			default:
				throw new InvalidEmployeeType(r.type);
		}
	}
}
```



### 📌서술적인 이름을 사용하기

작은 함수일수록 그 기능이 명확하고 이름을 붙이기가 더 쉽고, 일관성 있는 서술형 이름을 사용하면

코드를 순차적으로 이해하기 쉬워진다. 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용한다.



### 📌함수 인수

가장 이상적인 인수 갯수는 0개, 차선은 1개다.

#### 플래그 인수

bool 값을 넘기는 것 자체가 함수가 여러 가지를 처리한다고 대놓고 말하는 것과 마찬가지다. 쓰지말자.

#### 이항 함수

2개의 인수 간의 자연적인 순서가 있어야 함

#### 삼항 함수

이항 함수보다 이해하기 어렵고 위험도가 2배 이상 늘어난다.

...

🙎‍♀실제로 코드를 작성하다보면 비즈니스 또는 도메인에 의해 어쩔 수 없이 여러 인수를 쓸 수밖에 없어 전부 다 공감이 되지 않는다. 하지만 **플래그 인수 지양**, **자연적인 순서**는 지켜볼 만 하다.
{: .notice--info}


### 📌부수효과 일으키지 말기

함수에 한 가지만 하겠다고 약속하고는 남몰래 다른짓을 하지 말자.

아래 코드의 `Session.initialize()`는 패스워드를 확인한다는 함수명과는 맞지 않는 부수효과다.

```java
public class UserValidator {
	private Cryptographer cryptographer;
	public boolean checkPassword(String userName, String password) {
		User user = UserGateway.findByName(userName);
		if (user != User.NULL) {
			String codedPhrase = user.getPhraseEncodedByPassword();
			String phrase = cryptographer.decrypt(codedPhrase, password);
			if ("Valid Password".equals(phrase)) {
				Session.initialize();
				return true;
			}
		}
		return false;
	}
}
```

`checkPassword`보다는 `checkPasswordAndInitializeSession` 이름이 적당하다.



### 📌명령과 조회를 분리하기

`public boolean set(String attribute, String value);`
이름이 attribute인 속성을 찾아 값을 value로 설정한 후 성공하면 true, 실패시 false를 반환하는 함수다.

함수는 객체 상태를 변경하거나, 반환하거나 둘 중 하나만 수행해야 한다.

```java
if(attributeExists("username")) {
  setAttribute("username", "unclebob");
}
```



### 📌오류 코드보다 예외를 사용하기

#### Try/Catch 블록 뽑아내기

try/catch 블록은 추하다. 코드 구조에 혼란을 일으키며, 정상 동작과 오류 처리 동작을 뒤섞는다.

아래 코드의 delete는 모든 오류를 처리한다.

실제로 행동을 하는 함수`deletePageAndAllReferences`는 예외를 처리하지 않는다.

이렇게 정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다.

🙎‍♀ 즉, 가장 상위 레이어에서 오류를 처리하는게 깔끔하다.
{: .notice--info}

```java
public void delete(Page page) {
	try {
		deletePageAndAllReferences(page);
  	} catch (Exception e) {
  		logError(e);
  	}
}

private void deletePageAndAllReferences(Page page) throws Exception {
	deletePage(page);
	registry.deleteReference(page.name);
	configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e) {
	logger.log(e.getMessage());
}
```



#### Enum Error.java

오류 코드를 사용하게 되면 enum 코드를 쓰게 되는데 의존성 자석이 되버려서 다른 클래스나 모듈에서 참조 시 해당 enum을 import해서 사용해야 하므로 재변경/재배치가 번거롭다. 그러므로 예외를 사용하는 것이 더 안전하다.

🙎‍♀ enum에 대한 이점은 모듈, 특정 클래스 더미들에서만 사용할 경우에 해당되는 것 같다. request, response로 전파될 경우 enum 하나 추가될때마다 의존하는 모듈 전부 다 적용해야 하기 때문이다. 나 역시 enum이 편하고 깔끔해서 무작정 쓰기도 했는데, 이제는 enum의 사용 범위를 고려하여 사용해야겠다.
{: .notice--info}


### 📌반복하지 말기

중복은 모든 소프트웨어의 악의 근원, 평상시에 중복 코드 줄이는 데 노력하자.



### 📌구조적 프로그래밍

모든 함수와 함수 내 모든 블록에 입구와 출구는 하나여야 된다. 루프 안에서 break, continue 사용 금지. 특히, goto는 절대적으로 사용해선 안된다.

하지만, 구조적 프로그래밍은 큰 함수일 때 유용하다. 함수가 작다면 return, break, continue를 여러 번 사용해도 괜찮고, 오히려 단일 입/출구 규칙보다 의도를 표현하기 쉬워진다.



### 📌함수를 어떻게 짜죠?

반복되고 복잡한 초안을 작성한 후, TDD를 반복하며 코드를 다듬자.
