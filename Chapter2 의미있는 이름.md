**목차**

- [Chapter 2.의미 있는 이름](#chapter-2의미-있는-이름)
  - [의도를 분명히 밝혀라](#의도를-분명히-밝혀라)
  - [그릇된 정보를 피하라](#그릇된-정보를-피하라)
  - [의미 있게 구분하라](#의미-있게-구분하라)
  - [발음하기 쉬운 이름을 사용하라](#발음하기-쉬운-이름을-사용하라)
  - [검색하기 쉬운 이름을 사용하라](#검색하기-쉬운-이름을-사용하라)
  - [인터페이스 클래스와 구현 클래스](#인터페이스-클래스와-구현-클래스)
  - [클래스 이름](#클래스-이름)
  - [메서드 이름](#메서드-이름)
  - [한 개념에 한 단어를 사용하라](#한-개념에-한-단어를-사용하라)
  - [말장난을 하지 마라](#말장난을-하지-마라)
  - [해법 영역에서 가져온 이름을 사용하라](#해법-영역에서-가져온-이름을-사용하라)
  - [문제 영역에서 가져온 이름을 사용하라](#문제-영역에서-가져온-이름을-사용하라)
  - [의미 있는 맥락을 추가하라](#의미-있는-맥락을-추가하라)
  - [불필요한 맥락을 없애라](#불필요한-맥락을-없애라)
  - [결론](#결론)
---

# Chapter 2.의미 있는 이름

## 의도를 분명히 밝혀라

- 좋은 이름을 지으려면 시간이 걸리지만 **좋은 이름으로 절약하는 시간이 훨씬 더 많다**
- 따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말

```java
int d; // 결과 시간(단위: 날짜)

int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

- 아래 두 코드를 보자

```java
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x : theList)	// theList엔 뭐가 들었을까?        
        if(x[0] == 4)	// theList 0번째 값을 왜 확인하는걸까?, 값 4는 무슨 의미인가?
            list1.add(x);
    return list1;    // 함수가 반환하는 list1을 어떻게 사용하는가?
}
```

```java
public List<int[]> getFlaggedCells() {
    List<int[]> flaggedCells = new ArrayList<int[]>();
    for (int[] cell : gameBoard)
        if (cell[STATUS_VALUE] == FLAGGED)
            flaggedCells.add(cell);
    return flaggedCells;
}
```

- 연산자 수와 상수 수는 앞의 예와 동일하지만 네이밍만으로 코드는 더욱 명확해졌다
- 여기서 한단계 더 개선해보자

```java
public List<Cell> getFlaggedCells() {	// 칸을 Class화 한다
    List<Cell> flaggedCells = new ArrayList<Cell>();
    for (Cell cell : gameBoard)
        if (cell.isFlagged())	// 명시적인 함수를 사용해 FLAGGED라는 상수를 감춘다
            flaggedCells.add(cell);
    return flaggedCells;
}
```



## 그릇된 정보를 피하라

대표적으로 소문자 `l`과 대문자 `O`가 있다. 이 둘은 각각 숫자 `1`, 숫자 `0`과 비슷한 모양을 갖고 있다

```
left를 줄여 l, object를 줄여 o로 사용하는 예를 많이 봤고 나도 그렇게 써왔는데, 지양해야겠다
```



## 의미 있게 구분하라

```java
public static void copyChars(char a1[], char a2[]) {
    for (int i = 0; i < a1.length; i++){
        a2[i] = a1[i];
    }
}
```

연속적인 숫자를 덧붙인 이름은 의도적 이름과 정 반대다.

이런 이름은 그릇된 정보를 제공하지도 않으며, 아무런 정보를 제공하지 못한다.

아래처럼 바꿔보자

```java
public static void copyChars(char src[], char dest[]){
    for (int i = 0; i < a1.length; i++){
        dest[i] = src[i];
    }
}
```

위 예시 이외에도 `불용어`(의미가 불문명한 용어)를 주의하자

- Product`Info`
- Product`Data`

또한 구분하기 어려운 예시도 참고하자

- `Money`와 `MoneyAmount`
- `customerInfo`와 `customer`
- `accountData`와 `account`
- `theMessage`와 `message`

**읽는 사람이 차이를 알도록** 이름을 짓자

## 발음하기 쉬운 이름을 사용하라

아래 두 코드를 비교해보자

```java
class DtaRcrd102{
    private Date genymdhms;
    private Date modymdhms;
    private final String pszqint = "102";
}
```

```java
class Customer{
    private Date generationTimestamp;
    private Date modificationTimestamp;
    private final String recordId = "102";
}
```

쉽게, 회의 때 해당 변수나 클래스를 발음한다고 생각하면 된다.

프로그래밍은 결국 사회 활동 중 하나다.

## 검색하기 쉬운 이름을 사용하라

```java
for (int j=0; j<34; j++){
    s += (t[j]*4)/5;
}
```

```java
int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for (int j =0; j<NUMBER_OF_TASKS; j++){
    int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
    int realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
    sum += realTaskWeeks;
}
```

`sum`이 유용하진 않으나 최소한 `검색`이 가능하다. 그리고, 이름이 길어지더라도 최소한 <u>검색이 가능하다</u>는 것을 염두해두자

## 인터페이스 클래스와 구현 클래스

인터페이스 클래스와 구체 클래스 네이밍은 아래와 같이 한다

- ShapeFactory`Imp` - 인터페이스
- `C`ShapeFactory - concrete class

## 클래스 이름

- 명사나 명사구를 사용
- UpperCamelCase
  - **A**ccount
  - **A**ddress**P**arser
- Manager, Processor, Data, Info처럼 `불용어`는 피하자

## 메서드 이름

- 동사나 동사구를 사용
- LowerCalmelCase
  - **p**ost**P**ayment
  - **d**elete**P**age
  - **s**ave
- 접근자, 변경자, 조건자는 javabean 표준에 따라 값 앞에 `get`, `set`, `is`를 붙인다

```java
String name = employee.getName();
customer.setName("mike");
if (paycheck.isPosted())...
```

- 생성자(Constructor)를 오버로딩할 때는 **정적 팩토리 메서드**를 사용한다

```java
Complex fulcrumPoint = Complex.FromRealNumber(23.0);
Complex fulcrumPoint = new Complex(23.0); // 위 방식보단 아래 방식을 사용하자
```



## 한 개념에 한 단어를 사용하라

똑같은 메서드를 클래스마다 `fetch`, `retrieve`, `get`으로 제각각 부르면 혼란스럽다

```
이 코드를 처음 보는 사람은 fetch와 get이 다른가? 왜 따로 구분해놨지? 라고 생각할 수 있다.
```



## 말장난을 하지 마라

집중적인 탐구가 필요한 코드가 아니라 대충 훑어봐도 이해할 코드 작성이 목표다.

- add: 기존 값 두개를 더하거나 이어서 새로운 값을 만듦
- append, insert: 집합에 값 하나를 추가함

위 경우, 일관성을 위해 add를 사용하는 것은 말장난이며 맥락이 다르다. 반드시 구분하자.

의미를 해독할 책임은 독자에게 가선 안되고, **의도를 밝히는 책임을 지니는 저자가 되어야 한다.**

## 해법 영역에서 가져온 이름을 사용하라

> 기술 개념에는 기술 이름이 가장 적합한 선택이다

모든 이름을 `문제 영역(domain)`에서 가져오는 정책은 현명하지 못하다.

## 문제 영역에서 가져온 이름을 사용하라

> 문제 영역 개념과 관련이 깊은 코드라면 문제 영역에서 이름을 가져와야 한다

## 의미 있는 맥락을 추가하라

```java
String firstName;
String lastName;
String street;
String state;
...
```

위 코드를 봤을 때 `state`는 주소라는 사실을 알 수 있다.

```java
String state;
```

하지만 위처럼 `state`하나만 선언되어 있는 경우 주소의 일부라는 사실을 알아채기 힘들다.

```java
String addrState;
```

위 처럼 `addr`이라는 접두어를 추가하면 의미가 보다 명확해진다.

## 불필요한 맥락을 없애라

**의미가 분명한 경우에 한해서** <u>짧은 이름이 긴 이름보다 좋다</u>

고급 휘발유 충전소(Gas Station Deluxe)라는 앱을 짤 때, 모든 클래스 앞에 GSD라는 접두어는 바람직하지 못하다. 너무 포괄적인 개념이며, 중복적이다.

## 결론

사람들이 이름을 바꾸지 않으려는 이유 하나는 다른 개발자가 반대할까 두려워서다.

우리 대다수는 자신이 짠 클래스 이름과 메서드 이름을 모두 암기하지 못한다.

코드를 개선하려는 노력을 중단해서는 안 된다.