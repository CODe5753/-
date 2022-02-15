# Chapter 9. 단위 테스트

## TDD 법칙 세 가지

- 첫째, 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다
- 둘째, 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다
- 셋째, 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다

## 깨끗한 테스트 코드 유지하기

- 테스트 코드가 복잡할수록 실제 코드를 짜는 시간보다 테스트 케이스를 추가하는 시간이 더 걸리기 십상이다.
- 테스트 코드는 실제 코드만큼 중요하며, 실제 코드만큼 깨끗하게 짜야한다.

### 테스트는 유연성, 유지보수성, 재사용성을 제공한다

- 테스트 케이스가 없으면 실제 코드를 유연하게 만드는 버팀목도 사라진다.
- 코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 바로 단위테스트다.
- 테스트 케이스가 없다면 **모든 변경이 잠정적인 버그**다
- 실제 코드를 점검하는 **자동화된 단위 테스트 슈트**는 설계와 아키텍처를 최대한 깨끗하게 보존하는 열쇠다.
- 테스트 케이스가 있으면 코드 변경이 쉬워진다.

## 깨끗한 테스트 코드

- 깨끗한 테스트 코드엔 **가독성이 중요**하다.
- 테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.

```java
// 9-1
// SerializedPageResponderTest.java
public void testGetPageHieratchyAsXml() throws Exception {
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml); //중복
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}

public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throws Exception {
  WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  PageData data = pageOne.getData();
  WikiPageProperties properties = data.getProperties();
  WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
  symLinks.set("SymPage", "PageTwo");
  pageOne.commit(data);

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml); //중복
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
  assertNotSubString("SymPage", xml);
}

public void testGetDataAsHtml() throws Exception {
  crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");

  request.setResource("TestPageOne"); request.addInput("type", "data");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("test page", xml);
  assertSubString("<Test", xml);
}
```

- `addPage`와 `assertSubString`을 부르느라 중복되는 코드가 매우 많다.

- `PathParser`는 `crawler`가 사용하는 객체인데, 테스트와 무관하며 테스트 코드의 의도만 흐린다.
- `responder` 객체를 생성하는 코드와 `response`를 수집해 변환하는 코드 역시 마찬가지다.
- `resource`와 인수에서 요청 URL을 만드는 코드도 보인다.
- 독자에 대한 배려가 부족하다. 테스트와 무관한 코드를 이해한 후에야 테스트 케이스를 이해할 수 있다.

```java
// 9-2
// SerializedPageResponderTest.java (Refactored code)
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}

public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
  WikiPage page = makePage("PageOne");
  makePages("PageOne.ChildOne", "PageTwo");

  addLinkTo(page, "PageTwo", "SymPage");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
  assertResponseDoesNotContain("SymPage");
}

public void testGetDataAsXml() throws Exception {
  makePageWithContent("TestPageOne", "test page");

  submitRequest("TestPageOne", "type:data");

  assertResponseIsXML();
  assertResponseContains("test page", "<Test");
}
```

- `BUILD-OPERATE-CHECK` 패턴이 위와 같은 테스트 구조에 적합하다.
  1. 테스트 자료를 만든다.
  2. 테스트 자료를 조작한다.
  3. 조작한 결과가 올바른지 확인한다.
- 잡다하고 세세한 코드를 거의 다 없앴다.
- 테스트 코드는 본론에 돌입해 진짜 필요한 자료 유형과 함수만 사용한다.

### 도메인에 특화된 테스트 언어

- 위 코드는 도메인에 특화된 언어(DSL)로 테스트 코드를 구현하는 기법을 보여준다.
- 흔히 쓰는 시스템 조작 API를 사용하는 대신 API 위에 함수와 유틸리티를 구현한 후 그 함수와 유틸리티를 사용하므로 테스트 코드를 짜기도 읽기도 쉬워진다.
- 이렇게 구현한 유틸리티는 테스트 코드에서 사용하는 특수 API가 된다.
- 숙련된 개발자라면 자기 코드를 좀 더 간결하고 표현력이 풍부한 코드로 리팩터링해야 마땅하다.

### 이중 표준

- 테스트 API 코드에 적용하는 표준은 실제 코드에 적용하는 표준과 확실히 다르다.
- 단순하고, 간결하고, 표현력이 풍부해야 하지만, 실제 코드만큼 효율적일 필요는 없다.
- 실제 환경이 아니라 테스트 환경에서 돌아가는 코드이기 때문인데, 실제 환경과 테스트 환경은 요구사항이 판이하게 다르다.

```java
// 9-3
// EnvironmentControllerTest.java
// 온도가 급격하게 떨어지면 경보, 온풍기, 송풍기가 모두 가동되는지 확인하는 코드
@Test
public void turnOnLoTempAlarmAtThreashold() throws Exception {
  hw.setTemp(WAY_TOO_COLD); 
  controller.tic(); 
  assertTrue(hw.heaterState());   
  assertTrue(hw.blowerState()); 
  assertFalse(hw.coolerState()); 
  assertFalse(hw.hiTempAlarm());       
  assertTrue(hw.loTempAlarm());
}
```

- 위 코드는 세세한 사항이 아주 많다.
- 이런식으로 모든 state를 확인해야 하면 따분하고 읽기가 어렵다.

```java
// 9-4
// EnvironmentControllerTest.java (Refactored code)
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
    wayTooCold();
    assertEquals("HBchL", hw.getState());
}
```

- `tic` 함수는 `wayTooCold`라는 함수를 만들어 숨겼다.
- assertEquals를 주목한다. (`HBchL`)
  - 대문자는 '켜짐'이고 소문자는 '꺼짐'을 뜻한다.
  - 문자는 항상 {heater, blower, cooler, hi-temp-alarm, lo-temp-alarm} 순서다.
  - 위 방식이 그릇된 정보를 피하라는 규칙의 위반에 가깝지만 여기서는 적절해 보인다.

```java
// 9-5
// EnvironmentControllerTest.java (더 복잡한 선택)
@Test
public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
  tooHot();
  assertEquals("hBChl", hw.getState()); 
}

@Test
public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
  tooCold();
  assertEquals("HBchl", hw.getState()); 
}

@Test
public void turnOnHiTempAlarmAtThreshold() throws Exception {
  wayTooHot();
  assertEquals("hBCHl", hw.getState()); 
}

@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
  wayTooCold();
  assertEquals("HBchL", hw.getState()); 
}
```

- 의미만 안다면 눈길이 문자열을 따라 움직이며 결과를 재빨리 판단한다.

```java
// 9-6
// MockControlHardware.java
public String getState() {
  String state = "";
  state += heater ? "H" : "h"; 
  state += blower ? "B" : "b"; 
  state += cooler ? "C" : "c"; 
  state += hiTempAlarm ? "H" : "h"; 
  state += loTempAlarm ? "L" : "l"; 
  return state;
}
```

- StringBuffer는 보기에 흉하다.
- 실제 코드에서도 StringBuffer를 피한다.
- StringBuffer를 안써서 치르는 대가가 미미하다. (특별히 쓸 필요가 없다)

메모리나 CPU 효율과 관련있는 경우 실제 환경에서는 절대로 안 되지만 테스트 환경에서는 전혀 문제없는 방식이 있다.

## 테스트 당 assert 하나

- assert 문이 단 하나인 함수는 결론이 하나라서 코드를 이해하기 쉽고 빠르다.

```java
// 9-7
// SerializedPageResponderTest.java (단일 Assert)
public void testGetPageHierarchyAsXml() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

  whenRequestIsIssued("root", "type:pages");

  thenResponseShouldBeXML(); 
}

public void testGetPageHierarchyHasRightTags() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");

  whenRequestIsIssued("root", "type:pages");

  thenResponseShouldContain(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
  ); 
}
```

- 함수 이름을 바꿔 'given-when-then'이라는 관례를 사용했다는 사실에 주목한다. 그러면 테스트 코드 읽기가 쉬워진다.
- 하지만, 위에서 보듯 테스트를 분리하면 중복되는 코드가 많아진다.
- 아래와 같은 방법이 있지만 모두 배보다 배꼽이 더 크다. 결국 9-2처럼 assert 문을 여럿 사용하는 편이 좋다.
  - TEMPLATE METHOD 패턴을 사용하면 중복을 제거할 수 있다.
    - given/when 부분을 부모 클래스에 두고 then 부분을 자식 클래스에 두면 된다.
  - 아니면 완전히 독자적인 테스트 클래스를 만들어 @Before 함수에 given/when 부분을 넣고 @Test 함수에 then 부분을 넣어도 된다.

저자는 assert 문 개수는 최대한 줄여야 좋다고 생각한다.

### 테스트 당 개념 하나

어쩌면 "테스트 함수마다 한 개념만 테스트하라"는 규칙이 더 낫겠다.

이것저것 잡다한 개념을 연속으로 테스트하는 긴 함수는 피한다.

```java
// 9-8
// addMonths() 메서드를 테스트하는 장황한 코드
public void testAddMonths() {
  SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

  SerialDate d2 = SerialDate.addMonths(1, d1); 
  assertEquals(30, d2.getDayOfMonth()); 
  assertEquals(6, d2.getMonth()); 
  assertEquals(2004, d2.getYYYY());

  SerialDate d3 = SerialDate.addMonths(2, d1); 
  assertEquals(31, d3.getDayOfMonth()); 
  assertEquals(7, d3.getMonth()); 
  assertEquals(2004, d3.getYYYY());

  SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1)); 
  assertEquals(30, d4.getDayOfMonth());
  assertEquals(7, d4.getMonth());
  assertEquals(2004, d4.getYYYY());
}
```

- 위 코드는 바람직하지 못한 코드다.
- 독자적인 개념 세 개를 테스트하므로 독자적인 테스트 세 개로 쪼개야 마땅하다.
- 이를 한 함수에 몰아넣으면 독자가 각 절이 거기에 존재하는 이유와 각 절이 테스트하는 개념을 모두 이해해야한다.
- 셋으로 분리한 테스트 함수는 각각 다음 기능을 수행한다.
  - (5월처럼) 31일로 긑나는 달의 마지막 날짜가 주어지는 경우
    1. (6월처럼) 30일로 끝나는 한 달을 더하면 날자는 30일이 되어야지 31일이 되어서는 안 된다.
    2. 두 달을 더하면 그리고 두 번째 달이 31일로 끝나면 날짜는 31일이 되어야 한다.
  - (6월처럼) 30일로 끝나는 달의 마지막 날짜가 주어지는 경우
    1. 31일로 끝나는 한 달을 더하면 날짜는 30일이 되어야지 31일이 되면 안 된다.

이렇게 표현하면 장황한 테스트 코드 속에 감춰진 일반적인 규칙이 보인다.

- 2월 28일에 한 달을 더하면 3월 28일이 나와야하는데 이런 테스트 케이스가 빠졌으므로 채워 넣으면 좋다.



9-8은 assert 문이 여럿이라는 사실이 문제가 아니다.

한 테스트 함수에서 여러 개념을 테스트 한다는 점이 문제다.

그러므로 가장 좋은 규칙은 "개념 당 assert 문 수를 최소로 줄여라"와 "테스트 함수 하나는 개념 하나만 테스트하라"라 하겠다.

## F.I.R.S.T.

깨끗한 테스트는 다음 다섯 가지 규칙을 따른다.

### Fast 빠르게

- 테스트는 빨라야 한다. 빨리 돌아야 한다.
- 테스트가 느리면 자주 돌릴 엄두를 못 낸다.
- 자주 돌리지 않으면 초반에 문제를 찾아내 고치지 못한다.
- 코드를 마음껏 정리하지도 못하기 때문에, 결국 코드 품질이 망가지기 시작한다.

### Independent 독립적으로

- 각 테스트는 서로 의존하면 안 된다.
- 한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안 된다.
- 각 테스트는 독립적으로 그리고 어떤 순서로 실행해도 괜찮아야 한다.
- 서로에게 의존하면 하나가 실패할 때 나머지도 잇달아 실패하므로 원인을 진단하기 어려워지며 후반 테스트가 찾아내야 할 결함이 숨겨진다.

### Repeatable 반복가능하게

- 테스트는 어떤 환경에서도 반복 가능해야 한다.
- 실제 환경, QA 환경, 버스를 타고 집으로 가는 길에 사용하는 노트북 환경에서도 실행할 수 있어야 한다.
- 테스트가 돌아가지 않는 환경이 하나라도 있다면 테스트가 실패한 이유를 둘러댈 변명이 생긴다.
- 게다가 환경이 지원되지 않기에 테스트를 수행하지 못하는 상황에 직면한다.

### Self-Validating 자가검증하는

- 테스트는 부울(bool) 값으로 결과를 내야 한다. 성공 아니면 실패다.
- 통과 여부를 알려고 로그 파일을 읽게 만들어서는 안 된다.
- 통과 여부를 보려고 텍스트 파일 두 개를 수작업으로 비교하게 만들어서도 안 된다.
- 테스트가 스스로 성공과 실패를 가늠하지 않는다면 판단은 주관적이 되며 지루한 수작업 평가가 필요하게 된다.

### Timely 적시에

- 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다.
- 실제 코드를 구현한 다음에 테스트 코드를 만들면 실제 코드가 테스트하기 어렵다는 사실을 발견할지도 모른다.
- 어떤 실제 코드는 테스트하기 너무 어렵다고 판명날지 모른다.

- 테스트가 불가능하도록 실제 코드를 설계할지도 모른다.  

## 결론

- 테스트 코드는 실제 코드만큼이나 프로젝트 건강에 중요하다. 어쩌면 실제 코드보다 더 중요할 수 있다.
- 테스트 API를 구현해 도메인 특화 언어(Domain Specific Language)를 만들자. 그러면 그만큼 테스트 코드를 짜기가 쉬워진다.
- 테스트 코드가 방치되어 망가지면 실제 코드도 망가진다.
- **테스트 코드를 깨끗하게 유지하자.**

