# Effective Java 2장
# 목차
1. [생성자 대신 정적 팩터리 메서드를 고려하라](#생성자-대신-정적-팩터리-메서드를-고려하라)
2. [생성자에 매개변수가 많다면 빌더를 고려하라](#생성자에-매개변수가-많다면-빌더를-고려하라)
3. [private 생성자나 열거 타입으로 싱글턴임을 보증하라](#private-생성자나-열거-타입으로-싱글턴임을-보증하라)
4. [인스턴스화를 막으려거든 private 생성자를 사용하라]
5. [자원을 직접 명시하지 말고 의존 객체 주입을 사용하라]
6. [불필요한 객체 생성을 피하라]
7. [다 쓴 객체와 참조를 해제하라]
8. [finalizer와 cleaner 사용을 피하라]
9. [try=finally보다는 try=with-resources를 사용하라]
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

