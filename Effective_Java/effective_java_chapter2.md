# Effective Java 2장
# 생성자 대신 정적 팩터리 메서드를 고려하라

**정적 팩터리 메서드**

클래스의 인스턴스를 반환하는 정적 메서드.<br>
클라이언트가 클래스의 인스턴스를 얻는 전통적인 방법은 public 생성자이지만, 생성자와는 별도로 정적 팩터리 메서드를 제공할 수 있음.

```java
public static Boolean valueOf(boolean b){
  return b ? Boolean.TRUE : Boolean.FALSE;
}
```
기본 타입인 boolean 값을 받아 Boolean 객체 참조로 변환해주는 메서드.


---

정적 팩터리 메서드가 생성자보다 좋은 점

1. 이름을 가질 수 있다.
    - 이름을 잘 지음으로서 반환될 객체의 특성을 제대로 설명할 수 있음.
    - 한 클래스에 시그니처가 같은 생성자가 여러 개 필요할 것 같으면, 생성자를 정적 팩터리 메서드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어주면 좋다.

2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.
    - 분변 클래스는 인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 불필요한 객체 생성을 피할 수 있음.
        - Boolean.valueOf(boolean) 메서드는 객체를 아예 생성하지 않음.
        - 플라이웨이트 패턴도 이와 비슷함.
    - 반복되는 요청에 같은 객체를 반환하는 식으로, 언제 어느 인스턴스를 살아 있게 할지를 철저히 통제할 수 있음.
        - 이를 인스턴스 통제(instance_controlled) 클래스라 함.
        - 인스턴스를 통제하면 클래스를 싱글턴, 인스턴스화 불가로 만들 수 있음.
        - 인스턴스를 통제하면 동치인 인스턴스가 단 하나뿐임을 보장할 수 있음.
            - a == b 일때만 a.equals(b) 가 성립한다.
        - 인스턴스 통제는 플라이웨이트 패턴의 근간이 되며, 열거 타입은 인스턴스가 하나만 만들어짐을 보장함.

4. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
    - 반환할 객체의 클래스를 자유롭게 선택할 수 있게 함. 엄청난 유연성
    - pi를 만들 때 이를 응용하면 구현 클래스를 공개하지 않고도 객체를 반환할 수 있어 api를 작게 유지할 수 있다.

6. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
    - 반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관없음.

8. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.
    - 서비스 제공자 프레임워크를 만드는 근간이 됨.
        - JDBC
    - 서비스 제공자 프레임워크의 핵심 컴포넌트 3가지
        - 서비스 인터페이스
            - 구현체의 동작 정의
        - 제공자 등록 API
            - 제공자가 구현체를 등록할 때 사용
        - 서비스 접근 API
            - 클라이언트가 서비스의 인스턴스를 얻을 때 사용
        - 서비스 제공자 인터페이스 라는 네 번째 컴포넌트가 쓰이기도 함.
            - 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체를 설명해줌


---

정적 팩터리 메서드의 단점

1. 상속을 하려면 public이나 protected 생성자가 필요하다.
    - 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없음.
    - 상속보다 컴포지션을 사용하도록 유도하고 불변 타입으로 만들려면 이 제약을 지켜야 함.

3. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.
    - 사용자는 정적 팩터리 메서드 방식 클래스를 인스턴스화할 방법을 알아내야 함.


# 생성자에 매개변수가 많다면 빌더를 고려하라

점층적 생성자 패턴
```java
public class NutritionFacts{
  private final int servingSize;
  private final int servings;
  private final int calories;
  private final int fat;
  private final int sodium;
  private final int carbohydrate;
  
  public NutritionFacts(int servingSize, int servings){
    this(servingSize,servings,0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories){
    this(servingSize,servings,calories,0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories, int fat){
    this(servingSize,servings,calories,fat,0);
  }
  
}
```
