## 📚 **Optimizing Queries with PostgreSQL - Part 1**

### 🔗 **Additional Resources**

- [PostgreSQL Documentation: Queries Overview](https://www.postgresql.org/docs/current/queries-overview.html)  
- [Online Playground for Practice](https://pgplayground.com/)

### Table of Contents

1. [Overview](1_Overview.md)  
   - 1.1 General Syntax of SELECT  
   - 1.2 Simple Query Example  
   - 1.3 Selecting Specific Columns and Calculations  
   - 1.4 Using SELECT Without a Table  
   - 1.5 Using Functions in SELECT  

2. [Table Expressions](2_Table_Expressions.md)  
   - 2.1 The FROM Clause  
     - 2.1.1 Joined Tables  
     - 2.1.2 Table and Column Aliases  
     - 2.1.3 Subqueries  
     - 2.1.4 Table Functions  
     - 2.1.5 LATERAL Subqueries  
   - 2.2 The WHERE Clause  
   - 2.3 The GROUP BY and HAVING Clauses  
   - 2.4 GROUPING SETS, CUBE, and ROLLUP  
   - 2.5 Window Function Processing  

3. [Select Lists](3_Select_Lists.md)  
   - 3.1 Select-List Items  
   - 3.2 Column Labels  
   - 3.3 DISTINCT  

4. [Combining Queries](4_Combining_Queries.md)  
   - 4.1 UNION  
   - 4.2 INTERSECT  
   - 4.3 EXCEPT  
   - 4.4 Combining Multiple Set Operations  

5. [Sorting Rows](5_Sorting_Rows.md)  
   - 5.1 ORDER BY Clause Basics  
   - 5.2 Sorting with ASC and DESC  
   - 5.3 NULLS FIRST and NULLS LAST  
   - 5.4 Sorting with Column Aliases  
   - 5.5 Sorting Results of UNION, INTERSECT, and EXCEPT  

6. [LIMIT and OFFSET](6_LIMIT_and_OFFSET.md)  
   - 6.1 LIMIT Clause  
   - 6.2 OFFSET Clause  
   - 6.3 LIMIT with OFFSET  
   - 6.4 LIMIT ALL  
   - 6.5 LIMIT and OFFSET with Subqueries  

7. [VALUES Lists](7_VALUES_Lists.md)  
   - 7.1 Basic VALUES Syntax  
   - 7.2 Assigning Column Aliases  
   - 7.3 Using VALUES with SELECT  
   - 7.4 Using VALUES with UNION  
   - 7.5 Sorting VALUES with ORDER BY  
   - 7.6 LIMIT and OFFSET with VALUES  
   - 7.7 VALUES in INSERT Statements  
   - 7.8 VALUES as Subqueries  

8. [WITH Queries](8_WITH_Queries.md)  
   - 8.1 SELECT in WITH  
   - 8.2 Recursive Queries  
     - 8.2.1 Search Order  
     - 8.2.2 Cycle Detection  
   - 8.3 Common Table Expression Materialization  
   - 8.4 Data-Modifying Statements in WITH  


### 📚 **General Syntax of a PostgreSQL Query**

The following represents the **general order** of keywords in a PostgreSQL query. While not all clauses are required in every query, the structure highlights the **logical order of execution** and **syntax precedence**.


```sql
[WITH with_queries] -- Common Table Expressions (CTEs) are defined here

SELECT [DISTINCT | DISTINCT ON (expression [, ...])] 
       select_list -- Specify columns or expressions to retrieve

FROM table_expression -- Define source tables, joins, or subqueries

[WHERE search_condition] -- Filter rows based on a condition

[GROUP BY grouping_column_reference [, ...]] -- Group rows with similar values

[HAVING group_condition] -- Filter grouped rows

[WINDOW window_name AS (window_definition)] -- Define window functions

[UNION | INTERSECT | EXCEPT [ALL]] -- Combine query results with set operators

[ORDER BY sort_expression [ASC | DESC] [NULLS {FIRST | LAST}] [, ...]] -- Sort results

[LIMIT {count | ALL}] -- Limit the number of rows returned

[OFFSET start] -- Skip a number of rows before returning results
```


1. **WITH Clause (Common Table Expressions)**  
   - Used to define temporary result sets (CTEs).  
   - Often used for modularizing complex queries.  

   ```sql
   WITH cte_name AS (
       SELECT column FROM table
   )
   SELECT * FROM cte_name;
   ```

2. **SELECT Clause**  
   - Specifies columns, expressions, or aggregations to retrieve.  
   - Supports `DISTINCT` to remove duplicate rows.  
   - Can include aliases (`AS`) for clarity.  

   ```sql
   SELECT DISTINCT column1, column2 AS alias
   FROM table;
   ```

3. **FROM Clause**  
   - Defines the source tables, joins, subqueries, or table expressions.  

   ```sql
   FROM table1
   JOIN table2 ON table1.id = table2.ref_id;
   ```

4. **WHERE Clause**  
   - Filters rows based on specific conditions.  

   ```sql
   WHERE column1 > 10 AND column2 IS NOT NULL;
   ```

5. **GROUP BY Clause**  
   - Groups rows with identical values in specified columns.  
   - Often used with aggregate functions (`SUM`, `AVG`, `COUNT`).  

   ```sql
   GROUP BY column1, column2;
   ```

6. **HAVING Clause**  
   - Filters grouped rows based on aggregate conditions.  

   ```sql
   HAVING SUM(sales) > 1000;
   ```

7. **WINDOW Clause**  
   - Defines **window functions** for calculations across specific row groups.  

   ```sql
   WINDOW win AS (PARTITION BY column1 ORDER BY column2);
   ```

8. **Set Operators (UNION, INTERSECT, EXCEPT)**  
   - Combine results from multiple queries.  
   - `ALL` retains duplicates.  

   ```sql
   SELECT column1 FROM table1
   UNION
   SELECT column1 FROM table2;
   ```

9. **ORDER BY Clause**  
   - Sorts query results based on specified columns or expressions.  
   - Supports `ASC` (default) and `DESC` for ordering.  
   - Controls placement of `NULL` values.  

   ```sql
   ORDER BY column1 DESC NULLS FIRST;
   ```

10. **LIMIT Clause**  
    - Restricts the number of rows returned.  

    ```sql
    LIMIT 10;
    ```

11. **OFFSET Clause**  
    - Skips a specified number of rows before starting to return rows.  

    ```sql
    OFFSET 5;
    ```


### 🛠️ **Complete Query Example**

```sql
WITH regional_sales AS (
    SELECT region, SUM(amount) AS total_sales
    FROM orders
    GROUP BY region
)
SELECT region, product, SUM(quantity) AS product_units
FROM orders
WHERE region IN (SELECT region FROM regional_sales)
GROUP BY region, product
HAVING SUM(quantity) > 100
ORDER BY product DESC
LIMIT 10 OFFSET 5;
```

**Execution Order (Logical Processing):**
1. `WITH` (CTEs are evaluated first)  
2. `FROM` (Source data is identified)  
3. `WHERE` (Row-level filtering)  
4. `GROUP BY` (Grouping rows)  
5. `HAVING` (Filtering grouped rows)  
6. `SELECT` (Columns are selected)  
7. `WINDOW` (Window functions applied)  
8. `UNION | INTERSECT | EXCEPT` (Combine query results)  
9. `ORDER BY` (Sort the results)  
10. `LIMIT` (Limit rows)  
11. `OFFSET` (Skip rows)  

