# Effective Java 4장
# 클래스와 인터페이스

추상화의 기본 단위인 클래스와 인터페이스는 자바 언어의 심장과도 같음.

그래서 자바 언어에는 클래스와 인터페이스 설계에 사용하는 강력한 요소가 많이 있음.

이번 장에서는 이런 요소를 적절히 활용하여 클래스와 인터페이스를 쓰기 편하고, 견고하며, 유연하게 만드는 방법을 기술함.

---

# 목차

1. [클래스와 멤버의 접근 권한을 최소화하라](#클래스와-멤버의-접근-권한을-최소화하라)
2. [public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라](#public-클래스에서는-public-필드가-아닌-접근자-메서드를-사용하라)
3. [변경 가능성을 최소화하라](#변경-가능성을-최소화하라)
4. [상속보다는 컴포지션을 사용하라](#상속보다는-컴포지션을-사용하라)
5. [상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라](#상속을-고려해-설계하고-문서화하라-그러지-않았다면-상속을-금지하라)
6. [추상 클래스보다는 인터페이스를 우선하라](#추상-클래스보다는-인터페이스를-우선하라)
7. [인터페이스는 구현하는 쪽을 생각해 설계하라](#인터페이스는-구현하는-쪽을-생각해-설계하라)
8. [인터페이스는 타입을 정의하는 용도로만 사용하라](#인터페이스는-타입을-정의하는-용도로만-사용하라)
9. [태그 달린 클래스보다는 클래스 계층구조를 활용하라](#태그-달린-클래스보다는-클래스-계층구조를-활용하라)
10. [멤버 클래스는 되도록 static으로 만들라](#멤버-클래스는-되도록-static으로-만들라)
11. [톱레벨 클래스는 한 파일에 하나만 담으라](#톱레벨-클래스는-한-파일에-하나만-담으라)

---

# 클래스와 멤버의 접근 권한을 최소화하라

어설프게 설계된 컴포넌트와 잘 설계된 컴포넌츠의 차이는?

클래스 내부 에디터와 내부 구현 정보를 외부 컴포넌트로부터 얼마나 절 숨겼는가
- 잘 설계된 컴포넌트는 모든 내부 구현을 완벽히 숨겨, 구현과 API를 깔끔히 분리함.
- 오직 API를 통해서만 다른 컴포넌트와 소통하며 서로의 내부 동작 방식에는 전혀 개의치 않음.

정보 은닉, 혹은 캡슐화라고 하는 이 개념은 소프트웨어 설꼐의 근간이 되는 원리임.

---

**정보 은닉의 장점**

- 시스템 개발 속도를 높인다. 
    - 여러 컴포넌트를 병렬로 개발할 수 있기 때문.
- 시스템 관리 비용을 낮춘다. 
    - 각 컴포넌트를 더 빨리 파악하여 디버깅할 수 있고, 다른 컴포넌트로 교체하는 부담도 적기 때문.
- 정보 은닉 자체가 성능을 높여주지는 않지만, 성능 최적화에 도움을 준다.
    - 완성된 시스템을 프로파일링해 최적화할 컴포넌트를 정한 다음, 다른 컴포넌트에 영향을 주지 않고 해당 컴포넌트만 최적화할 수 있기 때문.
- 소프트웨어 재사용성을 높인다.
    - 외부에 거의 의존하지 않고 독자적으로 동작할 수 있는 컴포넌트라면 그 컴포넌트와 함께 개발되지 않은 낯선 환경에서도 유용하게 쓰일 가능성이 크기 때문.
- 큰 시스템을 제작하는 난이도를 낮춰준다.
    - 시스템 전체가 아직 완성되지 않은 상태에서도 개별 컴포넌트의 동작을 검증할 수 있기 때문.

정보 은닉의 장점 중 대부분은 시스템을 구성하는 컴포넌트들을 서로 독립시켜서 개발, 테스트, 최적화, 적용, 분석, 수정을 개별적으로 할 수 있게 해주는 것과 연관되어 있음.

---

접근 제한자(private, protected, publie)로 인해 각 요소의 접근성이 정해지는데, 접근 제한자를 제대로 활용하는 것이 정보 은닉의 핵심.

**모든 클래스와 멤버의 접근성을 가능한 한 좁혀야 한다.**

소프트웨어가 올바르게 동작하는 한, 항상 가장 낮은 접근 수준을 부여해야 한다는 뜻임.

---

톱레벨 클래스와 인터페이스에 부여할 수 있는 접근 수준은 package-private와 public 두가지임.

    톱레벨?
    
    가장 바깥이라는 의미

톱레벨 클래스, 인터페이스를 public으로 선언하면 공개 API가 되고, package-private로 선언하면 패키지 안에서만 이용 가능.

패키지 외부에서 쓸 일이 없다면 package-private 사용하기.

왜 그러는 것이 좋은가?

package-private로 선언하면 API가 아닌 내부 구현이 되어 언제든 수정할 수 있기 때문.

클라이언트에 아무런 피해 없이 다음 릴리스에서 수정, 교체, 제거할 수 있다는 뜻.

public으로 선언한다면 API가 되기 때문에, 하위 호환을 위해 영원히 관리해줘야 함.

---

한 클래스에서만 사용하는 package-private 톱레벨 클래스, 인터페이스는 이를 사용하는 클래스 안에 private static으로 중첩시키기.

톱레벨로 두면 같은 패키지의 모든 클래스가 접근할 수 있지만, private static으로 중첩시키면 바깥 클래스 하나에서만 접근 가능함.

**public일 필요가 없는 클래스의 접근 수준을 package-private 톱레벨 클래스로 좁히는 일이 중요함.**

public 클래스는 그 패키지의 API인 반면, package-private 톱레벨 클래스는 내부 구현에 속하기 때문.

---

멤버(필드, 메서드, 중첩 클래스, 중첩 인터페이스)에 부여할 수 있는 접근 수준은 네 가지임.

    중첩 클래스? 중첩 인터페이스?
    
    Nested Class, Nested Interface
    
    다른 인터페이스나 클래스 내부에 선언된 클래스와 인터페이스.
    
    Inner Class, Interface라고 불리기도 함.(내부 클래스, 내부 인터페이스)

- private: 멤버를 선언한 톱레벨 클래스에서만 접근할 수 있다.
- package-private: 멤버가 소속된 패키지 안의 모든 클래스에서 접근할 수 있다. 접근 제한자를 명시하지 않았을 때 적용되는 패키지 접근 수준이다.(단, 인터페이스의 멤버는 기본적으로 public이 적용된다.)
- protected: package-private의 접근 범위를 포함하며, 이 멤버를 선언한 클래스의 하위 클래스에서도 접근할 수 있다.
- public: 모든 곳에서 접근할 수 있다.

클래스의 공개 API를 세심히 설계한 후, 그 외 모든 멤버 private로 만들기. 

그 다음 오직 같은 패키지의 다른 클래스가 접근해야 하는 멤버에 한해 package-private로 풀어주기.

private과 package-private 멤버는 모두 해당 클래스의 구현에 해당하므로 보통은 공개 API에 영향을 주지 않음.

단, Serializable을 구현한 클래스에서 그 필드들도 의도치 않게 공개 API가 될 수 있음.

---

public 클래스에서는 멤버의 접근 수준을 package-private에서 protected로 바꾸는 순간 그 멤버에 접근할 수 있는 대상 범위가 엄청나게 넓어짐.

public 클래스의 protected 멤버는 공개 API이기 때문에 영원히 지원되어야 함.

---

멤버 접근성을 좁히지 못하게 방해하는 제약이 있음.

상위 클래스의 메서드를 재정의할 때는 그 접근 수준을 상위 클래스에서보다 좁게 설정할 수 없다는 것.

LSP(리스코프 치환 원칙)을 지키기 위해 필요한 제약임.

    LSP?
    
    상위 클래스의 인스턴스는 하위 클래스의 인스턴스로 대체해 사용할 수 있어야 한다.

위 규칙을 어기면 하위 클래스를 컴파일할 때 컴파일 오류가 남.

클래스가 인터페이스를 구현하는 건 이 규칙의 특별한 예로 볼 수 있고, 이 때 클래스는 인터페이스가 정의한 모든 메서드를 public으로 선언해야 함.

---

테스트만을 위해 클래스, 인터페이스, 멤버를 공개 API로 만들지 말것.

테스트 코드를 테스트 대상과 같은 패키지에 두면, package-private 요소에 접근할 수 있음.

---

**public 클래스의 인스턴스 필드는 되도록 public이 아니어야 함.**

필드가 가변 객체를 참조하거나, final이 아닌 인스턴스 필드를 public으로 선언하면 그 필드에 담을 수 있는 값을 제한할 힘을 잃게됨.

그 필드와 관련된 모든 것은 불변식을 보장할 수 없게 된다는 뜻.

또, 필드가 수정될 때 다른 작업을 할 수 없게 되므로, public 가변 필드를 갖는 클래스는 일반적으로 스레드 안전하지 않다.

필드가 final이면서 불변 객체를 참조하더라도, 내부 구현을 바꾸고 싶어도 그 public 필드를 없애는 방식으로는 리팩터링을 할 수 없게 된다.

예외로, 해당 클래스가 표현하는 추상 개념을 완성하는 데 꼭 필요한 구성요소로써의 상수라면 public static final 필드로 공개해도 좋음.

ex) public static final int MAX_SIZE = 16;

---

길이가 0이 아닌 배열은 모두 변경이 가능하니 주의.

클래스에서 public static final 배열 필드를 두거나, 이 필드를 반환하는 접근자 메서드를 제공해서는 안됨.

이런 필드나 접근자를 제공한다면 클라이언트에서 그 배열의 내용을 수정할 수 있게 됨.

```java
//보안 허점이 숨어있음.
public static final Thing[] VALUES = {...};
```

해결책은?

```java
//앞 코드의 public 배열을 private으로 만들고 public 불변 리스트를 추가하는 것.
private static final Thing[] VALUES = {...};
public static final List<Thing> VALUE = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
```

```java
//배열을 private으로 만들고 그 복사본을 반환하는 public 메서드를 추가하는 방법
//방어적 복사라고 불림
private static final Thing[] VALUES = {...};
public static final List<Thing> VALUE(){
    return PRIVATE_VALUES.clone();
}
```

    방어적 복사?
    
    생성자의 인자로 받은 객체의 복사본을 만들어 내부 필드를 초기화하거나,
    getter메서드에서 내부의 객체를 반환할 때, 객체의 복사본을 만들어 반환하는 것.

    방어적 복사를 사용할 경우, 외부에서 객체를 변경해도 내부의 객체는 변경되지 않는다.

    방어적 복사와 깊은 복사는 비슷해 보이지만 깊은 복사에는 문제점이 존재한다.
    깊은 복사의 clone을 사용함에 있어서 문제점이 발생하는데
    이 clone메소드가 사용자가 정의한 것이 아닐 수 있다는 점이다.
    즉, clone이 악의를 가진 하위 클래스의 인스턴스를 반환할 수도 있어 사용에 주의가 필요하다.

---

패키지 : 클래스들의 묶음.
모듈 : 패키지들의 묶음.

클래스가 패키지의 묶음이듯 모듈은 패키지의 묶음이다. 모듈은 속하는 패키지중 공개할 것들을 선언(module-info.java 파일에)한다.

protected 혹은 public 멤버라도 해당 패키지를 공개하지 않았다면 모듈 외부에서 접근이 불가능하다. 물론 모듈 안에서 공개(exports)로 선언했는지에 영향을 받지 않는다.

모듈 시스템을 사용하면 클래스를 외부에 공개하지 않고 같은 모듈을 이루는 패키지 사이에서는 자유롭게 공유할 수 있다.

앞서 다룬 4개의 기존 접근수준과 달리 모듈에 적용되는 새로운 두 접근 수준은 상당히 주의해서 사용해야 한다.

모듈의 jar파일을 애플리케이션의 클래스패스(classpath)에 두면 그 모듈안의 모든 패키지는 마치 모듈이 없는 것처럼 행동한다. 즉, 모듈이 공개됐는지 여부와 관계없이 public 클래스가 선언한 모든 public 혹은 protected 멤버를 모듈 밖에서도 접근할 수 있게 된다.

이 접근수준을 적극적으로 활용한 대표적인 예시로 JDK가 있다. 자바 라이브러리에서 공개하지 않은 패키지들은 해당 모듈 밖에서는 절대로 접근이 불가능하다.

**JDK 외에도 모듈 개념이 널리 받아들여질지 예측하기에는 아직 이른 감이 있음. 그러니 꼭 필요한 경우가 아니라면 당분간은 사용하지 않는 것이 좋음.**

---

핵심 정리

프로그램 요소의 접근성은 가능한 한 최소한으로 하라. 꼭 필요한 것만 골라 최소한의 public API를 설계하자. 그 외에는 클래스, 인터페이스, 멤버가 의도치 않게 API로 공개되는 일이 없도록 해야 한다. public 클래스는 상수용 public static final 필드 외에는 어떠한 public 필드도 가져서는 안된다. public static final 필드가 참조하는 객체가 불변인지 확인하라.

---

# public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

```java
//이런 클래스는 public이어서는 안된다
class Point{
    public double x;
    public double y;
}
```

이런 클래스는 데이터 필드에 직접 접근할 수 있으니, 캡슐화의 이점을 제공하지 못함.

API를 수정하지 않고는 내부 표현을 바꿀 수 없고, 불변식을 보장할 수 없고, 외부에서 필드에 접근할 때 부수 작업을 수행할 수 없음.

객체 지향 프로그래머는 이 상황에서 필드를 private로 바꾸고 public 접근자 getter를 추가하는 것을 선호함.

```java
//접근자와 변경자(mutator) 메서드를 사용해 데이터를 캡슐화
class Point{
    private double x;
    private double y;
    
    public Point(double x, double y){
        this.x=x;
        this.y=y;
    }
    
    public double getX(){return x;}
    public double getY(){return y;}
    public void setX(double x){this.x = x;}
    public void setY(){this.y = y;}
}
```

public 클래스에서라면 이 방식이 적합함.

패키지 바깥에서 접근할 수 있는 클래스라면 접근자를 제공함으로써 클래스 내부 표현 방식을 언제든 바꿀 수 있는 유연성을 얻을 수 있음.

**BUT**

package-private 클래스나 private 중첩 클래스라면 데이터 필드를 노출한다 해도 문제 없음.

그 클래스가 표현하려는 추상 개념만 올바르게 표현해주면 됨.

```java
public class Points {

    //private 중첩 클래스
    private class Point{
        public double x;
        public double y;
        
        public void setX(double x){this.x = x;}
        public void setY(){this.y = y;}
        
    }
    
    

    public Point getPoint(double a, double b){
        Point point = new Point();
        point.setX(a);
        point.setY(b);

        return point; 
    }
    //클라이언트 코드가 클래스 내부 표현에 묶이기는 하나,
    //클라이언트도 어차피 이 클래스를 포함하는 패키지 안에서만 동작하는 코드일 뿐임.
    //따라서 패키지 바깥 코드는 전혀 손대지 않고도 데이터 표현 방식을 바꿀 수 있음.
    //위 처럼 private 중첩 클래스일 경우 수정 범위가 더 좁아져서 이 클래스를 포함하는 외부 클래스까지로 제한됨.
}
```

---

public 클래스의 필드가 불변이라면 직접 노출할 때의 단점이 조금은 줄어들지만, 여전히 좋은 생각은 아님.

API를 변경하지 않고는 표현 방식을 바꿀 수 없고, 필드를 읽을 때 부수 작업을 수행할 수 없다는 단점은 여전함.

하지만 불변식은 보장할 수 있게 됨.

```java
//각 인스턴스가 유효한 시간을 표현함을 보장하는 클래스
//불변 필드를 노출한 public 클래스
//과연 좋은가?
public final class Time{
    private static final int HOURS_PER_DAY     =24;
    private static final int MINUTES_PER_HOUR  =60;
    
    public final int hour;
    public final int minute;
    
    public Time(int hour, int minute){
        if(hour<0||hour>=HOURS_PER_DAY)
            throw new IllegalArgumentException("시간: "+hour);
        if(minute<0||minute>=MINUTES_PER_HOUR)
            throw new IllegalArgumentException("분: "+minute);
        this.hour=hour;
        this.minute=minute;
    }
    
    ...//나머지 코드 생략
}
```

---

핵심 정리

public 클래스는 절대 가변 필드를 직접 노출해서는 안 된다. 불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다. 하지만 package-private 클래스나 private 중첩 클래스에서는 종종 (불변이든 가변이든) 필드를 노출하는 편이 나을 때도 있다.

---

# 변경 가능성을 최소화하라

    불변 클래스?
    
    그 인스턴스의 내부 값을 수정할 수 없는 클래스
    
    불변 인스턴스에 간직된 정보는 고정되어 객체가 파괴되는 순간까지 절대 달라지지 않음.

자바 플랫폼 라이브러리에도 다양한 불변 클래스가 있다.

String, 기본 타입의 박싱된 클래스들(Wrapper Class), BigInteger, BigDecimal이 이에 속함.

불변 클래스는 가변 클래스보다 설계하고 구현하고 사용하기 쉬우며, 오류가 생길 여지도 적고 훨씬 안전하기 때문에 위 클래스들을 불변으로 설계함.

---

클래스를 불변으로 만들기 위한 다섯 가지 규칙

- 객체의 상태를 변경하는 메서드(변경자)를 제공하지 않는다.
- 클래스를 확장할 수 없도록 한다.
    - 하위 클래스에서 부주의하게 혹은 나쁜 의도로 객체의 상태를 변하게 만드는 사태를 막아준다. 상속을 막는 대표적인 방법은 클래스를 final로 선언하는 것이지만, 다른 방법도 뒤에 살펴볼 것임.
- 모든 필드를 final로 선언한다.
    - 시스템이 강제하는 수단을 이용해 설계자의 의도를 명확히 드러내는 방법이다. 새로 생성된 인스턴스를 동기화 없이 다른 스레드로 건네도 문제없이 동작하게끔 보장하는 데도 필요하다.
- 모든 필드를 private로 선언한다.
    - 필드가 참조하는 가변 객체를 클라이언트에서 직접 접근해 수정하는 일을 막아준다. 기술적으로는 기본 타입 필드나 불변 객체를 참조하는 필드를 public final로만 선언해도 불변 객체가 되지만, 이렇게 하면 다음 릴리스에서 내부 표현을 바꾸지 못하므로 권하지는 않는다.
- 자신 외에는 내부 가변 컴포넌트에 접근할 수 없도록 한다.
    - 클래스에 가변 객체를 참조하는 필드가 하나라도 있다면 클라이언트에서 그 객체의 참조를 얻을 수 없도록 해야 한다. 이런 필드는 절대 클라이언트가 제공한 객체 참조를 가리키게 해서는 안 되며, 접근자 메서드가 그 필드를 그대로 반환해서도 안된다. 생성자, 접근자, readObject 메서드 모두에서 방어적 복사를 수행할 것.

---

```java
//불변 복소수 클래스
//Object의 메서드 몇개를 재정의함.
//실수부와 허수부 값을 반환하는 접근자 메서드와
//사칙연산 메서드를 정의함.
public final class Complex{
    private final double re;
    private final double im;
    
    public Complex(double re, double im){
        this.re=re;
        this.im=im;
    }
    
    public double realPart(){
        return re;
    }
    
    public double imaginaryPart(){
        return im;
    }
    
    public Complex plus(Complex c){
        return new Complex(re+c.re,im+c.im);
    }
    
    public Complex minus(Complex c){
        return new Complex(re-c.re,im-c.im);
    }
    
    public Complex times(Complex c){
        return new Complex(re*c.re-im*c.im, re*c.im+im*c.re);
    }
    
    public Complex divideBy(Complex c){
        double tmp=c.re*c.re+c.im+c.im;
        return new Complex((re*c.re+im*c.im)/tmp, (re*c.im-im*c.re)/tmp);
    }
    
    @Override
    public boolean equals(Object o){
        if(o==this)
            return true;
        if(!(o instanceof Complex))
            return false;
        Complex c= (Complex) o;
        
        //== 대신 compare를 사용
        return Double.compare(c.re,re)==0 && Double.compare(c.im,im)==0;
    }
    
    @Override 
    public int hashCode(){
        return 31*Double.hashCode(re)+Double.hashCode(im);
    }
    
    @Override
    public String toString(){
        return "("+re+" + "+im+"i)";
    }
}
```

해당 클래스에서, 사칙연산 메서드들이 인스턴스 자신은 수정하지 않고 새로운 Complex 메서드를 만들어 반환함.

이처럼 피연산자에 함수를 적용해 그 결과를 반환하지만, 피연산자 자체는 그대로인 프로그래밍 패턴을 **함수형 프로그래밍**이라고 함.

이와 달리 절차형, 명령형 프로그래밍에서는 메서드에서 피연산자인 자신을 수정해 자신의 상태가 변하게 됨.

위 복소수 클래스와 같은 방식으로 프로그래밍하면, 코드에서 불변이 되는 영역의 비율이 높아지는 장점이 있음.

---

불변 객체는 근본적으로 스레드 안전하여 따로 동기화할 필요가 없음.

클래스를 스레드 안전하게 만드는 가장 쉬운 방법이 불변 객체로 만드는 것임.

불변 객체에 대해서는 그 어떤 스레드도 다른 스레드에 영향을 줄 수 없으니 불변 객체는 안심하고 공유할 수 있다.

따라서 불변 클래스라면 한번 만든 인스턴스를 최대한 재활용하기를 권함.

ex)자주 쓰이는 값들을 상수(public static final)로 제공하기

```java
public static final Complex ZERO = new Complex(0,0);
public static final Complex ONE  = new Complex(1,0);
public static final Complex I    = new Complex(0,1);
```

한걸음 더 나아가면, 불변 클래스는 자주 사용되는 인스턴스를 캐싱하여 같은 인스턴스를 중복 생성하지 않게 해주는 정적 팩터리를 제공 가능함.

박싱된 기본 타입 클래스 전부(Wrapper Class), BigInteger가 여기 속함.

이런 정적 팩터리를 사용하면, 여러 클라이언트가 인스턴스를 공유하기 때문에 메모리 사용량과 가비지 컬렉션 비용이 줄어듬.

새로운 클래스를 설꼐할 때 public 생성자 대신 정적 팩터리를 만들어두면 클라이언트를 수정하지 않고도 필요에 따라 캐시 기능을 나중에 덧붙일 수 있음.

<br>

불변 객체를 자유롭게 공유할 수 있다는 점은 방어적 복사도 필요 없다는 결론으로 이어짐.

그러니 불변 클래스에서는 clone 메서드나 복사 생성자(챕터 3 clone 참조)를 제공하지 않는 게 좋음.

---

불변 객체는 자유롭게 공유할 수 있음.

불변 객체끼리는 내부 데이터를 공유할 수 있음.

BigInteger 클래스는 내부에서 값의 부호(sign)와 크기(magnitude)를 따로 표현함.

부호에는 int변수, 크기에는 int배열을 사용함.

negate 메서드는 크기가 같고 부호만 반대인 새로운 BigInteger를 생성하는데, 이때 배열은 가변이지만 복사하지 않고 원본 인스턴스와 공유해도 됨.

그 결과 새로 만든 BigInteger 인스턴스도 원본 인스턴스가 가리키는 내부 배열을 그대로 가리킴.

<br>

객체를 만들 때 다른 불변 객체들을 구성요소로 사용하면 이점이 많다.

불변식을 유지하기 훨씬 수월하기 때문

ex) 불변 객체는 맵의 키와 집합(Set)의 원소로 사용하기 좋음.

<br>

불변 객체는 그 자체로 실패 원자성을 제공한다.

    실패 원자성?
    
    메서드에서 예외가 발생한 후에도 그 객체는 여전히 유효한 상태여야 한다.
    
    ACID의 원자성

상태가 절대 변하지 않으니 잠깐이라도 불일치 상태에 빠질 가능성이 없음.

---

불변 클래스의 단점

값이 다르면 반드시 독립된 객체로 만들어야 함.

값의 가짓수가 많다면 비용이 너무 많이 들어감.

```java
//BigInteger 예제
BigInteger moby = ...;
moby=moby.flipBit(0);

//flipBit 메서드는 새로운 BigInteger 인스턴스를 생성함.
//원본과 단지 한 비트만 달라도 새로운 백만 비트짜리 인스턴스를 만듬.
```

```java
//BitSet 예제
BitSet moby = ...;
moby=moby.flipBit(0);

//BitSet은 BigInteger와 달리 가변임.
//원하는 비트 하나만 상수 시간 안에 바꿔주는 메서드를 제공함.
```

원하는 객체를 완성하기까지의 단계가 많고, 그 중간 단계에서 만들어진 객체들이 모두 버려진다면 성능 문제가 더 심해질 것.

이 문제에 대처하는 두 가지 방안.

- 다단계 연산 예측이 된다면? 
    - 연산 속도를 높여주는 가변 동반 클래스를 package-private으로 두기
- 예측이 안된다면?
    - 가변 동반 클래스를 public으로 제공하기.
    - ex) String과 가변 동반 클래스 StringBuilder

---

앞으로 불변 클래스를 만드는 또 다른 설계 방법을 설명함.

<br>

클래스가 불변임을 보장하려면 자신을 상속하지 못하게 해야 함.

자신을 상속하지 못하게 하는 가장 쉬운 방법은 final 클래스로 선언하는 것,

하지만 더 유연한 방법이 있음.

모든 생성자를 private 혹은 package-private으로 만들고 public 정적 팩터리를 제공하는 방법.

```java
//생성자 대신 정적 팩터리를 사용한 불변 클래스
public class Complex{
    private final double re;
    private final double im;
    
    private Complex(double re, double im){
        this.re=re;
        this.im=im;
    }
    
    public static Complex valueOf(double re, double im){
        return new Complex(re,im);
    }
    
    ...//나머지 생략
}
```

바깥에서 볼 수 없는 package-private 구현 클래스를 원하는 만큼 만들어 활용할 수 있으니 훨씬 유연함.

패키지 바깥의 클라이언트에서 바라본 이 불변 객체는 사실상 final임.

public이나 protected 생성자가 없으니 다른 패키지에서는 이 클래스를 확장하는게 불가능하기 때문임.

---

신뢰할 수 없는 하위 클래스의 인스턴스라고 확인되면, 이 인수들은 가변이라 가정하고 방어적으로 복사해 사용해야 한다.

```java
//방어적 복사
public static BigInteger safeInstance(BigInteger val){
    return val.getClass()==BigInteger.class ? val:new BigInteger(val.toByteArray());
}
```

---

초반 불변 클래스의 규칙에, 모든 필드가 final이고 어떤 메서드도 그 객체를 수정할 수 없어야 한다 했는데,

이 규칙은 좀 과한 감이 있어서 다음처럼 살짝 완화될 수 있음.

"어떤 메서드도 객체의 상태 중 외부에 비치는 값을 변경할 수 없다."

---

정리

게터가 있다고 무조건 세터를 만들지는 말자.

클래스는 꼭 필요한 경우가 아니라면 불변이어야 한다.

불변클래스는 장점이 많으며, 단점은 특정 상황에서의 잠재적 성능 저하 뿐이다.

단순한 값 객체(PhoneNumber, Complex 등등)은 항상 불변으로 만들자.

String과 BigInteger처럼 무거운 값 객체도 불변으로 만들 수 있는지 생각해보자.

성능 때문에 어쩔 수 없다면 불변 클래스와 쌍을 이루는 가변 동반 클래스를 public 클래스로 제공하자.

<br>

모든 클래스를 불변으로 만들 수는 없다.

불변으로 만들 수 없는 클래스라도 변경할 수 있는 부분을 최소한으로 줄이자.

그래야 그 객체를 예측하기 쉬워지고 오류가 생길 가능성이 낮아진다.

그러니 꼭 변경해야 할 필드를 뺀 나머지 모두를 final로 선언하자.

**다른 합당한 이유가 없다면 모든 필드는 private final이어야 한다**

<br>

생성자는 불변식 설정이 모두 완료된, 초기화가 완벽히 끝난 상태의 객체를 생성해야 한다.

확실한 이유가 없다면 생성자와 정적 팩터리 외에는 그 어떤 초기화 메서드도 public으로 제공하지 말것.

객체를 재활용할 목적으로 상태를 다시 초기화하는 메서드도 쓰지 말자.

복잡성만 커지고 성능 이점은 거의 없다.

---

# 상속보다는 컴포지션을 사용하라

상속은 코드를 재사용하는 강력한 수단이지만, 항상 최선은 아님.

잘못 사용하면 오류를 내기 쉬운 소프트웨어를 만들게 됨.

상위 클래스와 하위 클래스를 모두 같은 프로그래머가 통제하는 패키지 안에서라면 상속도 안전함.

<br>

확장할 목적으로 설계되었고 문서화도 잘 된 클래스도 안전함.

하지만 다른 패키지의 구체 클래스를 상속하는 일은 위험함.

---

메서드 호출과 달리 상속은 캡슐화를 깨트림.

상위 클래스가 어떻게 구현되느냐에 따라 하위 클래스의 동작에 이상이 생길 수 있다는 뜻.

상위 클래스 설계자가 확장을 충분히 고려하고 문서화도 제대로 해두지 않으면, 하위 클래스는 상위 클래스의 변화에 따라 수정되어야만 함.

<br>

```java
//HashSet을 사용하는 프로그램
//성능을 높이기 위해 처음 생성된 이후 원소가 몇 개 더해졌는지 알 수 있어야 함.
//그 메서드 추가
//add, addAll 재정의
public class InstrumentedHashSet<E> extends HashSet<E>{
    //추가된 원소의 수
    private int addCount=0;
    
    public InstrumentedHashSet(){
    }
    
    public InstrumentedHashSet(int initCap, float loadFactor){
        super(initCap, loadFactor);
    }
    
    @Override
    public boolean add(E e){
        addCount++;
        return super.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c){
        addCount+=c.size();
        return super.addAll(c);
    }
    
    public int getAddCount(){
        return addCount;
    }
}
```

위 클래스는 잘 구현된 것 처럼 보이지만, 제대로 작동하지 않음.

```java
//addAll 메서드로 원소 3개 더하기
//정적 팩터리 메서드 List.of로 리스트 생성
InstrumentedHashSet<String> s=new InstrumentedHashSet<>();
s.addAll(List.of("틱","틱틱","펑"));
```

getAddCount 메서드를 호출하면 3을 반환할 것 같지만, 실제로는 6을 반환함.

HashSet의 addAll 메서드가 add 메서드를 사용해 구현되었기 때문임.

addCount 값이 중복해서 더해져 이렇게 됨.

이 경우 하위 클래스에서 addAll 메서드를 재정의하지 않으면 문제를 고칠 수 있음.

하지만 HashSet의 addAll이 add 메서드를 이용해 구현했음을 가정한 해법이라는 한계를 지님.

<br>

addAll 메서드를 다른 색으로 재정의할 수도 있음.

주어진 컬렉션을 순회하며 원소 하나당 add 메서드를 한 번만 호출하는 것.

HashSet이 addAll을 더 이상 호출하지 않아 항상 결과가 옳다는 점에서 보았을 때 더 낫지만, 문제가 남아있음.

상위 클래스의 메서드 동작을 다시 구현하는 이 방식은 어렵고 시간도 더 들고 오류를 내거나 성능을 떨어트릴 수도 있음.

하위 클래스에서는 접근할 수 없는 private 필드를 써야 하는 상황이라면 이 방식으로는 구현이 불가함.

---

하위 클래스가 깨지기 쉬운 이유는 더 있음.

<br>

보안 때문에 컬렉션에 추가된 모든 원소가 특정 조건을 만족해야만 하는 프로그램을 예로 듬.

그 컬렉션을 상속하여 원소를 추가하는 모든 메서드를 재정의해 필요한 조건을 먼저 검사하게끔 하면 될 것 같지만, 상위 클래스에서 또 다른 원소 추가 메서드가 만들어진다면 하위 클래스에서 재정의하지 못한 그 새로운 메서드를 사용해 허용되지 않은 원소를 추가할 수 있게 됨.

---

위 사례들을 보고 생각해보면 메서드를 재정의하는 대신 새로운 메서드를 추가하면 괜찮으리라는 생각이 듬.

이 방식이 훨씬 안전하긴 하지만, 위험이 전혀 없는것은 아님.

다음 릴리즈에서 상위 클래스에 새 메서드가 추가됐는데, 하위 클래스에 추가한 메서드와 시그니처가 같고 반환 타입이 다르다면 컴파일 실패함.

반환타입마저 같다면 재정의와 같으니 앞선 문제를 다시 만나게 됨.

또한 상위 메서드가 요구하는 규약을 지키지 못할 가능성이 큼.

---

그렇다면 해법은?

기존 클래스를 확장하는 대신, 새로운 클래스를 만들고 private 필드로 기존 클래스의 인스턴스를 참조하게 하기.

기존 클래스가 새로운 클래스의 구성 요소로 쓰인다는 뜻에서 이러한 설계를 **컴포지션(composition; 구성)** 이라 함.

새 클래스의 인스턴스 메서드들은 private 필드로 참조하는 기존 클래스의 대응하는 메서드를 호출해 그 결과를 반환함.

이 방식을 전달(forwarding)이라 하며, 새 클래스의 메서드들을 전달 메서드(forwarding method)라 부름.

그 결과 새로운 클래스는 기존 클래스의 내부 구현 방식의 영향에서 벗어나며, 기존 클래스에 새로운 메서드가 추가되더라도 전혀 영향받지 않음.

```java
//래퍼 클래스
//상속 대신 컴포지션을 사용함.
public class InstrumentedSet<E> extends ForwardingSet<E>{
    private int addCount=0;
    
    public InstrumentedSet(Set<E> s){
        super(s);
    }
    
    @Override
    public boolean add(E e){
        addCount++;
        return super.add(e);
    }
    
    @Override
    public boolean addAll(Collection<? extends E> c){
        addCount+=c.size();
        return super.addAll(c);
    }
    
    public int getAddCount(){
        return addCount;
    }
    
}
```

```java
//재사용할 수 있는 전달 클래스
public class ForwardingSet<E> implements Set<E>{
    private final Set<E> s;
    
    public ForwardingSet(Set<E> s){
        this.s=s;
    }
    
    public void clear(){s.clear();}
    public boolean contains(Object o){return s.contains(o);}
    public boolean isEmpty(){return s.isEmpty();}
    public int size(){return s.size();}
    public Iterator<E> iterator(){return s.iterator();}
    public boolean add(E e){return s.ad(e);}
    public boolean remove(Object o){return s.remove(o)};
    public boolean containsAll(Collection<?> c){return s.conatinsAll(c);}
    public boolean addAll(Collection<? extends E> c){return s.addAll(c);}
    public boolean removeAll(Collection<?> c){return s.removeAll(c);}
    public boolean retainAll(Collection<?> c){return s.retainAll(c);}
    public Object[] toArray(){return s.toArray();}
    public <T> T[] toArray(T[] a){return s.toArray(a);}
    @Override
    public booelan equals(Object o){return s.equals(o);}
    @Override
    public int hashCode(){return s.hashCode();}
    @Override
    public String toString(){return s.toString();}
}
```

임의의 Set에 계측 기능을 덧씌워 새로운 Set으로 만드는 것이 이 클래스의 핵심임.

상속 방식과 다르게, 이 컴포지션 방식은 한번만 구현해두면 어떠한 Set 구현체라도 계측할 수 있으며 기존 생성자들과도 함께 사용할 수 있다.

    Set<Instant> times=new InstrumentedSet<>(new TreeSet<>(cmp));
    Set<E> s=new InstrumentedSet<>(new HashSet<>(INIT_CAPACITY));
    
    //InstrumentedSet을 이용하면 대상 Set 인스턴스를 특정 조건하에서만 임시로 계측할 수 있음.
    
    static void walk(Set<Dog> dogs){
        InstrumentedSet<Dog> iDogs=new InstrumentedSet<>(dogs);
        ...//이 메서드에서는 dogs 대신 iDogs 사용
    }

다른 Set 인스턴스를 감싸고(wrap) 있다는 뜻에서 InstrumentedSet같은 클래스를 래퍼 클래스라고 함.

다른 Set에 계측 기능을 덧씌운다는 뜻에서 데코레이터 패턴(디자인 패턴)이라고 함.

---

래퍼 클래스는 딱히 단점이 없지만, 한가지 주의할 점

래퍼 클래스는 콜백 프레임워크와는 어울리지 않음.

콜백 프레임워크에서는 자기 자신의 참조를 다른 객체에 넘겨서 다음 호출때 사용하도록 함.

내부 객체는 자신을 감싸고 있는 래퍼의 존재를 모르니 대신 자신(this)의 참조를 남기고, 콜백 때는 래퍼가 아닌 내부 객체를 호출하게 됨.

이를 SELF 문제라고 함.

---

상속은 반드시 하위 클래스가 상위 클래스의 진짜 하위 타입인 상황에서만 쓰여야 함.

클래스 B가 클래스 A와 is-a 관계일 때에만 클래스 A를 상속해야 한다는 뜻.

클래스 A를 상속하는 클래스 B를 작성하려 한다면, B가 정말 A인가? 라고 자문해보기

A는 B의 필수 구성요소가 아니라, 구현하는 방법중 하나일 뿐

<br>

컴포지션을 써야 할 상황에서 상속을 사용하는 건 내부 구현을 불필요하게 노출하는 꼴임.

그 결과 API가 내부 구현에 묶이고 그 클래스의 성능도 영원히 제한됨.

게다가 클라이언트가 노출된 내부에 직접 접근할 수 있다는 문제가 있음.

사용자를 혼란스럽게 할 수 있다는 뜻.

예를 들어 Properties의 인스턴스 p가 있을 때, p.getProperties(key)와 p.get(key)는 결과가 다를 수 있음.

전자가 Properties의 기본 독작인 데 반해 후자는 Properties의 상위 클래스인 Hashtable로부터 물려받은 메서드이기 때문.

---

컴포지션 대신 상속을 사용하기로 결정하기 전 마지막으로 자문해야 할 것

확장하려는 클래스의 API에 아무런 결함이 없는가?

결함이 있다면, 이 결함이 내 클래스의 API까지 전파되어도 괜찮은가?

컴포지션으로는 이런 결함을 숨기는 새로운 API를 설계할 수 있지만 상속은 결함까지도 그대로 승계함.

---

핵심 정리

상속은 강력하지만 캡슐화를 해친다는 문제가 있다. 상속은 상위 클래스와 하위 클래스가 순수한 is-a 관계일 때만 써야 한다. is-a 관계일 때도 안심할 수만은 없는 게, 하위 클래스의 패키지가 상위 클래스와 다르고, 사우이 클래스가 확장을 고려해 설꼐되지 않았다면 여전히 문제가 될 수 있다. 상속의 취약점을 피하려면 상속 대신 컴포지션과 전달을 사용하자. 특히 래퍼 클래스로 구현할 적당한 인터페이스가 있다면 더욱 그렇다. 래퍼 클래스는 하위 클래스보다 견고하고 강력하다.

---

# 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라

저번 단락에서 상속을 염두에 두지 않고 설계했고 상속할 때의 주의점도 문서화해놓지 않은 **외부** 클래스를 상속할 때의 위험을 경고했음.

여기서 외부란?

프로그래머의 통제권 밖에 있어서 언제 어떻게 변경될 지 모른다는 뜻.

---

그렇다면 상속을 고려한 설계와 문서화란 무엇을 뜻하는 것인가?

---

메서드를 재정의하려면 어떤 일이 일어나는지를 정확히 정리하여 문서로 남겨야 함.

상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지(자기사용) 문서로 남겨야 함.

재정의 가능 메서드를 호출할 수 있는 모든 상황을 문서로 남겨야 함.

ex) 백그라운드 스레드나 정적 초기화 과정에서도 호출이 일어날 수 있음.

---

API 문서의 메서드 설명 끝에서 종종 Implementation Requirements로 시작하는 절을 볼 수 있음.

그 메서드의 내부 동작 방식을 설명하는 곳임.

내부 동작 방식을 잘 설명해도, 상속이 캡슐화를 해친다는 점 때문에

좋은 API 문서란 '어떻게' 가 아닌 '무엇' 을 하는지를 설명해야 한다는 격언과 대치됨.

이처럼 내부 메커니즘을 문서로 남기는 것만이 상속을 위한 설계의 전부는 아님.

효율적인 하위 클래스를 큰 어려움 없이 만들 수 있게 하려면 클래스의 내부 동작 과정 중간에 끼어들 수 있는 훅(hook)을 잘 선별하여 protected 메서드 형태로 공개해야 할 수도 있음.

    훅(hook)?
    
    후크(Hook) 는 추상 클래스에 들어있는, 아무 일도 하지 않거나 기본 행동을 정의하는 메소드로, 서브 클래스에서 오버라이드 할 수 있다.

상속용 클래스를 설계할 때 어떤 메서드를 protected로 노출해야 할까?

잘 생각해서 실제 하위 클래스를 만들어 시험해 보는 것이 최선임.

상속용 클래스를 시험하는 방법은 직접 하위 클래스를 만들어 보는 것이 유일함.

그러니 상속용으로 설계한 클래스는 배포 전에 반드시 하위 클래스를 만들어 검증해야 함.

---

상속용 클래스의 생성자는 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안됨.

상위 클래스의 생성자가 하위 클래스의 생성자보다 먼저 실행되므로, 하위 클래스에서 재정의한 메서드가 하위 클래스의 생성자보다 먼저 호출되게 된다.

```java
public class Super{
    //잘못된 예
    //생성자가 재정의 가능 메서드를 호출함
    public Super(){
        overrideMe();
    }
    
    public void overrideMe(){
    }
}
```

```java
//overrideMe 메서드를 재정의한 하위 클래스 코드
public final class Sub extends Super{
    //초기화되지 않은 final 필드, 생성자에서 초기화함.
    private final Instant instant;
    
    Sub(){
        instant=Instant.now();
    }
    
    //재정의 가능 메서드
    //상위 클래스의 생성자가 호출함.
    @Override
    public void overrideMe(){
        System.out.println(instant);
    }
    
    public static void main(String[] args){
        Sub sub=new Sub();
        sub.overrideMe();
    }
}
```

이 프로그램이 instant를 두번 출력할 것 같지만, 첫 번째에는 null을 출력함.

상위 클래스의 생성자는 하위 클래스의 생성자가 인스턴스 필드를 초기화하기도 전에 overrideMe를 호출하기 때문임.

이 상황에서는 final 필드의 상태가 두 가지임에 주목할 것.(정상일 경우에는 하나뿐이어야 함.)

println이 null 입력값도 받아들이기 때문에 NullPointerException이 일어나지 않은 것. 다른 경우라면 일어났어야 함.

**private, final, static 메서드는 재정의가 불가능하니 생성자에서 안심하고 호출해도 됨.**

---

Cloneable과 Serializable 인터페이스는 상속용 설계의 어려움을 한층 더해줌.

둘 중 하나라도 구현한 클래스를 상속할 수 있게 설계하는 것은 일반적으로 좋지 않음.

clone과 readObject 메서드는 생성자와 비슷한 효과를 내기 때문에, 상속용 클래스에서 Cloneable이나 Serializable을 구현할지 정해야 한다면 이들을 구현할 때 따르는 제약도 생성자와 비슷하다는 점에 주의할 것.

즉 clone과 readObject 모두 직접적으로든 간접적으로든 재정의 가능 메서드를 호출해서는 안된다는 뜻임.

<br>

Serializable을 구현한 상속용 클래스가 readResolve나 writeReplace 메서드를 갖는다면 이 메서드들을 private가 아닌 protected로 선언해야 함.

private로 선언한다면 하위 클래스에서 무시되기 때문.

이 역시 상속을 허용하기 위해 내부 구현을 클래스 API로 공개하는 예 중 하나임.

---

위와 같이 클래스를 상속용으로 설계하려면 엄청난 노력이 들고 그 클래스에 안기는 제약도 상당함.

---

상속용이 아닌 일반 구체 클래스들 또한 상속하면 클래스에 변화가 생길 때마다 하위 클래스를 오동작하게 만들 수 있기 때문에 좋은 선택이 아님.

이 문제를 해결하는 가장 좋은 방법은 상속용으로 설계하지 않은 클래스는 상속을 금지하는 것임.

---

상속을 금지하는 방법

- 클래스를 final로 설정하기
- 모든 생성자를 private나 package-private로 선언하고, public 정적 팩터리를 만들어주기.

핵심 기능을 정의한 인터페이스가 있고, 클래스가 그 인터페이스를 구현했다면 상속을 금지해도 개발하는 데 아무런 어려움이 없음.

ex) Set, List, Map

래퍼 클래스 패턴도 적절한 대안이 될 수 있음.

<br>

구체 클래스가 표준 인터페이스를 구현하지 않았는데 상속을 금지하면 사용하기에 상당히 불편해짐.

이런 클래스라도 상속을 꼭 허용해야겠다면 합당한 방법이 하나 있음.

클래스 내부에서는 재저으이 가능 메서드를 사용하지 않게 만들고 이 사실을 문서로 남기는 것.

재정의 가능 메서드를 사용하지 않게 만들고, 이 사실을 문서로 남기는 것임.

이렇게 하면 메서드를 재정의해도 다른 메서드의 동작에 아무런 영향을 주지 않기 때문에, 상속해도 그리 위험하지 않은 클래스를 만들 수 있음.

---

핵심 정리

상속용 클래스를 설계하기란 결코 만만치 않다. 클래스 내부에서 스스로를 어떻게 사용하는지 모두 문서로 남겨야 하며, 일단 문서화한 것은 그 클래스가 쓰이는 한 반드시 지켜야 한다. 그러지 않으면 그 내부 구현 방식을 믿고 활용하던 하위 클래스를 오동작하게 만들 수 있다. 다른 이가 효율 좋은 하위 클래스를 만들 수 있도록 일부 메서드를 protected로 제공해야 할 수도 있다. 그러니 클래스를 확장해야 할 명확한 이유가 떠오르지 않으면 상속을 금지하는 편이 나을 것이다. 상속을 금지하려면 클래스를 final로 선언하거나 생성자 모두를 외부에서 접근할 수 없도록 해야 한다.

---

# 추상 클래스보다는 인터페이스를 우선하라

자바가 제공하는 다중 구현 메커니즘은 인터페이스와 추상 클래스 두 가지임.

둘의 가장 큰 차이는?

추상 클래스가 정의한 타입을 구현하는 클래스는 반드시 추상 클래스의 하위 클래스가 되어야 한다는 점.

자바는 단일 상속만 지원하니, 추상 클래스 방식은 새로운 타입을 정의하는 데 커다란 제약을 안게 되는 셈임.

반면 인터페이스가 선언한 메서드를 모두 정의하고 그 일반 규약을 잘 지킨 클래스라면, 다른 어떤 클래스를 상속했든 같은 타입으로 취급됨.

---

기존 클래스에서도 손쉽게 새로운 인터페이스를 구현해놓을 수 있음.

인터페이스가 요구하는 메서드를 추가하고, 클래스 선언에 implements 구문만 추가하면 됨.

<br>

하지만 기존 클래스ㅇ 위에 새로운 추상 클래스를 끼워넣기는 일반적으로 어려움.

---

인터페이스는 믹스인(mixin) 정의에 안성맞춤임.

    믹스인이란?
    
    클래스가 구현할 수 있는 타입으로,
    
    믹스인을 구현한 클래스에 원래의 '주된 타입' 외에도 특정 선택적 행위를 제공한다고 선언하는 효과를 준다.
    
    ex) Comparable
    
    이처럼 대상 타입의 주된 기능에 선택적 기능을 혼합한다고 해서 믹스인이라 부름.

---

인터페이스로는 계층구조가 없는 타입 프레임워크를 만들 수 있음.

```java
public interface Singer{
    AudioClip sing(Song s);
}

public interface SongWriter{
    Song compose(int chartPosition);
}
```

위 코드처럼 타입을 인터페이스로 정의하면 가수 클래스가 Singer와 Songwriter 모두를 구현해도 전혀 문제되지 않음.

심지어 Singer와 Songwriter 모두를 확장하고 새로운 메서드까지 추가한 제 3의 인터페이스를 정의할 수도 있음.

```java
public interface SingerSongWriter extends Singer,SongWriter{
    AudioClip strum();
    void actSensitive();
}
```

---

래퍼 클래스 관용구와 함께 사용하면 인터페이스는 기능을 향상이키는 안전하고 강력한 수단이 된다.

타입을 추상 클래스로 정의해두면 그 타입에 기능을 추가하는 방법은 상속뿐임.

상속해서 만든 클래스는 래퍼 클래스보다 활용도가 떨어지고 깨지기는 더 쉬움.

---

인터페이스 메서드 중 구현 방법이 명백한 것이 있다면, 그 구현을 디폴트 메서드로 제공할 수 있음.

디폴드 메서드를 제공할 때는 상속하려는 사람을 위한 설명을 @implSpec 자바독 태그를 붙여 문서화 해야함.

디폴트 메서드의 제약

- 많은 인터페이스가 equals와 hashCode같은 Object 메서드를 정의하고 있지만, 이들은 디폴트 메서드로 제공해서는 안됨.
- 인터페이스는 인스턴스 필드를 가질 수 없고 public이 아닌 정적 멤버도 가질 수 없다.(단 private 정적 메서드는 예외임)
- 우리가 만들지 않은 인터페이스에는 디폴트 메서드를 추가할 수 없다.

<br>

인터페이스와 추상 골격 구현(skeletal implementation) 클래스를 함께 제공하는 식으로 인터페이스와 추상 클래스의 장점을 모두 취하는 방법도 있음.

인터페이스로는 타입을 정의, 필요하다면 디폴트 메서드 몇개도 제공.

골격 구현 클래스는 다머지 메서드들까지 구현함.

이렇게 해두면 단순히 골격 구현을 확장하는 것만으로 이 인터페이스를 구현하는 데 필요한 일이 대부분 완료됨.

이를 템플릿 메서드 패턴이라 부름.(디자인 패턴)

---

제대로 설계했다면 골격 구현은 그 인터페이스로 나름의 구현을 만들려는 프로그래머의 일을 상당히 덜어줌.

```java
//골격 구현을 사용해 완성한 구체 클래스
static List<Integer> intArrayAsList(int[] a){
    Objects.requireNonNull(a);
    
    return new AbstractList<>(){
        @Override
        public Integer get(int i){
            return a[i];//오토박싱
        }
        
        @Override
        public Integer set(int i, Integer val){
            int oldVal=a[i];
            a[i]=val;//오토박싱
            return oldVal;//오토언박싱
        }
        
        @Override
        public int size(){
            return a.length;
        }
    };
}
```

이 예는 int 배열을 받아 Integer 인스턴스의 리스트 형태로 보여주는 어댑터(디자인 패턴)이기도 함.

int값과 Integer 인스턴스 사이의 변환 때문에 성능은 그리 좋지 않음.

---

골격 구현 클래스는 추상 클래스처럼 구현을 도와주기도 하고, 추상 클래스로 타입을 정의할 때 따라오는 심각한 제약에서 자유로움.

골격 구현을 확장하는 것으로 인터페이스 구현이 거의 끝나지만, 꼭 이렇게 해야하는 것은 아님.

구조상 골격 구현을 확장하지 못하는 처지라면 인터페이스를 직접 구현해야 함.

이런 경우라도 디폴트 메서드의 이점을 여전히 누릴 수 있음.

    시뮬레이트한 다중 상속(simulated multiple inheritance)?
    
    골격 구현 클래스를 우회적으로 이용
    
    인터페이스를 구현한 클래스에서 해당 골격 구현을 확장한 private 내부 클래스를 정의하고,
    각 메서드 호출을 내부 클래스의 인스턴스에 전달하는 것.

---

골격 구현 작성은 상대적으로 쉬움.

1. 인터페이스를 잘 살펴 다른 메서드들의 구현에 사용되는 기반 메서드들을 선정.
2. 기반 메서드들을 사용해 직접 구현할 수 있는 메서드를 모두 디폴트 메서드로 제공.

만약 인터페이스의 메서드 모두가 기반 메서드와 디폴트 메서드가 된다면, 골격 구현 클래스를 별도로 만들 이유가 없음.

기반 메서드나 디폴트 메서드로 만들지 못한 메서드가 남아 있다면 이 인터페이스를 구현하는 골격 구현 클래스를 하나 만들어 남은 메서드들을 작성해 넣기.

```java
//골격 구현 클래스
//Map.Entry 인터페이스
//getKey, getValue는 기반 메서드
//Object 메서드들은 디폴트 메서드로 제공해서는 안 되므로
//해당 메서드들은 모두 골격 구현 클래스에 구현함.
public abstract class AbstractMapEntry<K,V> implements Map.Entry<K,V>{
    
    //변경 가능한 엔트리는 이 메서드를 반드시 재정의해야 한다
    @Override
    public V setValue(V value){
        throw new UnsupportedOperationException();
    }
    
    //Map.Entry.equals의 일반 규약을 구현
    @Override 
    public boolean equals(Object o){
        if(o==this)
            return true;
        if(!(o instanceof Map.Entry))
            return false;
        Map.Entry<?,?> e=(Map.Entry) o;
        return Objects.equals(e.getKey(),getKey()) && Objects.equals(e.getValue(), getValue());
    }
    
    //Map.Entry.hashCode의 일반 규약 구현
    @Override
    public int hashCode(){
        return Objects.hahsCode(getKey()) ^ Objects.hashCode(getValue());
    }
    
    @Override
    public String toString(){
        return getKey()+"="+getValue();
    }
}

//Map.Entry 인터페이스나 그 하위 인터페이스로는 이 골격 구현을 제공할 수 없다.
//디폴트 메서드는 equals, hashCode, toString같은 Object 메서드를 재정의할 수 없기 때문이다.

```

---

골격 구현은 기본적으로 상속해서 사용하는 걸 가정하므로, 윗 단락에서 이야기한 설계 및 문서화 지침을 모두 따라야 함.

단순 구현(simple implementation)은 골격 구현의 작은 변종으로, 골격 구현과 같이 상속을 위해 인터페이스를 구현한 것이지만 추상 클래스가 아니란 점이 다름.

쉽게 말해 동작하는 가장 단순한 구현임.

---

핵심 정리

일반적으로 다중 구현용 타입으로는 인터페이스가 가장 적합하다. 복잡한 인터페이스라면 구현하는 수고를 덜어주는 골격 구현을 함께 제공하는 방법을 꼭 고려해보자. 골격 구현은 가능한 한 인터페이스의 디폴트 메서드로 제공하여 그 인터페이스를 구현한 모든 곳에서 활용되도록 하는 것이 좋다. 가능한 한이라고 한 이유는 인터페이스에 걸려 있는 구현상의 제약 때문에 골격 구현을 추상 클래스로 제공하는 경우가 더 흔하기 때문이다.

---

# 인터페이스는 구현하는 쪽을 생각해 설계하라

디폴트 메서드를 선언하면, 그 인터페이스를 구현한 후 디폴트 메서드를 재정의하지 않은 모든 클래스에서 디폴트 구현이 쓰이게 됨.

    default method? 
    
    인터페이스에 있는 구현 메서드를 의미함.

    기존의 추상 메서드와 다른 점?

    메서드 앞에 default 예약어를 붙인다.
    구현부 {} 가 있어야 한다.
    
    default method Interface 예제 코드

    public interface Interface {
        // 추상 메서드 
        void abstractMethodA();
        void abstractMethodB();
        void abstractMethodC();

	    // default 메서드
        default int defaultMethodA(){
    	    ...
        }
    }
    
자바 라이브러리의 디폴트 메서드는 코드 품질이 높고 넘용적이라 대부분 상황에서 잘 동작함.

하지만 생각할 수 있는 모든 상황에서 불변식을 해치지 않는 디폴트 메서드를 작성하기 어려움.

```java
//자바 8의 Collection 인터페이스에 추가된 디폴트 메서드
//
//주어진 boolean 함수(predicate;프레디키트)가 true를 반환하는 모든 원소를 제거.
//디폴트 구현은 반복자를 이용해 순회하면서 각 원소를 인수로 넣어 프레디키트를 호출하고,
//프레디키트가 true를 반환하면 반복자의 remove 메서드를 호출해 그 원소를 제거함.
default boolean removeIf(Predicate<? super E> filter){
    Objects.requireNonNull(filter);
    boolean result=false;
    for(Iterator<E> it=iterator();it.hasNext();){
        if(filter.test(it.next())){
            it.remove();
            result=true;
        }
    }
    return result;
}
```

좋은 코드이지만, 현존하는 모든 Collection 구현체와 잘 어우러지는 것은 아님.

---

디폴트 메서드는 컴파일에 성공하더라도 기존 구현체에 런타임 오류를 일으킬 수 있음.

기존 인터페이스에 디폴트 메서드로 새 메서드를 추가하는 일은 꼭 필요한 경우가 아니라면 피할 것.

새로운 인터페이스를 만드는 경우라면 표준적인 메서드 구현을 제공하는 데 아주 유용한 수단이고, 그 인터페이스를 더 쉽게 구현해 활용할 수 있게끔 해줌.

<br>

디폴트 메서드는 인터페이스로부터 메서드를 제거하거나 기존 메서드의 시그니처를 수정하는 용도가 아님을 명심할 것

---

인터페이스를 설계할 때는 여전히 세심한 주의를 기울여야 함.

디폴트 메서드로 기존 인터페이스에 새로운 메서드를 추가하면 커다란 위험이 딸려 옴.

새로운 인터페이스라면 릴리스 전에 반드시 테스트를 거쳐야 함.

인터페이스를 서로 다른 방식으로 최소 세 가지는 구현해 볼 것.

각 인터페이스의 인스턴스를 다양한 작업에 활용하는 클라이언트도 여러 개 만들어볼 것.

인터페이스를 릴리스한 후라도 결함을 수정하는 게 가능한 경우도 있겠지만, 그 가능성에 기대지는 말 것.

---

# 인터페이스는 타입을 정의하는 용도로만 사용하라

인터페이스는 자신을 구현한 클래스의 인스턴스를 참조할 수 있는 타입 역할을 함.

클래스가 어떤 인터페이스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에 얘기해주는 것임.

인터페이스는 오직 이 용도로만 사용해야 함.

---

```java
//상수 인터페이스 안티패턴
//인터페이스를 잘못 사용한 예
//
//상수 인터페이스란?
//메서드 없이 상수를 뜻하는 static final 필드로만 가득 찬 인터페이스
public interface PhysicalConstants{
	//아보가드로 수(1/몰)
	static final double AVOGADROS_NUMBER	= 6.022_14-_857e23;
	
	//볼츠만 상수(J/K)
	static final double BOLTZMANN_CONSTANT  = 1.380_648_52e-23;
	
	//전자 질량(kg)
	static final double ELECTRON_MASS		= 9.109_383_56e-31;
}
```

상수 인터페이스 안티패턴은 인터페이스를 잘못 사용한 예임.

클래스 내부에서 사용하는 상수는 외부 인터페이스가 아니라 내부 구현에 해당함.

따라서 상수 인터페이스를 구현하는 것은 이 내부 구현을 클래스의 API로 노출하는 행위임.

---

상수를 공개할 목적을 이루고 싶다면?

- 특정 클래스나 인터페이스와 강하게 연관된 상수라면 그 클래스나 인터페이스 자체에 추가해야 함.
	- ex) 모든 숫자 기본 타입의 박싱 클래스(Integer, Double에 선언된 MAX_VALUE,MIN_VALUE 상수)
- 열거 타입으로 나타내기 적합한 상수라면 열거 타입으로 만들어 공개하기
- 인스턴스화 할 수 없는 유틸리티 클래스에 담아 공개하기(private 생성자)

```java
//상수 유틸리티 클래스
public interface PhysicalConstants{

	private PhysicalConstants(){}//인스턴스화 방지
	
	//아보가드로 수(1/몰)
	public static final double AVOGADROS_NUMBER	   = 6.022_14-_857e23;
	
	//볼츠만 상수(J/K)
	public static final double BOLTZMANN_CONSTANT  = 1.380_648_52e-23;
	
	//전자 질량(kg)
	public static final double ELECTRON_MASS	   = 9.109_383_56e-31;
}
```

