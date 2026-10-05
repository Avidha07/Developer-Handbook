# Java Fundamentals — JDK, JRE, JVM & Bytecode

## 1. Big Picture

```text
Main.java
   │
   │ javac Main.java
   ▼
Main.class
(Bytecode)
   │
   │ java Main
   ▼
JVM
   │
   ├── Interpreter
   └── JIT Compiler
          │
          ▼
   Native Machine Code
          │
          ▼
         CPU
```

---

## 2. JDK, JRE, JVM

Easy hierarchy:

```text
JDK
└── JRE
    └── JVM
```

### JDK — Java Development Kit
Java programs **develop** karne ke tools deta hai.

Important tools:

```text
javac → compiler
java  → launcher
javap → bytecode/class inspection
```

```bash
javac Main.java
```

**Why?** `.java` source code ko compile karke `.class` bytecode banane ke liye.

### JRE — Java Runtime Environment
Java applications ko **run** karne ka runtime environment.

Conceptually:

```text
JRE
└── JVM + runtime libraries
```

Important: `javac` JRE ka job nahi hai. Compilation JDK ke compiler se hoti hai.

### JVM — Java Virtual Machine
`.class` bytecode ko execute karti hai.

Different platforms ke liye JVM implementations different hoti hain:

```text
Mac → Mac JVM
Linux → Linux JVM
Windows → Windows JVM
```

Same Java bytecode different JVMs par run ho sakta hai.

---

## 3. Java Compilation

Suppose:

```java
public class Main {
    public static void main(String[] args) {
        int a = 10;
        int b = 20;
        int sum = a + b;
        System.out.println(sum);
    }
}
```

### Step 1

```text
Main.java
```

Human-readable source code.

### Step 2

```bash
javac Main.java
```

Result:

```text
Main.class
```

This contains **JVM bytecode**.

### Step 3

```bash
java Main
```

Java launcher JVM ko start karke `Main` class ko execute karwata hai.

---

## 4. Bytecode

Bytecode `.class` file mein stored intermediate instructions hain.

```text
Java source
    ↓
javac
    ↓
Bytecode
    ↓
JVM
    ↓
Native execution
```

Bytecode platform-independent design ki wajah se Java ka basic idea hai:

> **Write Once, Run Anywhere**

Important: bytecode directly OS/CPU ka machine code nahi hota. JVM usse execute/translate/compile karti hai.

---

## 5. `java Main` vs `java Main.class`

Correct:

```bash
java Main
```

Incorrect:

```bash
java Main.class
```

`java` launcher **class name** expect karta hai, `.class` filename nahi.

```text
Main.class → file
Main       → class name
```

---

## 6. `javap` — Bytecode Dekhna

```bash
javap -c Main
```

**Why?** Compiled class ke bytecode instructions inspect karne ke liye.

You may see:

```text
iload
istore
iadd
getstatic
invokevirtual
return
```

---

## 7. JVM Stack-Based Execution

JVM bytecode uses an **operand stack** inside a method's stack frame.

For:

```java
int a = 10;
int b = 20;
int sum = a + b;
```

Conceptually:

```text
load a
  ↓
[10]

load b
  ↓
[10, 20]

add
  ↓
[30]

store sum
  ↓
sum = 30
```

Important instructions:

```text
iload  → int local variable ko operand stack par load
istore → operand stack se int ko local variable slot mein store
iadd   → two integers add
return → method execution complete
```

**Important distinction:** JVM stack aur operand stack same thing nahi hain. Each thread has a JVM stack; each method call has a stack frame, and the operand stack is part of that frame.

---

## 8. Default Constructor

Agar class mein constructor nahi likha:

```java
class Test {
}
```

compiler normally default no-argument constructor provide karta hai.

Conceptually:

```java
Test() {
    super();
}
```

Isliye `javap` output mein constructor dikh sakta hai even when you didn't write one.

---

## 9. Interpreter & JIT

### Interpreter
Bytecode instructions ko execute karta hai.

### JIT — Just-In-Time Compiler
Frequently executed **hot code** ko runtime par native machine code mein compile/optimize kar sakta hai.

```text
Hot code
   ↓
JIT
   ↓
Native machine code
   ↓
Faster repeated execution
```

JIT ka purpose runtime performance improve karna hai. JVM har method ko immediately native code mein compile nahi karti.

---

## 10. JIT Mental Model

```text
JVM starts
   ↓
code executes
   ↓
JVM observes execution
   ↓
hot code identified
   ↓
JIT compiles/optimizes it
   ↓
native execution
```

JIT compilation ka khud cost hota hai, isliye JVM hot code par optimization ka benefit leti hai.

---

## 11. `main()` Method

Traditional Java entry point:

```java
public static void main(String[] args)
```

Breakdown:

```text
public  → launcher ke liye accessible
static  → traditional class-level entry point
void    → returns nothing
main    → entry-point method name
String[] args → command-line arguments
```

### Java 25 note

Modern Java versions, including Java 25, additional/flexible main forms support karti hain.

Isliye blindly ye mat memorize karo:

> "`main` MUST always be static."

Lekin interviews aur traditional Java applications mein:

```java
public static void main(String[] args)
```

standard form hai.

---

## 12. Useful Terminal Commands

### Project folder

```bash
mkdir JavaBehindScenes
```

**Why?** New project directory banane ke liye.

### Folder mein jaana

```bash
cd JavaBehindScenes
```

**Why?** Us directory ke andar kaam karne ke liye.

### Current location

```bash
pwd
```

**Why?** Abhi kis directory mein ho, dekhne ke liye.

### Files dekhna

```bash
ls
```

**Why?** Current folder ke files dekhne ke liye.

### Compile

```bash
javac Main.java
```

**Why?** `.java → .class` bytecode.

### Run

```bash
java Main
```

**Why?** Main class ko JVM ke through execute karne ke liye.

### Bytecode inspect

```bash
javap -c Main
```

**Why?** Compiled bytecode instructions dekhne ke liye.

---

## 13. Complete Mental Model ⭐

```text
             Main.java
           Source Code
                │
                │ javac
                ▼
            Main.class
              Bytecode
                │
                │ java Main
                ▼
               JVM
                │
        ┌───────┴────────┐
        │                │
   Interpreter          JIT
        │                │
        └───────┬────────┘
                ▼
       Native Machine Code
                │
                ▼
               CPU
```

And:

```text
JDK
└── JRE
    └── JVM
```

---

# Interview Quick Revision

### JDK?
Java Development Kit — Java development ke tools, including compiler.

### JRE?
Java Runtime Environment — Java application run karne ka runtime environment.

### JVM?
Java Virtual Machine — JVM bytecode execute karti hai.

### `.java` → `.class`?

```bash
javac Main.java
```

### `.class` mein kya hota hai?
JVM bytecode.

### Same bytecode different OS par kaise?
Different platforms ke JVM implementations same bytecode ko execute karte hain.

### JIT?
Hot/frequently executed code ko runtime par native machine code mein compile/optimize karke performance improve karta hai.

### `java Main.class` kyun nahi?
Java launcher class name expect karta hai:

```bash
java Main
```

### `javap -c Main` kyun?
Bytecode inspect karne ke liye.

---

# Must Remember 🔥

```text
.java  → source code
javac  → compile
.class → bytecode
java   → launch
JVM    → execute bytecode
JIT    → optimize hot code
```

Most important:

```text
JDK
└── JRE
    └── JVM
```

> **Java source code is compiled into bytecode, and the JVM executes that bytecode.**
