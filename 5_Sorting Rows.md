## 📖 **5. Sorting Rows (ORDER BY)** <a id="sorting-rows-order-by"></a>

After a query has produced an output table, it can optionally be **sorted** using the `ORDER BY` clause. If sorting is **not explicitly specified**, the row order is unpredictable and depends on database internals like disk order or query execution plans.

### **Syntax:**

```sql
SELECT select_list
FROM table_expression
ORDER BY sort_expression1 [ASC | DESC] [NULLS { FIRST | LAST }]
         [, sort_expression2 [ASC | DESC] [NULLS { FIRST | LAST }] ...];
```

- **`sort_expression`:** Any valid column, alias, or expression from the `SELECT` clause.  
- **`ASC`:** Sort in **ascending order** (default).  
- **`DESC`:** Sort in **descending order**.  
- **`NULLS FIRST`:** Place `NULL` values **before** all non-null values.  
- **`NULLS LAST`:** Place `NULL` values **after** all non-null values.

### 📖 **5.1 Basic ORDER BY**

Sort the result set by a single column.

### 🛠️ **Example 1: Simple ORDER BY ASC**

**Input:**
```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name TEXT,
    salary INT
);

INSERT INTO employees (name, salary) 
VALUES 
('Alice', 5000),
('Bob', 7000),
('Charlie', 6000);
```

```sql
SELECT name, salary FROM employees
ORDER BY salary ASC;
```

**Output:**

| name    | salary |
|---------|--------|
| Alice   | 5000   |
| Charlie | 6000   |
| Bob     | 7000   |

- **Explanation:** Results are sorted in **ascending order** by `salary`.


### 🛠️ **Example 2: Simple ORDER BY DESC**

Sort in **descending order**.

**Input:**
```sql
SELECT name, salary FROM employees
ORDER BY salary DESC;
```

**Output:**

| name    | salary |
|---------|--------|
| Bob     | 7000   |
| Charlie | 6000   |
| Alice   | 5000   |

- **Explanation:** Results are sorted in **descending order** by `salary`.


### 📖 **5.2 ORDER BY Multiple Columns**

When rows have the same value in the first sort column, they are sorted by the second column.

### 🛠️ **Example 3: Multiple ORDER BY Columns**

**Input:**
```sql
INSERT INTO employees (name, salary) 
VALUES 
('David', 7000);
```

```sql
SELECT name, salary FROM employees
ORDER BY salary DESC, name ASC;
```

**Output:**

| name    | salary |
|---------|--------|
| Bob     | 7000   |
| David   | 7000   |
| Charlie | 6000   |
| Alice   | 5000   |

- **Explanation:** First sorted by `salary DESC`, then by `name ASC`.


### 📖 **5.3 NULL Handling in ORDER BY**

In PostgreSQL:
- `NULLS FIRST`: Place `NULL` values at the beginning.
- `NULLS LAST`: Place `NULL` values at the end.

### 🛠️ **Example 4: Handling NULLs**

**Input:**
```sql
INSERT INTO employees (name, salary) 
VALUES 
('Eve', NULL);
```

```sql
SELECT name, salary FROM employees
ORDER BY salary ASC NULLS FIRST;
```

**Output:**

| name    | salary |
|---------|--------|
| Eve     | NULL   |
| Alice   | 5000   |
| Charlie | 6000   |
| Bob     | 7000   |
| David   | 7000   |

- **Explanation:** `NULL` is placed **at the top** because of `NULLS FIRST`.


**Input:**
```sql
SELECT name, salary FROM employees
ORDER BY salary ASC NULLS LAST;
```

**Output:**

| name    | salary |
|---------|--------|
| Alice   | 5000   |
| Charlie | 6000   |
| Bob     | 7000   |
| David   | 7000   |
| Eve     | NULL   |

- **Explanation:** `NULL` is placed **at the bottom** because of `NULLS LAST`.


### 📖 **5.4 ORDER BY Using Column Aliases**

You can sort by column aliases defined in the `SELECT` clause.

### 🛠️ **Example 5: ORDER BY Alias**

**Input:**
```sql
SELECT name, salary * 12 AS annual_salary
FROM employees
ORDER BY annual_salary DESC;
```

**Output:**

| name    | annual_salary |
|---------|---------------|
| Bob     | 84000         |
| David   | 84000         |
| Charlie | 72000         |
| Alice   | 60000         |
| Eve     | NULL          |

- **Explanation:** Rows are sorted by the **alias** `annual_salary`.


### 📖 **5.5 ORDER BY Column Index**

You can sort by column index numbers in the `SELECT` list.

### 🛠️ **Example 6: ORDER BY Index**

**Input:**
```sql
SELECT name, salary FROM employees
ORDER BY 2 DESC;
```

**Output:**

| name    | salary |
|---------|--------|
| Bob     | 7000   |
| David   | 7000   |
| Charlie | 6000   |
| Alice   | 5000   |
| Eve     | NULL   |

- **Explanation:** `ORDER BY 2` sorts by the **second column** (`salary`).

### 📖 **5.6 ORDER BY with UNION, INTERSECT, EXCEPT**

When combining queries, you can sort the final result using `ORDER BY`.

### 🛠️ **Example 7: ORDER BY with UNION**

**Input:**
```sql
SELECT name FROM employees
UNION
SELECT 'Frank'
ORDER BY name ASC;
```

**Output:**

| name    |
|---------|
| Alice   |
| Bob     |
| Charlie |
| David   |
| Eve     |
| Frank   |

- **Explanation:** The combined result is sorted alphabetically.