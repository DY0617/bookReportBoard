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

---

**반사성**

객체는 자기 자신과 같아야 한다.

이 요건을 어긴 클래스의 인스턴스를 컬렉션에 넣은 다음 contains 메서드를 호출하면 false를 반환할 것임.

---

**대칭성**

두 객체는 서로에 대한 동치 여부에 똑같이 답해야 함.

```java
//대칭성을 위배하는 잘못된 코드
public final class CaseInsensitiveString{
    private final String s;
    
    public CaseInsensitiveString(String s){
        this.s=Object.requireNonNull(s);
    }
    
    //대칭성 위배
    @Override
    public boolean equals(Object o){
        if(o instanceof CaseInsensitiveString)
            return s.equalsIgnoreCase(
                ((CaseInsensitiveString) o).s);
        if(o instanceof String)//한 방향으로만 작동함
            return s.equalsIgnoreCase((String) o);
        return false;
    }
    ...
}
```

        CaseInsensitiveString cis= new CaseInsensitiveString("Polish");
        String s= "polish"

위 코드에서 cis.equals(s)는 true를 반환함.

하지만 Strig의 equals가 CaseInsensitiveString의 존재를 모르기 때문에 s.equals(cis)는 false를 반환함.

        List<CaseInsensitiveString> list=new ArrayList<>();
        list.add(cis);
        
이 다음에 list.contains(s)를 호출하면 jdk 버전에 따라 다른 결과가 나옴.

equals 규약을 어기면 그 객체를 사용하는 다른 객체들이 어떻게 반응할지 알 수 없음.

이 문제를 해결하려면 CaseInsensitiveString의 equals를 String과도 연동하겠다는 마음을 버려야 함.

```java
@Override
public boolean equals(Object o){
    return o instanceof CaseInsensitiveString && ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);
}
```

---

**추이성**

첫 번째 객체와 두 번째 객체가 같고, 두 번째 객체와 세 번째 객체가 같다면

첫 번째 객체와 세 번째 객체도 같아야 한다.

```java
public class Point{
    private final int x;
    private final int y;
    
    public Point(int x, int y){
        this.x=x;
        this.y=y;
    }
    
    @Override
    public boolean equals(Object o){
        if(!(o instanceof Point))
            return false;
        Point p=(Point)o;
        return p.x==x&&p.y==y;
    }
    
}

public class ColorPoint extends Point{
    private final Color color;
    
    public ColorPoint(int x, int y, Color color){
        super(x,y);
        this.color=color;
    }
    ...
}
```

equals 메서드를 그대로 둔다면 Point의 구현이 상속되어 생상 정보는 무시한 채 비교를 수행함. 

```java
//대칭성을 위배한 코드
@Override
public boolean equals(Object o){
    if(!(o instanceof ColorPoint))
        return false;
    return super.equals(o) && ((ColorPoint) o).color == color;
}
```

이 메서드는 일반 Point를 ColorPoint에 비교한 결과와 그 둘을 바꿔 비교한 결과가 다를 수 있음.

Point의 equals는 색상을 무시하고, ColorPoint의 equals는 입력 매개변수의 클래스 종류가 다르다며 매번 false를 반환할 것.

        Point p = new Point(1,2);
        ColorPoint cp= new ColorPoint(1,2,Color.RED);

p.equals(cp)는 true를 반환
cp.equals(p)는 false를 반환

ColorPoint.equals가 Point와 비교할 때는 색상을 무시하도록 한다면?

```java
//추이성을 위배하는 코드
@Override
public boolean equals(Object o){
    if(!(o instanceof Point))
        return false;

    if(!(o instanceof ColorPoint))
        return o.equals(this);

    return super.equals(o)&&((ColorPoint) o).color==color;
}
```

이 방식은 대칭성을 지켜주지만 추이성을 깨버림.

        ColorPoint p1= new ColorPoint(1,2,Color.RED);
        Point p2= new Point(1,2);
        ColorPoint p3= new ColorPoint(1,2,Color.BLUE);

p1.equals(p2)와 p2.equals(p3)가 true를 반환하지만, p1.equals(p3)는 false를 반환함.

이 코드 방식은 무한 재귀에 빠질 수 있음.

<br>

그렇다면 해법은?

사실 이 현상은 모든 객체 지향 언어의 동치관계에서 나타나는 근본적인 문제임.

구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않음.(객체 지향적 추상화의 이점을 포기하지 않는 한에 한함)

이 말은 얼핏 듣기에는 equals 안의 instanceof 검사를 getClass 검사로 바꾸면 규약도 지키고 값도 추가하면서 구체 클래스를 상속할 수 있다는 뜻으로 들림.

```java
//LSP 위배
@Override
public boolean equals(Object o){
    if(o==null || o.getClass()!=getClass())
        return false;
    Point p= (Point) o;
    return p.x==x&&p.y==y;
}
```

위 코드는 같은 구현 클래스의 객체와 비교할 때만 true를 반환함.

하지만 리스코프 치환 원칙을 위반함. 사용 불가.

Point의 하위 클래스는 정의상 여전히 Point이므로 어디서든 Point로써 활용될 수 있어야 함.

<br>

구체 클래스의 하위 클래스에서 값을 추가할 방법은 없지만, 괜찮은 우회 방법이 있음.

상속 대신 컴포지션을 사용하라.

Pointer를 상속하는 대신 Point를 ColorPoint의 private 필드로 두고, ColorPoint와 같은 위치의 일반 Point를 반환하는 뷰 메서드를 public으로 추가함.

```java
public class ColorPoint{
    private final Point point;
    private final Color color;
    
    public ColorPoint(int x, int y, Color color){
        point=new Point(x,y);
        this.color=Objects.requireNonNull(color);
    }
    
    //이 ColorPoint의 Point뷰 반환
    public Point asPoint(){
        return point;
    }
    
    @Override
    public boolean equals(Object o){
        if(!(o instanceof ColorPoint))
            return false;
        ColorPoint cp=(ColorPoint) o;
        return cp.point.equals(point)&&cp.color.equals(color);
    }
    ...
}
```

---

**일관성**

두 객체가 같다면 어느 하나 혹은 두 객체 모두가 수정되지 않는 한 앞으로도 영원히 같아야 함.

가변 객체는 비교 시점에 따라 서로 다를수도 있고 같을수도 있음. 불변 객체는 한번 다르면 끝까지 다르고, 한번 같으면 끝까지 같아야 함.

클래스가 가변이던 불변이던 간에 equals의 판단에 신뢰할 수 없는 자원이 끼어들게 해서는 안됨.

equals는 항시 메모리에 존재하는 객체만을 사용한 결정적(deterministic) 계산만 수행해야 함.

---

**null-아님**

마지막 요건은 공식 이름이 없음. 따라서 null-아님 이라고 표기함.

형변환에 맞서 instanceof 연산자로 입력 매개변수가 올바른 타입인지 검사해야 하기 때문에, 명시적 null 검사를 할 때 if(o==null) 할 필요 없음.

```java
@Override
public boolean equals(Object o){
    if(!(o instanceof MyType){
        return false;
    }
    Mytype mt=(Mytype) o;
    ...
}
```

---

지금까지 내용을 종합해서 양질의 equals 메서드 구현 방법들 단계별로 정리함.

1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
    - 자기 자신이면 true 반환. 단순 성능 최적화용임.
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
3. 입력을 올바른 타입으로 형변환한다.
4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사한다.
    - 모든 필드가 일치하면 true, 그렇지 안핟면 false 반환.

---

float와 double을 제외한 기본 타입 필드는 == 연산자로 비교하고, 참조 타입 필드는 각각의 equals 메서드로, float와 double 필드는 각각 정적 메서드인 Float.compare(float, float)와 Double.compare(double,double)로 비교함.

Float와 Double만 특별한 이유는?

Float.NaN, -0.0f, 특수한 부동소수 값 등을 다루어야 하기 때문.

null도 정상 값으로 취급하는 참조 타입 필드도 있음.

이런 필드는 정적 메서드인 Object.equals(Object,Object)로 비교해 NullPointerException 발생을 예방하기.

앞의 CaseInsensitiveString 예처럼 비교하기 아주 복잡한 필드를 가진 클래스도 있음.

그럴때는 그 필드의 표준형(canonical form)을 저장해둔 후 표준형끼리 비교하면 훨씬 경제적임.

---

어떤 필드를 먼저 비교하느냐가 equals의 성능을 좌우하기도 함.

최상의 성능을 바란다면 다를 가능성이 더 크거나 비교하는 비용이 싼 필드를 먼저 비교하기

---

equals를 다 구현했다면 세 가지만 자문해보기
- 대칭적인가?
- 추이성이 있는가?
- 일관적인가?

자문에서 끝내지 말고 단위 테스트 작성해서 돌려보기

단 equals 메서드를 AutoValue를 이용해 작성했다면 테스트를 생략해도 안심할 수 있음.

반사성과 null-아님도 만족해야 하지만, 이 둘이 문제되는 경우는 거의 X

```java
//전형적인 equals 메서드의 예
public final class PhoneNumber{
    private final short areaCode,prefix,lineNum;
    
    public PhoneNumber(int areaCode, int prefix, int lineNum){
        this.areaCode=rangeCheck(areaCode,999,"지역코드");
        this.prefix=rangeCheck(prefix,999,"프리픽스");
        this.lineNum=rangeCheck(lineNum,9999,"가입자 번호");
    }
    
    private static short rangeCheck(int val, int max, String arg){
        if(val<0||val>max)
            throw new IllegalArgumentException(arg+": "+val);
        return (short)val;
    }
    
    @Override
    public boolean equals(Object o){
        if(o==this)
            return true;
        if(!(o instanceof PhoneNumber))
            return false;
        PhoneNumber pn=(PhoneNumber)o;
        return pn.lineNum==lineNum&&pn.prefix==prefix&&pn.areaCode==areaCode;
    }
    ...
}
```

---

마지막 주의사항

- equals를 재정의할 땐 hashCode도 반드시 재정의하자
- 너무 복잡하게 해결하려 들지 말자
    - 필드들의 동치성만 검사해도 equals 규약을 어렵지 않게 지킬 수 있음.
- Object 외의 타입을 매개변수로 받는 equals 메서드를 선언하지 말자
    - 다중정의가 될 수 있음.

---

AutoValue??

구글의 오픈소스로, equals와 hashCode를 작성하고 테스트해줌.

---

핵심 정리

꼭 필요한 경우가 아니면 equals를 재정의하지 말자. 많은 경우에 Object의 equals가 여러분이 원하는 비교를 정확히 수행해준다. 재정의해야 할 때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯 가지 규약을 확실히 지켜가며 비교해야 한다.

---

# equals를 재정의하려거든 hashCode도 재정의하라

