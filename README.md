## 📚 **Optimizing Queries with PostgreSQL - Part 1**

### 🔗 **Additional Resources**

- [PostgreSQL Documentation: Queries](https://www.postgresql.org/docs/current/queries.html)  
- [Online Playground for Practice](https://pgplayground.com/)

### **1. Overview**

In PostgreSQL, retrieving data from a database is achieved using **queries**. The primary SQL command for querying data is `SELECT`.

---

### 📖 **1.1 General Syntax of SELECT**

```sql
[WITH with_queries] SELECT select_list FROM table_expression [sort_specification];
```

- **`WITH`**: Used for advanced queries with Common Table Expressions (CTEs).  
- **`select_list`**: Specifies the columns or expressions to return.  
- **`FROM table_expression`**: Defines the source table(s).  
- **`sort_specification`**: Determines the order of the result set (`ORDER BY`).  

---

### 🛠️ **1.2 Simple Query Example**

The simplest way to query a table:

```sql
SELECT * FROM table1;
```

- Retrieves **all rows** and **all columns** from `table1`.  
- The `*` indicates **all columns**.

**Example Table (`table1`):**

| a | b | c |
|---|---|---|
| 1 | 2 | 3 |
| 4 | 5 | 6 |
| 7 | 8 | 9 |

**Query Result:**

```sql
SELECT * FROM table1;
```

**Output:**
```
 a | b | c
---+---+---
 1 | 2 | 3
 4 | 5 | 6
 7 | 8 | 9
```

---

### 🛠️ **1.3 Selecting Specific Columns and Calculations**

You can query **specific columns** or use expressions:

```sql
SELECT a, b + c AS sum FROM table1;
```

- **`a`**: Directly selects column `a`.  
- **`b + c`**: Calculates the sum of columns `b` and `c`.  
- **`AS sum`**: Gives an alias (`sum`) to the calculated column.

**Output:**
```
 a | sum
---+-----
 1 | 5
 4 | 11
 7 | 17
```

---

### 🛠️ **1.4 Using SELECT Without a Table**

The `SELECT` statement can also be used as a **calculator**:

```sql
SELECT 3 * 4;
```

**Output:**
```
 ?column?
----------
       12
```

This approach can be useful for quick calculations or inline expressions.

---

### 🛠️ **1.5 Using Functions in SELECT**

You can call **functions** directly using `SELECT`:

```sql
SELECT random();
```

- **`random()`**: Generates a random floating-point number between 0 and 1.

**Output:**
```
     random
---------------
 0.8675309
```

---
