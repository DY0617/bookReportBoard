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

