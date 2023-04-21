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

객체 식별성(object identity : 두 객체가 물리적으로 같은가)이 아니라 논리적 동치성을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되지 않았을 때.

주로 값 클래스들이 여기 해당됨

값 클래스란??

Integer나 String처럼 값을 표현하는 클래스

두 값 객체를 equals로 비교하는 프로그래머는 객체가 같은지가 아니라 값이 같은지를 알고 싶어하기 때문.

equals가 논리적 동치성을 확인하도록 재정의해두면, 값을 비교하기를 원하는 프로그래머의 기대에 부응하고, Map의 키와 Set의 원소로도 사용할 수 있게 됨.

---

값 클래스라 해도 값이 같은 인스턴스가 둘 이상 만들어지지 않음을 보장하는 인스턴스 통제 클래스라면 equals를 재정의하지 않아도 됨.

ex) Enum

---

equals 메서드를 재정의할 때는 반드시 일반 규약을 따라야 한다.

Object 명세에 적힌 규약

equals 메서드는 동치관계를 구현하며, 다음을 만족한다.
- 반사성(reflexivity): null이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true다.
- 대칭성(symmetry): null이 아닌 모든 참조 값 x,y에 대해, x.equals(y)가 true면 y.equals(x)도 true다.
- 추이성(transitivity): null이 아닌 모든 참조 값 x,y,z에 대해 x.equals(y)가 true이고 y.equals(z)도 true면 x.equals(z)도 true다.
- 일관성(consistency): null이 아닌 모든 참조 값 x,y에 대해 x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.
- null-아님: null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false이다.

동치관계란??

Equivalence Relation

집합을 서로 같은 원소들로 이뤄진 부분집합으로 나누는 연산. 이 부분집합을 동치류(Equlvalence class : 동치 클래스)라 함.

<br>

**반사성**

객체는 자기 자신과 같아야 한다.

이 요건을 어긴 클래스의 인스턴스를 컬렉션에 넣은 다음 contains 메서드를 호출하면 false를 반환할 것임.

**대칭성**

두 객체는 서로에 대한 동치 여부에 똑같이 답해야 함.

```java
//대칭성을 위배하는 잘못된 코드
public final class CaseInsensitiveString{
}
```
