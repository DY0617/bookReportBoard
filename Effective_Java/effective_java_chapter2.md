# Effective Java 2장
# 목차
1. [생성자 대신 정적 팩터리 메서드를 고려하라](#생성자-대신-정적-팩터리-메서드를-고려하라)
2. [생성자에 매개변수가 많다면 빌더를 고려하라](#생성자에-매개변수가-많다면-빌더를-고려하라)
3. [private 생성자나 열거 타입으로 싱글턴임을 보증하라](#private-생성자나-열거-타입으로-싱글턴임을-보증하라)
4. [인스턴스화를 막으려거든 private 생성자를 사용하라](#인스턴스화를-막으려거든-private-생성자를-사용하라)
5. [자원을 직접 명시하지 말고 의존 객체 주입을 사용하라](#자원을-직접-명시하지-말고-의존-객체-주입을-사용하라)
6. [불필요한 객체 생성을 피하라](#불필요한-객체-생성을-피하라)
7. [다 쓴 객체 참조를 해제하라](#다-쓴-객체-참조를-해제하라)
8. [finalizer와 cleaner 사용을 피하라](#finalizer와-cleaner-사용을-피하라)
9. [try-finally보다는 try-with-resources를 사용하라](#try-finally보다는-try-with-resources를-사용하라)
---
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

---

# 생성자에 매개변수가 많다면 빌더를 고려하라

**점층적 생성자 패턴**
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
  
  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium){
    this(servingSize,servings,calories,fat,sodium,0);
  }
  
  public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate){
    this.servingSize=servingSize;
    this.servings=servings;
    this.calories=calories;
    this.fat=fat;
    this.sodium=sodium;
    this.carbohydrate=carbohydrate;
  }
  
}
```

점층적 생성자 패턴에서 인스턴스를 만드려면 원하는 매개변수를 모두 포함한 생성자 중 가장 짧은 것을 골라 호출하면 된다.

보통 이런 생성자는 사용자가 설정하길 원치 않는 매개변수까지 포함하기 쉬움.

매개변수가 많아질 수록 걷잡을 수 없게됨. ( 점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기 어려움. )

---

**자바빈즈 패턴(JavaBeans pattern)**

매개변수가 없는 생성자로 객체를 만든 후, 세터 메서드들을 호출해 원하는 매개변수의 값을 설정하는 방식.

```java
public class NutritionFacts{
  private int servingSize=-1;
  private int servings=-1;
  private int calories=0;
  private int fat=0;
  private int sodium=0;
  private int carbohydrate=0;
  
  public NutritionFacts(){}
  
  public void setServingSize(int val){ servingSize=val; }
  public void setServings(int val){ servings=val; }
  public void setCalories(int val){ calories=val; }
  public void setFat(int val){ fat=val; }
  public void setSodium(int val){ sodium=val; }
  public void setCarbohydrate(int val){ carbohydrate=val; }
}
```

점층적 생성자 패턴의 단점들이 보완됨.

**BUT**

자바빈즈 패턴에서는 객체 하나를 만들려면 메서드 여러 개를 호출해야 하고, 객체가 완전히 생성되기 전까지는 일관성이 무너진 상태에 놓이게 된다.

일관성이 무너지기 때문에 자바빈즈 패턴에서는 클래스를 불변( 챕터 4 참조 )으로 만들 수 없음.

스레드 안정성을 얻으려면 프로그래머가 추가 작업을 해줘야 함.

(단점을 완화하기 위해 생성이 끝난 객체를 수동으로 얼리고 얼리기 전에는 사용할 수 없도록 하기도 함. 허나 다루기 어려워서 실전에서 자주 쓰이지 않음.)


---

**빌더 패턴(Builder pattern)**

클라이언트는 필요한 객체를 직접 만드는 대신 필수 매개변수만으로 생성자를 호출해 빌더 객체를 얻음.

그런 다음 빌더 객체가 제공하는 일종의 세터 메서드들로 원하는 선택 매개변수들을 설정함.

마지막으로 매개변수가 없는 build 메서드를 호출해 우리에게 필요한 객체를 얻는다.

```java
public class NutritionFacts{
  private final int servingSize;
  private final int servings;
  private final int calories;
  private final int fat;
  private final int sodium;
  private final int carbohydrate;
  
  public static class Builder{
    private final int servingSize;
    private final int servings;
    
    private int calories=0;
    private int fat=0;
    private int sodium=0;
    private int carbohydrate=0;
    
    public Builder(int servingSize, int servings){
      this.servingSize=servingSize;
      this.servings=servings;
    }
    
    public Builder calories(int val){
      calories=val;
      return this;
    }
    
    public Builder fat(int val){
      fat=val;
      return this;
    }
    
    public Builder sodium(int val){
      sodium=val;
      return this;
    }
    
    public Builder carbohydrate(int val){
      carbohydrate=val;
      return this;
    }
    
    public NutritionFacts build(){
      return new NutritionFacts(this);
    }    
  }
  
  private NutritionFacts(Builder builder){
    servingSize=builder.servingSize;
    servings=builder.servings;
    calories=builder.calories;
    fat=builder.fat;
    sodium=builder.sodium;
    carbohydrate=builder.carbohydrate;
  }
}
```

빌더의 세터 메서드들은 빌더 자신을 반환하기 때문에 연쇄적으로 호출이 가능함.
- 메서드 호출이 흐르듯 연결된다는 뜻으로 플루언트 API ( fluent API ) 혹은 메서드 연쇄( method chaining )라 함.

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240,8).calories(100).sodium(35).carbohydrate(27).build();
```

빌더 패턴은 파이썬과 스칼라에 있는 명명된 선택적 매개변수를 흉내 낸 것.

잘못된 매개변수를 최대한 일찍 발견하려면 빌더의 생성자와 메서드에서 입력 매개변수를 검사하고, build 메서드가 호출하는 생성자에서 여러 매개변수에 걸친 불변식을 검사.

공격에 대비해 이런 불변식을 보장하려면 빌더로부터 매개변수를 복사한 후 해당 객체 필드들도 검사해야 함.

검사해서 잘못된 점을 발견하면 어떤 매개변수가 잘못되었는지를 자세히 알려주는 메시지를 담아 IllegalArgumentException을 던지면 된다.

---

**불변**

불변( immutable, immutability )은 어떠한 변경도 허용하지 않는다는 뜻.

가변 객체와 구분하는 용도로 쓰임.

ex) String 객체

---

**불변식**

불변식( invariant )은 프로그램이 실행되는 동안, 혹은 정해진 기간 동안 반드시 만족해야 하는 조건.

변경을 허용할 수는 있으나 주어진 조건 내에서만 허용함.

ex) 리시트의 크기는 반드시 0 이상이어야 하니, 한순간이라도 음수 값이 된다면 불변식이 깨진 것.
    
ex) Period 클래스에서 start 필드의 값은 반드시 end 필드의 값보다 앞서야 하므로, 두 값아 역전되면 불변식이 깨진 것.

가변 객체에서도 불변식은 존재할 수 있으며, 넓게 보면 불변은 불변식의 극단적인 예라 할 수 있음.

---

빌더 패턴은 계층적으로 설계된 클래스와 함께 쓰기에 좋다.

```java
public abstract class Pizza{
  public enum Topping{HAM,MUSHROOM,ONION,PEPPER,SAUSAGE}
  final Set<Topping> toppings;
  
  abstract static class Builder<T extends Builder<T>>{
    EnumSet<Topping> toppings=EnumSet.noneOf(Topping.class);
    public T addTopping(Topping topping){
      toppings.add(Objects.requireNonNuill(topping));
      return self();
    }
    
    abstract Pizza build();
    
    // 하위 클래스는 이 메서드를 재정의하여 this를 반환하도록 해야함.
    protected abstract T self();
  }
  
  Pizza(Builder<?> builder){
    toppings=builder.toppings.clone();
  }
}
```

Pizza.Builder 클래스는 재귀적 타입 한정( 챕터 5 참고 )을 이용하는 제네릭 타입.

추상 메서드인 self를 더해 하위 클래스에서는 형변환하지 않고도 메서드 연쇄를 지원할 수 있음.

self타입이 없는 자바를 위한 이 우회 방법을 시뮬레이트한 셀프 타입( simulated self-type ) 관용구라 함.

아래 Pizza의 하위 클래스 2개가 있음.

```java
public class NyPizza extends Pizza{
  public enum Size{SMALL,MEDIUM,LARGE}
  private final Size size;

  public static class Builder extends Pizza.Builder<Builder>{
    private final Size size;
  
    public Builder(Size size){
      this.size=Objects.requireNonNull(size);
    }
  
    @Override
    public NyPizza build(){
      return new NyPizza(this);
    }
  
    @Override
    protected Builder self(){
      return this;
    }
  }
  
  private NyPizza(Builder builder){
    super(builder);
    size=builder.size;
  }
}
```

```java
public class Calzone extends Pizza{  
  private final boolean sauceInside;

  public static class Builder extends Pizza.Builder<Builder>{
    private boolean sauceInside=false;
  
    public Builder sauceInside(){
      sauceInside=true;
      return this;
    }
  
    @Override
    public Calzone build(){
      return new Calzone(this);
    }
  
    @Override
    protected Builder self(){
      return this;
    }
  }
  
  private Calzone(Builder builder){
    super(builder);
    sauceInside=builder.sauceInside;
  }
}
```

공변 반환 타이핑 ( covariant return typing )

하위 클래스 메서드가 상위 클래스의 메서드가 정의한 반환 타입이 아닌, 그 하위 타입을 반환하는 기능

각 하위 클래스의 빌더가 정의한 build 메서드는 해당하는 구체 하위 클래스를 반환하도록 선언.

```java
NyPizza pizza=new NyPizza.Builder(SMALL).addTopping(SAUSAGE).addTopping(ONION).build();
Calzone calzone=new Calzone.Builder().addTopping(HAM).sauceInside().build();
```

빌더를 이용하면 가변인수 매개변수를 여러 개 사용할 수 있음 (생성자는 불가능)

메서드를 여러 번 호출하도록 하고 각 호출 때 넘겨진 매개변수들을 하나의 필드로 모을 수도 있다.
- 위의 addTopping 메서드가 이에 해당함.

---

빌더 패턴은 상당히 유연함
- 빌더 하나로 여러 객체를 순회하면서 만들 수도 있음.
- 빌더에 넘기는 매개변수에 따라 다른 객체를 만들 수도 있음.
- 객체마다 부여되는 일련번호와 같은 특정 필드는 빌더가 알아서 채우도록 할 수도 있음.

---

빌더 패턴의 단점
- 객체를 만들려면 그에 앞서 빌더부터 만들어야 함.(생성 비용이 크지는 않지만, 성능에 민감한 상황에서는 문제가 될 수 있음.)
- 점층적 생성자 패턴보다는 코드가 장황해서 매개변수가 4개 이상은 되어야 값어치를 함.
    - API는 시간이 지날수록 매개변수가 많아지는 경향이 있음을 명심.

---

생성자나 정적 팩터리 메서드 방식으로 시작했다가 나중에 빌더 패턴으로 전환할 수도 있지만, 이전에 만들어둔 방식이 도드라져 보일 것임.

애초에 빌더로 시작하는 편이 나을 때가 많음.

---

# private 생성자나 열거 타입으로 싱글턴임을 보증하라

**싱글턴**

인스턴스를 오직 하나만 생성할 수 있는 클래스

ex) 함수와 같은 무상태 객체, 설계상 유일해야 하는 시스템 컴포넌트

클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있음.

---

싱글턴을 만드는 방식

기본적으로 생성자는 private로 감춰두고, 유일한 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 하나 마련해 둠.

- public static 멤버가 final 필드인 방식

```java
public class Elvis{
  public static final Elvis INSTANCE=new Elvis();
  private Elvis(){ ... }
  public void leaveTheBuilding(){ ... }
}
```

private 생성자는 Elvis.INSTANCE를 초기화할 때 한번만 호출됨.

public, protected 생성자가 없으므로 Elvis 클래스가 초기화될 때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장됨.

해당 클래스가 싱글턴임이 API에 명백히 드러난다는 장점이 있음.

간결한 코드 작성 가능.

---

- 정적 팩터리 메서드를 public static 멤버로 제공하는 방식

```java
public class Elvis{
  private static final Elvis INSTANCE= new Elvis();
  private Elvis(){ ... }
  public static Elvis getInstance(){return INSTANCE;}
  
  public void leaveTheBuilding(){ ... }
}
```

Elvis.getInstance가 항상 같은 객체의 참조를 반환하므로, 인스턴스가 전체 시스템에서 하나뿐임이 보장됨.

API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있음.
- 유일한 인스턴스를 반환하던 팩터리 메서드가 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수 있음.

원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있음.

정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있음.

    Elvis::getInstance 를 Supplier<Elvis>로 사용.

---

위 두 방법 중 하나의 방법으로 만든 싱글턴 클래스를 직렬화하려면 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족하고, 모든 인스턴스 필드를 일시적(transient)이라고 선언하고 readResolve 메서드를 제공해야 함.
- 이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어짐.

```java
public class Elvis{
  private static final Elvis INSTANCE= new Elvis();
  private Elvis(){ ... }
  public static Elvis getInstance(){return INSTANCE;}
  
  public void leaveTheBuilding(){ ... }
  
  //위 코드에서 가짜 Elvis가 탄생함.
  //가짜 Elvis 탄생을 예방하고 싶다면 Elvis 클래스에 다음의 readResolve 메서드를 추가.
  
  private Object readResolve(){
    //진짜 Elvis를 반환하고 가짜 Elvis는 가비지 컬렉터에 맡긴다.
    return INSTANCE;
  }
}
```

---

- 원소가 하나인 열거 타입을 선언하는 방식

```java
public enum Elvis{
  INSTANCE;
  
  public void leaveTheBuilding(){ ... }
}
```

public 필드 방식과 비슷하지만, 더 간결하고 추가 노력 없이 직렬화할 수 있고, 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제 2의 인스턴스가 생기는 일을 완벽하게 막아줌.

**대부분의 상황에서 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법**

But

만들려는 싱글턴이 Enum 외의 클래스를 상속해야 하낟면 이 방법은 사용할 수 없음.
- 열거 타입이 다른 인터페이스를 구현하도록 선언 할 수는 있다.

---

# 인스턴스화를 막으려거든 private 생성자를 사용하라

정적 메서드와 정적 필드만을 담은 클래스도 나름 쓰임새가 있음.
- java.lang.Math, java.util.Arrays 처럼 기본 타입 값이나 배열 관련 메서드들을 모아놓을 수 있음.
- java.util.Collections처럼 특정 인터페이스를 구현하는 객체를 생성해주는 정적 메서드(혹은 팩터리)를 모아놓을 수 있음.
- final 클래스와 관련한 메서드들을 모아놓을 수 있음.

정적 멤버만 담은 유틸리티 클래스는 인스턴스로 만들어 쓰려고 설계한 게 아님.

하지만 생성자를 명시하지 않으면 컴파일러가 자동으로 기본 생성자를 만들어줌.

매개변수를 받지 않는 public 생성자가 만들어지며, 사용자는 이 생성자가 자동 생성된 것인지 구분할 수 없다는 뜻.

---

추상 클래스로 만드는 것으로는 인스턴스화를 막을 수 없음.

그렇다면 막는 방법은?

private 생성자를 추가하는 것.
- 컴파일러가 기본 생성자를 만드는 경우는 오직 명시된 생성자가 없을 때 뿐이기 때문.

```java
public class UtilityClass{
  // 기본 생성자가 만들어지는 것을 막는다(인스턴스화 방지용)
  private UtilityClass(){
    throw new AssertionError();
  }
  ...
}
```

명시적 생성자가 private기 때문에 클래스 바깥에서는 접근 불가.

에러를 던지면 클래스 안에서 실수로라도 생성자를 호출하지 않도록 해줌.(꼭 에러를 던질 필요는 없음)

생성자가 분명 존재하는데 호출할 수는 없기 때문에, 직관적이지 않음. 주석이 필요함.

private 생성자 추가는 상속을 불가능하게 하는 효과도 있음.

---

# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

맞춤법 검사기는 사전에 의존하는데, 이런 클래스를 정적 유틸리티 클래스나 싱글턴으로 구현한 모습을 드물지 않게 볼 수 있음.

```java
//정적 유틸리티를 잘못 사용한 예 - 유연하지 않고 테스트하기 어려움.
public class SpellChecker{
  private static final Lexicon directionary=...;
  
  private SpellChecker(){}
  
  public static boolean isValid(String word){ ... }
  public static List<String> suggestions(String typo){ ... }
}
```

```java
//싱글턴 잘못 사용한 예 - 유연하지 않고 테스트하기 어려움.
public class SpellChecker{
  private final Lexicon directionary=...;
  
  private SpellChecker(...){}
  public static SpellChecker INSTANCE= new SpellChecker(...);
  
  public static boolean isValid(String word){ ... }
  public static List<String> suggestions(String typo){ ... }
}
```

사전 하나로 실전을 모두 대응하기 어려움.

Spellchecker가 여러 사전을 사용할 수 있도록 만들기 위해서는?

dictionalry 필드에서 final 한정자를 제거하고, 다른 사전으로 교체하는 메서드를 추가한다면?

이 방식은 어색하고, 오류를 내기 쉬우며, 멀티스레드 환경에서는 쓸 수 없음.

사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.

---

클래스(Spellchecker)가 여러 자원 인스턴스를 지원해야 하며, 클라이언트가 원하는 자원(dictionary)을 사용해야 함.

**인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주는 방식이 이를 만족함**
- 의존 객체 주입의 한 형태로, 맞춤법 검사기를 생성할 때 의존 객체인 사전을 주입해주면 됨.

```java
//의존 객체 주입은 유연성과 테스트 용이성을 높여준다
public class SpellChecker{
  private final Lexicon dictionary;
  
  public SpellChecker(Lexion dictionary){
    this.dictionary=Objects.requrieNonNull(dictionary);
  }
  
  public boolean isValid(String word){ ... }
  public List<String> suggestions(String typo){ ... }
}
```

의존 객체 주입은 생성자, 정적 팩터리, 빌더 모두에 똑같이 응용이 가능함.

---

의존 객체 주입 패턴의 변형으로, 생성자에게 자원 팩터리를 넘겨주는 방식이 있음.

팩터리란?

호출할 때마다 특정 타입의 인스턴스를 반복해서 만들어주는 객체

ex) Supplier&#60;T&#62;
  
  Supplier&#60;T&#62;를 입력으로 받는 메서드는 일반적으로 한정적 와일드카드 타입을 사용해 팩터리의 타입 매개변수를 제한해야 함.
  
  이 방식을 이용해 클라이언트는 자신이 명시한 타입의 하위 타입이라면 무엇이든 생성할 수 있는 팩터리를 넘길 수 있음.

    Mosaic create(Supplier<? extends Tile> tileFactory){ ... }
    
의존 객체 주입이 유연성과 테스트 용이성을 개선해주긴 하지만, 의존성이 수천 개나 되는 큰 프로젝트에서는 코드를 어지럽게 만들기도 함.
- 대거, 주스, 스프링 같은 의존 객체 주입 프레임워크를 사용하면 이런 어질러짐을 해소할 수 있음.

---

핵심 정리

클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면 싱글턴과 정적 유틸리티 클래스는 사용하지 않는 것이 좋다. 이 자원들을 클래스가 직접 만들게 해서도 안된다. 대신 필요한 자원을(혹은 그 자원을 만들어주는 팩터리를) 생성자에(혹은 정적 팩터리나 빌더에) 넘겨주자. 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 기막히게 개선해준다.

---

# 불필요한 객체 생성을 피하라

똑같은 기능의 객체를 매번 생성하기보다는 객체 하나를 재사용하는 편이 나을 때가 많음.

    String s = new String("bikini");

이 문장은 실행될 때마다 String 인스턴스를 새로 만듬. 생성자에 넘겨진 "bikini" 자체가 이 생성자로 만들어내려는 String과 기능적으로 완전히 똑같기 때문에, 이 문장이 반복문이나 빈번히 호출되는 메서드 안에 있다면 쓸데없는 String 인스턴스가 수백만 개 만들어질 수도 있음.

    String s = "bikini";
    
이 코드는 새로운 인스턴스를 매번 만드는 대신 하나의 String 메서드를 사용함.

이 방식을 사용한다면 같은 가상 머신 안에서 이와 똑같은 문자열 리터럴을 사용하는 모든 코드가 같은 객체를 재사용함이 보장됨.

---

생성자 대신 정적 팩터리 매서드를 제공하는 불변 클래스에서는, 정적 팩터리 메서드를 사용해 불필요한 객체 생성을 피할 수 있음.

Boolean(String) 생성자 대신 Boolean.valueOf(String) 팩터리 메서드를 사용하는 것이 좋음.
- 생성자는 호출할 때마다 새로운 객체를 만들지만, 팩터리 메서드는 그렇지 않기 때문.

가변 객체라 해도 사용 중에 변경되지 않을 것임을 안다면 재사용이 가능함.

---

생성 비용이 아주 비싼 객체도 있음. 이런 비싼 객체가 반복해서 필요하다면 캐싱해서 재사용하는 것이 좋음.

```java
//정규표현식을 이용한 주어진 문자열이 유효한 로마 숫자인지 확인하는 메서드
static boolean isRomanNumeral(String s){
  return s.matches("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```

이 방식의 문제점은 String.matches 메서드를 사용한다는 데 있음.

String.matches는 정규표현식으로 문자열 형태를 확인하는 가장 쉬운 방법이지만, 성능이 중요한 상황에서 반복해 사용하기엔 적합하지 않음.

이 메서드가 내부에서 만드는 정규표현식용 Pattern 인스턴스는, 한 번 쓰고 버려져서 곧바로 가비지 컬렉션 대상이 되기 때문.

Pattern은 입력받은 정규표현식에 해당하는 유한 상태 머신을 만들기 때문에 인스턴스 생성 비용이 높음.

성능을 개선하려면???

필요한 정규표현식을 표현하는 (불변인) Pattern 인스턴스를 클래스 초기화(정적 초기화) 과정에서 직접 생성해 캐싱해두고, 나중에 isRomanNumeral 메서드가 호출될 때마다 이 인스턴스를 재사용한다.

```java
//값비싼 객체를 재사용해 성능 개선하기
public class RomanNumerals{
  private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})" + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
  
  static boolean isRomanNumeral(String s){
    return ROMAN.mater(s).matcher();
  }
}
```

---

객체가 불변이라면 재사용해도 안전하지만, 덜 명확하거나 직관에 반대되는 상황도 있음.

**어댑터**

어댑터(뷰 라고도 함)
- 클래스의 인터페이스를 사용자가 기대하는 인터페이스 형태로 적응(변환) 시킴. 서로 일치하지 않는 인터페이스를 갖는 클래스들을 함께 동작시킴.

어댑터는 실제 작업은 뒷단 객체에 위임하고, 자신은 제 2의 인터페이스 역할을 해주는 객체.

어댑터는 뒷단 객체만 관리하면 됨.

뒷단 객체 외에는 관리할 상태가 없으므로 뒷단 객체 하나당 어댑터 하나씩만 만들어지면 충분함.

<br>
<br>
<br>

Map 인터페이스의 keySet 메서드는 Map 객체 안의 키 전부를 담은 Set 뷰를 반환시킴.

반환된 Set 인스턴스가 일반적으로 가변이더라도 반환된 인스턴스들은 기능적으로 모두 똑같음.

반환한 객체 중 하나를 수정하면 다른 모든 객체가 따라서 바뀐다는 뜻.(모두 똑같은 Map 인스턴스를 대변하기 때문)

따라서 keySet이 뷰 객체를 여러 개 만들어도 상관은 없지만, 의미가 없음.

**오토박싱**

불필요한 객체를 만들어내는 또 다른 예

오토박싱이란?

프로그래머가 기본 타입과 박싱된 기본 타입을 섞어 쓸 때 자동으로 상호 변환해주는 기술.

오토박싱은 기본 타입과 그에 대응하는 박싱된 기본 타입의 구분을 흐려주지만, 완전히 없애주는 것은 아니다.

의미상으로는 별다를 것 없지만 성능에서 차이가 남.

```java
//박싱된 기본 타입을 사용해서 성능이 저하되는 상황
private static long sum(){
  Long sum=0L;
  for(long i=0;i<=Integer.MAX_VALUE;i++)
    sum+=i;
  
  return sum;
}
```

이 프로그램이 동작하긴 하지만 sum 변수를 long으로 선언하면(박싱된 기본 타입을 사용하지 않으면) 더 빠른 속도로 프로그램이 동작함.

**박싱된 기본 타입보다는 기본 타입을 사용하고, 의도치 않은 오토박싱이 숨어들지 않도록 주의하기**

---

이번 주제를 

객체 생성은 비싸니 피해야 한다

**로 오해하지 말것!!**

프로그램의 명확성, 간결성, 기능을 위해서 객체를 추가로 생성하는 것이라면 일반적으로 좋은 일임.

아주 무거운 객체가 아닌 다음에야 단순이 객체 생성을 피하고자 하지 말 것.

---

이번 아이템은 방어적 복사와 대조적임(챕터 8 참조)

기존 객체를 재사용해야 한다면 새로운 객체를 만들지 마라 <---> 새로운 객체를 만들어야 한다면 기존 객체를 재사용하지 마라

방어적 복사가 필요한 상황에서 객체를 재사용했을 때의 피해가, 필요 없는 객체를 반복 생성했을 때의 피해보다 훨씬 큼.

방어적 복사에 실패하면 언제 터져 나올지 모르는 버그와 보안 구멍으로 이어지지만, 불펼요한 객체 생성은 그저 코드 형태와 성능에만 영향을 줌.

---

# 다 쓴 객체 참조를 해제하라

자바의 가비지 컬렉터가 다 쓴 객체를 알아서 회수해 가기 때문에, 메모리 관리에 더 이상 신경 쓰지 않아도 된다고 오해할 수 있는데, 아니다.

```java
//누수가 일어나는 위치는?
public class Stack{
  private Object[] elements;
  private int size = 0;
  private static final int DEFAULT_INITIAL_CAPACITY = 16;
  
  public Stack(){
    elements=new Object[DEFAULT_INITIAL_CAPACITY];
  }
  
  public void push(Object e){
    ensureCapacity();
    elements[size++]=e;
  }
  
  public Object pop(){
    if(size==0){
      throw new EmptyStackException();
    }
    return elements[--size];
  }
  
  private void ensureCapacity(){
    if(elements.length==size)
      elements=Arrays.copyOf(elements,2*size+1);
  }
}
```

특별한 문제가 없어 보이지만, 메모리 누수가 숨어있음.

어디서 메모리 누수가 발생할까??

이 코드에서는 스택이 커졌다가 줄어들었을 때 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않음.
- 이 스택이 그 객체들의 다 쓴 참조(obsolete reference)를 여전히 가지고 있기 때문.

다 쓴 참조란??

문자 그대로 앞으로 다시 쓰지 않을 참조를 뜻함.
- 앞 코드에서 elements 배열의 활성 영역 밖의 참조들이 모두 여기에 해당함.

활성 영역은 인덱스가 size보다 작은 원소들로 구성됨.

---

가비지 컬렉션 언어에서는 의도치 않게 객체를 살려두는 메모리 누수를 찾기 아주 까다로움.

객체 참조 하나를 살려두면 가비지 컬렉터는 그 객체뿐 아니라 그 객체가 참조하는 모든 객체(그리고 또 그 객체들이 참조하는 모든 객체... 의 반복)를 회수해가지 못함.

해법은???

해당 참조를 다 썼을 때 null 처리 하기!!

```java
//제대로 구현한 pop 메서드
public Object pop(){
  if(size==0)
    throw new EmptyStackException();
  Object result=elements[--size];
  elements[size]==null;//다 쓴 참조 해제
  return result;
}
```

다 쓴 참조를 null 하면 다른 이점도 따라옴.

만약 null 처리한 참조를 실수로 사용하려 하면 프로그램은 즉시 NullPointerException을 던지며 종료됨.(오류를 빨리 발견할 수 있음)

**객체 참조를 null 처리하는 일은 예외적인 경우에만 한정해야 함**

다 쓴 참조를 해제하는 가장 좋은 방법은 그 참조를 담은 변수를 유효 범위 밖으로 밀어내는 것.

---

null 처리는 언제 해야 할까??

Stack 클래스가 메모리 누수에 취약한 이유는 스택이 자기 메모리를 직접 관리하기 때문임.

이 스택은 elements 배열로 저장소 풀을 만들어 원소들을 관리함. 배열의 활성 영역에 속한 원소들이 사용되고 비활성 영역은 쓰이지 않음.

하지만 가비지 컬렉터는 이 사실을 모름.

그러므로 프로그래머는 비활성 영역이 되는 순간 null 처리해서 해당 객체를 더는 쓰지 않을 것임을 가비지 컬렉터에 알려야 함.

---

캐시 역시 메모리 누수를 일으키는 주범임.

해결 방법은??

캐시 외부에서 키를 참조하는 동안만 엔트리가 살아 있는 캐시가 필요한 상황이라면 WeakHashMap을 사용해 캐시 작성.

하지만 WeakHashMap은 이러한 상황에서만 유용함.

캐시를 만들 때 보통 캐시 엔트리의 유효 기간을 정확히 정의하기 어렵기 때문에 시간이 지날수록 엔트리의 가치를 떨어트리는 방식을 흔히 사용함.

이런 방식에서는 쓰지 않는 엔트리를 이따금 청소해줘야 함. ScheduledThreadPoolExecutor같은 백그라운드 스레드를 활용하거나, 캐시에 새 엔트리를 추가할 때 부수 작업으로 수행하는 방법이 있음.

더 복잡한 캐시를 만들고 싶다면? java.lang.ref 패키지를 직접 활용.

---

세 번째 주범은 리스터 혹은 콜백이라 부르는 것임.

클라이언트가 콜백을 등록만 하고 명확히 해지하지 않는다면 뭔가 조치해주지 않는 한 콜백은 계속 쌓여감.

이럴 때 콜백을 약한 참조(weak reference)로 저장하면 가비지 컬렉터가 즉시 수거해감.

ex) WeakHashMap

---

핵심 정리

메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년 간 잠복하는 사례도 있다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.

---

# finalizer와 cleaner 사용을 피하라

