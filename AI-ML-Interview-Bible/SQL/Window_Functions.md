# Window Functions (ROW_NUMBER, RANK, DENSE_RANK, LEAD, LAG, SUM/AVG OVER, PARTITION BY)

## What is it?
Window functions perform calculations across a set of rows related to the current row, without collapsing rows like GROUP BY. ROW_NUMBER assigns a unique number per partition. RANK gives the same rank to ties, skipping numbers. DENSE_RANK gives same rank without skipping. LEAD/LAG access next/previous rows. Aggregate functions (SUM, AVG) can be used with OVER for running totals.

## Why do we use it?
Window functions solve real-world problems that are difficult with GROUP BY: ranking within groups, running totals, comparing current vs previous values, moving averages, and deduplication. They are essential for analytics, reporting, and data preparation in ML.

## Real-Time Example 1
**Sales Leaderboard**

A SaaS company wants to rank sales reps by monthly revenue within each region, and also show each rep's previous month revenue for comparison.

**Tables:**

```
sales
| rep_id | rep_name | region | month       | revenue |
|--------|----------|--------|-------------|---------|
| 1      | Alice    | North  | 2024-01-01  | 50000   |
| 1      | Alice    | North  | 2024-02-01  | 55000   |
| 2      | Bob      | North  | 2024-01-01  | 45000   |
| 2      | Bob      | North  | 2024-02-01  | 60000   |
| 3      | Charlie  | South  | 2024-01-01  | 70000   |
| 3      | Charlie  | South  | 2024-02-01  | 65000   |
```

**Goal:** Rank reps within region for February, and show their January revenue alongside.

## Real-Time Example 2
**Fraud Detection — Transaction Patterns**

A bank wants to flag accounts where a transaction amount is 5x higher than the previous transaction (possible fraud trigger).

**Table:** `transactions (txn_id, account_id, amount, txn_date)`

**Goal:** Use LAG to compare each transaction with the previous one by the same account.

## Concept Example 1

**Table:** `employees`

| emp_id | name    | department | salary |
|--------|---------|------------|--------|
| 1      | Alice   | Sales      | 60000  |
| 2      | Bob     | Sales      | 60000  |
| 3      | Charlie | Sales      | 55000  |
| 4      | Diana   | IT         | 80000  |
| 5      | Eve     | IT         | 75000  |
| 6      | Frank   | IT         | 75000  |
| 7      | Grace   | IT         | 72000  |

**Goal:** Rank employees by salary within each department. Show ROW_NUMBER, RANK, and DENSE_RANK to understand the differences.

**SQL:**
```sql
SELECT
    department,
    name,
    salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS row_num,
    RANK()       OVER (PARTITION BY department ORDER BY salary DESC) AS rank,
    DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dense_rank
FROM employees
ORDER BY department, salary DESC;
```

**Output:**

| department | name    | salary | row_num | rank | dense_rank |
|------------|---------|--------|---------|------|------------|
| IT         | Diana   | 80000  | 1       | 1    | 1          |
| IT         | Eve     | 75000  | 2       | 2    | 2          |
| IT         | Frank   | 75000  | 3       | 2    | 2          |
| IT         | Grace   | 72000  | 4       | 4    | 3          |
| Sales      | Alice   | 60000  | 1       | 1    | 1          |
| Sales      | Bob     | 60000  | 2       | 1    | 1          |
| Sales      | Charlie | 55000  | 3       | 3    | 2          |

**Explanation:**
- **ROW_NUMBER:** Unique number, even for ties (Eve=2, Frank=3).
- **RANK:** Same rank for ties, next rank skips (after two rank-2s, Grace gets 4).
- **DENSE_RANK:** Same rank for ties, no skipping (after two rank-2s, Grace gets 3).

## Concept Example 2

**Table:** `stock_prices`

| stock_id | price_date | close_price |
|----------|------------|-------------|
| AAPL     | 2024-01-01 | 190         |
| AAPL     | 2024-01-02 | 195         |
| AAPL     | 2024-01-03 | 192         |
| AAPL     | 2024-01-04 | 200         |
| AAPL     | 2024-01-05 | 205         |

**Goal:** Calculate daily price change (vs previous day), 3-day moving average, and cumulative total for the week.

**SQL:**
```sql
SELECT
    price_date,
    close_price,
    LAG(close_price) OVER (ORDER BY price_date) AS prev_day_close,
    close_price - LAG(close_price) OVER (ORDER BY price_date) AS daily_change,
    AVG(close_price) OVER (ORDER BY price_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg_3d,
    SUM(close_price) OVER (ORDER BY price_date) AS cumulative_total
FROM stock_prices
WHERE stock_id = 'AAPL'
ORDER BY price_date;
```

**Output:**

| price_date | close_price | prev_day_close | daily_change | moving_avg_3d | cumulative_total |
|------------|-------------|----------------|--------------|---------------|-----------------|
| 2024-01-01 | 190         | NULL           | NULL         | 190.00        | 190             |
| 2024-01-02 | 195         | 190            | 5            | 192.50        | 385             |
| 2024-01-03 | 192         | 195            | -3           | 192.33        | 577             |
| 2024-01-04 | 200         | 192            | 8            | 195.67        | 777             |
| 2024-01-05 | 205         | 200            | 5            | 199.00        | 982             |

**Explanation:** LAG accesses the previous row's value. The frame `ROWS BETWEEN 2 PRECEDING AND CURRENT ROW` creates a 3-day sliding window for the moving average. SUM with ORDER BY creates a running total.

## Common Mistakes

1. **Forgetting ORDER BY in OVER** — Without ORDER BY, window functions produce unpredictable results.
2. **Using PARTITION BY wrong** — Partition splits data into groups; the function resets for each partition.
3. **Confusing RANK vs DENSE_RANK** — RANK skips after ties, DENSE_RANK doesn't. Use DENSE_RANK for top-N where ties should occupy the same position without gaps.
4. **Misunderstanding frames** — `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` is default when ORDER BY is specified. Without ORDER BY, the frame is the entire partition.
5. **Using WHERE after window** — Window functions are evaluated after WHERE. You can't filter by window result directly; use a subquery or CTE.
6. **Not handling NULLs with LAG/LEAD** — LAG defaults to NULL if no previous row. Use `LAG(column, 1, 0)` to provide a default.

## How Interviewers Ask This

- "Find the top 3 salaries in each department."
- "What's the difference between RANK and DENSE_RANK?"
- "Write a query to calculate a running total."
- "How would you find the previous order date for each customer?"
- "Write a query to remove duplicate rows keeping the one with the highest ID."
- "Calculate a 7-day moving average of website traffic."
- "Show each employee's salary compared to the department average."

## How To Impress The Interviewer

- **Understand frames deeply:** `ROWS`, `RANGE`, `GROUPS` — and how they differ. `RANGE` includes tie values, `ROWS` does not.
- **Use multiple window functions in one query** — It's cleaner than multiple subqueries.
- **Performance tip:** Window functions with large frames can be slow. An index on the PARTITION BY + ORDER BY columns helps.
- **Explain the execution order:** Window functions are evaluated after JOIN, WHERE, GROUP BY, HAVING, but before ORDER BY and LIMIT.
- **Know the difference between aggregate window and GROUP BY:** Aggregate + OVER gives per-row output with group context; GROUP BY collapses rows.
- **Use FILTER (WHERE) with aggregates in PostgreSQL** for conditional aggregation within window: `COUNT(*) FILTER (WHERE status = 'active') OVER (PARTITION BY dept)`.
- **Deduplication with ROW_NUMBER**: This is the most common production use case — `ROW_NUMBER() OVER (PARTITION BY duplicate_key ORDER BY id DESC) = 1`.

## Most Asked Questions

### Q1: Write a query to find top 3 employees by salary in each department.
```sql
SELECT department, name, salary
FROM (
    SELECT department, name, salary,
           DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk <= 3;
```

### Q2: What is the difference between ROW_NUMBER, RANK, and DENSE_RANK?
- ROW_NUMBER: Unique sequential number, ties get arbitrary ordering.
- RANK: Same value for ties, next rank skips (1,1,3).
- DENSE_RANK: Same value for ties, no skipping (1,1,2).

### Q3: Write a query to find employees whose salary is higher than their department average.
```sql
SELECT name, department, salary, dept_avg
FROM (
    SELECT name, department, salary,
           AVG(salary) OVER (PARTITION BY department) AS dept_avg
    FROM employees
) dept_stats
WHERE salary > dept_avg;
```

### Q4: How would you calculate a running total?
```sql
SELECT order_date, amount,
       SUM(amount) OVER (ORDER BY order_date) AS running_total
FROM orders;
```

### Q5: Remove duplicate rows keeping the row with the highest ID.
```sql
DELETE FROM users
WHERE id IN (
    SELECT id
    FROM (
        SELECT id,
               ROW_NUMBER() OVER (PARTITION BY email ORDER BY id DESC) AS rn
        FROM users
    ) dup
    WHERE rn > 1
);
```

### Q6: Show each transaction with the previous and next transaction amount for the same customer.
```sql
SELECT txn_id, customer_id, amount,
       LAG(amount) OVER (PARTITION BY customer_id ORDER BY txn_date) AS prev_amount,
       LEAD(amount) OVER (PARTITION BY customer_id ORDER BY txn_date) AS next_amount
FROM transactions;
```

### Q7: Calculate a 7-day moving average of daily sales.
```sql
SELECT sale_date, daily_total,
       AVG(daily_total) OVER (
           ORDER BY sale_date
           ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
       ) AS moving_avg_7d
FROM daily_sales;
```

### Q8: What does `PARTITION BY` do?
PARTITION BY divides the result set into groups. The window function resets and restarts for each partition. Without PARTITION BY, the window function operates on the entire result set.

### Q9: Find the first order date for each customer using a window function.
```sql
SELECT customer_id, order_id, order_date
FROM (
    SELECT customer_id, order_id, order_date,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date) AS rn
    FROM orders
) first_orders
WHERE rn = 1;
```

### Q10: Write a query to find the salary difference between each employee and the next highest paid in the same department.
```sql
SELECT department, name, salary,
       salary - LEAD(salary) OVER (PARTITION BY department ORDER BY salary DESC) AS salary_gap
FROM employees;
```

### Q11: What is a window frame? Explain ROWS vs RANGE.
A frame defines which rows are included in the window calculation. ROWS is physical (based on row position relative to current). RANGE is logical (based on ORDER BY value). RANGE includes all rows with the same ORDER BY value as the current row.

### Q12: Write a query to find customers who increased their order value compared to their previous order.
```sql
SELECT customer_id, order_id, order_date, amount
FROM (
    SELECT customer_id, order_id, order_date, amount,
           LAG(amount) OVER (PARTITION BY customer_id ORDER BY order_date) AS prev_amount
    FROM orders
) with_prev
WHERE amount > COALESCE(prev_amount, 0);
```

### Q13: Calculate the percentile rank of each product by revenue.
```sql
SELECT product_name, revenue,
       PERCENT_RANK() OVER (ORDER BY revenue DESC) AS percentile_rank
FROM products;
```

### Q14: Show employee salaries with their department's min and max salary.
```sql
SELECT name, department, salary,
       MIN(salary) OVER (PARTITION BY department) AS dept_min,
       MAX(salary) OVER (PARTITION BY department) AS dept_max
FROM employees;
```

### Q15: Find the most recent order for each customer using NTH_VALUE.
```sql
SELECT DISTINCT customer_id,
       NTH_VALUE(order_id, 1) OVER (
           PARTITION BY customer_id
           ORDER BY order_date DESC
           ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
       ) AS latest_order_id
FROM orders;
```
