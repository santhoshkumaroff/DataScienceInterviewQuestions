# CTE (Common Table Expressions — WITH clause, Recursive CTE, Multiple CTEs)

## What is it?
A CTE (Common Table Expression) is a temporary named result set created using the WITH clause. It exists only for the duration of a single query. CTEs can be non-recursive (simple named subquery) or recursive (calling itself). Multiple CTEs can be defined in a single WITH clause, separated by commas.

## Why do we use it?
CTEs make complex queries readable, reusable, and easier to debug. They replace nested subqueries and derived tables. Recursive CTEs are the only practical way to query hierarchical data (org charts, category trees, bill of materials) in standard SQL.

## Real-Time Example 1
**E-commerce Category Tree**

An online store has a hierarchical product category structure (e.g., Electronics > Computers > Laptops > Gaming Laptops). They need to display the full path from root to leaf for every category.

**Table:**

```
categories
| category_id | name          | parent_id |
|-------------|---------------|-----------|
| 1           | Electronics   | NULL      |
| 2           | Computers     | 1         |
| 3           | Laptops       | 2         |
| 4           | Gaming Laptops| 3         |
| 5           | Phones        | 1         |
| 6           | Accessories   | 2         |
```

**Goal:** Show each category with its full path like "Electronics > Computers > Laptops > Gaming Laptops".

## Real-Time Example 2
**Analytics Pipeline — Monthly Active Users**

A SaaS company wants a report showing monthly active users, new users, and churned users. This requires multiple aggregations from the same source table, connected through a CTE pipeline.

**Tables:** `events (event_id, user_id, event_type, event_date)`, `users (user_id, signup_date)`

**Goal:** Business dashboard with MAU, new signups, and churned users month-over-month.

## Concept Example 1

**Table:** `employees`

| emp_id | name    | manager_id | department | salary |
|--------|---------|------------|------------|--------|
| 1      | CEO     | NULL       | Executive  | 200000 |
| 2      | VP_Sales| 1          | Sales      | 150000 |
| 3      | VP_Eng  | 1          | Engineering| 150000 |
| 4      | Alice   | 2          | Sales      | 80000  |
| 5      | Bob     | 3          | Engineering| 90000  |
| 6      | Charlie | 3          | Engineering| 85000  |

**Goal:** Show each employee's level in the hierarchy (1 for CEO, 2 for direct reports, etc.) and the full reporting chain.

**SQL (Recursive CTE):**
```sql
WITH RECURSIVE org_chart AS (
    -- Anchor: Get the CEO (top-level)
    SELECT emp_id, name, manager_id, 1 AS level, CAST(name AS VARCHAR(500)) AS chain
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Recursive: Join employees to their managers
    SELECT e.emp_id, e.name, e.manager_id, oc.level + 1,
           CAST(oc.chain || ' -> ' || e.name AS VARCHAR(500))
    FROM employees e
    INNER JOIN org_chart oc ON e.manager_id = oc.emp_id
)
SELECT emp_id, name, level, chain
FROM org_chart
ORDER BY level, name;
```

**Output:**

| emp_id | name      | level | chain                        |
|--------|-----------|-------|------------------------------|
| 1      | CEO       | 1     | CEO                          |
| 2      | VP_Sales  | 2     | CEO -> VP_Sales              |
| 3      | VP_Eng    | 2     | CEO -> VP_Eng                |
| 4      | Alice     | 3     | CEO -> VP_Sales -> Alice     |
| 5      | Bob       | 3     | CEO -> VP_Eng -> Bob         |
| 6      | Charlie   | 3     | CEO -> VP_Eng -> Charlie     |

**Explanation:** The anchor member gets the root (CEO with no manager). The recursive member joins employees to their managers from the CTE. Each iteration adds one level. The recursion stops when no more employees match.

## Concept Example 2

**Tables:**

```
orders
| order_id | customer_id | order_date | total |
|----------|-------------|------------|-------|
| 101      | 501         | 2024-01-05 | 250   |
| 102      | 501         | 2024-01-10 | 150   |
| 103      | 502         | 2024-01-12 | 300   |

order_items
| item_id | order_id | product_id | quantity | price |
|---------|----------|------------|----------|-------|
| 1       | 101      | P001       | 2        | 75    |
| 2       | 101      | P002       | 1        | 100   |
| 3       | 102      | P001       | 1        | 75    |
| 4       | 103      | P003       | 3        | 100   |
```

**Goal:** Show each order with its line items using multiple CTEs.

**SQL (Multiple CTEs):**
```sql
WITH
order_summary AS (
    SELECT order_id, customer_id, order_date, total
    FROM orders
    WHERE total > 100
),
item_details AS (
    SELECT oi.order_id, p.product_name, oi.quantity, oi.price,
           oi.quantity * oi.price AS line_total
    FROM order_items oi
    INNER JOIN products p ON oi.product_id = p.product_id
)
SELECT os.order_id, os.customer_id, id.product_name, id.quantity, id.line_total
FROM order_summary os
INNER JOIN item_details id ON os.order_id = id.order_id
ORDER BY os.order_id;
```

**Output:**

| order_id | customer_id | product_name | quantity | line_total |
|----------|-------------|--------------|----------|------------|
| 101      | 501         | Product A    | 2        | 150        |
| 101      | 501         | Product B    | 1        | 100        |
| 102      | 501         | Product A    | 1        | 75         |
| 103      | 502         | Product C    | 3        | 300        |

**Explanation:** Multiple CTEs are defined with comma separators. `order_summary` filters to orders > 100. `item_details` joins items with products and calculates line totals. The final query joins both CTEs.

## Common Mistakes

1. **Forgetting RECURSIVE keyword** — In MySQL and PostgreSQL, you need `WITH RECURSIVE`. SQL Server doesn't need RECURSIVE keyword.
2. **No UNION ALL in recursive CTE** — Recursive CTEs must use UNION ALL between anchor and recursive members.
3. **Infinite recursion** — Always ensure the recursive member has a condition that eventually produces no rows. Set a MAXRECURSION limit (e.g., `OPTION (MAXRECURSION 100)` in SQL Server).
4. **Not aliasing columns** — CTE column names must be unique. You can define them in the WITH clause: `WITH cte (col1, col2) AS (...)`.
5. **Using CTE where a subquery would be faster** — CTEs are materialized differently across databases. In PostgreSQL, CTEs are optimization fences (materialized by default). In SQL Server, they're inlined.
6. **Nesting CTEs unnecessarily** — Multiple CTEs in one WITH are fine; nesting CTEs inside CTEs is not needed.
7. **Forgetting that CTE is temporary** — A CTE only exists within the single query it's part of.

## How Interviewers Ask This

- "Write a query to generate a sequence of numbers from 1 to 10."
- "What is a recursive CTE and when would you use it?"
- "Write a CTE to find all employees under a given manager in an org hierarchy."
- "How do you write multiple CTEs?"
- "Write a query to find the hierarchy path for a product category."
- "What's the difference between a CTE and a subquery?"
- "Write a recursive CTE to calculate factorial."

## How To Impress The Interviewer

- **Use CTEs for readability** — Break complex queries into logical steps. Name CTEs descriptively (e.g., `active_users`, `revenue_by_month`).
- **Know materialization differences:** In PostgreSQL, CTEs are materialized (executed once, stored in memory). This can be good (avoids recomputation) or bad (prevents optimizer pushdown). In SQL Server and MySQL 8+, CTEs are inlined.
- **Recursive CTE pattern:** Always start with the anchor (base case), then UNION ALL, then the recursive member that references the CTE by name.
- **Limiting recursion depth:** Use a `level` counter column to prevent infinite loops: `WHERE level < 10`.
- **Multiple CTEs are sequential** — Later CTEs can reference earlier ones, creating a pipeline.
- **Recursive CTE alternatives** — In some databases, hierarchical queries use CONNECT BY (Oracle) or adjacency list patterns.
- **CTEs for data generation** — Recursive CTEs can generate date ranges, number series, or test data without external tables.

## Most Asked Questions

### Q1: What is a CTE and why use it over a subquery?
A CTE (WITH clause) is a named temporary result set. It improves readability by letting you define intermediate results at the top of the query rather than nesting subqueries. Unlike subqueries that are re-evaluated, CTEs can be referenced multiple times in the same query.

### Q2: Write a recursive CTE to generate numbers 1 through 10.
```sql
WITH RECURSIVE numbers(n) AS (
    SELECT 1
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 10
)
SELECT n FROM numbers;
```

### Q3: Find the hierarchy path for a category tree.
```sql
WITH RECURSIVE category_path AS (
    SELECT category_id, name, parent_id, CAST(name AS VARCHAR(500)) AS path
    FROM categories
    WHERE parent_id IS NULL
    UNION ALL
    SELECT c.category_id, c.name, c.parent_id,
           CAST(cp.path || ' > ' || c.name AS VARCHAR(500))
    FROM categories c
    INNER JOIN category_path cp ON c.parent_id = cp.category_id
)
SELECT category_id, name, path FROM category_path;
```

### Q4: Write a query that uses multiple CTEs.
```sql
WITH
dept_stats AS (
    SELECT department, AVG(salary) AS avg_salary, COUNT(*) AS emp_count
    FROM employees
    GROUP BY department
),
high_performers AS (
    SELECT e.*
    FROM employees e
    INNER JOIN dept_stats d ON e.department = d.department
    WHERE e.salary > d.avg_salary
)
SELECT department, name, salary, avg_salary
FROM high_performers
ORDER BY department, salary DESC;
```

### Q5: What is the difference between a CTE and a temporary table?
CTE exists only for one query (not stored). Temporary tables persist for the session and can have indexes. CTEs are simpler and don't need cleanup. Temporary tables are better for large intermediate results reused across multiple queries.

### Q6: Write a recursive CTE to find all subordinates under a specific manager.
```sql
WITH RECURSIVE subordinates AS (
    SELECT emp_id, name, manager_id
    FROM employees
    WHERE manager_id = 3  -- VP_Eng's team
    UNION ALL
    SELECT e.emp_id, e.name, e.manager_id
    FROM employees e
    INNER JOIN subordinates s ON e.manager_id = s.emp_id
)
SELECT * FROM subordinates;
```

### Q7: Write a CTE to calculate a running total.
```sql
WITH running_cte AS (
    SELECT order_date, amount,
           SUM(amount) OVER (ORDER BY order_date) AS running_total
    FROM orders
)
SELECT * FROM running_cte;
```

### Q8: Can a CTE reference itself multiple times? What about other CTEs?
A recursive CTE references itself in the recursive member. Multiple CTEs in one WITH can reference earlier CTEs (sequential). They cannot reference later CTEs or themselves except through recursion.

### Q9: Write a recursive CTE to calculate factorial.
```sql
WITH RECURSIVE factorial(n, fact) AS (
    SELECT 1, 1
    UNION ALL
    SELECT n + 1, fact * (n + 1) FROM factorial WHERE n < 10
)
SELECT n, fact FROM factorial;
```

### Q10: CTE vs derived table — which is better and when?
CTE is better for readability, especially with multiple steps or self-referencing. Derived tables are fine for simple subqueries. CTEs can be referenced multiple times; derived tables must be duplicated. Use CTE when the intermediate result is used more than once.

### Q11: Write a CTE to find the top 3 products by revenue in each category.
```sql
WITH ranked_products AS (
    SELECT category, product_name, revenue,
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn
    FROM products
)
SELECT category, product_name, revenue
FROM ranked_products
WHERE rn <= 3;
```

### Q12: Write a recursive CTE to find all possible flight routes between two cities.
```sql
WITH RECURSIVE routes AS (
    SELECT departure, arrival, CONCAT(departure, '->', arrival) AS path
    FROM flights
    WHERE departure = 'NYC'
    UNION ALL
    SELECT f.departure, f.arrival, CONCAT(r.path, '->', f.arrival)
    FROM flights f
    INNER JOIN routes r ON f.departure = r.arrival
    WHERE r.path NOT LIKE CONCAT('%', f.arrival, '%')  -- avoid cycles
)
SELECT * FROM routes WHERE arrival = 'LAX';
```

### Q13: How do you debug a CTE?
Run the CTE independently by selecting from it: `WITH my_cte AS (...) SELECT * FROM my_cte;`. This helps verify intermediate results. You can also add a `level` column to recursive CTEs to track recursion depth.

### Q14: Can you use aggregate functions inside a recursive CTE?
Aggregates are generally not allowed in the recursive member of a recursive CTE because the result depends on the full set, which isn't available during recursion. Use non-recursive CTEs or subqueries for aggregates.

### Q15: Write a CTE to find gaps (missing dates) in a date series.
```sql
WITH RECURSIVE date_series AS (
    SELECT MIN(order_date) AS dt FROM orders
    UNION ALL
    SELECT DATE(dt, '+1 day') FROM date_series
    WHERE dt < (SELECT MAX(order_date) FROM orders)
)
SELECT ds.dt AS missing_date
FROM date_series ds
LEFT JOIN orders o ON ds.dt = o.order_date
WHERE o.order_date IS NULL;
```
