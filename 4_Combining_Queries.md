## 📖 **4. Combining Queries (UNION, INTERSECT, EXCEPT)** <a id="combining-queries-union-intersect-except"></a>

In PostgreSQL, you can combine the results of two or more queries using **set operations**:

- `UNION`  
- `INTERSECT`  
- `EXCEPT`  

![Operations](https://miro.medium.com/v2/resize:fit:1100/format:webp/0*u1HpgMBdmuNfle6e.png)

Each operation produces a **single result set** based on the rows returned by the queries.


### 📖 **Syntax**

```sql
query1 UNION [ALL] query2;
query1 INTERSECT [ALL] query2;
query1 EXCEPT [ALL] query2;
```

- `UNION`: Combines the results of two queries and **removes duplicates**.  
- `UNION ALL`: Combines results **without removing duplicates**.  
- `INTERSECT`: Returns **common rows** from both queries, **removing duplicates**.  
- `INTERSECT ALL`: Includes duplicates.  
- `EXCEPT`: Returns rows present in **query1** but **not in query2**, **removing duplicates**.  
- `EXCEPT ALL`: Includes duplicates.

For these operations, the two queries must be **"union compatible"**, meaning:
1. Both queries must return the **same number of columns**.  
2. Corresponding columns must have **compatible data types**.


## 📖 **4.1 UNION**

The `UNION` operator combines the results of two queries and removes duplicate rows. Use `UNION ALL` to include duplicates.


### 🛠️ **Example 1: UNION**

**Step 1: Create Example Tables**

```sql
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name TEXT,
    grade INT
);

CREATE TABLE graduates (
    id SERIAL PRIMARY KEY,
    name TEXT,
    grade INT
);

INSERT INTO students (name, grade) 
VALUES 
('Alice', 85),
('Bob', 90);

INSERT INTO graduates (name, grade) 
VALUES 
('Charlie', 88),
('Alice', 85);
```

**Step 2: Query using UNION**

```sql
SELECT name, grade FROM students
UNION
SELECT name, grade FROM graduates;
```

**Output:**

| name    | grade |
|---------|-------|
| Alice   | 85    |
| Bob     | 90    |
| Charlie | 88    |

- **Explanation:** Duplicate rows (`Alice, 85`) are removed.


### 🛠️ **Example 2: UNION ALL**

```sql
SELECT name, grade FROM students
UNION ALL
SELECT name, grade FROM graduates;
```

**Output:**

| name    | grade |
|---------|-------|
| Alice   | 85    |
| Bob     | 90    |
| Charlie | 88    |
| Alice   | 85    |

- **Explanation:** Duplicate rows are **not removed**.


## 📖 **4.2 INTERSECT**

The `INTERSECT` operator returns rows that are **common** to both queries. Use `INTERSECT ALL` to include duplicates.


### 🛠️ **Example 1: INTERSECT**

```sql
SELECT name, grade FROM students
INTERSECT
SELECT name, grade FROM graduates;
```

**Output:**

| name    | grade |
|---------|-------|
| Alice   | 85    |

- **Explanation:** Only rows common to both tables are returned.


### 🛠️ **Example 2: INTERSECT ALL**

If there are duplicates in the results of both queries, `INTERSECT ALL` includes them.

```sql
SELECT name, grade FROM students
INTERSECT ALL
SELECT name, grade FROM graduates;
```

**Output:**

| name    | grade |
|---------|-------|
| Alice   | 85    |

- **Explanation:** In this case, the result is identical because there’s only one duplicate.


## 📖 **4.3 EXCEPT**

The `EXCEPT` operator returns rows that are present in **query1** but **not in query2**. Use `EXCEPT ALL` to include duplicates.


### 🛠️ **Example 1: EXCEPT**

```sql
SELECT name, grade FROM students
EXCEPT
SELECT name, grade FROM graduates;
```

**Output:**

| name | grade |
|------|-------|
| Bob  | 90    |

- **Explanation:** `Bob, 90` is present in `students` but not in `graduates`.


### 🛠️ **Example 2: EXCEPT ALL**

If duplicates exist in the results of query1, `EXCEPT ALL` preserves them.

```sql
SELECT name, grade FROM students
EXCEPT ALL
SELECT name, grade FROM graduates;
```

**Output:**

| name | grade |
|------|-------|
| Bob  | 90    |


## 📖 **4.4 Combining Multiple Set Operations**

You can combine `UNION`, `INTERSECT`, and `EXCEPT` in one query. Use parentheses to clarify precedence.

### 🛠️ **Example: Combined Operations**

```sql
(
    SELECT name FROM students
    UNION
    SELECT name FROM graduates
)
EXCEPT
SELECT name FROM graduates WHERE grade < 90;
```

**Output:**

| name  |
|-------|
| Bob   |

- **Explanation:** 
  1. `UNION` combines all names from `students` and `graduates`.
  2. `EXCEPT` removes names from `graduates` with `grade < 90`.


## 📖 **4.5 Using LIMIT with Set Operations**

If you want to apply `LIMIT` to one of the queries, you must use **parentheses**.

### 🛠️ **Example: LIMIT in Combined Query**

```sql
SELECT name FROM students 
UNION
SELECT name FROM graduates
LIMIT 1;
```

**Output:**

| name    |
|---------|
| Charlie |

- **Explanation:** `LIMIT 1` restricts the first query to one row before applying `UNION`.
