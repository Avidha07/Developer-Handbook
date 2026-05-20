# 🔑 Primary Key in DBMS

## 📌 What is a Primary Key?

A **Primary Key** is a column (or combination of columns) that uniquely identifies each row in a table.

Most people say:

> "Primary key is a unique identifier."

But in interviews, that answer is incomplete.

---

# ✅ Important Characteristics of Primary Key

## 1️⃣ UNIQUE

- No two rows can have the same primary key value.
- Every record must be different.

### Example

| id | name  |
|----|-------|
| 1  | Avidha |
| 2  | Rahul |

✅ Valid  
❌ Duplicate IDs are not allowed.

---

## 2️⃣ NOT NULL

- A primary key can never contain `NULL`.
- Every row must have a value.

### Invalid Example

| id | name |
|----|------|
| NULL | John |

❌ Not allowed.

---

## 3️⃣ Only ONE Primary Key Per Table

- A table can have many `UNIQUE` columns.
- But only **ONE PRIMARY KEY**.

### Example

```sql
CREATE TABLE Students (
    id INT PRIMARY KEY,
    email VARCHAR(100) UNIQUE,
    phone VARCHAR(15) UNIQUE
);
```

Here:

- `id` → Primary Key
- `email`, `phone` → Unique Keys

---

## 4️⃣ Automatically Creates Index

One of the most important interview points.

When a Primary Key is created:

- Database automatically creates an index
- Usually a **Clustered Index** (depends on DBMS)

This makes searching extremely fast.

### Example

```sql
SELECT * FROM Students WHERE id = 101;
```

Lookup becomes highly optimized because of indexing.

---

# ⚡ Why Searching by Primary Key is Fast?

Indexes work similarly to:

- 📖 Book indexes
- ⚡ Binary Search
- 🌳 B-Tree structures internally

Because of indexing, row lookup becomes extremely efficient.

> In many DBMS systems, searching through indexed keys is approximately logarithmic (`O(log n)`), not linear scanning.

---

## 5️⃣ Composite Primary Key

A primary key can also consist of multiple columns together.

This is called a **Composite Primary Key**.

### Example

```sql
CREATE TABLE Enrollments (
    student_id INT,
    course_id INT,
    
    PRIMARY KEY(student_id, course_id)
);
```

Here:

- `student_id` alone → Not unique
- `course_id` alone → Not unique
- Together → Unique

Used heavily in:

- Junction Tables
- Many-to-Many Relationships

---

# 🎯 Interview-Ready Answer

```text
A Primary Key uniquely identifies each row in a table.
It enforces UNIQUE and NOT NULL constraints,
allows only one primary key per table,
and automatically creates an index
which makes row lookups extremely fast.
```

---

# 🧠 Extra Interview Tips

## Difference Between PRIMARY KEY and UNIQUE KEY

| Feature | PRIMARY KEY | UNIQUE KEY |
|---------|-------------|-------------|
| Unique Values | ✅ | ✅ |
| NULL Allowed | ❌ | ✅ (usually one NULL) |
| Count Per Table | Only One | Multiple |
| Creates Index | ✅ | ✅ |

---

# 📚 Key Takeaways

✅ Uniquely identifies rows  
✅ Cannot be NULL  
✅ Only one per table  
✅ Automatically indexed  
✅ Supports composite keys  
✅ Improves search performance

---

# 🚀 Example Full Table

```sql
CREATE TABLE Employees (
    emp_id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100) UNIQUE
);
```

---

# 💡 Real-World Usage

Primary Keys are commonly used for:

- User IDs
- Employee IDs
- Order IDs
- Product IDs
- Database relationships

---

# 🏁 Conclusion

Primary Keys are one of the most fundamental concepts in DBMS.

They ensure:

- Data integrity
- Fast searching
- Reliable relationships between tables

Understanding them deeply is essential for:

- SQL Interviews
- Backend Development
- System Design
- Database Optimization
