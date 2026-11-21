# Java Fundamentals: Packages & Imports

> **Personal Revision & Self-Assessment**
> Use this README to *check* your understanding, not just to re-read definitions.
> If you cannot explain a concept without looking, mark it **Needs Revision**.

---

## Table of Contents

1. [What is a Package?](#1-what-is-a-package)
2. [Order: package, import, class](#2-order-package-import-class)
3. [What does `import` do?](#3-what-does-import-do)
4. [Types of imports](#4-types-of-imports)
5. [Wildcard imports do NOT include subpackages](#5-wildcard-imports-do-not-include-subpackages)
6. [Naming conflicts](#6-naming-conflicts-same-class-name-different-packages)
7. [What is an FQCN?](#7-what-is-an-fqcn)
8. [Practical example](#8-practical-example-i-completed)
9. [Import does not give access](#9-import-does-not-give-access)
10. [Common confusions](#10-common-confusions-to-avoid)
11. [Self-test](#11-self-test-try-without-looking-above)
12. [Revision tracker](#12-my-revision-tracker)
13. [Weak-point notes](#13-my-weak-point-notes)

---

## 1. What is a Package?

A **package** is a way to group related Java classes, interfaces, enums, etc. Think of it like a **folder** that keeps related code together.

**Why do we use packages?**

- To organize code into meaningful groups
- To avoid name clashes between classes
- To make code easier to maintain
- To help control who can access a class or member

**Example**

```java
package com.company.model;

public class Employee {
}
```

The package name here is `com.company.model`.

> **Remember:** The `package` line is normally the **first statement** in a Java file (before imports and the class).

**Self-check**

- [ ] I can explain why packages are useful in my own words.
- [ ] I understand that a package name is not the same as a class name.

---

## 2. Order: package, import, class

A Java file usually follows this order:

```java
package com.company.main;

import com.company.model.Employee;

public class Main {
    // code
}
```

**Order to remember**

1. `package` declaration
2. `import` statements
3. Class / interface / enum

**Self-check**

- [ ] I know where `package` goes.
- [ ] I know where `import` goes.
- [ ] I can spot a wrongly ordered package/import statement.

---

## 3. What does `import` do?

`import` lets me use a class from another package by its **short name**, instead of writing its **full name** everywhere.

**Without import**

```java
java.util.ArrayList<String> names = new java.util.ArrayList<>();
```

**With import**

```java
import java.util.ArrayList;

// Later in code:
ArrayList<String> names = new ArrayList<>();
```

> **Important:** Importing a class does **not** give access to its private or package-private members. `import` only helps Java find the *name*. Access modifiers still decide who can use what.

**Self-check**

- [ ] I can explain what `import` does.
- [ ] I know `import` does not change access permissions.
- [ ] I can write the same code with and without an import.

---

## 4. Types of imports

### A. Single-type import

Imports **one** specific class.

```java
import java.util.ArrayList;
import java.util.HashMap;
```

These are separate imports. Writing many single imports is perfectly valid.

### B. Wildcard import

```java
import java.util.*;
```

This lets me use any class directly inside `java.util` by its short name.

### C. Fully qualified name (no import needed)

```java
java.util.ArrayList<String> names = new java.util.ArrayList<>();
```

I can write the full name directly in code instead of importing.

**Self-check**

- [ ] I can tell single-type imports and wildcard imports apart.
- [ ] I know multiple individual imports are valid.
- [ ] I can recognize a fully qualified class name.

---

## 5. Wildcard imports do NOT include subpackages

This was an important checkpoint in my learning.

```java
import java.util.*;
```

This imports classes **directly inside** `java.util`. It does **not** import classes from its subpackages, like `java.util.concurrent`.

So this is valid, and both lines are needed:

```java
import java.util.*;
import java.util.concurrent.*;
```

**Remember**

| Import | What it covers |
|---|---|
| `java.util.*` | Classes directly in `java.util` |
| `java.util.concurrent.*` | Classes directly in `java.util.concurrent` |

A wildcard import is **not recursive**.

**Self-check**

- [ ] I understand why `java.util.*` does not cover `java.util.concurrent`.
- [ ] I can explain why both wildcard imports can be written together.
- [ ] I won't assume wildcard imports are recursive.

---

## 6. Naming conflicts: same class name, different packages

Two packages can have classes with the **same simple name**.

```
com.company.employee.Employee
com.company.manager.Employee
```

Both can exist because their **full names (FQCNs)** are different.

**What if I need both?**

Don't import both and then use just `Employee`. Java won't know which one you mean. Instead, use full names:

```java
com.company.employee.Employee employee =
        new com.company.employee.Employee();

com.company.manager.Employee manager =
        new com.company.manager.Employee();
```

> Java does **not** support import aliases like `import ... as ...` (unlike Python).

**Self-check**

- [ ] I understand how same-named classes can exist in different packages.
- [ ] I understand why importing two same-named classes causes a conflict.
- [ ] I can fix the conflict using fully qualified names.
- [ ] I don't confuse this with inheritance or OOP relationships.

---

## 7. What is an FQCN?

**FQCN = Fully Qualified Class Name**

It is the **package name + class name**.

**Example:**

```
com.company.main.Main
```

**Where did I use it?** When I ran my program:

```bash
java -cp out com.company.main.Main
```

`com.company.main.Main` is the class's fully qualified name.

**Self-check**

- [ ] I can define FQCN.
- [ ] I can spot an FQCN in Java code.
- [ ] I understand why the run command uses `com.company.main.Main` and not just `Main`.

---

## 8. Practical example I completed

### Folder structure

```
JavaPackages/
└── src/
    └── com/
        └── company/
            ├── main/
            │   └── Main.java
            └── model/
                └── Employee.java
```

### `Employee.java`

```java
package com.company.model;

public class Employee {
    public int id;
    public String name;

    public void displayDetails() {
        System.out.println("Employee ID: " + id);
        System.out.println("Employee Name: " + name);
    }
}
```

### `Main.java`

```java
package com.company.main;

import com.company.model.Employee;

public class Main {
    public static void main(String[] args) {
        Employee emp = new Employee();
        emp.id = 101;
        emp.name = "Avidha";
        emp.displayDetails();
    }
}
```

### Compile and run

```bash
javac -d out src/com/company/model/Employee.java src/com/company/main/Main.java
java -cp out com.company.main.Main
```

**Expected output**

```
Employee ID: 101
Employee Name: Avidha
```

### What do these commands mean?

| Command / part | Meaning |
|---|---|
| `javac` | Java compiler |
| `-d out` | Put the generated `.class` files in the `out` folder |
| `src/.../*.java` | The Java source files being compiled |
| `java` | Starts the Java program |
| `-cp out` | `out` is the classpath root where compiled classes live |
| `com.company.main.Main` | Fully qualified name of the class to run |

**Self-check**

- [ ] I can recreate this folder structure.
- [ ] I can explain what the package declaration means in both files.
- [ ] I can explain the import in `Main.java`.
- [ ] I can compile and run this without blindly copying commands.
- [ ] I understand the difference between `.java` and `.class` files.

---

## 9. Import does not give access

Importing a class does **not** make its fields or methods accessible.

Example: a package-private member can't be used from a different package just because the class was imported.

| Access modifier | Who can access |
|---|---|
| `public` | Anywhere the class/member is visible |
| `private` | Only inside the same class |
| *(no modifier)* package-private | Only inside the same package |
| `protected` | Same package, plus subclasses (following Java's protected rules) |

In this exercise, I made the fields `public` only so the cross-package demo would work.

> **Real code reminder:** Fields are usually `private` and accessed through methods/constructors. This is called **encapsulation**, and I'll study it in depth in the Access Modifiers topic.

**Self-check**

- [ ] I know `import` does not bypass access control.
- [ ] I understand why package-private fields failed across my two packages.
- [ ] I know making fields `public` fixed the demo, but it isn't automatically good design.

---

## 10. Common confusions to avoid

Check these carefully. They are the easiest to mix up:

- [ ] **Import vs access:** importing a class does not give access to its private fields.
- [ ] **Wildcard vs subpackage:** `java.util.*` does not import `java.util.concurrent`.
- [ ] **Package vs folder:** the folder structure should match the package structure; the `package` line defines the class's package.
- [ ] **Simple name vs FQCN:** `Main` is the simple name; `com.company.main.Main` is the FQCN.
- [ ] **Naming conflict vs inheritance:** same-named classes in different packages are a *name* problem, not an inheritance relationship.
- [ ] **Wildcard vs recursive import:** wildcard imports are never recursive.

---

## 11. Self-test (try without looking above)

### Questions

1. What is a package, and why do we use it?
2. What is the correct order of `package`, `import`, and class declarations?
3. What does an `import` statement help us do?
4. What is the difference between a single-type import and a wildcard import?
5. Does `import java.util.*;` also import `java.util.concurrent`? Why?
6. Is this valid?
   ```java
   import java.util.*;
   import java.util.concurrent.*;
   ```
7. Can two different packages contain a class named `Employee`?
8. What problem happens if I import two classes named `Employee` and use `Employee` without qualification?
9. How can I use both same-named classes in one file?
10. What does FQCN stand for? Give an example.
11. Does importing a class let me access its private members?
12. What does this command do?
    ```bash
    java -cp out com.company.main.Main
    ```

### Answer key (open only after attempting)

<details>
<summary>Show answers</summary>

1. A package groups related types, helps maintain code, avoids name clashes, and provides package-level access boundaries.
2. `package` declaration → `import` statements → class/type declaration.
3. It lets me use imported classes by their short name instead of the full package-qualified name.
4. A single-type import names one class; a wildcard import makes all classes in that package available by short name.
5. No. Wildcard imports do not include subpackages.
6. Yes. They import from two different packages.
7. Yes, as long as their fully qualified names are different.
8. The simple name becomes ambiguous, so Java can't tell which one you mean.
9. Use the fully qualified name for each class.
10. Fully Qualified Class Name, e.g. `com.company.main.Main`.
11. No. Access modifiers still apply.
12. It starts the JVM and runs the `Main` class, using `out` as the classpath root and the FQCN to identify the class.

</details>

---

## 12. My revision tracker

Use this honestly. Don't mark a topic as understood just because the definition looks familiar.

**Status key:** ⬜ Not checked yet | 🟡 Needs revision / not confident | 🟢 I can explain and apply it independently

| Concept | Status | What I should be able to do |
|---|:---:|---|
| Purpose of packages | ⬜ | Explain it without memorizing |
| Package declaration | ⬜ | Write it in the correct position |
| Single-type import | ⬜ | Use it correctly |
| Wildcard import | ⬜ | Explain its scope |
| Subpackages | ⬜ | Explain why wildcard isn't recursive |
| FQCN | ⬜ | Identify and use it |
| Naming conflict | ⬜ | Resolve same-named classes |
| Import vs access modifiers | ⬜ | Explain why import doesn't bypass access rules |
| Compile / run | ⬜ | Run the example from the terminal |

---

## 13. My weak-point notes

After revision, write down **only** the concepts that still feel confusing. Copy this template as many times as needed:

```
Concept I am confused about:

What I currently think:

Correct understanding:

One example I can write myself:
```

> This is the most valuable part of this README.

---

## Completion criteria

I can consider **Packages & Imports** revised when I can:

- [ ] Explain packages and imports in my own words.
- [ ] Write `package` and `import` statements in the right order.
- [ ] Tell apart single imports, wildcard imports, and fully qualified names.
- [ ] Explain why wildcard imports don't cover subpackages.
- [ ] Resolve same-name class conflicts.
- [ ] Explain that imports do not bypass access modifiers.
- [ ] Compile and run my two-package `Employee` example without blindly copying.

> **Final rule:** Familiarity is not mastery. If I can't explain it or use it without looking at this README, that concept needs another revision.
