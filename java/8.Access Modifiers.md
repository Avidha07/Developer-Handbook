# Java Access Modifiers

## 1. What are access modifiers?

Access modifiers control **where** a class, variable, constructor, or method can be accessed in Java.

Java has four access levels:

- `public`
- `protected`
- default (also called **package-private**), where no keyword is written
- `private`

The main purpose is to control visibility and protect class data (encapsulation).

---

## 2. `public`

A public class or member can be accessed from anywhere, provided the class itself is accessible.

```java
package accessmodifiers.samepackage;

public class Student {
    public String name = "Avidha";

    public void study() {
        System.out.println("Student is studying");
    }
}
```

It can be accessed from another package:

```java
package accessmodifiers.differentpackage;

import accessmodifiers.samepackage.Student;

public class Main {
    public static void main(String[] args) {
        Student s = new Student();
        System.out.println(s.name);
        s.study();
    }
}
```

### Important: `import` does not grant access

`import` only lets us use a class by its simple name. It does not change access permissions.

With an import:

```java
import accessmodifiers.samepackage.Student;

Student s = new Student();
```

Without an import, use the fully qualified class name (FQCN):

```java
accessmodifiers.samepackage.Student s =
        new accessmodifiers.samepackage.Student();
```

Both approaches work when the class and members are accessible.

**Interview check:** Is `import` mandatory to access a public class from another package?
**Answer:** No. We can use its fully qualified class name.

---

## 3. `private`

A private member can be accessed **only inside the class in which it is declared**.

```java
package accessmodifiers.samepackage;

public class Student {
    private String name = "Avidha";

    public void study() {
        System.out.println("Student is studying");
    }

    public void displayName() {
        System.out.println(name);
    }
}
```

This works because `displayName()` accesses `name` from inside `Student`:

```java
Student s = new Student();
s.displayName(); // Works
```

This does not work from another class, even in the same package:

```java
System.out.println(s.name); // Compilation error
```

Typical compiler message: `name has private access in Student`

**Remember:**

- `private` is based on the declaring class, not the package.
- Another class in the same package cannot directly access a private member.
- A public method can provide controlled access to private data.

### Encapsulation with getters and setters

The usual pattern is a private field with public methods:

```java
public class Student {
    private int age;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        if (age > 0) {          // validation protects the data
            this.age = age;
        }
    }
}
```

This is the main reason `private` exists: other classes cannot put the object into an invalid state.

---

## 4. Default (package-private)

When no access modifier is written, Java uses default (package-private) access.

```java
package accessmodifiers.samepackage;

class Student {
    String name = "Avidha";

    void study() {
        System.out.println("Student is studying");
    }
}
```

### Same package

Another class in `accessmodifiers.samepackage` can access the class and its default members.

```java
package accessmodifiers.samepackage;

public class Main {
    public static void main(String[] args) {
        Student s = new Student();
        System.out.println(s.name);
        s.study();
    }
}
```

### Different package

A class in `accessmodifiers.differentpackage` cannot access a package-private `Student` class. Even with an import, the compiler reports that `Student` is not public and cannot be accessed from outside its package.

### Important experiment: public class, default members

```java
package accessmodifiers.samepackage;

public class Student {
    String name = "Avidha";

    void study() {
        System.out.println("Student is studying");
    }
}
```

A class in another package can import and create a `Student` object, but cannot access `name` or `study()`.

**Remember:** Class accessibility and member accessibility are **two separate checks**. Making a class public does not automatically make its members public.

---

## 5. `protected`

A protected member can be accessed:

1. Inside the declaring class.
2. By other classes in the **same package**.
3. By **subclasses** in other packages, subject to the cross-package access rule below.

It cannot be accessed from an unrelated class in another package, and not through an ordinary superclass object.

Example superclass:

```java
package accessmodifiers.samepackage;

public class Student {
    protected String name = "Avidha";

    protected void study() {
        System.out.println("Student is studying");
    }
}
```

### Same package

Another class in the same package can access `name` and `study()`.

### Different package without inheritance

This does not work:

```java
package accessmodifiers.differentpackage;

import accessmodifiers.samepackage.Student;

public class Main {
    public static void main(String[] args) {
        Student s = new Student();

        System.out.println(s.name); // Compilation error
        s.study();                  // Compilation error
    }
}
```

The class is public and can be imported, but its protected members are not accessible here.

### Different package with inheritance

This works:

```java
package accessmodifiers.differentpackage;

import accessmodifiers.samepackage.Student;

public class Main extends Student {
    public static void main(String[] args) {
        Main obj = new Main();

        System.out.println(obj.name);
        obj.study();
    }
}
```

Output:

```
Avidha
Student is studying
```

**Why does it work?** `Main extends Student` makes `Main` a subclass. The protected members are inherited, and the subclass can access them through its own subclass context.

### Important cross-package rule

In a subclass in a different package, protected **instance** members are accessible through a reference of the **subclass type** (or a further subclass), not through an ordinary superclass reference.

```java
public class Main extends Student {
    void test() {
        System.out.println(name);        // OK (this.name)
        System.out.println(this.name);   // OK
        System.out.println(super.name);  // OK

        Main m = new Main();
        System.out.println(m.name);      // OK, reference type is Main

        Student s = new Student();
        System.out.println(s.name);      // Compilation error
    }
}
```

**Remember:** `protected` is not simply "public to subclasses". The **type of the reference** matters in cross-package access.

### Extra trap: protected constructors

If `Student` has a protected constructor:

```java
public class Student {
    protected Student() { }
}
```

then in a different package, `new Student();` fails, even inside a subclass. A subclass can only call it through `super()` in its own constructor. (Same-package code can call `new Student()` normally.)

### Extra note: protected static members

The reference-type restriction applies to **instance** members. A protected **static** member can be accessed from a subclass in another package using the class name, for example `Student.count`.

---

## 6. Comparison table

| Modifier  | Same class | Same package, other class | Different package, non-subclass | Different package, subclass |
|-----------|:----------:|:-------------------------:|:-------------------------------:|:---------------------------:|
| public    | Yes | Yes | Yes | Yes |
| protected | Yes | Yes | No  | Yes, through subclass context |
| default   | Yes | Yes | No  | No  |
| private   | Yes | No  | No  | No  |

This table describes ordinary Java source-code access. For `protected`, the cross-package subclass restriction described above applies.

---

## 7. Where can each modifier be used?

| Element | public | protected | default | private |
|---------|:------:|:---------:|:-------:|:-------:|
| Top-level class | Yes | No | Yes | No |
| Nested (inner) class | Yes | Yes | Yes | Yes |
| Fields | Yes | Yes | Yes | Yes |
| Methods | Yes | Yes | Yes | Yes |
| Constructors | Yes | Yes | Yes | Yes |
| Local variables | No | No | No | No |

Key points:

- A **top-level class** can only be `public` or default.
- A source file can contain **only one public top-level class**, and the file name must match that class name.
- **Local variables** (inside a method) cannot have access modifiers at all.

### Private constructor use case

A private constructor stops other classes from creating objects directly. It is used in the Singleton pattern and utility classes:

```java
public class Config {
    private static final Config INSTANCE = new Config();

    private Config() { }

    public static Config getInstance() {
        return INSTANCE;
    }
}
```

---

## 8. Access modifiers and inheritance

### Overriding cannot reduce visibility

An overriding method must have the **same or wider** access than the method it overrides.

```java
class Parent {
    protected void show() { }
}

class Child extends Parent {
    // void show() { }            // Error: weaker access (default < protected)
    public void show() { }        // OK: wider access
}
```

Order from narrowest to widest: `private` < default < `protected` < `public`.

### Private methods are not overridden

A private method is not visible to subclasses, so it is not inherited and cannot be overridden. A subclass method with the same name is simply a new, unrelated method.

### Interface methods

Interface methods are implicitly `public`. A class implementing the interface must declare the method `public`, otherwise it is a weaker-access error.

---

## 9. Common interview questions

**Q1. What are access modifiers in Java?**
They control the visibility and accessibility of classes and members.

**Q2. What is the default access modifier?**
When no modifier is specified, the member or top-level class has package-private access. It is accessible within the same package.

**Q3. Can a private member be accessed by another class in the same package?**
No. A private member is accessible only inside its declaring class.

**Q4. Can a protected member be accessed from another package?**
Yes, from a subclass, subject to the cross-package subclass access rule. It is not accessible to an unrelated class in another package.

**Q5. Does importing a class grant access to its members?**
No. Import only helps refer to a class by its simple name. Access modifiers still apply.

**Q6. Can a public class have private or default members?**
Yes. Class visibility and member visibility are separate.

**Q7. What is the difference between default and protected?**
Both allow access within the same package. `protected` additionally allows access through inheritance from a different package.

**Q8. Which modifier provides the most restricted access?**
`private`.

**Q9. Can a top-level class be private or protected?**
No. A top-level class can only be public or default. Nested classes can use all four.

**Q10. Can we override a method with weaker access?**
No. The overriding method must have the same or wider access.

**Q11. Can a private method be overridden?**
No. It is not visible to subclasses, so it is not inherited.

**Q12. Why use getters and setters instead of public fields?**
To protect data (encapsulation), add validation, and allow the internal implementation to change without affecting other classes.

**Q13. Can a class have a private constructor? Why?**
Yes. It prevents direct object creation, used in Singleton and utility classes.

---

## 10. Mistakes and weak points to watch

Use this section to identify what needs another revision.

- [ ] I can explain the difference between class access and member access.
- [ ] I understand that import does not grant access.
- [ ] I can explain why a private member cannot be accessed by another class in the same package.
- [ ] I can explain why a default class cannot be used from another package.
- [ ] I can explain why a public class can still have inaccessible default members.
- [ ] I understand protected access in the same package.
- [ ] I understand why protected access fails in a different package without inheritance.
- [ ] I understand protected access through inheritance in a different package.
- [ ] I understand why the reference type matters for cross-package protected access.
- [ ] I know that overriding cannot reduce visibility.
- [ ] I know which modifiers a top-level class can use.
- [ ] I can answer the interview questions without looking at the notes.

### Practical progress tracker

| Topic | Status |
|-------|--------|
| public | Completed |
| private | Completed |
| Default, same package | Completed |
| Default, different package | Completed |
| protected, same package | Practised |
| protected, different package, no inheritance | Practised |
| protected, different package, with inheritance | Practised |
| Comparison and interview revision | To revise |
| Overriding and access rules (new) | To practise |
| Protected constructor and static traps (new) | To practise |

---

## 11. One-minute revision

- `public`: accessible wherever the class/member is accessible.
- `private`: only inside the declaring class.
- default: only within the same package.
- `protected`: same package, plus qualifying subclass access across packages.
- `import` helps use a class name; it does not grant permission.
- A public class does not make all its members public.
- In cross-package protected access, use the subclass context rather than an ordinary superclass reference.
- Top-level classes: only `public` or default.
- Overriding can never reduce visibility.

**Best way to revise:** Recreate the `samepackage` and `differentpackage` experiments in IntelliJ and observe which lines compile and which do not. Then try the extra traps (protected constructor, overriding with weaker access) and read the compiler messages.
