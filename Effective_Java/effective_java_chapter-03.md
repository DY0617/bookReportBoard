# Effective Java 3장
# 모든 객체의 공통 메서드

Object는 객체를 만들 수 있는 구체 클래스지만, 기본적으로는 상속해서 사용하도록 설계됨.

Object에서 final이 아닌 메서드(equals, hashCode, toString, clone, finalize)는 모두 Overriding을 염두에 두고 설계된 것이라 재정의 시 지켜야 하는 일반 규약이 명확히 정의되어 있음.

그래서 Object를 상속하는 클래스, 즉 모든 클래스는 이 메서드들을 일반 규약에 맞게 재정의해야 함.

---
# 목차
1. [equals는 일반 규약을 지켜 재정의하라](#equals는-일반-규약을-지켜-재정의하라)
2. [equals를 재정의하려거든 hashCode도 재정의하라](#equals를-재정의하려거든-hashCode도-재정의하라)
3. [toString을 항상 재정의하라](#toString을-항상-재정의하라)
4. [clone 재정의는 주의해서 진행하라](#clone-재정의는-주의해서-진행하라)
5. [Comparable을 구현할지 고려하라](#Comparable을-구현할지-고려하라)

---

# equals는 일반 규약을 지켜 재정의하라

equals 메서드는 재정의하기 쉬워 보이지만 곳곳에 함정이 있어 까다로움.

다음 상황 중 하나에 해당한다면 재정의 하지 말걸
- 각 인스턴스가 본질적으로 고유하다.
    - 값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스가 해당됨.(ex) Thread)
- 인스턴스의 논리적 동치성(logical equality)을 검사할 일이 없다.
    - java.util.regex.Pattern은 equals를 재정의해서 두 Pattern의 인스턴스가 같은 정규표현식을 나타내는지를 검사하는, 논리적 동치성을 검사하는 방법도 있는데, 설꼐자는 클라이언트가 이 방식을 원하지 않거나 애초에 필요하지 않다고 판단할 수도 있다. 필요하지 않다고 판단했다면 Object의 기본 equals만으로도 해결됨.
- 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는다.
    - 대부분의 Set 구현체는 AbstractSet이 구현한 equals를 상속받아 쓰고, List 구현체들은 AbstractList로부터, Map 구현체들은 AbstractMap으로부터 상속받아 그대로 사용함.
- 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.
    - equals가 실수로라도 호출되는걸 막고 싶다면 위처럼 구현하면 됨.
```java
//호출 금지
@Override
public boolean equals(Object o){
  throw new AssertionError();
}
```

---

그렇다면 equals를 재정의해야 할 때는?

