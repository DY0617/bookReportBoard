# Effective Java 5장
# 제네릭

제네릭을 지원하기 전에는 컬렉션에서 객체를 꺼낼 때마다 형변환을 해야 했음.

제네릭을 사용하면 컬렉션이 담을 수 있는 타입을 컴파일러에 알려주게 됨.

그래서 컴파일러는 알아서 형변환 코드를 추가할 수 있게 되고, 엉뚱한 타입의 객체를 넣으려는 시도를 컴파일 과정에서 차단하여 더 안전하고 명확한 프로그램을 만들어 줌.

꼭 컬렉션이 아니더라도 이러한 이점을 누릴 수 있지만, 코드가 복잡해짐.

이번 장에서는 제네릭의 이점을 최대로 살리고 단점을 최소화 하는 법을 이야기함.

---

# 목차

1. [로 타입은 사용하지 말라](#로-타입은-사용하지-말라)
2. [비검사 경고를 제거하라](#비검사-경고를-제거하라)
3. [배열보다는 리스트를 사용하라](#배열보다는-리스트를-사용하라)
4. [이왕이면 제네릭 타입으로 만들라](#이왕이면-제네릭-타입으로-만들라)
5. [이왕이면 제네릭 메서드로 만들라](#이왕이면-제네릭-메서드로-만들라)
6. [한정적 와일드카드를 사용해 API 유연성을 높이라](#한정적-와일드카드를-사용해-API-유연성을-높이라)
7. [제네릭과 가변인수를 함께 쓸 때는 신중하라](#제네릭과-가변인수를-함께-쓸-때는-신중하라)
8. [타입 안전 이종 컨테이너를 고려하라](#타입-안전-이종-컨테이너를-고려하라)

---

# 로 타입은 사용하지 말라

클래스와 인터페이스 선언에 타입 매개변수 (type parameter)가 쓰이면 이를 제네릭 클래스, 혹은 제네릭 인터페이스라 함.

    타입 매개변수?
    
    실행 시 인자로 전달하는 타입을 변수의 타입으로 지정하는 것
    
    class 클래스명<타입 매개변수>{}

예를 들어 List 인터페이스는 완전한 이름이 List&#60;E&#62;지만 짧게 그냥 List라고 자주 씀.
    
제네릭 클래스와 제네릭 인터페이스를 통틀어 제네릭 타입이라 함.

---

각각의 제네릭 타입은 일련의 매개변수화 타입(parameterized type)을 정의함.

먼저 클래스 혹은 인터페이스의 이름이 나오고, &#60;&#62; 안에 실제 타입 매개변수들을 나열함.

ex) List&#60;String&#62;은 원소의 타입이 String인 리스트를 뜻하는 매개변수화 타입임. 여기서 String이 정규 타입 매개변수 E에 해당하는 실제 타입 매개변수임.

**<> 안에 있는 String은 실제 타입 매개변수, List 인터페이스에 선언되어있는 List의 E를 형식 타입 매개변수라 함.**

---

제네릭 타입을 하나 정의하면 그에 딸린 로 타입(raw type)도 함께 정의됨.

    로 타입?
    
    제네릭 타입에서 타입 매개변수를 전혀 사용하지 않을 때를 말함.

List&#60;E&#62;의 로 타입은 List임.

로 타입은 타입 선언에서 제네릭 타입 정보가 전부 지워진 것처럼 동작하는데, 제네릭이 나오기 전 코드와 호환되도록 하기 위함임.

---

```java
//컬렉션의 로 타입
//좋지 않은 코드임.
private final Collection stamps=...;

//이 코드를 사용하면 실수로 Stamp 대신 Coin을 넣어도 아무 오류 없이 컴파일되고 실행됨.
stamps.add(new Coin(...));

//컬렉션에서 이 동전을 꺼내기 전에는 오류를 알아채지 못하게 됨.
for(Iterator i=stamps.iterator();i.hasNext();){
    Stamp stamp=(Stamp)i.next();
    stamp.cancel();
}
```

오류는 가능한 한 발생 즉시, 이상적으로는 컴파일할 때 발견하는 것이 좋음.

그 이유로 위 코드는 문제가 많다.

제네릭을 활용하면 이 문제가 깔끔하게 해결됨.

```java
//매개변수화된 컬렉션 타입
private final Collection<Stamp> stamps=...;
```

이렇게 선언하면 컴파일러는 stamps에는 Stamp의 인스턴스만 넣어야 함을 컴파일러가 인지하게 됨.

따라서 아무 경고 없이 컴파일된다면 의도대로 동작할 것임을 보장함.

이제 stamps에 엉뚱한 타입의 인스턴스를 넣으려 하면 컴파일 오류가 발생하게 됨.

컴파일러는 컬렉션에서 원소를 꺼내는 모든 곳에 보이지 안흔 형변환을 추가하여 절대 실패하지 않음을 보장함.

---

로 타입을 쓰면 제네릭이 안겨주는 안전성과 표현력을 모두 잃게 됨.

로 타입을 언어 차원에서 막아놓지는 않았지만, 절대로 쓰지는 말 것.

로 타입은 단지 제네릭이 나오기 전 코드들과 호환은 위한 것일 뿐임.

---

List 같은 로 타입은 사용하면 안되지만, List&#60;Object&#62;같은 임의 객체를 허용하는 매개변수화 타입은 사용 가능함.

둘의 차이는?

List는 제네릭 타입에서 완전히 발을 뺀 반면, List&#60;Object&#62;는 모든 타입을 허용한다는 의사를 컴파일러에 명확히 전달한 것임.

List&#60;String&#62;은 로 타입 List의 하위 타입이지만, List&#60;Object&#62;의 하위 타입은 아니기 때문에, 매개변수로 List를 받는 메서드에 List&#60;String&#62;을 넘길 수 있지만, List&#60;Object&#62;를 받는 메서드에는 넘길 수 없음.

결론은, List&#60;Object&#62;같은 매개변수화 타입을 사용할 때와 달리 List 같은 로 타입을 사용하면 타입 안정성을 잃게 된다는 것.

```java
//unsafeAdd 메서드가 로 타입을 사용함.
//런타임 실패
public static void main(String[] args){
    List<String> strings=new ArrayList<>();
    unsafeAdd(strings, Integer.valueOf((42));
    String s=strings.get(0);    //컴파일러가 자동으로 형변환 코드를 넣어줌.
}

private static void unsafeAdd(List list,Object o){
    list.add(o);
}
```

이 코드는 컴파일은 되지만 로 타입인 List를 사용하여 경고가 발생함.

이 코드를 실행하면 strings.get(0)의 결과를 형변환하려 할 때 ClassCastException을 던짐.

Integer를 String으로 형변환 시도했기 때문.

<br>

그렇다면 List를 List&#60;Object&#62;로 바꾼 다음 다시 실행하면?

이제는 컴파일조차 되지 않음.

---

```java
//2개의 Set을 받아 공통 원소를 반환하는 메서드
//모르는 타입의 원소도 받는 로 타입을 사용함.
static int numElementsInCommon(Set s1, Set s2){
    int result=0;
    for(Object o1:s1)
        if(s2.contains(o1))
            result++;
    return result;
}
```

이 메서드는 동작은 하지만 로 타입을 사용해 안전하지 않음.

따라서 비한정적 와일드카드 타입(unbounded wildcard type)을 대신 사용하는 게 좋음.

    와일드 카드?
    
    제네릭 표시에서 물음표 ? 로 표기되어 있는 것을 말함.
    아직 알려지지 않은 타입을 나타냄.
    
    비한정적 와일드카드 타입?
    
    extends나 super 없이 와일드카드인 ?만 사용할 때를 말함.
    extends나 super를 사용하면 한정적 와일드카드 타입이라 함.
    ex) List<?>

```java
//비한정적 와일드카드 타입 사용
//타입 안전하며 유연함.
static int numElementsInCommon(Set<?> s1, Set<?> s2){...}
```

로 타입 Set와 비한정적 와일드카드 타입 Set&#60;?&#62;의 차이는?

로 타입 컬렉션에는 아무 원소나 넣을 수 있으니 타입 불변식을 훼손하기 쉬움.

반면 Collections&#60;?&#62;에는 null 외에는 어떤 원소도 넣을 수 없음. 다른 원소를 넣으려 하면 컴파일할 때 오류 메세지를 출력함.

로 타입은 안전하지 않지만, 와일드카드 타입은 안전하다는 것.

<br>

컴파일러는 어떤 원소도 Collections&#60;?&#62;에 넣지 못하게 했으며 컬렉션에서 꺼낼 수 있는 객체의 타입도 전혀 알 수 없게 했음.

이러한 제약을 받아들일 수 없다면 제네릭 메서드나 한정적 와일드카드 타입을 사용하면 됨.

---

로 타입을 써야할 때도 있음.

class 리터럴에는 로 타입을 써야 함.

    클래스 리터럴?
    
    클래스, 인터페이스, 배열 타입들의 이름 또는 기본 타입, void 뒤에 "." 이 따라오고
    class 토큰이 붙는 형식으로 구성된 표현법이다.
    String.class, Integer.class 등을 말하며, String.class의 타입은 Class<String>, Integer.class의 타입은 Class<Integer>다.

자바 명세는 class 리터럴에 매개변수화 타입을 사용하지 못하게 했기 때문.(List&#60;String&#62;.class 등등)

<br>

instanceof 연산자에는 로 타입을 써도 됨.

이 상황에서는 로 타입이든 비한정적 와일드카드 타입이든 완전히 똑같이 동작하기 때문임.

```java
//제네릭 타입에 instanceof를 사용하는 올바른 예
if(o instanceof Set){       //로 타입
    Set<?> s=(Set<?>) o;    //와일드카드 타입
}
```

o의 타입이 Set임을 확인한 다음 와일드카드 타입인 Set&#60;?&#62;로 형변환해야 한다.

---

핵심 정리

로 타입을 사용하면 런타임에 예외가 일어날 수 있으니 사용하면 안 된다. 로 타입은 제네릭이 도입되기 이전 코드와의 호환성을 위해 제공될 뿐이다. 빠르게 훑어보자면, Set&#60;Object&#62;는 어떤 타입의 객체도 저장할 수 있는 매개변수화 타입이고, Set&#60;?&#62;는 모종의 타입 객체만 저장할 수 있는 와일드카드 타입이다. 그리고 이들의 로 타입인 Set은 제네릭 타입 시스템에 속하지 않는다. Set&#60;Object&#62;와 Set&#60;?&#62;는 안전하지만, 로 타입인 Set은 안전하지 않다.

---

|한글 용어|영문 용어|예|
|------|---|---|
|매개변수화 타입|parameterized type|List&#60;String&#62;|
|실제 타입 매개변수|actual type parameter|String|
|제네릭 타입|generic type|List&#60;E&#62;|
|정규 타입 매개변수|formal type parameter|E|
|비한정적 와일드카드 타입|unbounded wildcard type|List&#60;?&#62;|
|로 타입|raw type|List|
|한정적 타입 매개변수|bounded type parameter|&#60;E extends Number&#62;|
|재귀적 타입 한정|recursive type bound|&#60;T extends Comparable&#60;T&#62;&#62;|
|한정적 와일드카드 타입|bounded wildcard type|List&#60;? extends Number&#62;|
|제네릭 메서드|generic method|static &#60;E&#62; List&#60;E&#62; asList(E[] a)|
|타입 토큰|type token|String.class|

---

# 비검사 경고를 제거하라

제네릭을 사용하기 시작하면 비검사 형변환 경고, 비검사 메서드 호출 경고, 비검사 매개변수화 가변인수 타입 경고, 비검사 변환 경고 등 여러 컴파일러 경고를 보게 될 것.

대부분의 비검사 경고는 쉽게 제거가 가능함.

```java
//잘못 작성한 코드
Set<Lark> exaltation=new HashSet();
```

위 코드에서 컴파일러가 다음과 같은 오류를 출력함.(-Xlint:uncheck 옵션을 추가해야 함.)

    Venery.java:4: warning: [unchecked] unchecked conversion Set<Lark> exaltation = new HashSet();
                                                                                    ^
    required:   Set<Lark>
    found:      HashSet

컴파일러가 알려준대로 수정하면 경고가 사라짐.

컴파일러가 알려준 타입 매개변수를 명시하지 않고, 자바 7부터 지원하는 다이아몬드 연산자(<>) 만으로 해결할 수 있음.

다이아몬드 연산자를 사용하면 컴파일러가 올바른 실제 타입 매개변수를 추론해줌.

```java
//다이아몬드 연산자로 해결
Set<Lark> exaltation=new HashSet<>();
```

---

제거하기 훨씬 어려운 경고도 있음.

하지만 할 수 있는 한 모든 비검사 경고를 제거하는 게 좋음.

모두 제거한다면 그 코드는 타입 안정성이 보장됨.

<br>

경고를 제거할 수는 없지만 타입 안전하다고 확신할 수 있으면 @SuppressWarnings("unchecked") 애너테이션을 달아 경고를 숨기자.

이 애너테이션은 개별 지역변수 선언부터 클래스 전체까지 어떤 선언에도 달 수 있지만, 항상 가능한 한 좁은 범위에 적용할 것.

자칫 심각한 경고를 놓칠 수 있으니 절대 클래스 전체에 적용하지 말기.

```java
//ArrayList에서 가져온 toArray 메서드
//애너테이션은 선언에만 달 수 있기 때문에 return문에는 @SuppressWarnings 불가.
//그렇다고 메서드 전체에 애너테이션을 달면 범위가 필요 이상으로 넓어짐.
//해결방법은?
//반환값을 담을 지역변수를 하나 선언하고, 그 변수에 애너테이션을 달아주기
public <T> T[] toArray(T[] a){
    if(a.length<size){
        //생성한 배열과 매개변수로 받은 배열의 타입이 모두 T[]로 같으므로 올바른 형변환임.
        @SuppressWarnings("unchecked") T[] result=
            (T[]) Arrays.copyOf(elements,size,a.getClass());
        return result;
    }
    System.arraycopy(elements,0,a,0,size);
    if(a.length>size)
        a[size]=null;
    return a;
}
```

@SuppressWarnings("unchecked") 애너테이션을 사용할 때면 그 경고를 무시해도 안전한 이유를 항상 주석으로 남길 것.

---

핵심 정리

비검사 경고는 중요하니 무시하지 말자. 모든 비검사 경고는 런타임에 ClassCastException을 일으킬 수 있는 잠재적 가능성을 뜻하니 최선을 다해 제거하라. 경고를 없앨 방법을 찾지 못하겠다면, 그 코드가 타입 안전함을 증명하고 가능한 한 범위를 좁혀 @SuppressWarnings("unchecked") 애너테이션으로 경고를 숨겨라. 그런 다음 경고를 숨기기로 한 근거를 주석으로 남겨라.

---

# 배열보다는 리스트를 사용하라

배열과 제네릭 타입에는 중요한 차이가 두 가지 있음.

1. 배열은 공변(convariant)이고, 제네릭은 불공변(invariant)이다.
2. 배열은 실체화(reify)된다.

---

**배열은 공변(convariant)이고, 제네릭은 불공변(invariant)이다.**

    공변? 불공변?
    
    공변의 뜻은 다음과 같음.
    함께 변한다는 뜻.
    Sub가 Super의 하위 타입이라면 배열 Sub[]는 배열 Super[]의 하위 타입이 됨.
    
    불공변은 다음과 같음.
    서로 다른 타입 Type1과 Type2가 있을 때 List<Type1>은 List<Type2>의 하위 타입도 아니고 상위 타입도 아니다.
    
    
    

    불공변 or 무공변 (Invariant)	
    List<String>은 List<Object>의 하위 타입이 아니다. 
    
    공변 (Covariant)	
    String 이 Object의 하위 타입이면, List<String>은 List<? extend Object> 의 하위 타입이다.
    
    반공변 (Contravariant)	
    String 이 Object의 하위 타입이면, List<Object>은 List<? super String> 의 하위 타입이다.

```java
//런타임에 실패
//문법에 맞는 코드임
Object[] objectArray= new Long[1];
objectArray[0]="타입이 달라 넣을 수 없다.";   //ArrayStoreException

//컴파일 실패
//문법에 맞지 않는 코드임
List<Object> ol=new ArrayList<Long>();      //호환되지 않는 타입임.
ol.add("타입이 달라 넣을 수 없다.");
```

두 코드 다 Long용 저장소에 String을 넣을 수 없음.

하지만 배열에서는 그 실수를 런타임에서 알게 되고, 리스트는 컴파일 시 바로 알 수 있게 됨.

---

**배열은 실체화(reify)된다.**

    실체화? reify?
    
    배열은 런타임에도 자신이 담기로 한 원소의 타입을 인지하고 확인함.
    그래서 Long 배열에 String을 넣으려 하면 ArrayStoreException을 출력함.
    
배열과 달리 제네릭은 타입 정보가 런타임에는 소거(erasure)됨.

원소 타입을 컴파일타임에만 검사하며 런타임에는 알수 없음.

---

위 두 이유 때문에 배열과 제네릭은 잘 어우러지지 못함.

배열은 제네릭 타입, 매개변수화 타입, 타입 매개변수로 사용할 수 없음.

<br>

타입 안전하지 않기 때문에 제네릭 배열은 만들지 못하게 막음.

이를 허용한다면 컴파일러가 자동 생성한 형변환 코드에서 런타임에 ClassCastException이 발생할 수 있기 때문임.

런타임에 ClassCastException이 발생하는 일을 막아주겠다는 제네릭 타입 시스템의 취지와 어긋남.

```java
List<String>[] stringLists=new List<String>[1];     //(1)
List<Integer> intList=List.of(42);                  //(2)
Object[] objects=stringLists;                       //(3)
objects[0]=intList;                                 //(4)
String s=stringLists[0].get(0);                     //(5)
```

제네릭 배열 생성하는 (1)이 허용된다고 가정해보기.

(2)는 원소가 하나인 List&#60;Integer>를 생성함.

(3)은 (1)에서 생성한 List&#60;String>의 배열을 Object 배열에 할당함. 배열은 공변이니 성공함.

(4)는 (2)에서 생성한 List&#60;Ingeter>의 인스턴스를 Object 배열의 첫 원소로 저장함. 제네릭은 소거 방식으로 구현되어 이 역시 성공함. 즉 런타임에는 List&#60;Integer> 인스턴스의 타입은 단순히 List가 되고, List&#60;Integer>[] 인스턴스의 타입은 List[]가 된다. 따라서 (4)에서도 ArrayStoreException을 일으키지 않는다.

이제부터 문제가 되는데, List&#60;String> 인스턴스만 담겠다고 선언한 stringLists 배열에는 지금 List&#60;Integer> 변수가 저장되어 있음. 그리고 (5)에서 그를 꺼내려 함.

컴파일러는 꺼낸 원소를 자동으로 String으로 형변환하는데, 이 원소가 Integer이기 때문에 런타임에 ClassCastException이 발생함.

이런 일을 방지하려면 (1)에서 컴파일 오류를 내야 함.

---

E, List&#60;E>, List&#60;String>같은 타입을 실체화 불가 타입(non-reifiable type)라고 함.

    실체화 불가 타입? non-reifiable type?
    
    실체화되지 않아서 런타임에는 컴파일타임보다 타입 정보를 적게 가지는 타입.

소거 메커니즘 때문에 매개변수화 타입 가운데 실체화될 수 있는 타입은 List&#60;?>와 Map&#60;?,?>같은 비한정적 와일드카드 타입 뿐임.

배열을 비한정적 와일드카드 타입으로 만들 수는 있지만, 유용하게 쓰일 일은 거의 없음.

---

배열을 제네릭으로 만들 수 없어 귀찮을 때도 있음.

예를 들어 제네릭 컬렉션에서는 자신의 원소 타입을 담은 배열을 반환하는게 보통은 불가능함.

제네릭 타입과 가변인수 메서드(varargs method)를 함께 쓰면 해석하기 어려운 경고 메시지를 받게 됨.
- 가변인수 메서드를 호출할 때마다 가변인수 매개변수를 담을 배열이 하나 만들어지는데, 이때 그 배열의 원소가 실체화 불가 타입이라면 경고가 발생함.
- @SafeVarargs 애너테이션으로 대처할 수 있음.

배열로 형변환할 때 제네릭 배열 생성 오류나 비검사 형변환 경고가 뜨는 경우 대부분은 배열인 E[] 대신 컬렉션인 List&#60;E>를 사용하면 해결됨.
- 코드가 조금 복잡해지고 성능이 살짝 나빠질 수도 있지만, 대신 타입 안전성과 상호운용성은 좋아짐.

