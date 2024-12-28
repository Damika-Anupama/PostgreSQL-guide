## 📖 **3. Select Lists** <a id="select-lists"></a>

After processing the `FROM`, `WHERE`, `GROUP BY`, and `HAVING` clauses, the **select list** determines which columns or expressions are included in the final output.

- The simplest select list uses `*` to include **all columns**.  
- You can also specify specific columns, calculated expressions, or constants.  


### 📖 **3.1 Select-List Items** <a id="select-list-items"></a>

The `SELECT` clause specifies the columns or expressions to include in the output.

**Syntax:**
```sql
SELECT column1, column2, expression
FROM table_name;
```

- Use `*` to include all columns.  
- Select specific columns by name.  
- Perform **calculations** or **arithmetic expressions** directly.  
- Reference columns using **table aliases**.


#### 🛠️ **Example 1: Select All Columns**

Retrieve all columns from a table.

**Input:**
```sql
SELECT * FROM employees;
```

**Output:**
| id | name    | department | salary |
|----|---------|-----------|--------|
| 1  | Alice   | HR        | 5000   |
| 2  | Bob     | IT        | 7000   |
| 3  | Charlie | Finance   | 6000   |


#### 🛠️ **Example 2: Select Specific Columns**

Retrieve specific columns.

**Input:**
```sql
SELECT name, salary FROM employees;
```

**Output:**
| name    | salary |
|---------|--------|
| Alice   | 5000   |
| Bob     | 7000   |
| Charlie | 6000   |


#### 🛠️ **Example 3: Arithmetic Expressions in Select-List**

Perform calculations in the select list.

**Input:**
```sql
SELECT name, salary, salary * 1.1 AS updated_salary FROM employees;
```

**Output:**
| name    | salary | updated_salary |
|---------|--------|---------------|
| Alice   | 5000   | 5500          |
| Bob     | 7000   | 7700          |
| Charlie | 6000   | 6600          |


#### 🛠️ **Example 4: Select Columns from Multiple Tables**

Using table aliases for clarity.

**Input:**
```sql
SELECT e.name, d.name AS department_name 
FROM employees e 
JOIN departments d ON e.department = d.name;
```

**Output:**
| name    | department_name |
|---------|-----------------|
| Alice   | HR             |
| Bob     | IT             |
| Charlie | Finance        |


### 📖 **3.2 Column Labels** <a id="column-labels"></a>

Column labels rename or clarify output column names using the `AS` keyword.

**Syntax:**
```sql
SELECT column AS alias_name
FROM table_name;
```

- `AS` is optional but recommended for clarity.  
- Use **double quotes** for reserved keywords.


#### 🛠️ **Example 1: Assign Column Labels**

Rename output columns for clarity.

**Input:**
```sql
SELECT name AS employee_name, salary AS monthly_salary FROM employees;
```

**Output:**
| employee_name | monthly_salary |
|---------------|----------------|
| Alice         | 5000          |
| Bob           | 7000          |
| Charlie       | 6000          |


#### 🛠️ **Example 2: Reserved Keywords as Column Labels**

When column names conflict with PostgreSQL keywords.

**Input:**
```sql
SELECT name AS "from", salary AS monthly_salary FROM employees;
```

**Output:**
| from   | monthly_salary |
|--------|----------------|
| Alice  | 5000          |
| Bob    | 7000          |
| Charlie| 6000          |


#### 🛠️ **Example 3: Complex Expressions with Labels**

Label the result of a calculated column.

**Input:**
```sql
SELECT name, salary, salary * 0.1 AS tax FROM employees;
```

**Output:**
| name    | salary | tax |
|---------|--------|-----|
| Alice   | 5000   | 500 |
| Bob     | 7000   | 700 |
| Charlie | 6000   | 600 |


### 📖 **3.3 DISTINCT** <a id="distinct"></a>

The `DISTINCT` keyword eliminates **duplicate rows** from the output.

**Syntax:**
```sql
SELECT DISTINCT column1, column2 
FROM table_name;
```

- Duplicate rows are identified based on all selected columns.  
- Use `DISTINCT ON (expression)` to specify distinct criteria.


#### 🛠️ **Example 1: DISTINCT with Single Column**

Retrieve unique department names.

**Input:**
```sql
SELECT DISTINCT department FROM employees;
```

**Output:**
| department |
|-----------|
| HR        |
| IT        |
| Finance   |


#### 🛠️ **Example 2: DISTINCT with Multiple Columns**

Ensure unique combinations of columns.

**Input:**
```sql
SELECT DISTINCT name, department FROM employees;
```

**Output:**
| name    | department |
|---------|-----------|
| Alice   | HR        |
| Bob     | IT        |
| Charlie | Finance   |


#### 🛠️ **Example 3: DISTINCT ON**

Select unique rows based on the `department` column, returning the **first match** in each group.

**Input:**
```sql
SELECT DISTINCT ON (department) name, department, salary 
FROM employees 
ORDER BY department, salary DESC;
```

**Output:**
| name    | department | salary |
|---------|-----------|--------|
| Alice   | HR        | 5000   |
| Bob     | IT        | 7000   |
| Charlie | Finance   | 6000   |
