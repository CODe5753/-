- [Chapter 6. 객체와 자료 구조](#chapter-6-객체와-자료-구조)
  - [자료 추상화](#자료-추상화)
      - [Question. 그래서 왜 추상적 개념이 좋다는걸까?](#question-그래서-왜-추상적-개념이-좋다는걸까)
  - [자료/객체 비대칭](#자료객체-비대칭)
  - [디미터 법칙](#디미터-법칙)
    - [기차 충돌](#기차-충돌)
    - [잡종 구조](#잡종-구조)
    - [구조체 감추기](#구조체-감추기)
  - [자료 전달 객체](#자료-전달-객체)
    - [결론](#결론)
---
# Chapter 6. 객체와 자료 구조

변수를 비공개(private)로 정의하는 것은 남들이 변수에 의존하지 않게 만들고 싶어서다.

그렇다면 get, set 메서드를 공개(public)해 왜 비공개 변수를 외부에 노출할까?

## 자료 추상화

```java
// 구체적인 Point class
public class Point {
    public double x;
    public double y;
}

// [Best] 추상적인 Point 클래스
public interface Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
    double getR();
    double getTheta();
    void setPolar(double r, double theta);
}
```



```java
// 구체적인 Vehicle 클래스
public interface Vehicle {
    double getFuelTankCapacityInGallons();
    double getGallonsOfGasoline();        
}

// [Best] 추상적인 Vehicle 클래스
public interface Vehicle {
    double getPercentFuelRemaining();
}
```



자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다.

#### Question. 그래서 왜 추상적 개념이 좋다는걸까?

## 자료/객체 비대칭

```java
// 절차적인 도형
// 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉬움
// 새로운 자료 구조를 추가하기 어려움
public class Square {
    public Point topLeft;
    public double side;
}

public class Rectangle {
    public Point topLeft;
    public double height;
    public double width;
}

public class Circle {
    public Point center;
    public double radius;
}

public class Geometry {
    public final double PI = 3.141592653589793;
    
    public double area(Object shape) throws NoSuchShapeException {
        if (shape instanceof Square) {
            Square s = (Square) shape;
            return s.side * s.side;
        }
        else if (shape instanceof Rectangle) {
            Rectangle r = (Rectangle) shape;
            return r.height * r.width;
        }
        else if (shape instanceof Circle) {
            Circle c = (Circle) shape;
            return PI * c.radius * c.radius;
        }
        throw new NoSuchShapeException();
    }
}
```

```java
// 다형적인 도형
// 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉬움
// 새로운 함수를 추가하기 어려움. 그러려면 모든 클래스를 고쳐야 함
public class Square implements Shape {
    private Point topleft;
    private double side;
    
    public double area() {
        return side * side;
    }
}

public class Rectangle implements Shape {
    private Point topleft;
    private double height;
    private double width;
    
    public double area() {
        return height * width
	}
}

public class Circle implements Shape {
    private Point center;
    private double radius;
    public final double PI = 3.141592653589793;
    
    public double area() {
        return PI * radius * radius;
    }
}
```

때로는 **단순한 자료 구조와 절차적인 코드가 가장 적합한 상황**도 있다.

## 디미터 법칙

> 잘 알려진 휴리스틱(heuristic)으로, 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙

즉, 객체는 조회 함수로 내부 구조를 공개하면 안된다는 의미다.

### 기차 충돌

```java
final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

위 코드는 아래와 같이 변경하는 것이 좋다.

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

위 두 코드는 디미터 법칙에 대해 고민하게 된다.

반면, 아래 코드처럼 구현했다면 디미터 법칙을 거론할 필요가 없어진다.

```java
final String outputDir = ctxt.options.scratchDir.absolutePath;
```

### 잡종 구조

이러한 혼란으로 절반은 객체, 절반은 자료 구조인 잡종 구조가 나온다.

이런 잡종 구조는 새로운 함수는 물론이고 새로운 자료 구조도 추가하기 어렵다. 그러므로 되로록 피하자.

### 구조체 감추기

만약 ctxt, options, scratchDir이 객체라면? 앞 코드처럼 줄줄이 엮어서는 안 된다.

객체라면 내부 구조를 감춰야 하기 때문이다.

그렇다면 임시 디렉터리의 절대 경로는 어떻게 얻어야 좋을까?

```java
// case 1
// ctxt 객체에 공개해야 하는 메서드가 너무 많아진다
ctxt.getAbsolutePathOfScratchDirectoryOption(); 

// case 2
// getScratchDirectoryOption()이 객체가 아니라 자료구조를 반환한다고 가정한다.
ctx.getScratchDirectoryOption().getAbsolutePath; 
```

결국 위 코드의 목적은 절대 경로를 얻어 어딘가에 쓰기 위해서다.

바로 임시 파일을 생성하기 위해서이다.

```java
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```

ctxt 객체에 임시 파일을 생성하라고 시키는 것은 어떨까? 객체에 맡기기에 적당해 보인다.

## 자료 전달 객체

자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다. 이를 DTO(Data Transfer Object)라 한다.

좀 더 일반적인 형태는 빈(bean) 구조다. 빈은 비공개(private) 변수를 조회/설정 함수로 조작한다. 하지만 이는, 별다른 이익을 제공하지 않는다.

```java
public class Address {
    private String street;
    ...
        
    public Address(String street) {
        this.street = street;
    }
    
    public String getStreet() {
        return street;
    }
}
```

### 결론

어떤 시스템을 구현할 때, 새로운 자료 타입을 추가하는 유연성이 필요하다면 객체를

새로운 동작을 추가하는 유연성이 필요하면 자료 구조와 절차적인 코드가 더 적합하다.

최적의 해결책을 찾자.

