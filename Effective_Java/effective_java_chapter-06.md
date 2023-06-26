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

열거 타입 상수 각각의 특정 데이터와 연결지으려면, 생성자에서 데이터를 받아 인스턴스 필드에 저장하면 됨.

열거 타입은 근본적으로 불변이라 모든 필드는 final이어야 함.

필드를 public으로 선언해도 되지만 private로 두고 별도의 pbulic 접근자 메서드를 두는게 나음.

```java
//어떤 객체의 지구에서의 무게를 입력받아 여덟 행성에서의 무게를 출력하는 코드
//짧게 작성이 가능함
public class WeightTable{
    public static void main(String[] args){
        double earthWeight=Double.parseDouble(args[0]);
        double mass=earthWeight/Planet.EARTH.surfaceGravity();
        for(Planet p: Planet.values())
            System.out.println("%s에서의 무게는 %f이다.%n",p,p.surfaceWeight(mass));
    }
}
```

열거 타입은 자신 안에 정의된 상수들의 값을 배열에 담아 반환하는 정적 메서드인 values를 제공함.

값들은 선언된 순서대로 저장됨.

각 열거 타입 값의 toString 메서드는 상수 이름을 문자열로 반환하므로 println과 printf로 출력하기 좋음.

toString을 원하는대로 재정의하는 것도 가능.

---

열거 타입에서 상수를 하나 제거한다 해도 제거한 상수를 참조하지 않는 클라이언트에는 아무 영향이 없음.

참조하는 클라이언트 프로그램을 다시 컴파일하면 제거된 상수를 참조하는 줄에서 디버깅에 유용한 메시지를 담은 컴파일 오류가 발생함.

이를 보고 오류 수정이 가능함.

정수 열거 패턴과 달리 오류를 바로 확인 가능.

---

열거 타입을 선언한 클래스 혹은 그 패키지에서만 유용한 기능은 private나 package-private 메서드로 구현하기.

이렇게 구현된 열거 타입 상수는 자신을 선언한 클래스 혹은 패키지에서만 사용할 수 있는 기능을 담게 됨.

일반 클래스와 마찬가지로 선택하면 됨.

기능을 클라이언트에 노출해야 할 합당한 이유가 없다면 private, 필요하다면 package-private로 선언하기.

---

널리 쓰이는 열거 타입은 톱레벨 클래스로 만들고, 특정 톱레벨 클래스에서만 쓰인다면 해당 클래스의 멤버 클래스로 만들기.

    ex) 소수 자릿수의 반올림 모드를 뜻하는 열거 타입인 java.math.RoundingMode는 BigDecimal이 사용함. 
        그런데 반올림 모드는 BigDecimal과 관련 없는 영역에서도 유용한 개념이라 자바 라이브러리 설계자는 RoundingMode를 톱레벨로 올림.
        이 개념을 많은 곳에서 사용하여 다양한 API가 더 일관된 모습을 갖출 수 있도록 장려한 것.

---

상수가 더 다양한 기능을 제공해줬으면 할 때도 있음.

```java
//switch문을 이용해 상수의 값에 따라 분기하는 방법
//더 좋은 방법이 있음.
public enum Operation{
    PLUS,MINUS,TIMES,DIVEDE;

    //상수가 뜻하는 연산 수행
    public double apply(double x, double y){
        switch(this){
            case PLUS:        return x+y;
            case MINUS:       return x-y;
            case TIMES:       return x*y;
            case DIVIDE:      return x/y;
        }
        throw new AssertionError("알 수 없는 연산: "+this);
    }
}
```

동작은 하지만 그리 이쁘지 않음.

마지막 throw문은 실제로 동작할 일은 없지만, 기술적으로는 도달할 수 있기 때문에 생략하면 컴파일조차 되지 않음.

또한 새로운 상수를 추가하면 해당 case문도 추가해야 해서 깨지기 쉬운 코드임.

깜빡한다면 컴파일은 되지만 새로 추가한 연산을 수행하려 할 때 알 수 없는 연산이라는 런타임 오류를 내며 프로그램 종료.

더 좋은 방법이 존재함.

```java
//상수별 메서드 구현을 활용
//열거 타입에 apply라는 추상 메서드를 선언하고 상수별 클래스 몸체,
//즉 각 상수에서 자신에 맞게 재정의하는 방법
public enum Operation{
    PLUS  {public double apply(double x, double y){return x+y;}}
    MINUS {public double apply(double x, double y){return x-y;}}
    TIMES {public double apply(double x, double y){return x*y;}}
    DIVIDE{public double apply(double x, double y){return x/y;}}

    public abstract double apply(double x, double y);
}
```

apply 메서드가 상수 선언 바로 옆에 붙어있으니 새로운 상수를 추가할 때 apply도 재정의해야 한다는 사실을 깜빡하기 어려움.

apply가 추상 메서드이므로 재정의하지 않았다면 컴파일 오류로 알려줌.

<br>

상수별 메서드 구현을 상수별 데이터와 결합할 수도 있음.

```java
//상수별 클래스 몸체(class body)와 데이터를 사용한 열거 타입
public enum Operation{
    PLUS("+")  {
        public double apply(double x, double y){
            return x+y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y){
            return x-y;
        }
    },
    TIMES("*") {
        public double apply(double x, double y){
            return x*y;
        }
    },
    DIVIDE("/"){
        public double apply(double x, double y){
            return x/y;
        }
    };

    private final String symbol;

    Operation(String symbol){this.symbol=symbol;}

    @Override
    public string toString(){
        return symbol;
    }

    public abstract double apply(double x, double y);
}
```

위 toString은 계산식 출력을 엄청나게 간편하게 해줌.

```java
public static void main(String[] args){
    double x=Double.parseDouble(args[0]);
    double y=Double.parseDouble(args[1]);
    for(Operation op:Operation.values())
        System.out.printf("%f %s %f = %f%n",
                         x,op,y,op.apply(x,y));
}
```

위 명령어 인수에 2와 4를 주어 프로그램을 실행하면 다음과 같은 결과를 볼 수 있음

    2.000000 + 4.000000 = 6.000000
    2.000000 - 4.000000 = -2.000000
    2.000000 * 4.000000 = 8.000000
    2.000000 / 4.000000 = 0.500000

열거 타입에는 상수 이름을 입력받아 그 이름에 해당하는 상수를 반환해주는 valueOf(String) 메서드가 자동 생성됨.

한편, 열거 타입의 toString 메서드를 재정의하려거든 toString이 반환하는 문자열을 해당 열거 타입 상수로 변환해주는 fromString 메서드도 함께 제공하는 것을 고려해보기.

```java
//모든 열거 타입에서 사용할 수 있도록 구현한 fromString
//타입 이름을 적절히 바꿔야 하고 모든 상수의 문자열 표현이 고유해야 성립함.
private static final Map<String,Operation> stringToEnum = 
        Stream.of(values()).collect(
            toMap(Object::toString, e->e));

//지정한 문자열에 해당하는 Operation이 존재한다면 반환하기
public static Optional<Operation> fromString(String symbol){
    return Optional.ofNullable(stringToEnum.get(symbol));
}
```

Operation 상수가 stringToEnum 맵에 추가되는 시점은 열거 타입 상수 생성 후 정적 필드가 초기화될 때임.

앞의 코드는 values 메서드가 반환하는 배열 대신 스트림을 사용함.

열거 타입의 정적 필드 중 열거 타입의 생성자에서 접근할 수 있는 것은 상수 변수 뿐임.

열거 타입 생성자가 실행되는 시점에는 정적 필드들이 아직 초기화되기 전이라 자기 자신을 추가하지 못하게 하는 제약이 꼭 필요함.

ex) 열거 타입 생성자에서 같은 열거 타입의 다른 상수에도 접근할 수 없음.

(열거 타입의 각 상수는 해당 열거 타입의 인스턴스를 public static final 필드로 선언한 것임을 떠올리자. 즉, 다른 형제 상수도 static이므로 열거 타입 생성자에서 정적 필드에 접근할 수 없다는 제약이 적용됨.)

---

fromString이 Optional<Operation>을 반환하는 점을 주의할 것.

주어진 문자열이 가리키는 연산이 존재하지 않을 수 있음을 클라이언트에 알리고, 그 상황을 클라이언트에서 대처하도록 한 것임.

---

상수별 메서드 구현에는 열거 타입 상수끼리 코드를 공유하기 어렵다는 단점이 있음.

```java
//급여명세서에 쓸 요일을 표현하는 열거 타입
//직원의 시간당 기본 임금과 그날 일한 시간이 주어지면 일당을 계산해주는 메서드 보유
//주중에 오버타임이 발생하면 잔업수당 ++
//주말에는 무조건 잔업수당 ++
//switch문을 이용하면 case문을 날짜별로 두어 이 계산을 쉽게 수행 가능

//값에 따라 분기하여 코드를 공유하는 열거 타입
//좋은 방법일까?

enum PayrollDay{
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;

    private static final int MINS_PER_SHIFT = 8*60;

    int pay(int minutesWorked, int payRate){
        int basePay=minutesWorked*payRate;

        int overtimePay;
        switch(this){
            case SATURDAY: case SUNDAY:
                overtimePay=basePay/2;
                break;
            default:
                overtimePay=minutesWorked<=MINS_PER_SHIFT ? 0 : (minutesWorked-MINS_PER_SHIFT)*payRate/2;
        }

        return basePay+overtimePay;
    }
}
```

간결하지만, 관리 관점에서는 위험한 코드임.

왜 위험한지?

휴가와 같은 새로운 값을 열거 타입에 추가하려면 그 값을 처리하는 case 문을 잊지 말고 쌍으로 넣어줘야 하기 때문.

상수별 메서드 구현으로 급여를 정확히 계산하는 방법은 두 가지임.

1. 잔업수당을 계산하는 코드를 모든 상수에 중복해서 넣기
2. 계산 코드를 평일용과 주말용으로 나눠 각각을 도우미 메서드로 작성한 다음 각 상수가 자신에게 필요한 메서드를 적절히 호출하기

두 방식 모두 코드가 장황해져 가독성이 크게 떨어지고 오류 발생 가능성이 높아짐.

이를 해결할 방법은?

```java
//새로운 상수를 추가할 때 잔업수당 '전략'을 선택하도록 하기
//잔업수당 계산을 private 중첩 열거 타입으로 옮기고 PayrollDay 열거 타입의 생성자에서 이 중 적당한 것을 선택하기
//그러면 PayrollDay 열거 타입은 잔업수당 계산을 그 전략 열거 타입에 위임하여 switch문이나 상수별 메서드 구현이 필요없어짐.
//switch문보다 복잡하지만 더 안전하고 유연함.

//전략 열거 타입 패턴
enum PayrollDay{
    MONDAY(WEEKDAY), TUESDAY(WEEKDAY), WEDNESDAY(WEEKDAY),
    THURSDAY(WEEKDAY), FRIDAY(WEEKDAY),
    SATURDAY(WEEKEND), SUNDAY(WEEKEND);

    private final PayType payType;

    PayrollDay(PayType payType){this.payType=payType;}

    int pay(int minutesWorked, int payRate){
        return payType.pay(minutesWorked, payRate);
    }

    //전략 열거 타입
    enum PayType{
        WEEKDAY{
            int overtimePay(int minsWorked, int payRate){
                return minsWorked<=MINS_PER_SHIFT?0:(minsWorked-MINS_PER_SHIFT)*payRate/2;
            }
        },
        WEEKEND{
            int overtimePay(int minsWorked, int payRate){
                return minsWorked*payRate/2;
            }
        };

        abstract int overtimePay(int mins, int payRate);
        private static final int MINS_PER_SHIFT=8*60;

        int pay(int minsWorked, int payRate){
            int basePay=minsWorked*payRate;
            return basePay+overtimePay(minsWorked,payRate);
        }
    }
}
```

switch문은 열거 타입의 상수별 동작을 구현하는 데 적합하지 않음.

하지만 기존 열거 타입에 상수별 동작을 혼합해 넣을 때는 switch문이 좋은 선택이 될 수 있음.

```java
//switch문을 이용해 원래 열거 타입에 없는 기능을 수행한다.
public static Operation inverse(Operation op){
    switch(op){
        case PLUS:     return Operation.MINUS;
        case MINUS:    return Operation.PLUS;
        case TIMES:    return Operation.DIVIDE;
        case DIVIDE:   return Operation.TIMES;

        default: throw new AssertionError("알 수 없는 연산: " + op);
    }
}
```

추가하려는 메서드가 의미상 열거 타입에 속하지 않는다면 직접 만든 열거 타입이라도 이 방식을 적용하는게 좋음.

종종 쓰이지만 열거 타입 안에 포함할 만큼 유용하지는 않은 경우도 마찬가지임.

---

열거 타입을 언제 쓰면 좋을까?

**필요한 원소를 컴파일타임에 다 알 수 있는 상수 집합이라면 항상 열거 타입을 사용하기.**

열거 타입에 정의된 상수 개수가 영원히 고정 불변일 필요는 없음.

열거 타입은 나중에 상수가 추가돼도 바이너리 수준에서 호환되도록 설계되었음.

---

핵심 정리

열거 타입은 확실히 정수 상수보다 뛰어나다. 더 읽기 쉽고 안전하고 강력하다. 대다수 열거 타입이 명시적 생성자나 메서드 없이 쓰이지만, 각 상수를 특정 데이터와 연결짓거나 상수마다 다르게 동작하게 할 때는 필요하다. 드물게는 하나의 메서드가 상수별로 다르게 동작해야 할 때도 있다. 이런 열거 타입에서는 switch 문 대신 상수별 메서드 구현을 사용하자. 열거 타입 상수 일부가 같은 동작을 공유한다면 전략 열거 타입 패턴을 사용하자.

---

### ordinal 메서드 대신 인스턴스 필드를 사용하라

