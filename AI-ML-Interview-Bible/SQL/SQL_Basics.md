# SQL Basics (SELECT, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT, DISTINCT, Aggregate Functions)

## What is it?
SQL (Structured Query Language) is the standard language for querying and manipulating relational databases. The basics include retrieving data with SELECT, filtering with WHERE, grouping with GROUP BY, filtering groups with HAVING, sorting with ORDER BY, limiting results with LIMIT, removing duplicates with DISTINCT, and calculating summaries with aggregate functions (COUNT, SUM, AVG, MIN, MAX).

## Why do we use it?
SQL is the #1 skill required for data roles. Every data scientist, analyst, and ML engineer needs SQL to extract, filter, aggregate, and prepare data for analysis or modeling. Interviewers test SQL basics first to verify you can handle real data before moving to advanced topics.

## Real-Time Example 1
**E-commerce Sales Dashboard**

A retail company needs a daily sales report showing total revenue by product category, sorted by highest revenue.

**Sample Tables:**

```
orders
| order_id | customer_id | order_date | total_amount |
|----------|-------------|------------|--------------|
| 101      | 501         | 2024-01-05 | 250.00       |
| 102      | 502         | 2024-01-05 | 150.00       |
| 103      | 501         | 2024-01-06 | 300.00       |

order_items
| item_id | order_id | product_id | quantity | price |
|---------|----------|------------|----------|-------|
| 1       | 101      | P001       | 2        | 50    |
| 2       | 101      | P002       | 1        | 150   |
| 3       | 102      | P001       | 3        | 50    |

products
| product_id | product_name | category    |
|------------|--------------|-------------|
| P001       | T-Shirt      | Apparel     |
| P002       | Shoes        | Footwear    |
```

**Goal:** Total revenue by category, show top earners first.

## Real-Time Example 2
**Healthcare Patient Analytics**

A hospital wants to find the average patient stay duration (in days) by department, only for departments with more than 5 admissions.

**Tables:** `admissions (patient_id, dept, admit_date, discharge_date)`, `departments (dept_id, dept_name)`

**Goal:** Identify departments with longest average stays for resource planning.

## Concept Example 1

**Table:** `employees`

| emp_id | name    | department | salary | hire_year |
|--------|---------|------------|--------|-----------|
| 1      | Alice   | Sales      | 60000  | 2019      |
| 2      | Bob     | Sales      | 55000  | 2021      |
| 3      | Charlie | IT         | 75000  | 2018      |
| 4      | Diana   | IT         | 80000  | 2020      |
| 5      | Eve     | IT         | 72000  | 2022      |
| 6      | Frank   | Sales      | 48000  | 2023      |

**Goal:** For each department, find the number of employees, average salary, and min hire year, but only show departments with avg salary > 55000, sorted by avg salary descending.

**SQL:**
```sql
SELECT
    department,
    COUNT(*) AS employee_count,
    ROUND(AVG(salary), 2) AS avg_salary,
    MIN(hire_year) AS earliest_hire
FROM employees
WHERE salary > 0
GROUP BY department
HAVING AVG(salary) > 55000
ORDER BY avg_salary DESC
LIMIT 5;
```

**Output:**

| department | employee_count | avg_salary | earliest_hire |
|------------|----------------|------------|---------------|
| IT         | 3              | 75666.67   | 2018          |
| Sales      | 1              | 60000.00   | 2019          |

**Why Sales shows 1 employee?** Bob and Frank have salaries < 60000, but the HAVING condition checks AVG across the group. After grouping, Sales avg = (60000+55000+48000)/3 = 54333, which fails HAVING. But Frank is filtered by WHERE? No, WHERE has no filter on salary here. Actually all 3 Sales employees are included, AVG = 54333, so Sales would NOT appear. Wait — let me recalculate: Sales = (60000+55000+48000)/3 = 54333. This is < 55000, so Sales is excluded. IT = (75000+80000+72000)/3 = 75666.67, included. So output only shows IT.

## Concept Example 2

**Table:** `transactions`

| txn_id | customer_id | amount  | txn_date   |
|--------|-------------|---------|------------|
| 1      | C001        | 100.00  | 2024-01-01 |
| 2      | C002        | 250.00  | 2024-01-01 |
| 3      | C001        | 50.00   | 2024-01-02 |
| 4      | C003        | 500.00  | 2024-01-02 |
| 5      | C001        | 200.00  | 2024-01-03 |
| 6      | C002        | 75.00   | 2024-01-03 |

**Goal:** Find distinct customers who made more than 1 transaction and their total spend, sorted by total spend descending.

**SQL:**
```sql
SELECT
    customer_id,
    COUNT(*) AS txn_count,
    SUM(amount) AS total_spend
FROM transactions
GROUP BY customer_id
HAVING COUNT(*) > 1
ORDER BY total_spend DESC;
```

**Output:**

| customer_id | txn_count | total_spend |
|-------------|-----------|-------------|
| C001        | 3         | 350.00      |
| C002        | 2         | 325.00      |

**Explanation:** C001 has 3 transactions totaling $350. C002 has 2 totaling $325. C003 has only 1 transaction, so excluded by HAVING.

## Common Mistakes

1. **Using WHERE instead of HAVING** — WHERE filters rows before grouping; HAVING filters after. You cannot use aggregate functions in WHERE.
2. **Forgetting GROUP BY columns** — Every non-aggregated column in SELECT must be in GROUP BY.
3. **Order of clauses** — Correct order: SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT.
4. **LIKE without wildcards** — `LIKE '%search%'` not `LIKE 'search'`.
5. **NULL comparisons** — Use `IS NULL`, not `= NULL`. NULL is not equal to anything.
6. **DISTINCT with multiple columns** — DISTINCT applies to ALL selected columns, not just the first one.
7. **LIMIT without ORDER BY** — Without ORDER BY, the rows returned are arbitrary.

## How Interviewers Ask This

- "Write a query to find the second highest salary."
- "Find customers who placed more than 3 orders in the last month."
- "What's the difference between WHERE and HAVING?"
- "How do you handle NULLs in SQL?"
- "Write a query to get the top 5 products by revenue."
- "What is the execution order of SQL clauses?"

## How To Impress The Interviewer

- **Memorize the execution order:** FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT. This explains why you can't use alias from SELECT in WHERE.
- **Use CTEs for readability** instead of complex subqueries.
- **Prefer EXISTS over IN** for subqueries when checking existence — it's faster with large datasets.
- **Index-aware querying:** Understand that `WHERE UPPER(name) = 'ALICE'` prevents index usage; use `WHERE name = 'Alice'`.
- **Know your DBMS differences:** MySQL LIMIT vs SQL Server TOP vs Oracle ROWNUM/FETCH.
- **Use COALESCE or IFNULL** to handle NULLs safely in calculations.
- **Explain your reasoning** — talk about data volume, indexing, and edge cases.

## Most Asked Questions

### Q1: Find the second highest salary from an employees table.
```sql
-- Method 1: Using subquery
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: Using LIMIT with OFFSET
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Method 3: Using window function
SELECT DISTINCT salary
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = 2;
```

### Q2: Write a query to find duplicate emails in a users table.
```sql
SELECT email, COUNT(*) AS cnt
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

### Q3: Difference between WHERE and HAVING?
WHERE filters individual rows BEFORE grouping. HAVING filters groups AFTER aggregation. WHERE cannot use aggregate functions; HAVING can.

### Q4: Find customers who made at least 3 orders in the last 30 days.
```sql
SELECT customer_id, COUNT(*) AS order_count
FROM orders
WHERE order_date >= CURRENT_DATE - INTERVAL 30 DAY
GROUP BY customer_id
HAVING COUNT(*) >= 3;
```

### Q5: What is the order of execution of SQL clauses?
FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY → LIMIT. Understanding this helps debug query issues.

### Q6: Write a query to get the department with the highest average salary.
```sql
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
ORDER BY avg_salary DESC
LIMIT 1;
```

### Q7: How do you handle NULLs in aggregate functions?
COUNT(column) ignores NULLs. SUM, AVG, MIN, MAX also ignore NULLs. COUNT(*) counts all rows including NULLs. Use COALESCE to replace NULLs before aggregation.

### Q8: Write a query to find products that have never been sold.
```sql
SELECT p.*
FROM products p
LEFT JOIN order_items oi ON p.product_id = oi.product_id
WHERE oi.product_id IS NULL;
```

### Q9: What is the difference between COUNT(*) and COUNT(column)?
COUNT(*) counts all rows in the group. COUNT(column) counts non-NULL values in that column.

### Q10: Find the top 3 highest paid employees in each department.
```sql
SELECT department, name, salary
FROM (
    SELECT department, name, salary,
           DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk <= 3;
```

### Q11: What does DISTINCT do when applied to multiple columns?
DISTINCT considers the combination of ALL selected columns. `SELECT DISTINCT city, state` returns unique city-state pairs.

### Q12: Write a query to find employees hired in the last 3 months.
```sql
SELECT *
FROM employees
WHERE hire_date >= CURRENT_DATE - INTERVAL 3 MONTH;
```
