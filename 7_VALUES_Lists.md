## 📖 **7. VALUES Lists** <a id="values-lists"></a>

The `VALUES` clause provides a convenient way to generate a **constant table** directly in a query without having to create and populate a persistent table in the database.


```sql
VALUES (expression1 [, ...]) [, ...];
```

- Each parenthesized list represents a **row**.  
- Each list must have the **same number of columns**.  
- Columns must have **compatible data types**.  
- By default, PostgreSQL assigns generic column names like `column1`, `column2`, etc.  
- Use **table aliases** to assign meaningful column names.  


## 📖 **7.1 Basic VALUES Example**

### 🛠️ **Example 1: Creating a Constant Table**

**Input:**
```sql
VALUES (1, 'one'), (2, 'two'), (3, 'three');
```

**Output:**

| column1 | column2 |
|---------|---------|
| 1       | one     |
| 2       | two     |
| 3       | three   |

- **Explanation:**  
   - A constant table with two columns and three rows is created.  
   - Column names are assigned as `column1` and `column2`.


### 🛠️ **Example 2: Assigning Column Aliases**

Use table aliases for clarity.

**Input:**
```sql
SELECT * 
FROM (VALUES (1, 'one'), (2, 'two'), (3, 'three')) AS t (num, letter);
```

**Output:**

| num | letter |
|-----|--------|
| 1   | one    |
| 2   | two    |
| 3   | three  |

- **Explanation:**  
   - Alias `t` is assigned to the table.  
   - Column names are overridden with `num` and `letter`.


## 📖 **7.2 Using VALUES in a SELECT Query**

The `VALUES` clause can behave like a subquery in a `SELECT` statement.

### 🛠️ **Example 3: Using VALUES with SELECT**

**Input:**
```sql
SELECT * 
FROM (VALUES (1, 'apple'), (2, 'banana'), (3, 'cherry')) AS fruits(id, name)
WHERE id > 1;
```

**Output:**

| id | name   |
|----|--------|
| 2  | banana |
| 3  | cherry |

- **Explanation:** The `WHERE` clause filters rows where `id > 1`.


## 📖 **7.3 Using VALUES with UNION**

You can combine `VALUES` with other queries using `UNION`.

### 🛠️ **Example 4: VALUES with UNION**

**Input:**
```sql
SELECT id, name 
FROM (VALUES (1, 'apple')) AS t1(id, name)
UNION
SELECT id, name 
FROM (VALUES (2, 'banana')) AS t2(id, name);
```

**Output:**

| id | name   |
|----|--------|
| 1  | apple  |
| 2  | banana |

- **Explanation:** The rows from both `VALUES` queries are combined.


## 📖 **7.4 Sorting VALUES with ORDER BY**

You can apply `ORDER BY` to sort rows from `VALUES`.

### 🛠️ **Example 5: Sorting with ORDER BY**

**Input:**
```sql
SELECT * 
FROM (VALUES (3, 'cherry'), (1, 'apple'), (2, 'banana')) AS t(id, name)
ORDER BY id ASC;
```

**Output:**

| id | name   |
|----|--------|
| 1  | apple  |
| 2  | banana |
| 3  | cherry |

- **Explanation:** Rows are sorted by `id` in ascending order.


## 📖 **7.5 Using VALUES with LIMIT and OFFSET**

You can limit the number of rows or skip rows using `LIMIT` and `OFFSET`.

### 🛠️ **Example 6: VALUES with LIMIT and OFFSET**

**Input:**
```sql
SELECT * 
FROM (VALUES (1, 'apple'), (2, 'banana'), (3, 'cherry')) AS t(id, name)
ORDER BY id ASC
LIMIT 2 OFFSET 1;
```

**Output:**

| id | name   |
|----|--------|
| 2  | banana |
| 3  | cherry |

- **Explanation:**  
   - `LIMIT 2`: Return up to 2 rows.  
   - `OFFSET 1`: Skip the first row.


## 📖 **7.6 Using VALUES in INSERT Statements**

The `VALUES` clause is commonly used in `INSERT` statements.

### 🛠️ **Example 7: INSERT with VALUES**

**Input:**
```sql
CREATE TABLE fruits (id SERIAL PRIMARY KEY, name TEXT);

INSERT INTO fruits (id, name)
VALUES (1, 'apple'), (2, 'banana'), (3, 'cherry');
```

**Query to Retrieve Data:**
```sql
SELECT * FROM fruits;
```

**Output:**

| id | name   |
|----|--------|
| 1  | apple  |
| 2  | banana |
| 3  | cherry |

- **Explanation:** The `VALUES` clause is used to insert multiple rows into the `fruits` table.


## 📖 **7.7 VALUES as Subquery**

You can use `VALUES` as part of a subquery in larger queries.

### 🛠️ **Example 8: Subquery with VALUES**

**Input:**
```sql
SELECT f.name
FROM fruits f
JOIN (VALUES (1), (3)) AS v(id) ON f.id = v.id;
```

**Output:**

| name   |
|--------|
| apple  |
| cherry |

- **Explanation:** The `VALUES` clause is treated as a table and joined with the `fruits` table.
