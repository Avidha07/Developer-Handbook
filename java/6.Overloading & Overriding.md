# Java Methods: Overloading & Overriding

Revision notes for Core Java. This document covers method overloading, constructor overloading, method overriding, primitive widening, extends, and @Override.

## 1. Method Overloading

Method overloading means defining multiple methods with the same name but different parameter lists.

```java
class Calculator {
    static int add(int a, int b) {
        return a + b;
    }

    static int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

### Three valid ways to overload

**A. Different number of parameters**

```java
void show(int a) { }
void show(int a, int b) { }
```

**B. Different parameter types**

```java
void show(int a) { }
void show(String a) { }
```

**C. Different parameter order**

```java
void show(int a, String b) { }
void show(String a, int b) { }
```

All three are valid because the parameter lists differ.

### Can we overload using only the return type?

No. The return type alone cannot distinguish overloaded methods.

```java
// Invalid: same name and same parameter list
static int calculate(int a) { return a * 2; }
static double calculate(int a) { return a * 2.0; }
```

Both methods have the signature `calculate(int)`, so this causes a compilation error.

## 2. How Java selects an overloaded method

Java resolves overloaded calls at compile time.

A useful simplified preference order is:

1. An applicable exact match.
2. An applicable primitive widening conversion.
3. Other applicable conversions, such as boxing/unboxing, according to Java's overload-resolution rules.

### Exact match

```java
static void display(int x) { System.out.println("int"); }
static void display(double x) { System.out.println("double"); }

public static void main(String[] args) {
    display(10);
}
```

Output:

```text
int
```

`10` is an `int` literal, so `display(int)` is an exact match.

### Widening conversion

```java
static void display(double x) {
    System.out.println("double");
}

public static void main(String[] args) {
    display(10);
}
```

Output:

```text
double
```

Java widens the `int` argument to `double`.

### Choosing between widening options

```java
static void show(long x) { System.out.println("long"); }
static void show(double x) { System.out.println("double"); }

public static void main(String[] args) {
    show(10);
}
```

Output:

```text
long
```

Both `int` to `long` and `int` to `double` are widening conversions. `long` is the more specific applicable choice here.

### Common primitive widening conversions

```text
byte -> short -> int -> long -> float -> double
char ------------> int -> long -> float -> double
```

This is a useful reference, not a claim that every primitive type converts to every type shown. `boolean` does not participate in numeric widening. For complicated overloads, use Java's full overload-resolution rules rather than relying only on a memorised ladder.

## 3. Constructor Overloading

Constructors can also be overloaded. A constructor has the same name as its class and has no return type.

```java
class Student {
    Student() {
        System.out.println("Default constructor");
    }

    Student(String name) {
        System.out.println("Name: " + name);
    }

    Student(String name, int age) {
        System.out.println(name + " " + age);
    }
}
```

Calls:

```java
Student s1 = new Student();
Student s2 = new Student("Avidha");
Student s3 = new Student("Avidha", 24);
```

Output:

```text
Default constructor
Name: Avidha
Avidha 24
```

Constructor overloading follows the same parameter-list principle as method overloading.

## 4. Method Overriding

Method overriding happens when a subclass provides its own implementation of an inherited instance method, using a compatible method signature.

```java
class Animal {
    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Bark");
    }
}
```

Example:

```java
Animal animal = new Dog();
animal.sound();
```

Output:

```text
Bark
```

The implementation is selected at runtime based on the actual object (`Dog`). This is runtime polymorphism.

### Important overriding rules

- Overriding requires an inheritance relationship.
- The overriding method must have a compatible signature.
- The overriding method cannot reduce the visibility of the inherited method.
- A `final` method cannot be overridden.
- A `private` method is not inherited and therefore is not overridden.
- Static methods are hidden, not overridden.
- `@Override` is optional but strongly recommended.

## 5. extends keyword

`extends` establishes a class inheritance relationship.

```java
class Parent {
    void display() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    // Child inherits accessible members from Parent.
}
```

Here, `Child` is a subclass of `Parent`.

Remember: `extends` establishes inheritance; it does not itself perform method overriding.

## 6. @Override annotation

`@Override` tells the compiler that a method is intended to override an inherited method.

```java
class Parent {
    void display() { }
}

class Child extends Parent {
    @Override
    void display() {
        System.out.println("Child display");
    }
}
```

If the method does not actually override a compatible inherited method, the compiler reports an error.

```java
class Parent {
    void display() { }
}

class Child extends Parent {
    @Override
    void displays() { } // Compilation error: does not override display()
}
```

`@Override` is optional, but it catches spelling and signature mistakes. It is useful in Core Java and Spring Boot.

## 7. Overloading vs Overriding

| Method Overloading | Method Overriding |
|---|---|
| Same method name | Same method signature (subject to overriding rules) |
| Different parameter lists | Compatible inherited method signature |
| Can happen in the same class | Happens in a subclass |
| Inheritance is not required | Inheritance is required |
| Compile-time polymorphism | Runtime polymorphism for instance methods |
| Return type alone cannot distinguish overloads | Return type must be compatible with the overridden method |

## 8. Common mistakes

1. Overloading does not mean same parameters; it requires different parameter lists.
2. Return type alone cannot create an overload.
3. Overriding is not just using the same method name; it involves an inherited method and a compatible signature.
4. `extends` and `@Override` are different: `extends` establishes inheritance; `@Override` asks the compiler to verify the intended override.
5. `@Override` is useful in all Java projects, not only Spring Boot.
6. Widening is not narrowing. Passing an `int` to a `long` parameter is allowed by widening; passing a `double` to an `int` parameter requires an explicit conversion and may lose information.

## 9. Interview quick revision

- What is method overloading? Same method name with different parameter lists.
- What is method overriding? A subclass provides its own implementation of an inherited method.
- Is overloading compile-time or runtime polymorphism? Compile-time.
- Is overriding compile-time or runtime polymorphism? Runtime polymorphism for instance methods.
- Can methods be overloaded by changing only the return type? No.
- Can constructors be overloaded? Yes.
- Can static methods be overloaded? Yes.
- Are static methods overridden? No. Static methods are hidden.
- Is `@Override` mandatory? No, but strongly recommended.
- What does `extends` do? Establishes class inheritance.

## 10. Practice questions

Try these before checking the answer key.

**Q1. What is the output?**

```java
class Demo {
    static void show(int x) { System.out.println("int"); }
    static void show(String x) { System.out.println("String"); }

    public static void main(String[] args) {
        show(10);
        show("Java");
    }
}
```

**Q2. Is this valid overloading?**

```java
void print(int a, String b) { }
void print(String a, int b) { }
```

**Q3. Is this valid overloading?**

```java
int calculate(int a) { return a * 2; }
double calculate(int a) { return a * 2.0; }
```

**Q4. Which overload is selected?**

```java
static void show(long x) { System.out.println("long"); }
static void show(double x) { System.out.println("double"); }

public static void main(String[] args) {
    show(10);
}
```

**Q5. What is the output?**

```java
class Parent {
    void sound() { System.out.println("Parent"); }
}

class Child extends Parent {
    @Override
    void sound() { System.out.println("Child"); }
}

public class Main {
    public static void main(String[] args) {
        Parent obj = new Child();
        obj.sound();
    }
}
```

### Answer key

1. `int`, then `String`.
2. Yes. The parameter order is different.
3. No. The parameter lists are identical; return type alone cannot overload a method.
4. `long`. Both are widening candidates, and `long` is more specific for this call.
5. `Child`. The actual object is a `Child`, so its overridden instance method runs.

## Progress tracker

- [x] Method overloading
- [x] Different parameter counts, types, and order
- [x] Return type restriction
- [x] Overload resolution and primitive widening
- [x] Constructor overloading
- [x] Method overriding
- [x] extends keyword
- [x] @Override annotation
- [x] Overloading vs overriding comparison
- [ ] Method scope and local variables
- [ ] Remaining Methods chapter topics

**Status:** Methods chapter — In Progress

Keep this as a focused revision reference. Add the remaining method topics as you learn them.
