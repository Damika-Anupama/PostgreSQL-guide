## 📖 **6. LIMIT and OFFSET** <a id="limit-and-offset"></a>

The `LIMIT` and `OFFSET` clauses allow you to **retrieve a specific subset of rows** from a query's result set. These clauses are particularly useful for **pagination** and **optimizing query performance**.


```sql
SELECT select_list
FROM table_expression
[ORDER BY column [ASC | DESC]]
[LIMIT { count | ALL }]
[OFFSET start];
```

- **`LIMIT count`:** Returns up to `count` rows. If fewer rows exist, fewer rows are returned.  
- **`LIMIT ALL`:** No row limit is applied (equivalent to omitting `LIMIT`).  
- **`OFFSET start`:** Skips the first `start` rows and starts returning rows from `start+1`.  

**Key Notes:**  
1. `OFFSET` is applied **before counting rows for LIMIT**.  
2. `ORDER BY` is **strongly recommended** when using `LIMIT` and `OFFSET` to ensure **consistent results**.  
3. Using a **large OFFSET** can be inefficient because skipped rows are still processed by the database.

```sql
CREATE TABLE employees (
    name VARCHAR(50),
    salary INT
);

INSERT INTO employees (name, salary) VALUES
('Alice', 5000),
('Bob', 7000),
('Charlie', 6000),
('David', 7000);
```

## 📖 **6.1 LIMIT Clause**

The `LIMIT` clause restricts the **number of rows returned** by a query.

### 🛠️ **Example 1: Basic LIMIT**

**Input:**
```sql
SELECT name, salary FROM employees
ORDER BY salary DESC
LIMIT 2;
```

**Output:**

| name    | salary |
|---------|--------|
| Bob     | 7000   |
| David   | 7000   |

- **Explanation:** Only the **top 2 rows** based on salary are returned.


### 🛠️ **Example 2: LIMIT with NULL**

**Input:**
```sql
SELECT name, salary FROM employees
ORDER BY salary DESC
LIMIT NULL;
```

**Output:**

| name    | salary |
|---------|--------|
| Bob     | 7000   |
| David   | 7000   |
| Charlie | 6000   |
| Alice   | 5000   |

- **Explanation:** `LIMIT NULL` behaves as if `LIMIT` was not applied.


## 📖 **6.2 OFFSET Clause**

The `OFFSET` clause **skips rows** before starting to return results.

### 🛠️ **Example 3: Basic OFFSET**

**Input:**
```sql
SELECT name, salary FROM employees
ORDER BY salary DESC
OFFSET 2;
```

**Output:**

| name    | salary |
|---------|--------|
| Charlie | 6000   |
| Alice   | 5000   |

- **Explanation:** The **first 2 rows** are skipped, and rows start from the 3rd row.


## 📖 **6.3 LIMIT with OFFSET**

Using `LIMIT` and `OFFSET` together allows you to **paginate results**.

### 🛠️ **Example 4: LIMIT with OFFSET (Pagination)**

**Input:**
```sql
SELECT name, salary FROM employees
ORDER BY salary DESC
LIMIT 2 OFFSET 1;
```

**Output:**

| name    | salary |
|---------|--------|
| David   | 7000   |
| Charlie | 6000   |

- **Explanation:**  
   - `OFFSET 1`: Skips the **first row**.  
   - `LIMIT 2`: Returns the **next 2 rows**.


### 📖 **6.4 Real-World Pagination Example**

Suppose we want to paginate results where each page displays 2 records.

**🛠️ Page 1:**
```sql
SELECT name, salary FROM employees
ORDER BY salary DESC
LIMIT 2 OFFSET 0;
```

**Output:**

| name    | salary |
|---------|--------|
| Bob     | 7000   |
| David   | 7000   |

**🛠️ Page 2:**
```sql
SELECT name, salary FROM employees
ORDER BY salary DESC
LIMIT 2 OFFSET 2;
```

**Output:**

| name    | salary |
|---------|--------|
| Charlie | 6000   |
| Alice   | 5000   |

- **Explanation:** Pagination fetches rows in chunks.


## 📖 **6.5 LIMIT ALL**

The `LIMIT ALL` keyword removes the limit entirely, similar to omitting `LIMIT`.

### 🛠️ **Example 5: LIMIT ALL**

**Input:**
```sql
SELECT name, salary FROM employees
ORDER BY salary DESC
LIMIT ALL OFFSET 2;
```

**Output:**

| name    | salary |
|---------|--------|
| Charlie | 6000   |
| Alice   | 5000   |
| Eve     | NULL   |

**Explanation:** 
- `LIMIT ALL` means no row limit, but `OFFSET` still applies.
- `LIMIT NULL` also interpreted as no limit. In many SQL databases, `LIMIT NULL` behaves the same as `LIMIT ALL`

In practice, both `LIMIT ALL` and `LIMIT NULL` will return all rows that match the query, so there is no functional difference between the two. However, `LIMIT ALL` is more explicit in its intent to return all rows.


## 📖 **6.6 LIMIT and OFFSET with Subqueries**

You can use `LIMIT` and `OFFSET` in subqueries for fine control.

### 🛠️ **Example 6: Subquery with LIMIT and OFFSET**

**Input:**
```sql
SELECT * FROM (
    SELECT name, salary FROM employees
    ORDER BY salary DESC
    LIMIT 3 OFFSET 1
) AS subset
ORDER BY salary ASC;
```

**Output:**

| name    | salary |
|---------|--------|
| Charlie | 6000   |
| David   | 7000   |
| Bob     | 7000   |

- **Explanation:**  
   - The **inner query** fetches 3 rows after skipping 1 row.  
   - The **outer query** reorders those rows in ascending order.
