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

# ordinal 메서드 대신 인스턴스 필드를 사용하라

대부분의 열거 타입 상수는 자연스럽게 하나의 정숫값에 대응됨.

그리고 모든 열거 타입은 해당 상수가 그 열거 타입에서 몇 번째 위치인지를 반환하는 ordinal 메서드를 제공함.

열거 타입 상수와 연결된 정숫값이 필요하면 ordinal 메서드를 이용하고 싶은 유혹에 빠지지만, 사용하는 것을 추천하지 않음.

```java
//ordinal을 잘못 사용한 예
//따라하지 말것
public enum Ensemble{
    SOLO,DUET,TRIO,QUARTET,QUINTET,SEXTET,SEPTET,OCTET,NONET,DECTET;

    public int numberOfMusicians(){return ordinal()+1;}
}
```

위 코드는 동작은 하지만 유지보수하기가 굉장히 힘듬.

상수 선언 순서를 바꾸는 순간 numberOfMusicians가 오동작하며, 이미 사용 중인 정수와 값이 같은 상수는 추가할 방법이 없음.

예를 들어 똑같이 8명이 연주하는 복4중주는 추가할 수 없음.

또한 값을 중간에 비워둘 수도 없음. 쓰이지 않는 더미 상수를 같이 추가해야 한다는 뜻.

<br>

해결책은?

열거 타입 상수에 연결된 값은 ordinal 메서드로 얻지 말고, 인스턴스 필드에 저장하기.

```java
public enum Ensemble{
    SOLO(1),DUET(2),TRIO(3),QUARTET(4),QUINTET(5),SEXTET(6),SEPTET(7),
    OCTET(8),DOUBLE_QUARTET(8),NONET(9),DECTET(10),TRIPLE_QUARTET(12);

    private final int numberOfMusicians;
    Ensemble(int size){this.numberOfMusicians=size;}
    public int numberOfMusicians(){return numberOfMusicians;}
}
```

Enum의 API 문서를 보면 ordinal 메서드는 EnumSet과 EnumMap과 같이 열거 타입 기반의 범용 자료구조에 쓸 목적으로 설계되었다고 나와있음.

따라서 이런 용도가 아니라면 ordinal 메서드는 절대 사용하지 말 것.

---

# 비트 필드 대신 EnumSet을 사용하라

```java
//비트 필드 열거 상수
//좋지 않은 방법임.
public class Text{
    public static final int STYLE_BOLD            1<<0;
    public static final int STYLE_ITALIC          1<<0;
    public static final int STYLE_UNDERLINE       1<<0;
    public static final int STYLE_STRIKETHROUGH   1<<0;

    //매개변수 styles는 0개 이상의 STYLE_ 상수를 비트별 OR한 값임.
    public void applyStyles(int styles){...};
}
```

다음과 같은 식으로 비트별 OR을 사용해 여러 상수를 하나의 집합으로 모을 수 있으며, 이렇게 만들어진 집합을 비트 필드라 함.

    text.applyStyles(STYLE_BOLE|STYLE_ITALIC);

비트 필드를 사용하면 비트별 연산을 사용해 합집합과 교집합 같은 집합 연산을 효율적으로 수행할 수 있음.

하지만 비트 필드는 정수 열거 상수의 단점을 그대로 지니며, 추가로 다음과 같은 문제까지 안고 있음.

1. 비트 필드 값이 그대로 출력되면 해석하기가 훨씬 어려움.
2. 비트 필드 하나에 녹아 있는 모든 원소를 순회하기도 까다로움.
3. 최대 몇 비트가 필요한지를 API 작성 시 미리 예측하여 적절한 타입을 선택해야 함.
    - API를 수정하지 않고는 비트 수를 더 늘릴 수 없기 때문.
  
---

java.util 패키지의 Enumset 클래스는 열거 타입 상수의 값으로 구성된 집합을 효과적으로 표현해줌.

Set 인터페이스를 완벽히 구현하며, 타입 안전하고, 다른 어떤 Set 구현체와도 함께 사용될 수 있음.

하지만 EnumSet의 내부는 비트 벡터로 구현됨.

원소가 총 64개 이하라면, EnumSet 전체를 long 변수 하나로 표현하여 비트 필드에 비견되는 성능을 보여줌.

removeAll과 retainAll같은 대량 작업ㄷ은 비트를 효율적으로 처리할 수 있는 산술 연산을 써서 구현했음.

난해한 작업을 EnumSet이 다 처리해주기 때문에 비트를 직접 다룰 때 흔히 겪는 오류들에서 해방됨.

```java
//비트 필드를 대체하는 EnumSet
public class Text{
    public enum Style{BOLD, ITALIC, UNDERLINE, STRIKETHROUGH}

    public void applyStyles(Set<Style> styles)(Set<Style> styles){...};
}
```

    다음은 applyStyles 메서드에 EnumSet 인스턴스를 건네는 클라이언트 코드임.
    EnumSet은 집합 생성 등 다양한 기능의 정적 팩터리를 제공하는데, 다음 코드에서는 그 중 of 메서드를 사용했음.

    text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));

---

핵심 정리

열거할 수 있는 타입을 한데 모아 집합 형태로 사용한다고 해도 비트 필드를 사용할 이유는 없다. EnumSet 클래스가 비트 필드 수준의 명료함과 성능을 제공하고 열거 타입의 장점까지 선사하기 때문이다.

---

# ordinal 인덱싱 대신 EnumMap을 사용하라

ordinal 인덱싱은 위에 서술한 것처럼 문제점이 많음.

```java
//식물을 간단히 나타낸 클래스
class Plant{
    enum LifeCycle {ANNUAL, PERENNIAL, BIENNIAL}

    final String name;
    final LifeCycle lifeCycle;

    Plant(String name, LifeCycle lifeCycle){
        this.name=name;
        this.lifeCycle=lifeCycle;
    }

    @Override public String toString(){
        return name;
    }
}


//정원에 심은 식물들을 배열 하나로 관리하고, 이들을 생애주기별로 묶어보기
//ordinal()을 배열 인덱스로 사용함. 안좋은 예

Set<Plant>[] plantsByLifeCycle = (Set<Plant>[]) new Set[Plant.LifeCycle.values().length];
for(int i=0; i< plantsByLifeCycle.length; i++)
    plantsByLifeCycle[i]=new HashSet<>();

for(Plant p : garden)
    plantsByLifeCycle[p.lifeCycle.ordinal()].add(p);

//결과 출력
for(int i=0;i<plantsByLifeCycle.length;i++){
    System.out.printf("%s: %s%n", Plant.LifeCycle.values()[i], plantsByLifeCycle[i]);
}
```

배열은 제네릭과 호환되지 않으니 비검사 형변환을 수행해야 하고, 깔끔히 컴파일되지 않을 것.

배열은 각 인덱스의 의미를 모르니 출력 결과에 직접 레이블을 달아야 함.

정확한 정숫값을 사용한다는 것을 우리가 직접 보증해야 함.(가장 큰 문제)
- 정수는 열거 타입과 달리 타입 안전하지 않기 때문임.
- 잘못된 값을 사용하면 잘못된 동작을 묵묵히 수행하거나, 운이 좋다면 ArrayIndexOutOfBoundsException을 밷을 것.

<br>

### 해결책은?

배열은 실질적으로 열거 타입 상수를 값으로 매핑하는 작업을 함. 그러니 Map을 사용하는 것도 가능함.

열거 타입을 키로 사용하도록 설계한 아주 빠른 Map 구현체가 있는데, EnumMap이라 함.

```java
//EnumMap을 사용해 데이터와 열거 타입을 매핑함
Map<Plant.LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);
for(Plant.LifeCycle lc : Plant.LifeCycle.values())
    plantsByLifeCycle.put(lc,new HashSet<>));
for(Plant p : garden)
    plantsByLifeCycle.get(p.lifeCycle).add(p);
System.out.println(plantsByLifeCycle);
```
더 짧고, 명료하고, 안전하고, 성능도 원래 버전과 비등함.

안전하지 않은 형변환은 쓰지 않고, 맵의 키인 열거 타입이 그 자체로 출력용 문자열을 제공함. (출력 결과에 직접 레이블을 달 일이 없음)

배열 인덱스를 계산하는 과정에서 오류가 날 가능성도 사라짐.

내부에서 배열을 사용하기 때문에, EnumMap의 성능은 ordinal을 쓴 배열과 비슷함.

내부 구현 방식을 안으로 숨겨서 Map의 타입 안전성과 배열의 성능을 모두 얻어냄.

EnumMap의 생성자가 받는 키 타입의 Class 객체는 한정적 타입 토큰으로, 런타임 제네릭 타입 정보를 제공함.

---

스트림을 사용해 맵을 관리하면 코드를 더 줄일 수 있음.

```java
//스트림을 사용한 코드
//EnumMap 사용 X
System.out.println(Arrays.stream(garden).collect(groupingBy(p->p.lifeCycle)));

//EnumMap 사용
System.out.println(Arrays.stream(garden).collect(groupingBy(p->p.lifeCycle, ()-> new EnumMap<>(LifeCycle.class), toSet())));
```

EnumMap 사용을 하지 않은 코드는 고유한 맵 구현체를 사용했기 때문에 EnumMap을 써서 얻은 공간과 성능 이점이 사라짐. 

---

스트림을 사용하면 EnumMap만 사용했을 때와는 살짝 다르게 동작함.

EnumMap 버전은 언제나 시ㅏㄱ물의 생애주기당 하나씩의 중첩 맵을 만들지만, 스트림 버전에서는 해당 생애주기에 속하는 식물이 있을 때에만 만듬.

정원에 한해살이와 여러해살이 식물만 살고, 두해살이 식물이 없다면 EnumMap 버전에서는 맵을 3개, 스트림 버전에서는 2개만 만든다.

---

```java
//배열들의 배열의 인덱스에 ordinal()을 사용
//안좋은 예
public enum Phase{
    SOLID, LIQUID, GAS;
    public enum Transition{
        MELT, FREEZE, BOIL, CONDENSE, SUBLIME, DEPOSIT;

        //행은 from의 ordinal을, 열은 to의 ordinal을 인덱스로 씀
        private static final Transition[][] TRANSITIONS = {
            {null, MELT, SUBLIME},
            {FREEZE, null, BOIL},
            {DEPOSIT, CONDENSE, null}
        };

        //한 상태에서 다른 상태로의 전이를 반환
        public static Transition from(Phase from, Phase to){
            return TRANSITIONS[from.ordinal()][to.ordinal()];
        }
    }
}
```

컴파일러는 ordinal과 배열 인덱스의 관계를 알 수 없음.

Phase나 Phase.Transition 열거 타입을 수정하면서 상전이 표 TRANSITIONS를 함께 수정하지 않거나 실수로 잘못 수정하면 런타임 오류가 날 것.

그리고 상전이 표의 크기는 상태의 가짓수가 늘어나면 제곱해서 커지며 null로 채워지는 칸도 늘어날 것

전이 하나를 얻으려면 이전 상태와 이후 상태가 필요하니, 맵 2개를 중첩하셔 EnumMap을 사용하면 훨씬 나음.

안쪽 맵은 이전 상태와 전이를 연결하고, 바깥 맵은 이후 상태와 안쪽 맵을 연결.

전이 전후의 두 상태를 전이 열거 타입 Transition의 입력으로 받아, 이 Transition 상수들로 중첩된 EnumMap을 초기화하면 됨.

```java
//중첩 EnumMap으로 데이터와 열거 타입 쌍을 연결함
public enum Phase{
    SOLID, LIQUID, GAS;
    public enum Transition{
        MELT(SOLID,LIQUID), FREEZE(LIQUID,SOLID), BOIL(LIQUID,GAS), CONDENSE(GAS,LIQUID), SUBLIME(SOLID,GAS), DEPOSIT(GAS,SOLID);

        private final Phase from;
        private final Phase to;

        Transition(Phase from, Phase to){
            this.from=from;
            this.to=to;
        }

        //상전이 맵을 초기화
        private static final Map<Phase, Map<Phase,Transition>>
        m = Stream.of(values()).collect(groupingBy(t->t.from,()->new EnumMap<>(Phase.class),
        toMap(t->t.to,t->t,(x,y)->y,()->new EnumMap<>(Phase.class))));

        public static Transition from(Phase from, Phase to){
            return m.get(from).get(to);
        }
    }
}
```

맵의 타입인 Map<Phase,Map<Phase,Transition>>은 이전 상태에서 '이후 상태에서 전이로의 맵'에 대응시키는 맵이라는 뜻임.

냅을 초기화하기 위해 수집기(java.util.stream.Collector) 2개를 차례로 사용함.

groupingBy에서는 전이를 이전 상태를 기준으로 묶고, toMap에서는 이후 상태를 전이에 대응시키는 EnumMap을 생성함.

두 번째 수집기의 병합 함수인 (x,y)->y는 선언만 하고 실제로 쓰이지는 않는데, 이는 단지 EnumMap을 얻으려면 맵 팩터리가 필요하고 수집기들은 점층적 팩터리를 제공하기 때문임.

<br>

여기에 새로운 상태인 플라스마를 추가해보자.

이 상태와 연결된 전이는 기체에서 플라스마로 변하는 IONIZE, 플라스마에서 기체로 변하는 DEIONIZE가 있음.

배열과 달리 EnumMap 버전은 상태 목록에 PLASMA 를 추가하고, 전이 목록에 IONIZE, DEIONIZE만 추가하면 끝남.

```java
// EnumMap 버전에 새로운 상태 추가
public enum Phase{
 SOLID, LIQUID, GAS, PLASMA;
    public enum Transition{
        MELT(SOLID,LIQUID), FREEZE(LIQUID,SOLID), BOIL(LIQUID,GAS), CONDENSE(GAS,LIQUID), SUBLIME(SOLID,GAS), DEPOSIT(GAS,SOLID),
        IONIZE(GAS, PLASMA), DEIONIZE(PLASMA, GAS);

        ...
    }
}
```

실제 내부에서는 맵들의 맵이 배열들의 배열로 구현되니 낭비되는 공간과 시간도 거의 없이 명확하고 안전하고 유지보수하기 좋음.

---

핵심 정리

배열의 인덱스를 얻기 위해 ordinal을 쓰는 것은 일반적으로 좋지 않으니, 대신 EnumMap을 사용하라. 다차원 관계는 EnumMap<..., EnumMap<...>> 으로 표현하라. 애플리케이션 프로그래머는 Enum.ordinal을 웬만해서는 사용하지 말아야 한다는 일반 원칙의 특수한 사례임.

---

# 확장할 수 있는 열거 타입이 필요하면 인터페이스를 사용하라

타입 안전 열거 패턴은 확장할 수 있으나, 열거 타입은 그럴 수 없음.

타입 안전 열거 패턴은 열거한 값들을 그대로 가져온 다음 값을 더 추가하여 다른 목적으로 쓸 수 있는 반면, 열거 타입을 그렇게 할 수 없다는 뜻.

대부분의 상황에서는 열거 타입을 확장하는 것이 그리 좋지 않음.
- 확장한 타입의 원소는 기반 타입의 원소로 취급하지만, 그 반대는 성립하지 않는다면 이상함

기반 타입과 확장된 타입들의 원소 모두를 순회할 방법도 마땅치 않음

확장성을 높이려면 고려할 요소가 늘어나 설계와 구현이 더 복잡해짐.

---

확장할 수 있는 열거 타입이 어울리는 쓰임이 최소한 하나는 있음.
- 연산 코드(operation code, opcode)

연산 코드의 각 원소는 특정 기계가 수행하는 연산을 뜻함.

---

열거 타입으로 API가 제공하는 기본 연산 외에 사용자 확장 연산을 추가할 수 있는 효과를 낼 수 있음.

열거 타입이 임의의 인터페이스를 구현할 수 있다는 사실을 이용하는 것.

연산 코드용 인터페이스를 정의하고, 열거 타입이 이 인터페이스를 구현하게 하면 됨.

이 때 열거 타입이 그 인터페이스의 표준 구현체 역할을 함

```java
//인터페이스를 이용해 확장 가능 열거 타입을 흉내냄
//Operation 타입을 확장할 수 있게 만든 코드
public interface Operation{
    double apply(double x, double y);
}

public enum BasicOperation implements Operation{
    PLUS("+"){
        public double apply(double x, double y) {return x+y;}
    },
    MINUS("-"){
        public double apply(double x, double y) {return x-y;}
    },
    TIMES("*"){
        public double apply(double x, double y) {return x*y;}
    },
    DIVIDE("/"){
        public double apply(double x, double y) {return x/y;}
    };

    private final String symbol;

    BasicOperation(String symbol){
        this.symbol=symbol;
    }

    @Override public String toString(){
        return symbol;
    }
}
```

열거 타입인 BasicOperation은 확장할 수 없지만 인터페이스인 Operation은 확장할 수 있고, 이 인터페이스를 연산의 타입으로 사용하면 됨.

이렇게 하면 Operation을 구현한 또 다른 열거 타입을 정의해 기본 타입인 BasicOperation을 대체할 수 있음.

```java
//확장 가능 열거 타입
public enum ExtendedOperation implements Operation{
    EXP("^"){
        public double apply(double x, double y){
            return Math.pow(x,y);
        }
    },
    REMAINDER("%"){
        public double apply(double x, double y){
            return x%y;
        }
    };

    private final String symbol;

    ExtendedOperation(String symbol){
        this.symbol=symbol;
    }

    @Override public String toString(){
        return symbol;
    }
}
```

새로 작성한 연산은 기존 연산을 쓰던 곳이면 어디든 쓸 수 있음.

Operation 인터페이스를 사용하도록 작성되어 있기만 하면 됨.

apply가 인터페이스에 선언되어 있으니 열거 타입에 따로 추상 메서드로 선언하지 않아도 됨.

---

개별 인스턴스 수준에서뿐 아니라 타입 수준에서도 기본 열거 타입 대신 확장된 열거 타입을 넘겨 확장된 열거 타입의 원소 모두를 사용하게 할 수도 있음.

```java
public static void main(String[] args)}
    double x=Double.parseDouble(args[0]);
    double y=Double.parseDouble(args[1]);
    test(ExtendedOperation.class,x,y);
}

private static <T extends Enum<T> & Operation> void test(
        Class<T> opEnumType, double x, double y){
    for(Operation op: opEnumType.getEnumConstants())
        System.out.printf("%f %s %f = %f%n",x,op,y,op.apply(x,y));
}
```

main 메서드는 test 메서드에 ExtendedOperation의 class 리터럴을 넘겨 확장된 연산들이 무엇인지 알려줌.

여기서 class 리터럴은 한정적 타입 토큰 역할을 함.

opEnumType 매개변수 선언 T extends Enum~~ 은 Class 객체가 열거 타입인 동시에 Operation의 하위 타입이어야 한다는 뜻이다.

열거 타입이어야 원소를 순회할 수 있고, Operation이어야 원소가 뜻하는 연산을 수행할 수 있기 때문이다.

---

두 번째 대안은 Class 객체 대신 한정적 와일드카드 타입인 Collection<? extends Operation>을 넘기는 방법임.

