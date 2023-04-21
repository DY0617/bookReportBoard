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

