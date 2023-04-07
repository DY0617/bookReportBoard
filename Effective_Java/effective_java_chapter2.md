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

