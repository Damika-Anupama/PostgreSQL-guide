## 📖 **2. Table Expressions**

A **table expression** computes a table. It consists of a `FROM` clause optionally followed by `WHERE`, `GROUP BY`, and `HAVING` clauses.

- **Trivial Table Expressions:** Reference a **single table** from disk.  
- **Complex Table Expressions:** Modify or combine base tables using joins, subqueries, etc.  

Each clause transforms the table step-by-step into a **virtual table**.



## 📖 **2.1 The FROM Clause** <a id="the-from-clause"></a>

The `FROM` clause derives a table from one or more tables using a **comma-separated table reference list**:

```sql
FROM table_reference [, table_reference [, ...]]
```

- **`table_reference`:** Can be:
   - Table name  
   - Subquery  
   - JOIN clause  

The result of the `FROM` list is an **intermediate virtual table** processed further by `WHERE`, `GROUP BY`, and `HAVING`.



### 🛠️ **Example: Simple FROM Clause**

#### **Input:**

```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name TEXT,
    department TEXT,
    salary INT
);

INSERT INTO employees (name, department, salary) 
VALUES 
('Alice', 'HR', 5000),
('Bob', 'IT', 7000),
('Charlie', 'Finance', 6000);
```

```sql
SELECT * FROM employees;
```

#### **Output:**

| id | name    | department | salary |
|----|---------|-----------|--------|
| 1  | Alice   | HR        | 5000   |
| 2  | Bob     | IT        | 7000   |
| 3  | Charlie | Finance   | 6000   |



## 📖 **2.1.1 Joined Tables** <a id="joined-tables"></a>

Joins combine rows from two or more tables. Common types of joins:

1. **CROSS JOIN**  
2. **INNER JOIN**  
3. **LEFT JOIN**  
4. **RIGHT JOIN**  
5. **FULL JOIN**  

### 🛠️ **Example: CROSS JOIN**
![cross join](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.javatpoint.com%2Fpostgresql-cross-join&psig=AOvVaw2A_j8qb_TyuGLoZLZvGg1J&ust=1735468821316000&source=images&cd=vfe&opi=89978449&ved=0CBQQjRxqFwoTCPjMzeyjyooDFQAAAAAdAAAAABAE)

#### **Input:**

```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name TEXT,
    department TEXT,
    salary INT
);

INSERT INTO employees (name, department, salary)
VALUES 
('Alice', 'HR', 5000),
('Bob', 'IT', 7000),
('Charlie', 'Finance', 6000);
```

```sql
CREATE TABLE departments (
    id SERIAL PRIMARY KEY,
    name TEXT
);

INSERT INTO departments (name)
VALUES 
('HR'),
('IT'),
('Finance');
```

```sql
SELECT e.name AS employee, d.name AS department
FROM employees e
CROSS JOIN departments d;
```

#### **Output:**

| employee | department |
|----------|-----------|
| Alice    | HR        |
| Alice    | IT        |
| Alice    | Finance   |
| Bob      | HR        |
| Bob      | IT        |
| Bob      | Finance   |
| Charlie  | HR        |
| Charlie  | IT        |
| Charlie  | Finance   |



### 🛠️ **Example: INNER JOIN**

![inner join](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.alphacodingskills.com%2Fpostgresql%2Fnotes%2Fpostgresql-keyword-inner-join.php&psig=AOvVaw00kYM5wzDCvgNqwSGy8UP3&ust=1735469035749000&source=images&cd=vfe&opi=89978449&ved=0CBQQjRxqFwoTCKDhzo2kyooDFQAAAAAdAAAAABAJ)

#### **Input:**

```sql
SELECT e.name AS employee, d.name AS department
FROM employees e
INNER JOIN departments d ON e.department = d.name;
```

#### **Output:**

| employee | department |
|----------|-----------|
| Alice    | HR        |
| Bob      | IT        |
| Charlie  | Finance   |



### 🛠️ **Example: LEFT JOIN**

![left join](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.alphacodingskills.com%2Fpostgresql%2Fnotes%2Fpostgresql-keyword-left-join.php&psig=AOvVaw3)

#### **Input:**

```sql
SELECT e.name AS employee, d.name AS department
FROM employees e
LEFT JOIN departments d ON e.department = d.name;
```

#### **Output:**

| employee | department |
|----------|-----------|
| Alice    | HR        |
| Bob      | IT        |
| Charlie  | Finance   |

### 🛠️ **Example: RIGHT JOIN**

![right join](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.alphacodingskills.com%2Fpostgresql%2Fnotes%2Fpostgresql-keyword-right-outer-join.php&psig=AOvVaw3B-RbGNDDASX8oftu1BzUe&ust=1735469757318000&source=images&cd=vfe&opi=89978449&ved=0CBQQjRxqFwoTCJi_2eSmyooDFQAAAAAdAAAAABAE)

#### **Input:**

```sql
SELECT e.name AS employee, d.name AS department
FROM employees e
RIGHT JOIN departments d ON e.department = d.name;
```

#### **Output:**

| employee | department |
|----------|-----------|
| Alice    | HR        |
| Bob      | IT        |
| Charlie  | Finance   |

### 🛠️ **Example: FULL JOIN**

![full join](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.alphacodingskills.com%2Fpostgresql%2Fpostgresql-full-join.php&psig=AOvVaw24FALML4G1xJmEup4ANVzY&ust=1735469790594000&source=images&cd=vfe&opi=89978449&ved=0CBQQjRxqFwoTCIDI_PSmyooDFQAAAAAdAAAAABAE)

#### **Input:**

```sql
SELECT e.name AS employee, d.name AS department
FROM employees e
FULL JOIN departments d ON e.department = d.name;
```

#### **Output:**

| employee | department |
|----------|-----------|
| Alice    | HR        |
| Bob      | IT        |
| Charlie  | Finance   |




## 📖 **2.1.2 Table and Column Aliases** <a id="table-and-column-aliases"></a>

Aliases simplify table and column references.

```sql
SELECT e.name AS employee_name, e.salary AS monthly_salary
FROM employees AS e;
```

#### **Output:**

| employee_name | monthly_salary |
|---------------|----------------|
| Alice         | 5000          |
| Bob           | 7000          |
| Charlie       | 6000          |



## 📖 **2.1.3 Subqueries** <a id="subqueries"></a>

Subqueries act as temporary tables.

### 🛠️ **Example: Subquery in FROM Clause**

#### **Input:**

```sql
SELECT avg_salary FROM (
    SELECT AVG(salary) AS avg_salary FROM employees
) AS salary_stats;
```

#### **Output:**

| avg_salary |
|-----------|
| 6000      |



## 📖 **2.1.4 Table Functions** <a id="table-functions"></a>

Table functions return sets of rows and can be used in `FROM`.

### 🛠️ **Example: Table Function**

#### **Input:**

```sql
CREATE FUNCTION high_salary_employees(min_salary INT)
RETURNS SETOF employees AS $$
    SELECT * FROM employees WHERE salary > min_salary;
$$ LANGUAGE SQL;

SELECT * FROM high_salary_employees(6000);
```

#### **Output:**

| id | name | department | salary |
|----|------|-----------|--------|
| 2  | Bob  | IT        | 7000   |



## 📖 **2.1.5 LATERAL Subqueries** <a id="lateral-subqueries"></a>

`LATERAL` allows referencing columns from preceding `FROM` items.

### 🛠️ **Example: LATERAL Subquery**

#### **Input:**

```sql
SELECT e.name, s.total
FROM employees e,
LATERAL (SELECT SUM(salary) AS total FROM employees WHERE department = e.department) s;
```

#### **Output:**

| name    | total |
|---------|-------|
| Alice   | 5000  |
| Bob     | 7000  |
| Charlie | 6000  |

## 📖 **2.2 The WHERE Clause** <a id="the-where-clause"></a>

The `WHERE` clause is used to **filter rows** returned by a query. It applies conditions to rows retrieved by the `FROM` clause **before grouping or aggregation** occurs.

### **Syntax**
```sql
SELECT column1, column2 
FROM table_name 
WHERE condition;
```

- **`condition`:** A Boolean expression (e.g., `=`, `>`, `<`, `!=`)  
- Supports logical operators: `AND`, `OR`, `NOT`  
- Supports pattern matching with `LIKE` and `ILIKE`  
- Can use subqueries for more complex filtering  


### 🛠️ **Example 1: Basic WHERE Clause**

#### **Input:**
```sql
SELECT * FROM employees WHERE salary > 6000;
```

#### **Output:**
| id | name | department | salary |
|----|------|-----------|--------|
| 2  | Bob  | IT        | 7000   |


### 🛠️ **Example 2: WHERE with Logical Operators**

#### **Input:**
```sql
SELECT * FROM employees 
WHERE salary > 5000 AND department = 'IT';
```

#### **Output:**
| id | name | department | salary |
|----|------|-----------|--------|
| 2  | Bob  | IT        | 7000   |


### 🛠️ **Example 3: WHERE with Pattern Matching**

- `LIKE`: Case-sensitive  
- `ILIKE`: Case-insensitive

#### **Input:**
```sql
SELECT * FROM employees 
WHERE name LIKE 'A%';
```

#### **Output:**
| id | name | department | salary |
|----|------|-----------|--------|
| 1  | Alice | HR        | 5000   |


### 🛠️ **Example 4: WHERE with Subqueries**

Filter rows using results from another query.

#### **Input:**
```sql
SELECT * FROM employees 
WHERE salary > (SELECT AVG(salary) FROM employees);
```

#### **Output:**
| id | name | department | salary |
|----|------|-----------|--------|
| 2  | Bob  | IT        | 7000   |


### 🛠️ **Example 5: WHERE with BETWEEN and IN**

#### **Input:**
```sql
SELECT * FROM employees 
WHERE salary BETWEEN 5000 AND 7000;
```

#### **Output:**
| id | name | department | salary |
|----|------|-----------|--------|
| 1  | Alice | HR        | 5000   |
| 2  | Bob   | IT        | 7000   |
| 3  | Charlie | Finance  | 6000   |


## 📖 **2.3 The GROUP BY and HAVING Clauses** <a id="the-group-by-and-having-clauses"></a>

- **`GROUP BY`:** Groups rows that have the **same values** in specified columns.  
- **`HAVING`:** Filters **grouped rows** based on aggregate conditions.

### **Syntax**
```sql
SELECT column, aggregate_function(column)
FROM table_name
GROUP BY column
HAVING aggregate_function(column) condition;
```

- Aggregate functions: `SUM`, `AVG`, `COUNT`, `MIN`, `MAX`  


### 🛠️ **Example 1: GROUP BY Clause**

Calculate total salary per department.

#### **Input:**
```sql
SELECT department, SUM(salary) AS total_salary
FROM employees
GROUP BY department;
```

#### **Output:**
| department | total_salary |
|-----------|-------------|
| HR        | 5000        |
| IT        | 7000        |
| Finance   | 6000        |


### 🛠️ **Example 2: GROUP BY with HAVING**

Filter grouped results where total salary exceeds 6000.

#### **Input:**
```sql
SELECT department, SUM(salary) AS total_salary
FROM employees
GROUP BY department
HAVING SUM(salary) > 6000;
```

#### **Output:**
| department | total_salary |
|-----------|-------------|
| IT        | 7000        |


### 🛠️ **Example 3: GROUP BY with COUNT**

Count employees per department.

#### **Input:**
```sql
SELECT department, COUNT(*) AS employee_count
FROM employees
GROUP BY department;
```

#### **Output:**
| department | employee_count |
|-----------|---------------|
| HR        | 1             |
| IT        | 1             |
| Finance   | 1             |


## 📖 **2.4 GROUPING SETS, CUBE, and ROLLUP** <a id="grouping-sets-cube-and-rollup"></a>

Advanced grouping strategies in PostgreSQL:

1. **GROUPING SETS:** Multiple groupings in one query.  
2. **ROLLUP:** Hierarchical grouping.  
3. **CUBE:** All possible combinations of grouping.


### 🛠️ **Example 1: GROUPING SETS**

#### **Input:**
```sql
SELECT department, SUM(salary)
FROM employees
GROUP BY GROUPING SETS ((department), ());
```

#### **Output:**
| department | sum |
|-----------|-----|
| HR        | 5000|
| IT        | 7000|
| Finance   | 6000|
| NULL      | 18000|


### 🛠️ **Example 2: ROLLUP**

Hierarchical totals.

#### **Input:**
```sql
SELECT department, SUM(salary)
FROM employees
GROUP BY ROLLUP(department);
```

#### **Output:**
| department | sum |
|-----------|-----|
| HR        | 5000|
| IT        | 7000|
| Finance   | 6000|
| NULL      | 18000|


### 🛠️ **Example 3: CUBE**

All combinations of department and salary totals.

#### **Input:**
```sql
SELECT department, SUM(salary)
FROM employees
GROUP BY CUBE(department);
```

#### **Output:**
| department | sum |
|-----------|-----|
| HR        | 5000|
| IT        | 7000|
| Finance   | 6000|
| NULL      | 18000|


## 📖 **2.5 Window Function Processing** <a id="window-function-processing"></a>

**Window functions** operate over partitions of a result set.

### **Syntax**
```sql
SELECT column, aggregate_function() OVER (PARTITION BY column ORDER BY column);
```


### 🛠️ **Example 1: RANK Function**

Rank employees by salary.

#### **Input:**
```sql
SELECT name, salary, RANK() OVER (ORDER BY salary DESC) AS rank
FROM employees;
```

#### **Output:**
| name    | salary | rank |
|---------|--------|------|
| Bob     | 7000   | 1    |
| Charlie | 6000   | 2    |
| Alice   | 5000   | 3    |


### 🛠️ **Example 2: ROW_NUMBER**

Assign a row number.

#### **Input:**
```sql
SELECT name, salary, ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS row_num
FROM employees;
```

#### **Output:**
| name    | salary | row_num |
|---------|--------|---------|
| Alice   | 5000   | 1       |
