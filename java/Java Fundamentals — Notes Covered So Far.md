# Java Fundamentals — Notes Covered So Far

Quick-reference notes on how Java works behind the scenes, plus core OOP basics: classes, objects, instance vs static members, `this`, and `main()`.

## Table of Contents

1. [JDK, JRE, JVM](#1-jdk-jre-jvm)
2. [Compile, Run & Inspect](#2-compile-run--inspect)
3. [Bytecode, Operand Stack, Interpreter & JIT](#3-bytecode-operand-stack-interpreter--jit)
4. [Class and Object](#4-class-and-object)
5. [Instance Variables](#5-instance-variables)
6. [Static Variables](#6-static-variables)
7. [Instance Methods & `this`](#7-instance-methods--this)
8. [Static Methods](#8-static-methods)
9. [Static vs Instance](#9-static-vs-instance)
10. [`main()` Method](#10-main-method)
11. [Interview Quick Revision](#interview-quick-revision)
12. [Remember This Picture](#remember-this-picture)

---

## 1. JDK, JRE, JVM

| Term | Full form | Purpose |
|------|-----------|---------|
| **JDK** | Java Development Kit | Java development tools, including `javac` |
| **JRE** | Java Runtime Environment | Runtime environment and libraries needed to run Java applications |
| **JVM** | Java Virtual Machine | Executes Java bytecode |

**Conceptual hierarchy:**

```text
JDK
└── JRE
    └── JVM
```

Different operating systems have suitable JVM implementations, so the same bytecode can run on different platforms.

---

## 2. Compile, Run & Inspect

```bash
javac Main.java
```
**Why:** Compiles Java source into a `.class` file containing bytecode.

```bash
java Main
```
**Why:** Launches the `Main` class through the Java runtime/JVM. Use the **class name**, not `Main.class`.

```bash
javap -c Main
```
**Why:** Inspects bytecode instructions in the compiled class.

**Other terminal commands:**

```bash
mkdir JavaBehindScenes  # create a folder
cd JavaBehindScenes     # enter the folder
pwd                     # show current folder
ls                      # list files
```

---

## 3. Bytecode, Operand Stack, Interpreter & JIT

- **Bytecode:** intermediate instructions in `.class`; not CPU-specific machine code.
- **Interpreter:** executes bytecode instructions.
- **JIT (Just-In-Time compiler):** can compile frequently executed "hot" code into native machine code to improve performance. Not every method is JIT-compiled immediately.
- **Operand stack:** part of a method's stack frame; not the same as the entire JVM thread stack.

**Example:**

```java
int a = 10;
int b = 20;
int sum = a + b;
```

**Conceptually:**

```text
iload  → load an int local variable onto the operand stack
iadd   → add two integers on the operand stack
istore → store an int from the operand stack in a local-variable slot
```

> If no constructor is declared, Java normally supplies a **default no-argument constructor**.

---

## 4. Class and Object

- **Class:** blueprint/template.
- **Object:** an instance created using `new`.

```java
BankAccount a1 = new BankAccount();
BankAccount a2 = new BankAccount();
```

`a1` and `a2` refer to **two separate objects**.

---

## 5. Instance Variables

An instance variable belongs to an **individual object**.

```java
class BankAccount {
    int balance = 1000;
}
```

```java
a1.balance = 5000;
a2.balance = 2000;
```

```text
a1.balance → 5000
a2.balance → 2000
```

Changing one object's balance does **not** change the other's.

---

## 6. Static Variables

A static variable belongs to the **class** and is **shared**.

```java
class BankAccount {
    static String bankName = "SBI";
}
```

If:

```java
a1.bankName = "HDFC";
```

then `a2.bankName` also reads `"HDFC"` because both access the same static variable.

**Best practice:** use the class name to access static members:

```java
BankAccount.bankName = "HDFC";
```

---

## 7. Instance Methods & `this`

An instance method is called on an object:

```java
void showBalance() {
    System.out.println(this.balance);
}
```

- `this` refers to the **current object** inside an instance method.
- `a1.showBalance()` → `this` refers to `a1`.
- `a2.showBalance()` → `this` refers to `a2`.

**Example:**

```java
a1.balance = 5000;
a2.balance = 2000;
a2.showBalance(); // prints 2000
```

---

## 8. Static Methods

A static method belongs to the **class** and can be called without creating an object.

```java
static void hello() {
    System.out.println("Hello");
}
```

```java
BankAccount.hello();
```

A static method has no particular current object, so `this` is unavailable.

### Access rules

- An instance method can directly access **instance and static** members.
- A static method can directly access **static** members.
- A static method **cannot directly access** an instance variable because no particular object is specified.
- A static method **can** access an instance variable through an object reference:

```java
static void showBalance(BankAccount account) {
    System.out.println(account.balance); // valid
}
```

---

## 9. Static vs Instance

| Feature | Instance | Static |
|---------|----------|--------|
| Belongs to | Object | Class |
| Variable state | Each object has its own | One shared class-level variable |
| `this` in method | Yes | No |
| Directly access instance variables | Yes | No |
| Directly access static variables | Yes | Yes |
| Preferred variable access | `a1.balance` | `BankAccount.bankName` |

---

## 10. `main()` Method

Traditional entry point:

```java
public static void main(String[] args)
```

| Part | Meaning |
|------|---------|
| `public` | Accessible to the launcher |
| `static` | Traditional class-level entry point |
| `void` | Returns no value |
| `String[] args` | Command-line arguments |

> Modern Java versions, including Java 25, support additional/flexible `main` forms. So "`main` must always be `static`" is not universally true for modern Java.

---

## Interview Quick Revision

| # | Question | Answer |
|---|----------|--------|
| 1 | What compiles `.java` into `.class`? | `javac` |
| 2 | What is in `.class`? | JVM bytecode |
| 3 | How do you run a class? | `java Main` |
| 4 | How do you inspect bytecode? | `javap -c Main` |
| 5 | What is a class? | A blueprint for objects |
| 6 | What is an object? | An instance of a class |
| 7 | Instance variable? | Each object has its own value |
| 8 | Static variable? | One shared class-level value |
| 9 | What is `this`? | Reference to the current object in an instance method/constructor |
| 10 | Why can't a static method directly access an instance variable? | It has no particular object (`this`) to identify which object's variable to use |

---

## Remember This Picture

**Instance:**

```text
a1 → balance = 5000
a2 → balance = 2000
```

**Static:**

```text
BankAccount.bankName = "HDFC"
       ↑
   shared by a1 and a2
```

### Must remember

- `.java` → `javac` → `.class` bytecode → JVM
- `this` refers to the object currently executing an instance method.
- Instance variables are per-object; static variables are shared by the class.
- Static methods have no `this` and cannot directly access instance variables.
