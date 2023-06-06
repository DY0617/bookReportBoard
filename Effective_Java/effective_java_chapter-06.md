# Effective Java 6장
# 열거 타입과 애너테이션

자바에는 특수한 목적의 참조 타입이 두 가지 있음.

클래스의 일종인 열거 타입(enum)과, 인터페이스의 일종인 애너테이션

이번 장에서는 이 타입들을 올바르게 사용하는 방법을 알아봄.

---

# 목차

1. [int 상수 대신 열거 타입을 사용하라](#int-상수-대신-열거-타입을-사용하라)
2. [ordinal 메서드 대신 인스턴스 필드를 사용하라](#ordinal-메서드-대신-인스턴스-필드를-사용하라)
3. [비트 필드 대신 EnumSet을 사용하라](#비트-필드-대신-EnumSet을-사용하라)
4. [ordinal 인덱싱 대신 EnumMap을 사용하라](#ordinal-인덱싱-대신-EnumMap을-사용하라)
5. [확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라](#확장할-수-있는-열거-타입이-필요하면-인터페이스를-사용하라)
6. [명명 패턴보다 애너테이션을 사용하라](#명명-패턴보다-애너테이션을-사용하라)
7. [@Override 애너테이션을 일관되게 사용하라](#@Override-애너테이션을-일관되게-사용하라)
8. [정의하려는 것이 타입이라면 마커 인터페이스를 사용하라](#정의하려는-것이-타입이라면-마커-인터페이스를-사용하라)

---

# int 상수 대신 열거 타입을 사용하라

열거 타입은 일정 개수의 상수 값을 정의한 다음, 그 외의 값은 허용하지 않는 타입임.

```java
//정수 열거 패턴
//취약함
public static final int APPLE_FUJI          = 0;
public static final int APPLE_PIPPIN        = 1;
public static final int APPLE_GRANNY_SMITH  = 2;

public static final int ORANGE_NAVEL        = 0;
public static final int ORANGE_TEMPLE       = 1;
public static final int ORANGE_BLOOD        = 2;
```

정수 열거 패턴에는 단점이 많음.

1. 타입 안전을 보장할 방법이 없음.
2. 표현력이 좋지 않음.

오렌지를 건네야 할 메서드에 사과를 보내고 동등 연산자로 비교하더라도 컴파일러는 아무런 경고 메시지를 출력하지 않음.

<br>

정수 열거 패턴을 사용한 프로그램은 깨지기 쉬움.

평번한 상수를 나열한 것 뿐이라 컴파일하면 그 값이 클라이언트 파일에 그대로 새겨짐.

따라서 상수의 값이 바뀌면 클라이언트도 반드시 다시 컴파일해야 함.

다시 컴파일하지 않은 클라이언트는 실행이 되더라도 엉뚱하게 동작할 것임.

정수 상수는 그 값을 출력하거나 디버거로 살펴보면 단지 숫자로만 보여서 잘 알아보기도 힘듬.

---

이러한 단점들을 해결하기 위해 열거 타입(enum type)을 사용함.

```java
public enum Apple{FUJI,PIPPIN,GRANNY_SMITH}
public enum Orange{NAVEL,TEMPLE,BLOOD}
```

자바의 열거 타입은 완전한 형태의 클래스이기 때문에 단순한 정숫값일 뿐인 다른 언어의 열거 타입보다 훨씬 더 강력함.

열거 타입 자체는 클래스이며, 상수 하나당 자신의 인스턴스를 하나씩 만들어 public static final 필드로 공개함.

열거 타입은 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 final임.

따라서 열거 타입 선언으로 만들어진 인스턴스들은 딱 하나씩만 존재함이 보장됨.

열거 타입은 인스턴스 통제 된다는 뜻.

싱글턴은 원소가 하나뿐인 열거 타입이라 할 수 있고, 열거 타입은 싱글턴의 일반화 형태라 할 수 있음.

<br>

열거 타입은 컴파일타임 타입 안전성을 제공함.

위 코드의 Apple 열거 타입을 매개변수로 받는 메서드를 선언했다면, 건네받은 참조는 Apple의 세 가지 값 중 하나임이 확실함.

<br>

열거 타입에는 각자의 이름공간이 있어서 이름이 같은 상수도 평화롭게 공존함.

공개되는 것이 오직 필드의 이름뿐이기 때문에, 정수 열거 패턴과 달리 상수 값이 클라이언트로 컴파일되어 각인되지 않기 때문에 열거 타입에 새로운 상수를 추가하거나 순서를 바꿔도 다시 컴파일하지 않아도 됨.

열거 타입의 toString 메서드는 출력하기에 적합한 문자열을 내어줌.

<br>

열거 타입에는 임의의 메서드나 필드를 추가할 수 있고, 임의의 인터페이스를 구현하게 할 수도 있음.

ex) Object, Comparable, Serializable 등등..

---

```java
//데이터와 메서드를 갖는 열거 타입
//행성 정보
public enum Planet{
    MERCURY(3.302e+23,2.439e6),
    VENUS  (4.869e+24,6.052e6),
    EARTH  (5.975e+24,6.378e6),
    MARS   (6.419e+23,3.393e6),
    JUPITER(1,899e+27,7.149e7),
    SATURN (5.685e+26,6.027e7),
    URANUS (8.683e+25,2.556e7),
    NEPTUNE(1.024e+26,2.477e7);
    
    private final double mass;
    private final double radius;
    private final double surfaceGravity;
    
    private static final double G = 6.67300E-11;
    
    Planet(double mass, double radius){
        this.mass=mass;
        this.radius=radius;
        surfaceGravity=G*mass/(radius*radius);
    }
    
    public double mass()            {return mass;}
    public double radius()          {return radius;}
    public double surfaceGravity()  {return surfaceGravity;}
    
    public double surfaceWeight(double mass){
        return mass*surfaceGravity;
    }
}
```

