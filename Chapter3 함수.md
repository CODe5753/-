# Chapter3. 함수

> 어떤 프로그램이든 가장 기본적인 단위가 `함수`다

- 읽기 쉽고 이해하기 쉬운 함수는 무슨 이유에서일까?
- 의도를 분명히 표현하는 함수를 어떻게 구현할 수 있을까?
- 함수에 어떤 속성을 부여해야 처음 읽는 사람이 프로그램 내부를 직관적으로 파악할 수 있을까?

## 작게 만들어라!

> 작은 함수, 코드가 짧은 함수를 만들어라

### 블록과 들여쓰기

- `if`/`else`/`while`문 등에 들어가는 블록은 한 줄이어야 한다.
  - 대개 이 곳에서 함수를 호출하기 때문이다.
  - 바깥을 감싸는 함수(`enclosing function`)가 작아진다.
  - 호출 함수 이름을 적절히 짓는다면 코드를 이해하기도 쉬워진다.

- 즉, 중첩 구조가 생길만큼 함수가 커져서는 안 된다.

## 한 가지만 해라!

> 함수는 `한 가지`를 해야 한다. 그 `한 가지`를 잘 해야 한다. 그 `한 가지`만을 해야 한다.

## 함수 당 추상화 수준은 하나로!

#### 위에서 아래로 코드 읽기: 내려가기 규칙

> 코드는 위에서 아래로 이야기처럼 읽혀야 좋다

## Switch문

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType{
    switch (e.type){
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

위 함수에서 몇 가지 문제가 있다.

1. 함수가 길다.

   - 새 직원 유형을 추가하면 더 길어지기 때문

2. `한 가지` 작업만 수행하지 않는다.

3. SRP(Single Responsibility Principle)를 위반한다.

   - 코드를 변경할 이유가 여럿이기 때문

4. OCP(Open Closed Principle)를 위반한다.

   - 새 직원 유형을 추가할 때마다 코드를 변경하기 때문

5. **위 함수와 구조가 동일한 함수가 무한정 존재한다는 사실**이다.

   ```java
   isPayday(Employee e, Date date);
   deliverPay(Employee e, Money pay);
   ...
   ```

   가능성은 무한하며, 모두가 똑같이 유해한 구조다.

```java
// 위 문제를 해결한 코드
public abstract class Employee {
    public abstract boolean isPayDay();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);    
}
//
public interface EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
//
public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
        switch (r.type) {
            case COMMISSIONED:
                return new CommissionedEmployee(r);
            case HOURLY:
                return new HourlyEmployee(r);
            case SALARIED:
                return new SalariedEmployee(r);
            default:
                throw new InvalidEmployeeType(r.type);
        }
    }
}
```

이렇게 상속 관계로 숨긴 후에는 절대로 다른 코드에 노출하지 않는다.

## 서술적인 이름을 사용하라!

> "코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 된다"

- 이름이 길어도 괜찮다.
- 길고 서술적인 이름이, 짧고 어려운 이름보다 좋다.
- 길고 서술적인 이름이, 길고 서술적인 주석보다 좋다.
- 함수 이름을 정할 때는 여러 단어가 쉽게 읽히는 명명법을 사용한다.
- 여러 단어를 사용해 함수 기능을 잘 표현하는 이름을 선택한다.

- 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다

- 이름을 붙일 때는 일관성이 있어야 한다.

  - 모듈 내에서 함수 이름은 **같은 문구, 명사, 동사**를 사용한다.
    - **include**Setup**And**Teardown**Pages**
    - **include**Suite**Setup**Page
    - **include**Setup**Page**

  - 문체가 비슷하면 이야기를 순차적으로 풀어가기도 쉬워진다

## 함수 인수

> 이상적인 인수 개수는 0개(무항)다. 4개 이상(다항)은 가능한 피하는 편이 좋다.

`includeSetupPageInto(new PageContent)`보다 `includeSetupPage()`가 이해하기 더 쉽다.

만약, 인수가 여러개이고 테스트 케이스를 작성한다고 가정하자. 각 인수의 조합으로 함수를 검증해야 하므로 굉장히 번거로워진다.

### 많이 쓰는 단항 형식

### 플래그 인수

### 이항 함수

### 삼항 함수

### 인수객체

> 인수가 2-3개 필요하다면 클래스 변수로 선언할 것을 고려한다

```java
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

### 인수 목록

**인수 개수가 가변적인 함수**도 필요하다.

```java
String.format("%s worked %.2f" hours.", name, hours");
```

가변 인수 전부를 동등하게 취급하면 <u>List 형 인수 하나로 취급할 수 있다</u>. 

즉, String.format은 2개의 인수를 받는 `이항 함수`이다.

```java
public String format(String format, Object... args)
```

가변 인수를 취하는 모든 함수에 같은 원리가 적용된다.

### 동사와 키워드

```java
write(name);
writeField(name); // 이름(name)이 필드(field)라는 사실이 분명히 드러남
```

단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.



```java
assertEquals(expected, actual);
assertExpectedEqualsActual(expected, actual); // 함수 이름에 인수 이름이 있기에 인수 순서를 기억할 필요가 없어진다
```

함수 이름에 키워드를 추가하는 형식

## 부수 효과를 일으키지 마라

```java
// UserValidator.java
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

`userName`과 `password`를 확인하고 두 인수가 올바르면 true를 아니면 false를 반환하는 함수다.

하지만, 함수는 부수 효과를 일으킨다.