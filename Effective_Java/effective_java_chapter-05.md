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

---

간단한 예제

```java
//생성자에서 컬렉션을 받는 Chooser 클래스
//컬렉션 안의 원소 중 하나를 무작위로 선택해 반환하는 choose 메서드를 제공
//제네릭을 쓰지 않고 구현함.
//제네릭의 적용하기 전 코드임.
public class Chooser{
    private final Object[] choiceArray;
    
    public Chooser(Collection choices){
        choiceArray=choices.toArray();
    }
    
    public Object choose(){
        Random rnd=ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```

이 클래스를 사용하려면 choose 메서드를 호출할 때마다 반환된 Object를 원하는 타입으로 형변환해야 함.

다른 타입의 원소가 들어 있었다면 런타임에 형변환 오류가 날 것임.

```java
//Chooser를 제네릭으로 만들기 위한 첫 시도
//컴파일되지 않음.
public class Chooser<T>{
    private final T[] choiceArray;
    
    public Chooser(Collection<T> choices){
        choiceArray=choices.toArray();
    }
    
    public Object choose(){
        Random rnd=ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}
```

이 클래스를 컴파일하면 형변환 오류 메시지가 출력됨.

이는 Object 배열을 T 배열로 형변환하면 됨.

    choiceArray=(T[]) choices.toArray();

그런데 이번에는, T가 무슨 타입인지 알 수 없으니 컴파일러는 이 형변환이 런타임에도 안전한지 보장할 수 없다는 경고 메시지를 보냄.

제네릭에서는 원소의 타입 정보가 소거되어 런타임에는 무슨 타입인지 알 수 없음.

단지 컴파일러가 안전을 보장하지 못할 뿐, 이 프로그램은 동작함.

코드를 작성하는 사람이 안전하다고 확신한다면 주석을 남기고 애너테이션을 달아 경고를 숨겨도 됨.

하지만 비검사 경고를 제거하는 편이 훨씬 좋음.

<br>

비검사 형변환 경고를 제거하려면?

배열 대신 리스트를 사용하기.

```java
//리스트 기반 Chooser
//타입 안정성 확보
//오류,경고 없이 컴파일 된다.
public class Chooser<T>{
    private final List<T> choiceList;
    
    public Chooser(Collection<T> choices){
        choiceList=new ArrayList<>(choices);
    }
    
    public T choose(){
        Random rnd=ThreadLocalRandom.current();
        return choiceList.get(rnd.nextInt(choiceList.size()));
    }
}
```

코드 양이 조금 늘었고 조금 더 느리지만, 런타임에 ClassCastException을 만날 일은 없으니 그만한 가치가 있음.

---

핵심 정리

배열과 제네릭에는 매우 다른 타입 규칙이 적용된다. 배열은 공변이고 실체화되는 반면, 제네릭은 불공변이고 타입 정보가 소거된다. 그 결과 배열은 런타임에는 타입 안전하지만 컴파일타임에는 그렇지 않다. 제네릭은 반대다. 그래서 둘을 섞어 쓰기란 쉽지 않다. 둘을 섞어 쓰다가 컴파일 오류나 경고를 만나면, 가장 먼저 배열을 리스트로 대체하는 방법을 적용해보자.

---

# 이왕이면 제네릭 타입으로 만들라

JDK가 제공하는 제네릭 타입과 메서드를 사용하는 일은 쉬움.

제네릭 타입을 새로 만드는 일은 조금 더 어렵지만, 배워둘만한 가치가 있음.

```java
//Object 기반 스택
//제네릭이면 좋음.
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
    if(size==0)
      throw new EmptyStackException();
    Object result=elements[--size];
    elements[size]==null;//다 쓴 참조 해제
    return result;
  }
  
  public boolean isEmpty(){
    return size==0;
  }
  
  private void ensureCapacity(){
    if(elements.length==size)
      elements=Arrays.copyOf(elements,2*size+1);
  }
}
```

위 클래스는 원래 제네릭 타입이어야 마땅함.

천천히 제네릭으로 만들어 보기.

```java
//첫 단계로 클래스 선언에 타입 매개변수를 추가하기
//스택이 담을 원소의 타입 하나 E를 추가하겠음
//이 상태에서는 컴파일되지 않음.
public class Stack<E>{
  private E[] elements;
  private int size = 0;
  private static final int DEFAULT_INITIAL_CAPACITY = 16;
  
  public Stack(){
    elements=new E[DEFAULT_INITIAL_CAPACITY];
  }
  
  public void push(E e){
    ensureCapacity();
    elements[size++]=e;
  }
  
  public E pop(){
    if(size==0)
      throw new EmptyStackException();
    E result=elements[--size];
    elements[size]==null;//다 쓴 참조 해제
    return result;
  }
  
  //isEmpty와 ensureCapacity 메서드는 변화 X
  
  public boolean isEmpty(){
    return size==0;
  }
  
  private void ensureCapacity(){
    if(elements.length==size)
      elements=Arrays.copyOf(elements,2*size+1);
  }
}
```

위 단락에서 설명한 것 처럼 E와 같은 실체화 불가 타입으로는 배열을 만들 수 없음.

    elements = new E[DEFAULT_INITIAL_CAPACITY];

하여 위 코드에서 오류가 발생하게 됨.

해결책은?

1. 제네릭 배열 생성을 금지하는 제약을 대놓고 우회하기.

        elements = (E[]) new Object[DEFAULT_INITIAL_CAPACITY];

컴파일러가 오류 대신 경고를 내보내게 됨.

일반적으로 타입 안전하지 않은 방법임.

배열 elements는 private 필드에 저장되고, 클라이언트로 반환되거나 다른 메서드에 전달되는 일이 전혀 없음. push 메서드를 통해 배열에 저장되는 원소의 타입은 항상 E임.

따라서 위 상황에서 해당 비검사 형변환은 확실히 안전함.

```java
//배열을 사용한 코드를 제네릭으로 만드는 방법 첫번째
//배열의 런타임 타입은 E[]가 아닌 Object[]임
@SuppressWarnings("unchecked")
public stack(){
    elements=(E[]) new Object[DEFAULT_INITIAL_CAPACITY];
}
```

2. elements 필드의 타입을 E[]에서 Object[]로 바꾸기.

이렇게 되면 첫 번째와는 다른 오류가 발생함.

    E result=elements[--size];

위 코드에서 오류가 발생하게 됨.

배열이 반환한 원소를 E로 형변환하면 오류 대신 경고를 내보내게 됨.

    E result= (E) elements[--size];

E는 실체화 불가 타입이므로 컴파일러는 런타임에 이뤄지는 형변환이 안전한지 증명할 방법이 없음.

본인이 직접 증명하고 경고를 숨겨야 함.

```java
//배열을 사용한 코드를 제네릭으로 만드는 방법 두번째
//비검사 경고를 적절히 숨기기
public E pop(){
    if(size==0)
        throw new EmptyStackException();
    
    //push에서 E 타입만 허용하므로 이 형변환은 안전함.
    @SuppressWarnings("unchecked")
    E result=(E) elements[--size];
    elements[size]==null;//다 쓴 참조 해제
    return result;
}
```

두 방법 모두 장단점이 있음.

첫 번째 방법은 가독성이 더 좋음. 

배열의 타입을 E[]로 선언해 오직 E 타입 인스턴스만 받음을 확실히 어필함.

코드도 더 짧음.

보통의 제네릭 클래스라면 코드 이곳저곳에서 이 배열을 자주 사용할 것임.

첫 번째 방식에서는 형변환을 배열 생성 시 단 한 번만 해주면 되지만, 두 번째 방식에서는 배열에서 원소를 읽을 때마다 해 줘야 함.

그래서 현업에서는 첫 번째 방법을 더 많이 사용함.

하지만 힙 오염의 위험이 있음.

    힙 오염?
    
    JVM의 메모리공간인 Heap Area가 오염된 상태
    주로 매개변수화 타입의 변수가 타입이 다른 객체를 참조할 때 발생한다. 
    
    위 상황에서는 E가 Object가 아닌 한 배열의 런타임 타입이 컴파일타임 타입과 달라 힙 오염을 일으킴.
    이번 예시에서는 힙 오염이 해가 되지 않음.

---

```java
//제네릭 Stack을 사용하는 맛보기 프로그램
//명령줄 인수들을 역순으로 바꿔 대문자로 출력하는 프로그램임.
//Stack에서 꺼낸 원소에서 String의 toUpperCase 메서드를 호출할 때
//명시적 형변환을 수행하지 않음.
//또한 컴파일러에 의해 자동 생성된 이 형변환이 항상 성공함을 보장하게 됨.
public static void main(String[] args){
    Stack<String> stack=new Stack<>();
    for(String args:args)
        stack.push(arg);
    while(!stack.isEmpty())
        System.out.println(stack.pop().toUpperCase());
}
```

---

지금까지 설명한 Stack 예는 배열보다 리스트를 우선하라는 저번 목차와 모순되어 보일 수 있음.

사실 제네릭 타입 안에서 리스트를 사용하는 게 항상 가능하지도 않고, 꼭 더 좋은것도 아님.

자바가 리스트를 기본 타입으로 제공하지 않으므로, ArrayList같은 제네릭 타입도 결국 기본 타입인 배열을 사용해 구현해야 함.

또한 HashMap 같은 제네릭 타입은 성능을 높일 목적으로 배열을 사용하기도 함.

<br>

대다수의 제네릭 타입은 타입 매개변수에 아무런 제약을 두지 않음.

ex) Stack&#60;Object>, Stack&#60;int[]>, Stack&#60;List&#60;String>>, Stack 등등...

하지만 기본 타입을 사용할 수 없음.

Stack&#60;int>나 Stack&#60;double>같은 기본 타입을 이용해서 만들려고 하면 컴파일 오류가 나게 됨.

하지만 박싱된 기본 타입을 이용해 우회할 수 있음.

    왜 기본 타입은 사용할 수 없지?
    
    타입 소거(type erasure) 때문
    
    타입 소거??
    제네릭 타입이 특정 타입으로 제한되어 있을 경우 해당 타입에 맞춰 컴파일시 타입 변경이 발생하고,
    타입 제한이 없을 경우 Object 타입으로 변경됨.
    
    헌데 기본 타입의 경우 Object 클래스를 상속받고 있지 않기 때문에 하위 호환성을 지키기 위해 오류가 발생하게 됨.
    
    따라서 박싱된 기본 타입을 이용해 우회함.

---

타입 매개변수에 제약을 두는 제네릭 타입도 있음.

    class DelayQueue<E extends Delayed> implements BlockingQueue<E>

타입 매개변수 목록인 &#60;E extends Delayed>는 java.util.concurrent.Delayed의 하위 타입만 받는다는 뜻임.

이러한 타입 매개변수 E를 한정적 타입 매개변수(bounded type parameter)라고 함.

---

핵심 정리

클라이언트에서 직접 형변환해야 하는 타입보다 제네릭 타입이 더 안전하고 쓰기 편하다. 그러니 새로운 타입을 설계할 때는 형변환 없이도 사용할 수 있도록 하라. 그렇게 하려면 제네릭 타입으로 만들어야 할 경우가 많다. 기존 타입 중 제네릭이었어야 하는 게 있다면 제네릭 타입으로 변경하자. 기존 클라이언트에는 아무 영향을 주지 않으면서, 새로운 사용자를 훨씬 편하게 해주는 길이다.

---

# 이왕이면 제네릭 메서드로 만들라

메서드도 제네릭으로 만들 수 있음.

매개변수화 타입을 받는 정적 유틸리티 메서드는 보통 제네릭임.

ex) Collections의 binarySearch, sort 등

```java
//합집합을 반환하는 메서드
//로 타입을 사용함
//문제있는 코드
public static Set union(Set s1,Set s2){
    Set result=new HashSet(s1);
    result.addAll(s2);
    return result;
}
```

컴파일은 되지만, 비검사 경고가 첫째 줄, 둘째 줄에서 발생함.

경고를 없애려면 이 메서드를 타입 안전하게 만들어야 함.

메서드 선언에서의 세 집합(입력 2개, 반환 1개)의 원소 타입을 타입 매개변수로 명시하고, 메서드 안에서도 이 타입 매개변수만 사용하게 수정하기.

**타입 매개변수들을 선언하는 타입 매개변수 목록은 메서드의 제한자와 반환 타입 사이에 온다.**

다음 코드에서, 타입 매개변수 목록은 &#60;E>이고 반환 타입은 Set&#60;E>이다.

타입 매개변수의 명명 규칙은 제네릭 타입과 같음.

```java
//제네릭 메서드
public static <E> Set<E> union(Set<E> s1, Set<E> s2){
    Set<E> result=new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

단순한 제네릭 메서드라면 이정도로 충분함.

```java
//제네릭 메서드를 활용하는 간단한 프로그램
public static void main(String[] args){
    Set<String> guys=Set.of("톰","딕","해리");
    Set<String> stooges=Set.of("래리","모에","컬리");
    Set<String> aflCio=union(guys,stooges);\
    System.out.println(alfCio);
}
```

이 프로그램을 실행하면 [모에,톰,해리,래리,컬리,딕] 이 출력됨. (원소 순서는 구현 방식에 따라 달라짐)

위 제네릭 메서드 코드에서 union 메서드는 집합 3개의 타입이 모두 같아야 함.

한정적 와일드카드 타입을 사용해 더 유연하게 개선 가능함.

---

때때로 불변 객체를 여러 타입으로 활용할 수 있게 만들어야 할 때가 있음.

제네릭은 런타임에 타입 정보가 소거되므로 하나의 객체를 어떤 타입으로든 매개변수화할 수 있음.

하지만 이렇게 하려면 요청한 타입 매개변수에 맞게 매번 그 객체의 타입을 바꿔주는 정적 팩터리를 만들어야 함.

이 패턴을 제네릭 싱글턴 팩터리라 함.

---

항등함수(identity function)을 담은 클래스를 만들고 싶다고 가정.

```java
//항등함수 객체는 상태가 없으니 요청할 때 마다 새로 생성하는 것은 낭비.
//소거 방식 덕분에 제네릭 싱글턴 하나면 충분함.
//제네릭 싱글턴 팩터리 패턴
private static UnaryOperator<Object> IDENTITY_FN=(t)->t;

@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction(){
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

항등함수란 입력 값을 수정 없이 그대로 반환하는 특별한 함수이기 때문에 T가 어떤 타입이든 UnaryOperator&#60;T>를 사용해도 타입 안전함.


```java
//제네릭 싱글턴을 사용하는 예
public static void main(String[] args){
    String[] strings={"삼베","대마","나일론"};
    UnaryOperator<String> sameString=identityFunction();
    for(String s:strings)
        System.out.println(sameString.apply(s));
    
    Number[] numbers={1,2.0,3L};
    UnaryOperator<Number> sameNumber=identityFunction();
    for(Number n:numbers)
        System.out.println(sameNumber.apply(n));
}
```

---

드물지만, 자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 한정할 수 있음.

이를 재귀적 타입 한정(recursive type bound)라고 함.

주로 타입의 자연적 순서를 정하는 Comparable 인터페이스와 함께 쓰임.

```java
public interface Comparable<T>{
    int compareTo(T o);
}
```

여기서 타입 매개변수 T는 Comparable&#60;T>를 구현한 타입이 비교할 수 있는 원소의 타입을 정의함.

실제로 거의 모든 타입은 자신과 같은 타입의 원소와만 비교할 수 있음.

```java
//재귀적 타입 한정을 이용해 상호 비교할 수 있음을 표현함.
public static <E extends Comparable<E>> E max(Collection<E> c);

//타입 한정인 <E extends Comparable<E>>는
//모든 타입 E는 자신과 비교할 수 있다 라고 읽을 수 있음.
//상호 비교 가능하다는 뜻을 아주 정확하게 표현한 것.
```

Comparable을 구현한 원소의 컬렉션은 입력받는 메서드들은 주로 그 원소들을 정렬 혹은 검색하거나, 최솟값이나 최댓값을 구하는 식으로 사용됨.

```java
//방금 선언한 메서드의 구현
//컬렉션에서 최댓값을 반환
//재귀적 타입 한정 사용
public static <E extends Comparable<E>> E max(Collection<E> c){

    //이 메서드에서 빈 컬렉션을 건네면 IllegalArgumentException을 던지니, Optional<E>를 반환하도록 고치는 편이 더 나을 것.
    if(c.isEmpty())
        throw new IllegalArgumentException("컬렉션이 비어 있습니다.");
    
    E result=null;
    for(E e:c)
        if(result==null||e.compareTo(result)>0)
            result=Objects.requireNonNull(e);
    
    return result;

}
```

---

핵심 정리

제네릭 타입과 마찬가지로, 클라이언트에서 입력 매개변수와 반환값을 명시적으로 형변환해야 하는 메서드보다 제네릭 메서드가 더 안전하며 사용하기도 쉽다. 타입과 마찬가지로, 메서드도 형변환 없이 사용할 수 있는 편이 좋으며, 많은 경우 그렇게 하려면 제네릭 메서드가 되어야 한다. 역시 타입과 마찬가지로, 형변환을 해줘야 하는 기존 메서드는 제네릭하게 만들자. 기존 클라이언트는 그대로 둔 채 새로운 사용자의 삶을 훨씬 편하게 만들어줄 것이다.

---

# 한정적 와일드카드를 사용해 API 유연성을 높이라

매개변수화 타입은 불공변임.

List&#60;String>은 List&#60;Object>의 하위 타입이 아니라는 뜻임.

List&#60;Object>에는 어떤 객체도 넣을 수 있지만, List&#60;String>에는 문자열만 넣을 수 있음.

즉 List&#60;String>은 List&#60;Object>가 하는 일을 제대로 수행하지 못하니 하위 타입이 될 수 없음.

LSP에 어긋남.

하지만 때로는 불공변 방식보다 유연한 무언가가 필요함.

```java
//Stack 클래스의 public API
public class Stack<E>{
    public Stack();
    public void push(E e);
    public E pop();
    public boolean isEmpty();
}
```

여기에 일련의 원소를 스택에 넣는 메서드를 추가해야 한다고 해보기

```java
//와일드카드 타입을 사용하지 않은 pushAll 메서드
//결함이 있음.
public void pushAll(Iterable<E> src){
    for(E e:src)
        push(e);
}
```

Iterable src의 원소 타입이 스택의 원소 타입과 일치하면 잘 동작하지만, Stack&#60;Number>로 선언한 후 int 타입 intVal로 pushAll(intVal)을 호출하면 오류 메시지가 뜸.

Integer는 Number의 하위 타입이니 잘 동작 해야 할 것 같은데, 왜 오류 메시지가 뜰까?

매개변수화 타입이 불공변이기 때문이다.

해결책은?

pushAll의 입력 매개변수 타입은 E의 Iterable이 아니라, E의 하위 타입의 Iterable이어야 함.

와일드카드 타입 Iterable&#60;? extends E>가 이를 뜻함.

```java
//생산자 매개변수에 와일드카드 타입 적용.
public void pushAll(Iterable<? extends E> src){
    for(E e:src)
        push(e);
}
```

이 수정으로 말끔하게 컴파일 됨. 이제 모든 타입에 안전함.

---

popAll 메서드 작성하기

```java
//와일드카드 타입을 사용하지 않은 popAll 메서드
//결함이 있음
public void popAll(Collection<E> dst){
    while(!isEmpty())
        dst.add(pop());
}
```

주어진 컬렉션 원소 타입이 스택의 원소 타입과 일치한다면 문제없지만, 아닐 경우 문제가 생김.

Stack&#60;Number>의 원소를 Object용 컬렉션으로 옮기려 한다고 해보기.

    Stack<Number> numberStack=new Stack<>();
    Collection<Object> objects=...;
    numberStack.popAll(objects);

이 클라이언트 코드를 앞의 popAll 코드와 함께 컴파일하면 "Collection&#60;Object>는 Collection&#60;Number>의 하위 타입이 아니다"라는, pushAll을 사용했을 때와 비슷한 오류가 발생함.

이번에도 와일드카드 타입으로 해결할 수 있음.

이번에는 popAll의 입력 매개변수 타입이 E의 Collection이 아니라 E의 상위 타입의 Collection이어야 함.(모든 타입은 자기 자신의 상위 타입임)

이를 Collection&#60;? super E>라고 함.

```java
//소비자 매개변수에 와일드카드 적용
public void popAll(Collection<? super E> dst){
    while(!isEmpty())
        dst.add(pop());
}
```

### 유연성을 극대화하려면 원소의 생산자나 소비자용 입력 매개변수에 와일드카드 타입을 사용할 것.

한편, 입력 매개변수가 생산자와 소비자 역할을 동시에 한다면 와일드카드 타입을 써도 좋을 게 없음.

타입을 정확히 지정해야 하는 상황이기 때문.

---

    PECS:producer-extends, consumer-super

매개변수화 타입 T가 생산자라면 <? extends T>를 사용하고, 소비자라면  <? super T>를 사용할 것.

PECS 공식은 와일드카드 타입을 사용하는 기본원칙.

겟풋 원칙으로 부르기도 함.

---

위 공식을 기억해두고 Chooser 살펴보기

```java
//생성자에서 컬렉션을 받는 Chooser 클래스
//컬렉션 안의 원소 중 하나를 무작위로 선택해 반환하는 choose 메서드를 제공
//제네릭을 쓰지 않고 구현함.
//제네릭의 적용하기 전 코드임.
public class Chooser{
    private final Object[] choiceArray;
    
    public Chooser(Collection choices){
        choiceArray=choices.toArray();
    }
    
    public Object choose(){
        Random rnd=ThreadLocalRandom.current();
        return choiceArray[rnd.nextInt(choiceArray.length)];
    }
}



//Chooser 생성자
//T타입의 값을 생산하기만 하니 T를 확장하는 와일드카드 타입을 사용해 선언하기
public Chooser(Collections<? extends T> choice)
```

---

위 공식을 기억해두고 union 메서드 살펴보기

```java
//제네릭 메서드
public static <E> Set<E> union(Set<E> s1, Set<E> s2){
    Set<E> result=new HashSet<>(s1);
    result.addAll(s2);
    return result;
}



//s1과 s2 모두 E의 생산자이니 PECS 공식에 따라 다음처럼 선언하기
public static <E> Set<E> union(Set<? extends E> s1,Set<? extends E> s2)
```

---

위 공식을 기억해두고 max 메서드 살펴보기

```java
public static <E extends Comparable<E>> E max(Collection<E> c){

    //이 메서드에서 빈 컬렉션을 건네면 IllegalArgumentException을 던지니, Optional<E>를 반환하도록 고치는 편이 더 나을 것.
    if(c.isEmpty())
        throw new IllegalArgumentException("컬렉션이 비어 있습니다.");
    
    E result=null;
    for(E e:c)
        if(result==null||e.compareTo(result)>0)
            result=Objects.requireNonNull(e);
    
    return result;
}


//와일드카드 타입을 사용해 다듬기
public static <E extends Coparable<? super E>> E max(List<? extends E> list)

//입력 매개변수에서 E 인스턴스를 생산하므로 원래의 List<E>를 List<? extends E>로 수정
//Comparable<E>는 E 인스턴스를 소비하여 Comparable<? super E>로 수정
//Comparable, Comparater는 언제나 소비자이므로 항상 Comparable<? super E>를 사용하는게 좋다.

```

---

```java
//swap 메서드의 두 가지 선언
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
```

두 선언중 어떤 선언이 더 나을까?

public API라면 간단한 두 번째가 나음.

어떤 리스트든 이 메서드에 넘기면 명시한 인덱스의 원소들을 교환해 줄 것. 신경 써야 할 타입 매개변수도 없음.

**메서드 선언에 타입 매개변수가 한 번만 나오면 와일드카드로 대체할 것**

이 때 비한정적 타입 매개변수라면 비한정적 와일드카드로 바꾸고, 한정적 타입 매개변수라면 한정적 와일드카드로 바꿀 것.

하지만 두 번째 swap 선언에는 문제가 하나 있음.

```java
//다음 코드가 컴파일되지 않음
public static void swap(List<?> list, int i, int j){
    list.set(i,list.set(j,list.get(i)));
}
```

리스트의 타입이 List&#60;?>인데, List&#60;?>에는 null 외에는 어떤 값도 넣을 수 없기 때문.

와일드카드의 실제 타입을 알려주는 메서드를 private 도우미 메서드로 따로 작성하여 활용하는 방법으로 해결 가능.

```java
public static void swap(List<?> list, int i, int j){
    swapHelper(list,i,j);
}

//와일드카드 타입을 실제 타입으로 바꿔주는 private 도우미 메서드
private static <E> void swapHelper(List<E> list, int i, int j){
    list.set(i,list.set(j,list.get(i)));
}
```

swapHelper 메서드는 리스트가 List&#60;E>임을 알고 있음.

고로 깔끔히 컴파일 됨.

---

핵심 정리

조금 복잡하더라도 와일드카드 타입을 적용하면 API가 훨씬 유연해진다. 그러니 널리 쓰일 라이브러리를 작성한다면 반드시 와일드카드 타입을 적절히 사용해줘야 한다. PECS 공식을 기억하자. 즉, 생산자는 extends를 소비자는 super를 사용한다. Comparable과 Comparator는 모두 소비자라는 사실도 잊지 말자.

---

# 제네릭과 가변인수를 함께 쓸 때는 신중하라

가변인수 메서드를 호출하면 가변인수를 담기 위한 배열이 자동으로 하나 만들어짐.

그런데 내부로 감춰야 했을 이 배열을 클라이언트에 노출하는 문제가 생김.

그 결과 varags 매게변수에 제네릭이나 매개변수화 타입이 포함되면 알기 어려운 컴파일 경고가 발생함.

메서드를 선언할 때 실체화 불가 타입으로 varags 매개변수를 선언하면 컴파일러 경고를 보냄.

가변인수 메서드를 호출할 때도 varags 매개변수가 실체화 불가 타입으로 추론되면, 그 호출에 대해서도 경고를 냄.

---

매개변수화 타입의 변수가 타입이 다른 객체를 참조하면 힙 오염이 발생함.

```java
//제네릭과 varags를 혼용하면 타입 안정성이 깨짐
static void dangerous(List<String>... stringLists){
    List<Integer> intList=List.of(42);
    Object[] objects = stringLists;
    objects[0]=intList;             //힙 오염 발생
    String s=stringLists[0].get(0)  //ClassCastException
}
```

마지막 줄에 컴파일러가 생성한 보이지 않는 형변환이 숨어있어 ClassCastException을 던짐.

타입 안정성이 깨지니 제네릭 varargs 배열 매개변수 값을 저장하는 것은 안전하지 않음.

---

제네릭이나 매개변수화 타입의 varags 매개변수를 받는 메서드가 실무에서 매우 유용하기 때문에, 제네릭 배열을 프로그래머가 직접 생성하는건 허용하지 않지만 제네릭 varags 매개변수를 받는 메서드를 선언할 수 있게 하는 모순을 수용함.

ex) Arrays.asList(T... a), Collections.addAll(Collection<? super T> c, T... elements), EnumSet.of(E first,E... rest) 등등

위 예들은 타입 안전함.

@SafeVarargs 애너테이션으로 메서드 작성자가 그 메서드가 타입 안전함을 보장할 수 있음.

메서드가 안전한 게 확실하지 않다면 절대 위 에너테이션을 달아서는 안됨.

<br>

메서드가 안전한지 확신하는 방법은?

