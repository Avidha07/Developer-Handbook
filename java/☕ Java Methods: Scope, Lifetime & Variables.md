# ☕ Java Methods: Scope, Lifetime & Variables

> Core Java revision notes covering **local variables, block scope, variable lifetime, static variables, shadowing, instance variables, and default values.**
---

## 1. 🔍 Variable Scope

**Scope** is the region of a program where a variable can be accessed.

### Local variable

A variable declared inside a method, constructor, or block is a **local variable**.

```java
class Demo {
    static void show() {
        int age = 25; // Local variable
        System.out.println(age);
    }

    public static void main(String[] args) {
        show();
    }
}
```

**Output:**

```text
25
```

`age` can be accessed inside `show()`, but it cannot be directly accessed from `main()`.

```java
static void show() {
    int number = 100;
}

public static void main(String[] args) {
    show();
    System.out.println(number); // ❌ Compilation error
}
```

> ❓ **Why?** `number` is local to `show()`. Its scope does not include `main()`.

---

## 2. 🧱 Block Scope

A variable declared inside a block `{ }` is accessible only within that block.

```java
class Demo {
    public static void main(String[] args) {
        if (true) {
            int age = 25;
            System.out.println(age); // 25
        }

        // System.out.println(age); // ❌ Compilation error
    }
}
```

The variable `age` is in scope inside the `if` block. Its scope ends at the block's closing brace.

> 💡 **Remember:** A variable is accessible only within its declared scope.

---

## 3. ⏳ Variable Scope vs Variable Lifetime

These are related but different concepts:

| Concept | Question it answers |
|---|---|
| **Scope** | Where can the variable be accessed in the source code? |
| **Lifetime** | During what period does the variable exist during program execution? |

For a local variable, its lifetime is associated with execution of its enclosing method or block. Each new method invocation gets its own local-variable instance.

### Example: Calling a method more than once

```java
class Demo {
    static void show() {
        int count = 0;
        count++;
        System.out.println(count);
    }

    public static void main(String[] args) {
        show();
        show();
        show();
    }
}
```

**Output:**

```text
1
1
1
```

Each call to `show()` creates a fresh local `count`, initialized to `0`. It is incremented to `1`, printed, and its invocation ends.

> 📌 The local variable does not retain its previous value between calls.

---

## 4. ⚖️ Static Variable vs Local Variable

A **static variable** belongs to the class and is shared by its instances. A **local variable** belongs to an execution of its enclosing method or block.

```java
class Demo {
    static int count = 10; // Static variable

    static void show() {
        int number = 5;     // Local variable

        count++;
        number++;

        System.out.println(count + " " + number);
    }

    public static void main(String[] args) {
        show();
        show();
    }
}
```

**Output:**

```text
11 6
12 6
```

| Method call | Static `count` | Local `number` | Output |
|---|---|---|---|
| First `show()` | 11 | 6 | `11 6` |
| Second `show()` | 12 | 6 | `12 6` |

- ✅ `count` is shared and retains its updated value between calls.
- ✅ `number` is initialized to `5` on every call, then incremented to `6`.

---

## 5. 🎭 Variable Shadowing

Variable shadowing occurs when a variable declared in an inner scope has the same name as a variable in an outer scope. The inner variable takes precedence within its scope.

```java
class Demo {
    static int number = 100; // Class-level static variable

    static void show() {
        int number = 50;     // Local variable shadows the static variable
        System.out.println(number);
    }

    public static void main(String[] args) {
        show();
        System.out.println(number);
    }
}
```

**Output:**

```text
50
100
```

### Why?

- Inside `show()`, the local `number` is found first, so it prints `50`.
- In `main()`, there is no local variable named `number`, so the static field is accessed and `100` is printed.

You can explicitly refer to the static field using the class name:

```java
System.out.println(Demo.number);
```

---

## 6. 🆚 Local Variable vs Instance Variable

An **instance variable** is declared inside a class but outside methods, constructors, and blocks. Each object gets its own instance fields.

```java
class Student {
    int age = 25; // Instance variable

    void display() {
        int marks = 90; // Local variable

        System.out.println(age);
        System.out.println(marks);
    }
}
```

| Feature | Local variable | Instance variable |
|---|---|---|
| Declaration | Inside a method, constructor, or block | Inside class, outside methods/constructors/blocks |
| Belongs to | Enclosing method/block execution | An object |
| Default value | No automatic default value | Yes |
| Scope | Declaring block | Available through the object, subject to access rules |
| Lifetime | Associated with enclosing execution | As long as the object exists |
| Stored as | Not an object field | Part of object state |

### ⚠️ Local variables must be initialized

```java
void display() {
    int marks;
    // System.out.println(marks); // ❌ Compilation error
}
```

Local variables do not receive default values. They must be definitely assigned before they are read.

---

## 7. 🧮 Default Values of Instance and Static Fields

Instance and static fields receive default values when they are created/initialized by the JVM.

| Field type | Default value |
|---|---|
| `byte`, `short`, `int`, `long` | `0` |
| `float` | `0.0f` |
| `double` | `0.0d` |
| `char` | `'\u0000'` |
| `boolean` | `false` |
| Reference types (for example, `String`) | `null` |

**Example:**

```java
class Student {
    int age;
    String name;
    boolean active;

    void display() {
        System.out.println(age);    // 0
        System.out.println(name);   // null
        System.out.println(active); // false
    }
}
```

> ⚠️ **Important distinction:** These defaults apply to **fields** (instance and static), **not** local variables.

---

## 8. 👥 Instance Variables Belong to Individual Objects

```java
class Student {
    int age;
}

public class Main {
    public static void main(String[] args) {
        Student s1 = new Student();
        Student s2 = new Student();

        s1.age = 20;
        s2.age = 25;

        System.out.println(s1.age);
        System.out.println(s2.age);
    }
}
```

**Output:**

```text
20
25
```

**Conceptually:**

```text
s1  ----> Student object A: age = 20
s2  ----> Student object B: age = 25
```

Each object has its own copy of the instance field `age`.

---

## 9. 📝 Practice Questions

Try to predict each output before checking the answers.

### Q1. Local variable scope

```java
class Demo {
    static void show() {
        int number = 100;
    }

    public static void main(String[] args) {
        show();
        System.out.println(number);
    }
}
```

Does this compile? Why?

### Q2. Block scope

```java
public class Demo {
    public static void main(String[] args) {
        if (true) {
            int age = 25;
            System.out.println(age);
        }

        System.out.println(age);
    }
}
```

Does this compile? Explain.

### Q3. Local variable lifetime

```java
class Demo {
    static void show() {
        int count = 0;
        count++;
        System.out.println(count);
    }

    public static void main(String[] args) {
        show();
        show();
        show();
    }
}
```

What is the output?

### Q4. Static vs local

```java
class Demo {
    static int count = 10;

    static void show() {
        int number = 5;
        count++;
        number++;
        System.out.println(count + " " + number);
    }

    public static void main(String[] args) {
        show();
        show();
    }
}
```

What is the output? Explain why the two variables behave differently.

### Q5. Variable shadowing

```java
class Demo {
    static int number = 100;

    static void show() {
        int number = 50;
        System.out.println(number);
    }

    public static void main(String[] args) {
        show();
        System.out.println(number);
    }
}
```

What is the output?

### Q6. Instance variable vs local variable

```java
class Employee {
    int salary = 50000;

    void calculate() {
        int bonus = 5000;
        salary = salary + bonus;
    }

    public static void main(String[] args) {
        Employee e1 = new Employee();
        Employee e2 = new Employee();

        e1.calculate();

        System.out.println(e1.salary);
        System.out.println(e2.salary);
    }
}
```

What is the output? Explain why `e2.salary` does not change.

---

## 10. ✅ Answer Key

<details>
<summary><b>Click to reveal answers</b></summary>

1. **Compilation error.** `number` is local to `show()` and cannot be accessed directly from `main()`.
2. **Compilation error.** `age` is declared inside the `if` block and is out of scope after the block.
3. **Output is `1`, `1`, `1`.** Each method call gets a fresh local `count`.
4. **Output is `11 6`, then `12 6`.** Static `count` is shared; local `number` is recreated on each call.
5. **Output is `50`, then `100`.** The local variable shadows the static field inside `show()`.
6. **Output is `55000`, then `50000`.** `salary` is an instance variable. Only `e1` is updated when `e1.calculate()` runs.

</details>

---

## 🚀 Progress Tracker

- [x] Local variables
- [x] Method scope
- [x] Block scope
- [x] Variable scope vs lifetime
- [x] Static vs local variables
- [x] Variable shadowing
- [x] Local vs instance variables
- [x] Default values of fields
- [x] Object-specific instance variables

**Status:** Method Scope & Variables — ✅ Completed

> The broader Methods chapter can remain **In Progress** until all planned Methods topics and revision are completed.
