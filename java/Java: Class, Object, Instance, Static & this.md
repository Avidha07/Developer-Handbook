# Java: Class, Object, Instance, Static & `this`

## 1. Class vs Object

- **Class** = blueprint/template.
- **Object** = actual instance created from a class using `new`.

```java
BankAccount a1 = new BankAccount();
BankAccount a2 = new BankAccount();
```

Here `a1` and `a2` are two different objects of `BankAccount`.

---

## 2. Instance Variable

An instance variable belongs to a particular object.

```java
class BankAccount {
    int balance = 1000;
}
```

Each object gets its **own copy**:

```text
a1 → balance = 5000
a2 → balance = 2000
```

So:

```java
a1.balance  // 5000
a2.balance  // 2000
```

---

## 3. Static Variable

A `static` variable belongs to the **class**, not to individual objects.

```java
class BankAccount {
    static String bankName = "SBI";
}
```

There is **one shared copy**:

```text
BankAccount
└── bankName = "SBI"
       ↑
      a1
      a2
```

If:

```java
a1.bankName = "HDFC";
```

then:

```java
System.out.println(a2.bankName);
```

prints:

```text
HDFC
```

Because both objects access the same static variable.

### Best practice

Instead of:

```java
a1.bankName
```

prefer:

```java
BankAccount.bankName
```

because it clearly shows that `bankName` belongs to the class.

---

## 4. Instance Method

An instance method belongs to an object and is normally called using an object.

```java
class BankAccount {

    int balance = 1000;

    void showBalance() {
        System.out.println(balance);
    }
}
```

Call:

```java
BankAccount a1 = new BankAccount();
a1.showBalance();
```

Because the method is called on `a1`, it works with `a1`'s state.

---

## 5. `this` Keyword ⭐

`this` refers to the **current object** inside an instance method.

```java
void showBalance() {
    System.out.println(this.balance);
}
```

If:

```java
a1.showBalance();
```

then:

```text
this → a1
this.balance → a1.balance
```

If:

```java
a2.showBalance();
```

then:

```text
this → a2
this.balance → a2.balance
```

### Easy rule

> **`this` = the object on which the current instance method was called.**

---

## 6. Static Method

A static method belongs to the class and does not need an object.

```java
class BankAccount {

    static void hello() {
        System.out.println("Hello");
    }
}
```

Call:

```java
BankAccount.hello();
```

A static method has **no particular object**, so it does not have `this`.

---

## 7. Static vs Instance

| Feature | Instance | Static |
|---|---|---|
| Belongs to | Object | Class |
| Copies | One per object | One shared copy |
| Object required to call? | Usually yes | No |
| `this` available? | Yes | No |
| Directly access instance variable? | Yes | No |
| Directly access static variable? | Yes | Yes |

### Remember this picture

```text
INSTANCE

a1 → balance = 5000
a2 → balance = 2000


STATIC

BankAccount
└── bankName = "HDFC"
       ↑
     a1 + a2
```

---

## 8. Most Important Rule

```text
Instance method
      ↓
has a current object
      ↓
`this` is available
      ↓
can access instance + static members


Static method
      ↓
no particular object
      ↓
no `this`
      ↓
can directly access static members
      ↓
cannot directly access instance members
```

### Example

```java
class BankAccount {

    int balance = 1000;
    static String bankName = "SBI";

    void show() {
        System.out.println(this.balance); // ✅
        System.out.println(bankName);      // ✅
    }

    static void test() {
        System.out.println(bankName);     // ✅
        // System.out.println(balance);   // ❌
    }
}
```

---

## 9. Interview One-Liners

**What is a class?**  
A blueprint/template used to create objects.

**What is an object?**  
An instance of a class containing its own instance state.

**What is an instance variable?**  
A variable whose separate copy belongs to each object.

**What is a static variable?**  
A class-level variable with one shared copy associated with the class.

**What is `this`?**  
A reference to the current object inside an instance method/constructor.

**Why can't static methods directly access instance variables?**  
Because a static method is class-level and has no particular object (`this`) to identify which instance variable should be used.

---

## 10. One Example to Remember Everything

```java
class BankAccount {

    int balance = 1000;              // instance
    static String bankName = "SBI";  // static

    void show() {                    // instance method
        System.out.println(this.balance);
        System.out.println(bankName);
    }

    static void changeBank() {       // static method
        bankName = "HDFC";
    }
}
```

Usage:

```java
BankAccount a1 = new BankAccount();
BankAccount a2 = new BankAccount();

a1.balance = 5000;
a2.balance = 2000;

BankAccount.changeBank();

a1.show();
a2.show();
```

Output:

```text
5000
HDFC
2000
HDFC
```

**Why?**

- `balance` → separate for each object.
- `bankName` → shared by all objects.
- `this.balance` → current object's balance.
- `changeBank()` → changes the shared static value.
