# Java Fundamentals — Methods & Parameters

Revision notes, common mistakes to fix, and practice questions with simple, step-by-step answers.

> **How to use this:** Read the notes, then try the practice questions **before** looking at the answers.

## Table of Contents

1. [What is a Method?](#1-what-is-a-method)
2. [Parameters vs Arguments](#2-parameters-vs-arguments)
3. [`return` vs `void` vs `println`](#3-return-vs-void-vs-println)
4. [How a Method Call Executes](#4-how-a-method-call-executes)
5. [Pass-by-Value with Primitive Variables](#5-pass-by-value-with-primitive-variables)
6. [Pass-by-Value with Objects](#6-pass-by-value-with-objects)
7. [Common Confusions — Mistakes to Fix](#7-common-confusions--mistakes-to-fix)
8. [Practice Questions](#8-practice-questions)
9. [Answers with Simple Explanations](#9-answers-with-simple-explanations)
10. [Next Topics Still To Study](#10-next-topics-still-to-study)
11. [Final Quick Recall](#final-quick-recall)

---

## 1. What is a Method?

A **method** is a named block of code that performs a task. We call it whenever we need that task.

```java
static int add(int a, int b) {
    return a + b;
}
```

**Call it:**

```java
int result = add(10, 20);
System.out.println(result); // 30
```

### Method structure

```text
static int add(int a, int b)
  │     │   │       │
  │     │   │       └── parameters
  │     │   └────────── method name
  │     └────────────── return type
  └──────────────────── static modifier
```

- `static`: the method belongs to the class; call it using the class name, without creating an object.
- `int`: the method returns an integer.
- `add`: the method name.
- `int a, int b`: parameters.
- `return`: sends a value back to the caller.

> `static` is **not required** for every method. Instance methods are called on objects.

---

## 2. Parameters vs Arguments

```java
static int multiply(int a, int b) { // a and b are parameters
    return a * b;
}

int result = multiply(4, 5);        // 4 and 5 are arguments
```

| Term | Meaning | In the example |
|------|---------|----------------|
| **Parameters** | Variables declared in the method definition | `a`, `b` |
| **Arguments** | Actual values passed when calling the method | `4`, `5` |

**Output:** `20`

> **Remember:** parameter = receiving variable; argument = value supplied to it.

---

## 3. `return` vs `void` vs `println`

### A method that returns a value

```java
static int add(int a, int b) {
    return a + b;
}

int result = add(10, 20);
System.out.println(result); // 30
```

`return` sends `30` back to the caller. The caller can store or use that value.

### A `void` method that prints

```java
static void addAndPrint(int a, int b) {
    System.out.println(a + b);
}

addAndPrint(10, 20); // prints 30
```

- `void` means the method does **not return** a value.
- `System.out.println(...)` **prints** to the screen.
- `return` sends a value back; it does **not** print anything by itself.

### Important mistake to avoid

```java
static void calculate(int a, int b) {
    System.out.println(a + b);
}

int result = calculate(10, 20); // COMPILE ERROR
```

**Why?** A `void` method does not produce a value, so there is nothing to put into `result`.

**Correct if you only want to print:**

```java
calculate(10, 20);
```

**Correct if you need a result:**

```java
static int calculate(int a, int b) {
    return a + b;
}

int result = calculate(10, 20);
```

### Print + return in the same method

```java
static int calculate(int a, int b) {
    System.out.println(a + b);
    return a + b;
}

int result = calculate(10, 20);
System.out.println(result);
```

**Output:**

```text
30
30
```

The first `println` prints inside the method. Then `return` sends `30` to the caller, and the second `println` prints the stored result.

---

## 4. How a Method Call Executes

```java
static int multiply(int a, int b) {
    return a * b;
}

int result = multiply(4, 5);
```

**Flow:**

```text
multiply(4, 5)
      ↓
a = 4, b = 5
      ↓
a * b = 20
      ↓
return 20
      ↓
result = 20
```

The method runs, then control goes back to the caller at the place where the method was called.

---

## 5. Pass-by-Value with Primitive Variables

Java uses **pass-by-value**. For a primitive argument, the **value is copied** into the parameter.

```java
static void change(int x) {
    x = 100;
}

int number = 10;
change(number);
System.out.println(number); // 10
```

**Visual:**

```text
Before call: number = 10
             │ copy value 10
             ▼
Inside method: x = 10 → x = 100

After method: number is still 10
```

**Why?** `x` and `number` are **separate variables**. Changing `x` does not change `number`.

Calling the same method twice still leaves `number` as `10`:

```java
change(number);
change(number);
System.out.println(number); // 10
```

Each call gets a fresh copy of the current value `10`.

### If the returned value is assigned

```java
static int change(int x) {
    x = 100;
    return x;
}

int number = 10;
number = change(number);
System.out.println(number); // 100
```

The method did not directly change `number`. It **returned** `100`, and the **assignment** changed `number`.

> **Important:** `void` vs `int` describes the **return type**. Pass-by-value describes **how the argument is passed**. These are two different concepts.

---

## 6. Pass-by-Value with Objects

Java is **still pass-by-value** when passing objects. The value that gets copied is the **object reference**.

```java
class Account {
    int balance = 1000;
}

static void changeBalance(Account account) {
    account.balance = 5000;
}

Account a1 = new Account();
changeBalance(a1);
System.out.println(a1.balance); // 5000
```

**Why does it print `5000`?**
`a1` and the parameter `account` refer to the **same object**. Changing that object's field is visible through both references.

```text
a1 ───────────┐
              ▼
          Account object
          balance = 5000
              ▲
account ──────┘
```

### Changing an object's field vs reassigning the parameter

These are **different operations**.

**A. Mutate the same object:**

```java
static void changeBalance(Account account) {
    account.balance = 7000;
}
```

The caller sees the change, because both references point to the same object.

**B. Reassign the local parameter:**

```java
static void changeBalance(Account account) {
    account = new Account();
    account.balance = 7000;
}

Account a1 = new Account();
a1.balance = 1000;
changeBalance(a1);
System.out.println(a1.balance); // 1000
```

Inside the method, `account` now refers to a **new object**, but `a1` still refers to the **original object**.

```text
Before reassignment:
a1 ───────┐
          ▼
       Object A (balance = 1000)
          ▲
account ──┘

After account = new Account():
a1 ───────────► Object A (balance = 1000)

account ──────► Object B (balance = 7000)
```

When the method ends, the local parameter `account` goes out of scope. The new object may become eligible for garbage collection if nothing reachable points to it. It is not necessarily deleted immediately.

### Golden rule

- `account.balance = 7000` → changes the **field** of the referenced object.
- `account = new Account()` → changes only the **local parameter** to refer to another object.
- Java does **not** pass objects by reference. It passes a **copy of the reference value**.

---

## 7. Common Confusions — Mistakes to Fix

### Gap 1: `void` vs pass-by-value

A common wrong reason: *"It is `void`, so it cannot return anything; therefore the number stays the same."*

The output may be right, but the full reason is:

- `void` means no value is returned.
- `number` stays unchanged because the method only changes its **local copy** `x`.

Keep these as **two separate ideas**.

### Gap 2: Printing vs returning

- `System.out.println(value)` **displays** a value.
- `return value` **sends** a value back to the caller.
- A method can print and return the same value, so you may see the number printed **twice**.

### Gap 3: Reassigning an object parameter

- Changing `account.balance` affects the **shared object**.
- Reassigning `account` does **not** reassign `a1`.

### Gap 4: Explain the execution in order

For output questions, trace each line in this order:

1. Initial values.
2. Method call and argument copy.
3. Changes inside the method.
4. Return value, if any.
5. Assignment at the call site.
6. Final print statement.

Don't jump straight from "method called" to the output.

---

## 8. Practice Questions

Try each one yourself first.

### Q1. Parameters and arguments

```java
static int subtract(int x, int y) {
    return x - y;
}
System.out.println(subtract(9, 4));
```

- What is the output?
- Which are the parameters?
- Which are the arguments?

### Q2. `void` method

```java
static void show(int x) {
    System.out.println(x);
}
show(7);
```

Will it print `7`? Does it return a value?

### Q3. Compile error or not?

```java
static void show(int x) {
    System.out.println(x);
}
int n = show(7);
```

Does this compile? Why?

### Q4. Print and return

```java
static int doubleIt(int x) {
    System.out.println(x * 2);
    return x * 2;
}
int answer = doubleIt(6);
System.out.println(answer);
```

What are the two output lines?

### Q5. Primitive pass-by-value

```java
static void update(int x) {
    x = 50;
}
int n = 5;
update(n);
System.out.println(n);
```

What is printed, and why?

### Q6. Return + assignment

```java
static int update(int x) {
    x = 50;
    return x;
}
int n = 5;
n = update(n);
System.out.println(n);
```

What is printed, and what changes `n`?

### Q7. Object field mutation

```java
Account a = new Account(); // balance starts at 1000
static void update(Account x) {
    x.balance = 3000;
}
update(a);
System.out.println(a.balance);
```

What is printed, and why?

### Q8. Object parameter reassignment

```java
Account a = new Account(); // balance starts at 1000
static void update(Account x) {
    x = new Account();
    x.balance = 3000;
}
update(a);
System.out.println(a.balance);
```

What is printed, and why?

### Q9. Two calls

```java
static void update(int x) {
    x = 99;
}
int n = 4;
update(n);
update(n);
System.out.println(n);
```

What is printed?

### Q10. Explain in your own words

Explain the difference between:

1. `System.out.println(x)`
2. `return x`
3. `x = 100`

---

## 9. Answers with Simple Explanations

> Check these only after you have tried the questions.

### A1. Parameters and arguments

**Output:** `5`

- **Parameters:** `x` and `y` (the variables in the method definition).
- **Arguments:** `9` and `4` (the values we pass when calling).

**Steps:** `x = 9`, `y = 4` → `9 - 4 = 5` → `return 5` → `println` prints `5`.

### A2. `void` method

**Yes, it prints `7`. No, it does not return a value.**

The method only prints. `void` means "I will not give any value back."

### A3. Compile error or not?

**No, it does not compile.**

`show` is `void`, so it gives back nothing. But `int n = show(7);` tries to put a value into `n`. There is no value to put, so Java shows a compile-time error.

### A4. Print and return

**Output:**

```text
12
12
```

**Steps:**

1. `doubleIt(6)` is called, so `x = 6`.
2. Inside the method, `println(x * 2)` prints **12** (first line).
3. `return x * 2` sends `12` back.
4. `answer = 12`.
5. `println(answer)` prints **12** (second line).

### A5. Primitive pass-by-value

**Output:** `5`

**Why?** When we call `update(n)`, Java copies the value `5` into `x`. The method changes only `x` to `50`. The original `n` is a separate variable, so it stays `5`.

### A6. Return + assignment

**Output:** `50`

**What changes `n`?** The **assignment** `n = update(n)`.

**Steps:**

1. `x` gets a copy of `5`.
2. `x` becomes `50`.
3. `return x` sends `50` back.
4. `n = 50` stores it in `n`.

The method did not change `n` by itself. The returned value, assigned back to `n`, did.

### A7. Object field mutation

**Output:** `3000`

**Why?** For objects, Java copies the **reference** (the address of the object). So `a` and `x` both point to the **same object**. `x.balance = 3000` changes that shared object, and `a.balance` shows the change.

### A8. Object parameter reassignment

**Output:** `1000`

**Why?**

1. At the start, `a` and `x` both point to the original object (balance `1000`).
2. `x = new Account()` makes `x` point to a **new object**. Now `x` and `a` point to different objects.
3. `x.balance = 3000` changes the **new** object only.
4. `a` still points to the original object, so `a.balance` is still `1000`.

**Simple way to remember:** changing the *thing inside the object* is visible to the caller. Pointing the parameter to a *new object* is not.

### A9. Two calls

**Output:** `4`

**Why?** Each call gets its own fresh copy of `4`. Both times, only the local copy `x` becomes `99`. The original `n` never changes.

### A10. Explain in your own words

1. **`System.out.println(x)`** → *shows* the value of `x` on the screen. It does not send anything back to the caller.
2. **`return x`** → *sends* the value of `x` back to the code that called the method. It does not print anything.
3. **`x = 100`** → *changes* what is stored in the variable `x`. It does not print or return anything.

**Quick example:** a cashier who *shows* you the bill is like `println`. A cashier who *hands* you the bill is like `return`. Writing a new amount on the bill is like `x = 100`.

---

## 10. Next Topics Still To Study

This chapter is not finished yet. Still to cover:

- [ ] Method overloading
- [ ] Method signature
- [ ] Varargs (`...`)
- [ ] Recursion basics
- [ ] More method/parameter coding and interview questions

After these, revise the whole chapter and take a test **without** the answer key.

---

## Final Quick Recall

```text
Parameters     = variables in method definition
Arguments      = values passed at method call
void           = returns no value
return         = sends a value to caller
println        = prints to console
Primitive      = argument value is copied
Object         = reference value is copied
Field mutation = shared object's field can change
Reassignment   = only local parameter points elsewhere
```
