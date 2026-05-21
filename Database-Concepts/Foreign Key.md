# 🔑 Foreign Key — The Answer That Gets You Hired

> Most candidates say *"it links two tables."*
> Senior devs talk about **Referential Integrity** and **ON DELETE behavior.**
> That one difference is what gets you selected.

---

## ❌ What Most Candidates Say

> "Foreign key links two tables."

**Result:** Interviewer smiles. Writes something down. You don't get selected. 💀

---

## ✅ The Answer That Gets You Selected

### 1️⃣ What It Actually Is

- A column in one table that **refers to the PRIMARY KEY** of another table
- Creates a **parent-child relationship** between tables
- **Child table** holds the Foreign Key
- **Parent table** holds the Primary Key

```sql
CREATE TABLE Customers (
    customer_id INT PRIMARY KEY,  -- Parent table
    name VARCHAR(100)
);

CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    customer_id INT,              -- Foreign Key (Child table)
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);
```

---

### 2️⃣ What It Enforces — Referential Integrity 🔑

- You **CANNOT** insert a value in the child table that doesn't exist in the parent table
- Prevents **orphan records**

```sql
-- ❌ This will FAIL if Customer ID 99 doesn't exist
INSERT INTO Orders (order_id, customer_id) VALUES (1, 99);

-- ✅ This will SUCCEED if Customer ID 1 exists
INSERT INTO Orders (order_id, customer_id) VALUES (1, 1);
```

---

### 3️⃣ ON DELETE Behavior — What 99% Miss 💀

This is the answer that separates juniors from seniors.

| Behavior | What Happens |
|---|---|
| `ON DELETE CASCADE` | Delete parent → child records **auto-deleted** |
| `ON DELETE SET NULL` | Delete parent → child FK becomes **NULL** |
| `ON DELETE RESTRICT` | **Cannot delete** parent if child records exist |
| `ON DELETE NO ACTION` | Same as RESTRICT (default in most DBs) |

```sql
-- CASCADE: Delete customer → all their orders deleted too
FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
    ON DELETE CASCADE

-- SET NULL: Delete customer → orders remain, customer_id set to NULL
FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
    ON DELETE SET NULL

-- RESTRICT: Cannot delete customer if they have orders
FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
    ON DELETE RESTRICT
```

---

### 4️⃣ Real-World Example

```
Customers Table (Parent)        Orders Table (Child)
┌─────────────┬────────┐        ┌──────────┬─────────────┐
│ customer_id │ name   │        │ order_id │ customer_id │
├─────────────┼────────┤        ├──────────┼─────────────┤
│      1      │ Alice  │◄───────│    101   │      1      │
│      2      │ Bob    │◄───────│    102   │      2      │
└─────────────┴────────┘        │    103   │      1      │
                                └──────────┴─────────────┘
```

- `Orders.customer_id` is the **Foreign Key**
- `Customers.customer_id` is the **Primary Key**
- No orphan orders possible 🧠

---

## ⚡ The Answer That Gets You Selected

> *"A Foreign Key enforces **Referential Integrity** between two tables — it ensures child records can only reference values that exist in the parent table. The critical part is the **ON DELETE behavior**: CASCADE auto-deletes child records, SET NULL nullifies the FK, and RESTRICT prevents deletion if children exist. The right choice depends on your business logic."*

---

## 🧠 Bonus: FK vs PK in One Line

| | Primary Key | Foreign Key |
|---|---|---|
| **Purpose** | Uniquely identifies a row in **its own** table | References the PK of **another** table |
| **Uniqueness** | Must be unique | Can repeat |
| **Nulls** | Never NULL | Can be NULL (with SET NULL) |
| **Count** | One per table | Can have multiple |

> **One-liner:** *"Primary Key uniquely identifies a row in its own table; Foreign Key references a Primary Key in another table to enforce a relationship."*

---

## 📚 Quick Reference

```sql
-- Full example with all concepts
CREATE TABLE Customers (
    customer_id INT PRIMARY KEY,
    name        VARCHAR(100) NOT NULL
);

CREATE TABLE Orders (
    order_id    INT PRIMARY KEY,
    customer_id INT,
    amount      DECIMAL(10,2),
    FOREIGN KEY (customer_id)
        REFERENCES Customers(customer_id)
        ON DELETE CASCADE      -- Change to SET NULL or RESTRICT as needed
        ON UPDATE CASCADE
);
```

---

*That one line about ON DELETE behavior = senior dev thinking. 🧠*
