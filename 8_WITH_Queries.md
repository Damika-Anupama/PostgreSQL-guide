## 📖 **8. WITH Queries (Common Table Expressions)** <a id="with-queries-common-table-expressions"></a>

The `WITH` clause in PostgreSQL is used to define **Common Table Expressions (CTEs)**, which are temporary result sets available only during the execution of a query. They simplify complex queries by allowing step-by-step breakdowns.


```sql
WITH cte_name AS (
    query_definition
)
SELECT * FROM cte_name;
```

- **`cte_name`:** The name of the Common Table Expression.  
- **`query_definition`:** A valid SQL query.  

**Create Sample Table**

```sql
CREATE TABLE orders (
    region TEXT,
    product TEXT,
    amount INT,
    quantity INT
);

INSERT INTO orders (region, product, amount, quantity) 
VALUES 
('North', 'Laptop', 1000, 2),
('South', 'Tablet', 500, 5),
('North', 'Phone', 800, 3),
('West', 'Laptop', 1200, 1);
```

## 📖 **8.1 SELECT in WITH**

Using `SELECT` in a `WITH` clause simplifies complex queries by breaking them into manageable parts.


### 🛠️ **Example 1: WITH Clause for Modular Queries**

```sql
WITH regional_sales AS (
    SELECT region, SUM(amount) AS total_sales
    FROM orders
    GROUP BY region
),
top_regions AS (
    SELECT region
    FROM regional_sales
    WHERE total_sales > (SELECT SUM(total_sales)/10 FROM regional_sales)
)
SELECT region, product, SUM(quantity) AS product_units, SUM(amount) AS product_sales
FROM orders
WHERE region IN (SELECT region FROM top_regions)
GROUP BY region, product;
```

**Output:**

| region | product | product_units | product_sales |
|--------|---------|---------------|---------------|
| North  | Laptop  | 2             | 1000          |
| North  | Phone   | 3             | 800           |

- **Explanation:**  
   - `regional_sales`: Calculates total sales per region.  
   - `top_regions`: Selects regions contributing more than 10% of total sales.  
   - Final Query: Fetches sales details only for `top_regions`.


## 📖 **8.2 Recursive Queries**

Recursive `WITH` queries allow queries to refer to their own output, enabling hierarchical data traversal.

### 🛠️ **Example 2: Recursive Query for Summing Integers**

```sql
WITH RECURSIVE t(n) AS (
    VALUES (1)
    UNION ALL
    SELECT n + 1 FROM t WHERE n < 5
)
SELECT SUM(n) FROM t;
```

**Output:**

| sum |
|-----|
| 15  |

- **Explanation:**  
   - `VALUES (1)` starts the recursion with `1`.  
   - `SELECT n + 1` increments `n` until `n < 5`.  
   - `SUM(n)` aggregates the numbers.


### 🛠️ **Example 3: Recursive Hierarchical Data**

**Step 1: Create Sample Table**

```sql
CREATE TABLE parts (
    sub_part TEXT,
    part TEXT,
    quantity INT
);

INSERT INTO parts (sub_part, part, quantity)
VALUES 
('wheel', 'car', 4),
('door', 'car', 2),
('window', 'door', 1);
```

**Step 2: Recursive Query**

```sql
WITH RECURSIVE included_parts(sub_part, part, quantity) AS (
    SELECT sub_part, part, quantity FROM parts WHERE part = 'car'
    UNION ALL
    SELECT p.sub_part, p.part, p.quantity * pr.quantity
    FROM included_parts pr, parts p
    WHERE p.part = pr.sub_part
)
SELECT sub_part, SUM(quantity) AS total_quantity
FROM included_parts
GROUP BY sub_part;
```

**Output:**

| sub_part | total_quantity |
|----------|----------------|
| wheel    | 4              |
| door     | 2              |
| window   | 2              |

- **Explanation:** The recursive query computes all direct and indirect subparts of `car`.


## 📖 **8.3 Common Table Expression Materialization**

Materialization defines whether the CTE query will be executed once and cached (`MATERIALIZED`) or if it will be folded into the main query (`NOT MATERIALIZED`).

### 🛠️ **Example 4: MATERIALIZED vs NOT MATERIALIZED**

**Input:**
```sql
WITH w AS MATERIALIZED (
    SELECT * FROM orders
)
SELECT * FROM w WHERE region = 'North';

WITH w AS NOT MATERIALIZED (
    SELECT * FROM orders
)
SELECT * FROM w WHERE region = 'North';
```

- **Explanation:**  
   - **`MATERIALIZED`:** Executes the CTE once and caches it.  
   - **`NOT MATERIALIZED`:** Executes the CTE directly within the main query.

## 📖 **8.4 Data-Modifying Statements in WITH**

You can use data-modifying statements like `INSERT`, `UPDATE`, and `DELETE` within `WITH`.

### 🛠️ **Example 5: Moving Rows Between Tables**

**Step 1: Create Tables**

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name TEXT,
    date DATE
);

CREATE TABLE products_log (
    id SERIAL PRIMARY KEY,
    name TEXT,
    date DATE
);

INSERT INTO products (name, date) 
VALUES 
('Product A', '2024-01-01'),
('Product B', '2024-01-15');
```

**Step 2: Use WITH for Row Movement**

```sql
WITH moved_rows AS (
    DELETE FROM products
    WHERE date < '2024-01-10'
    RETURNING *
)
INSERT INTO products_log SELECT * FROM moved_rows;
```

**Step 3: Check Logs**

```sql
SELECT * FROM products_log;
```

**Output:**

| id | name      | date       |
|----|-----------|------------|
| 1  | Product A | 2024-01-01 |

- **Explanation:**  
   - `DELETE`: Removes rows matching the condition and returns them with `RETURNING`.  
   - `INSERT`: Adds these rows into `products_log`.