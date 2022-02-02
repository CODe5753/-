# Chapter 5. 형식 맞추기
* [형식을 맞추는 목적](#----------)
* [적절한 행 길이를 유지하라](#--------------)
+ [신문 기사처럼 작성하라](#------------)
+ [개념은 빈 행으로 분리하라](#--------------)
+ [세로 밀집도](#------)
+ [수직 거리](#-----)
    - [변수 선언](#-----)
    - [인스턴스 변수](#-------)
    - [종속 함수](#-----)
    - [개념적 유사성](#-------)
* [가로 형식 맞추기](#---------)
+ [가로 공백과 밀집도](#----------)
+ [들여쓰기](#----)
    - [들여쓰기 무시하기](#---------)
+ [가짜 범위](#-----)
* [팀 규칙(컨벤션)](#---------)
---
## 형식을 맞추는 목적

코드 형식은 융통성 없이 맹목적으로 따르면 안 된다.
오늘 구현한 코드의 가독성은 앞으로 바뀔 코드의 품질에 큰 영향을 미치기 때문이다.

## 적절한 행 길이를 유지하라

500줄을 넘지 않고 대부분 200줄 정도인 파일로도 커다란 시스템을 구축할 수 있다

### 신문 기사처럼 작성하라

이름은 간단하면서도 설명이 가능하게 짓자.

### 개념은 빈 행으로 분리하라

import 문, 각 함수 사이에 빈 행을 넣자. 가독성에 큰 차이가 있다.

### 세로 밀집도

서로 밀접한 코드 행은 세로로 가까이 놓여야 한다.

### 수직 거리

같은 파일에 속할 정도로 밀접한 두 개념은 **세로 거리**로 연관성을 표현한다.

두 개념이 멀리 떨어져 있으면 코드를 읽는 사람이 소스 파일과 클래스를 여기저기 뒤지게 된다.

#### 변수 선언

변수는 사용하는 위치에 최대한 가까이 선언한다.

```java
// 다소 긴 함수
private static void readPreferences() {
    InputStream is = null;
    try {
        is = new FileInputStream(getPreferencesFile());
        setPreferences(new Properties(getPreferences()));
        getPreferences().load(is);
    } catch (IOException e) {
        try {
            if (is != null)
                is.close();            
        } catch (IOException e1) {
            
        }
    }
}
```

```java
// 작고 귀여운 함수
public int countTestCases() {
    int count = 0;
    for (Test each : tests)
        count += each.countTestCases();
    return count;
}
```

#### 인스턴스 변수

Java의 경우 인스턴스 변수는 **클래스 맨 처음에 선언**한다. 변수 간에 세로로 거리를 두지 않는다.

C++에서는 모든 인스턴스 변수를 클래스 마지막에 선언하는 `가위 규칙(Scissors rule)`을 적용한다.

#### 종속 함수

한 함수가 다른 함수를 호출한다면 두 함수는 세로로 가까이 배치한다.

가능하다면 **호출하는 함수를 호출되는 함수보다 먼저 배치**한다.

```java
private static int combination(int n, int r){
    int factN = fibonacci(n); // call fibonacci function
    int factR = fibonacci(r);
    int factNR = fibonacci(n-r);
    ... 
}

private static int fibonacci(int n) {
    ...
    return n * fibonacci(n-1);
}
```

#### 개념적 유사성

비슷한 동작을 수행하는 함수가 대표적인 예다.

```java
public class Assert {
    static public void assertTrue(String message, boolean condition) {
        if(!condition)
            fail(message);
    }
    
    static public void assertTrue(boolean condition) {
        assertTrue(null, condition);
    }
    
    static public void assertFalse(String message, boolean condition) {
        assertTrue(message, !condition);
    }
    
    static public void assertFalse(boolean condition) {
        assertFalse(null, condition);
    }
}
```

위 함수들은 다음과 같은 이유로 개념적 친화도가 매우 높다.

- 명명법 동일
- 기본 기능이 유사

## 가로 형식 맞추기

최대 120자 정도가 적당하다.

### 가로 공백과 밀집도

```java
private void measureLine(String line) {
    lineCount++;
    int lineSize = line.length();
    totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
}
```

할당 연산자를 강조하려고 **앞 뒤에 공백**을 줬다. 할당문은 왼쪽 요소와 오른쪽 요소가 분명히 나뉜다.

반면, **함수 이름과 이어지는 괄호 사이에는 공백을 넣지 않았다**.

공백을 넣으면 한 개념이 아니라 별개로 보이기 때문이다.

```java
...
return (-b - Math.sqrt(determinant)) / (2*a);
```

항 사이에 공백이 들어가면 가독성이 올라간다.

### 들여쓰기

#### 들여쓰기 무시하기

```java
public String render() throws Exception {return ""; }
```

가급적 지양하는 게 좋을 것 같다.

### 가짜 범위

```java
while (dis.read(buf, 0, readBufferSize) != -1)
;
```

세미콜론은 새 행에다 제대로 들여써서 넣어준다. 그렇지 않으면 눈에 잘 띄지 않는다.

## 팀 규칙(컨벤션)

한 소스 파일에서 봤던 형식이 다른 소스 파일에도 쓰이리라는 신뢰감을 독자에게 줘야 한다.