---
name: Java-핵심
description: 정보처리기사 실기 Java 코드 추적 핵심 — OOP, 상속, 예외처리, static
source_pdf: 2026_정보처리기사_실기_기출문제집_핵심요약(20260223).pdf
part: 프로그래밍
keywords: [Java, 상속, 오버라이딩, 예외처리, static, 인터페이스]
tags: [#programming, #java, #priority-1, #concept]
---

# Java 핵심

> [!important] 출제 빈도 ★★★★★
> 매 회차 2~4문제. 코드 실행 결과 추적. 최근 난이도 상승 추세

---

## 상속 & 오버라이딩

### 핵심 규칙: 런타임 다형성

```java
A a = new B();  // 참조타입=A, 실제객체=B
a.method();     // B의 method() 호출 (오버라이딩된 메서드)
```

> [!warning] static 메서드는 예외!
> - `static` 메서드는 **컴파일 타임에 결정** → 참조 타입(A)의 메서드 호출
> - 인스턴스 메서드는 **런타임에 결정** → 실제 객체(B)의 메서드 호출

```java
class A {
    String f(Object x) { return "1"; }
    String g() { return f("a"); }  // this.f() = B의 f()!
}
class B extends A {
    String f(Object x) { return "2"; }   // 오버라이딩
    String f(String x) { return "3"; }   // 오버로딩 (다른 메서드!)
}
A a = new B();
System.out.println(a.g());  // 2
// g()는 A.g() → f("a") 호출 → 실제 객체 B의 f(Object) → "2"
```

> [!example] 기출 (2026년 1회차 7번) → **2**

### super 키워드

```java
class Square extends Rectangle {
    Square(int a) {
        super(a, a);  // 부모 클래스 생성자 호출
    }
}
```

> [!example] 기출 (2025년 3회차 12번) → `super`

### 정적(static) 메서드 vs 인스턴스 메서드

```java
class Parent {
    public int x(int i) { return i + 2; }       // 인스턴스 → 오버라이딩
    public static String id() { return "P"; }    // static → 컴파일타임 결정
}
class Child extends Parent {
    public int x(int i) { return i + 3; }
    public static String id() { return "C"; }
}
Parent ref = new Child();
System.out.println(ref.x(2) + ref.id());  // 5P
// ref.x(2): 런타임 → Child.x(2) = 5
// ref.id(): static, 참조타입 Parent → "P"
```

> [!example] 기출 (2025년 2회차 10번) → **5P**

---

## 생성자 호출 순서

```java
class Parent {
    Parent() { System.out.println("Parent"); }
}
class Child extends Parent {
    Child() {
        // 암묵적으로 super() 호출
        System.out.println("Child");
    }
}
new Child();
// 출력: Parent → Child
```

> [!tip] 상속 시 자식 생성자 실행 전에 부모 생성자가 먼저 실행됨

### this() 생성자 체이닝

```java
class Parent {
    int x;
    Parent() { this(500); }    // 다른 생성자 호출
    Parent(int x) { this.x = x; }
}
```

> [!example] 기출 (2023년 1회차 20번)
> `Child()` → `this(5000)` → `Child(int x)` (super() 암묵 호출) → `Parent()` → `this(500)` → `Parent(500)`
> → `getX()` 오버라이딩 없으므로 `Parent.getX()` → `Parent.x = 500` → **500**

---

## 인터페이스 (Interface)

```java
interface Machine {
    void run();
}
class WashingMachine implements Machine {  // implements 키워드
    public void run() {
        System.out.println("Washing machine running");
    }
}
```

> [!example] 기출 (2025년 3회차 8번) → `implements`

---

## 예외 처리

```java
try {
    System.out.print(a/b);  // 0으로 나누기 → ArithmeticException
} catch (ArithmeticException e) {
    System.out.print("출력1");
} catch (Exception e) {
    System.out.print("출력4");
} finally {
    System.out.print("출력5");  // 항상 실행
}
// 출력: 출력1출력5
```

> [!warning] finally는 항상 실행됨 (return, exception 무관)

> [!example] 기출 (2025년 1회차 5번) → `출력1출력5`

### throws vs throw

```java
static void func() throws Exception {
    throw new NullPointerException();  // 실제 던지기
}
// catch 블록에서 NullPointerException이 먼저 매칭
// sum = 0 + 1 + 100 = 101
```

> [!example] 기출 (2024년 3회차 18번) → **101**

---

## static 멤버

```java
class Static {
    public int a = 20;   // 인스턴스 변수
    static int b = 0;    // 클래스(static) 변수 — 공유됨
}
a = 10;
Static.b = a;      // b = 10
Static st = new Static();
System.out.println(Static.b++); // 10 (출력 후 b=11)
System.out.println(st.b);       // 11 (같은 static 변수)
System.out.println(a);          // 10
System.out.print(st.a);         // 20 (인스턴스 변수)
```

> [!example] 기출 (2023년 1회차 1번) → 10, 11, 10, 20

---

## Enum

```java
enum Tri {
    A("A"), B("AB"), C("ABC");
    private String code;
    Tri(String code) { this.code = code; }
    public String code() { return code; }
}
Tri t = Tri.values()[Tri.A.name().length()];
// Tri.A.name() = "A" → length() = 1
// Tri.values()[1] = Tri.B
System.out.print(t.code()); // "AB"
```

> [!example] 기출 (2025년 3회차 17번) → **AB**

---

## 제네릭 & 오버로딩 해결

```java
class Collection<T> {
    T value;
    void print() {
        new Printer().print(value);  // T는 Object로 erasure
    }
    class Printer {
        void print(Integer a) { System.out.print("A" + a); }
        void print(Object a)  { System.out.print("B" + a); }  // 이것이 선택됨
        void print(Number a)  { System.out.print("C" + a); }
    }
}
new Collection<>(0).print(); // B0
```

> [!warning] 제네릭 타입 소거(Type Erasure): 런타임에 T→Object, 오버로딩은 컴파일타임 결정
> → `print(Object)` 선택됨

> [!example] 기출 (2024년 3회차 19번) → **B0**

---

## 문자열 연산

```java
int x1 = 9, x2 = 2;
String x3 = "3";
System.out.println(x1 + x2 + "2" + x3);
// 9 + 2 = 11 (정수 덧셈)
// 11 + "2" = "112" (문자열 연결)
// "112" + "3" = "1123"
```

> [!example] 기출 (2026년 1회차 17번) → **1123**

### split()

```java
String str = "ITISTESTSTRING";
String[] result = str.split("T");
// ["I", "IS", "ES", "S", "RING"]
System.out.print(result[3]); // "S"
```

> [!example] 기출 (2024년 3회차 20번) → **S**

---

## 재귀 함수 (오버로딩 포함)

```java
static int calc(String str) {
    int value = Integer.valueOf(str);  // "5" → 5
    if (value <= 1) return value;
    return calc(value - 1) + calc(value - 3);  // int 버전 호출!
}
// calc("5") → calc(4) + calc(2)
// calc(4) → calc(3) + calc(1) = (calc(2)+calc(0)) + 1 = (2+0)+1 = 3
// calc(2) → calc(1) + calc(-1) = 1+0 = ... 재귀 추적 필요
```

> [!example] 기출 (2025년 1회차 20번) → **4**

[[프로그래밍-연습문제]] | [[C언어-핵심]] | [[Python-핵심]]
