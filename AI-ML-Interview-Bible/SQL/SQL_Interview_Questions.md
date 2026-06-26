# SQL Interview Questions (40+ Medium-Hard Queries)

This file contains 40+ additional SQL interview questions covering all topics. These are medium to hard difficulty — the kind asked at top tech companies.

---

## Question 1: User Session Analysis

You have a table of website visits. Find users who visited on 3 consecutive days.

**Table:** `visits (user_id INT, visit_date DATE)`

```sql
WITH user_dates AS (
    SELECT user_id, visit_date,
           LAG(visit_date, 2) OVER (PARTITION BY user_id ORDER BY visit_date) AS date_minus_2
    FROM visits
    GROUP BY user_id, visit_date
)
SELECT DISTINCT user_id
FROM user_dates
WHERE visit_date = DATE_ADD(date_minus_2, INTERVAL 2 DAY);
```

---

## Question 2: Median Salary

Find the median salary for each department.

**Table:** `employees (emp_id INT, name VARCHAR, department VARCHAR, salary DECIMAL)`

```sql
WITH ranked AS (
    SELECT department, salary,
           ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary) AS rn,
           COUNT(*) OVER (PARTITION BY department) AS cnt
    FROM employees
)
SELECT department, AVG(salary) AS median_salary
FROM ranked
WHERE rn IN (FLOOR((cnt + 1) / 2.0), CEIL((cnt + 1) / 2.0))
GROUP BY department;
```

---

## Question 3: Top 5 Products by Revenue (Rolling)

Find the top 5 products by revenue for each month, including running total across months.

**Tables:** `products (product_id, name)`, `order_items (order_id, product_id, quantity, price)`, `orders (order_id, order_date)`

```sql
WITH monthly_revenue AS (
    SELECT DATE_TRUNC('month', o.order_date) AS month,
           p.product_id, p.name,
           SUM(oi.quantity * oi.price) AS revenue
    FROM orders o
    INNER JOIN order_items oi ON o.order_id = oi.order_id
    INNER JOIN products p ON oi.product_id = p.product_id
    GROUP BY month, p.product_id, p.name
),
ranked AS (
    SELECT month, product_id, name, revenue,
           ROW_NUMBER() OVER (PARTITION BY month ORDER BY revenue DESC) AS rn
    FROM monthly_revenue
)
SELECT month, name, revenue
FROM ranked
WHERE rn <= 5
ORDER BY month, rn;
```

---

## Question 4: Department Salary Ranking (Handling Ties)

Find the 3rd highest salary in each department. If a department has fewer than 3 employees, show NULL.

```sql
WITH ranked AS (
    SELECT department, salary,
           DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk
    FROM employees
)
SELECT department, MAX(CASE WHEN rnk = 3 THEN salary END) AS third_highest
FROM ranked
GROUP BY department;
```

---

## Question 5: Product Bundling (Market Basket Analysis)

Find pairs of products that are frequently bought together (at least 3 times).

```sql
SELECT a.product_id AS product_a, b.product_id AS product_b, COUNT(*) AS times_together
FROM order_items a
INNER JOIN order_items b ON a.order_id = b.order_id AND a.product_id < b.product_id
GROUP BY a.product_id, b.product_id
HAVING COUNT(*) >= 3
ORDER BY times_together DESC;
```

---

## Question 6: Employee Attendance (Gap Detection)

Find employees who were absent for 3+ consecutive days.

**Table:** `attendance (emp_id INT, date DATE, status VARCHAR)`

```sql
WITH numbered AS (
    SELECT emp_id, date,
           ROW_NUMBER() OVER (PARTITION BY emp_id ORDER BY date) AS rn
    FROM attendance
    WHERE status = 'Absent'
),
groups AS (
    SELECT emp_id, date,
           DATE_ADD(date, INTERVAL -rn DAY) AS grp
    FROM numbered
)
SELECT emp_id, MIN(date) AS absence_start, MAX(date) AS absence_end, COUNT(*) AS days_absent
FROM groups
GROUP BY emp_id, grp
HAVING COUNT(*) >= 3;
```

---

## Question 7: Running Total Reset (Monthly Reset)

Calculate running total of sales that resets each month.

```sql
SELECT order_date, amount,
       SUM(amount) OVER (PARTITION BY DATE_TRUNC('month', order_date) ORDER BY order_date) AS monthly_running_total
FROM orders
ORDER BY order_date;
```

---

## Question 8: Recursive Tree Path (Full Hierarchy)

Given a categories table with parent_id, show the full path from root to leaf as a single string.

**Table:** `categories (id INT, name VARCHAR, parent_id INT)`

```sql
WITH RECURSIVE cat_tree AS (
    SELECT id, name, parent_id, CAST(name AS VARCHAR(500)) AS path
    FROM categories WHERE parent_id IS NULL
    UNION ALL
    SELECT c.id, c.name, c.parent_id,
           CAST(ct.path || ' > ' || c.name AS VARCHAR(500))
    FROM categories c
    INNER JOIN cat_tree ct ON c.parent_id = ct.id
)
SELECT id, name, path FROM cat_tree;
```

---

## Question 9: Pivot Data (Rows to Columns)

Convert rows of monthly sales into columns per month.

**Table:** `sales (year INT, month INT, revenue DECIMAL)`

```sql
SELECT year,
       SUM(CASE WHEN month = 1 THEN revenue END) AS Jan,
       SUM(CASE WHEN month = 2 THEN revenue END) AS Feb,
       SUM(CASE WHEN month = 3 THEN revenue END) AS Mar,
       SUM(CASE WHEN month = 4 THEN revenue END) AS Apr,
       SUM(CASE WHEN month = 5 THEN revenue END) AS May,
       SUM(CASE WHEN month = 6 THEN revenue END) AS Jun
FROM sales
GROUP BY year
ORDER BY year;
```

---

## Question 10: Unpivot Data (Columns to Rows)

**Table:** `quarterly_sales (product_id, Q1, Q2, Q3, Q4)`

```sql
SELECT product_id, 'Q1' AS quarter, Q1 AS revenue FROM quarterly_sales
UNION ALL
SELECT product_id, 'Q2', Q2 FROM quarterly_sales
UNION ALL
SELECT product_id, 'Q3', Q3 FROM quarterly_sales
UNION ALL
SELECT product_id, 'Q4', Q4 FROM quarterly_sales
ORDER BY product_id, quarter;
```

---

## Question 11: Finding Gaps in Sequence (Missing IDs)

Find missing order IDs in a table with gaps.

**Table:** `orders (order_id INT PRIMARY KEY)`

```sql
WITH all_ids AS (
    SELECT MIN(order_id) AS start_id, MAX(order_id) AS end_id FROM orders
    UNION ALL
    SELECT start_id + 1, end_id FROM all_ids WHERE start_id < end_id
)
SELECT start_id AS missing_id
FROM all_ids
WHERE start_id NOT IN (SELECT order_id FROM orders)
OPTION (MAXRECURSION 10000);
```

---

## Question 12: Moving Average with Variable Window

Calculate a 3-day moving average, but for weekends use only the preceding day.

```sql
SELECT sale_date, daily_sales,
       AVG(daily_sales) OVER (
           ORDER BY sale_date
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
       ) AS moving_avg
FROM daily_sales;
```

---

## Question 13: Finding Consecutive Numbers (Three in a Row)

Find all numbers that appear at least 3 times consecutively.

**Table:** `logs (id INT, num INT)`

```sql
SELECT DISTINCT num AS consecutive_numbers
FROM (
    SELECT num,
           LAG(num, 1) OVER (ORDER BY id) AS prev1,
           LAG(num, 2) OVER (ORDER BY id) AS prev2
    FROM logs
) t
WHERE num = prev1 AND num = prev2;
```

---

## Question 14: Cancel Rate Calculation

Calculate the cancellation rate for each day (trips cancelled by driver or client).

**Tables:** `trips (id, client_id, driver_id, status, request_date)`

```sql
SELECT request_date,
       ROUND(SUM(CASE WHEN status LIKE '%cancelled%' THEN 1 ELSE 0 END) * 1.0 / COUNT(*), 2) AS cancel_rate
FROM trips
GROUP BY request_date
ORDER BY request_date;
```

---

## Question 15: Friendship Request Acceptance

Given friend requests and acceptances, calculate the overall acceptance rate.

**Tables:** `friend_requests (sender_id, send_to_id, request_date)`, `friend_accepts (requester_id, accepter_id, accept_date)`

```sql
SELECT
    ROUND(
        (SELECT COUNT(DISTINCT CONCAT(requester_id, '-', accepter_id)) FROM friend_accepts) * 1.0 /
        NULLIF((SELECT COUNT(*) FROM friend_requests), 0),
    2) AS acceptance_rate;
```

---

## Question 16: Spot the Difference (Find Changed Values)

Compare two versions of a table and find rows that changed.

**Tables:** `products_old (product_id, price)`, `products_new (product_id, price)`

```sql
SELECT COALESCE(o.product_id, n.product_id) AS product_id,
       o.price AS old_price, n.price AS new_price,
       CASE
           WHEN o.price IS NULL THEN 'New'
           WHEN n.price IS NULL THEN 'Removed'
           WHEN o.price <> n.price THEN 'Changed'
           ELSE 'Same'
       END AS status
FROM products_old o
FULL JOIN products_new n ON o.product_id = n.product_id
WHERE o.price IS NULL OR n.price IS NULL OR o.price <> n.price;
```

---

## Question 17: Recursive Bill of Materials

Find ALL components needed to build a product, including sub-components at every level.

**Table:** `bom (parent_id INT, component_id INT, quantity INT)`

```sql
WITH RECURSIVE bom_explosion AS (
    SELECT parent_id, component_id, quantity, 1 AS level, CAST(component_id AS VARCHAR(500)) AS path
    FROM bom WHERE parent_id = 100  -- product_id = 100
    UNION ALL
    SELECT b.parent_id, b.component_id, b.quantity * be.quantity, be.level + 1,
           CAST(be.path || '->' || b.component_id AS VARCHAR(500))
    FROM bom b
    INNER JOIN bom_explosion be ON b.parent_id = be.component_id
)
SELECT component_id, SUM(quantity) AS total_quantity, level, path
FROM bom_explosion
GROUP BY component_id, level, path
ORDER BY level, component_id;
```

---

## Question 18: Hall of Fame (First to Achieve)

For each product category, find the first customer to purchase (minimum order date).

```sql
WITH first_purchases AS (
    SELECT o.customer_id, p.category, o.order_date,
           ROW_NUMBER() OVER (PARTITION BY p.category ORDER BY o.order_date) AS rn
    FROM orders o
    INNER JOIN order_items oi ON o.order_id = oi.order_id
    INNER JOIN products p ON oi.product_id = p.product_id
)
SELECT category, customer_id, order_date
FROM first_purchases
WHERE rn = 1;
```

---

## Question 19: Hierarchical Rollup (Cumulative by Level)

Show cumulative salary by org level.

```sql
WITH RECURSIVE org AS (
    SELECT emp_id, name, manager_id, salary, 1 AS level, salary AS cumulative_salary
    FROM employees WHERE manager_id IS NULL
    UNION ALL
    SELECT e.emp_id, e.name, e.manager_id, e.salary, o.level + 1, o.cumulative_salary + e.salary
    FROM employees e
    INNER JOIN org o ON e.manager_id = o.emp_id
)
SELECT emp_id, name, level, salary, cumulative_salary FROM org;
```

---

## Question 20: Finding Duplicate Transactions

Find transactions that happened within 10 minutes of each other for the same customer with the same amount (possible duplicates).

```sql
SELECT a.txn_id, b.txn_id, a.customer_id, a.amount, a.txn_time, b.txn_time
FROM transactions a
INNER JOIN transactions b ON a.customer_id = b.customer_id
    AND a.amount = b.amount
    AND a.txn_id < b.txn_id
    AND ABS(TIMESTAMPDIFF(MINUTE, a.txn_time, b.txn_time)) <= 10;
```

---

## Question 21: Sessions from Events

Given clickstream events, identify user sessions (30 minutes of inactivity = new session).

```sql
WITH session_starts AS (
    SELECT user_id, event_time,
           CASE WHEN TIMESTAMPDIFF(MINUTE, LAG(event_time) OVER (PARTITION BY user_id ORDER BY event_time), event_time) >= 30
                THEN 1 ELSE 0 END AS is_new_session
    FROM events
),
session_groups AS (
    SELECT user_id, event_time,
           SUM(is_new_session) OVER (PARTITION BY user_id ORDER BY event_time ROWS UNBOUNDED PRECEDING) AS session_id
    FROM session_starts
)
SELECT user_id, session_id,
       MIN(event_time) AS session_start,
       MAX(event_time) AS session_end,
       COUNT(*) AS event_count
FROM session_groups
GROUP BY user_id, session_id;
```

---

## Question 22: Year-over-Year Growth

Calculate YoY revenue growth percentage.

```sql
WITH yearly AS (
    SELECT EXTRACT(YEAR FROM order_date) AS year, SUM(amount) AS revenue
    FROM orders GROUP BY year
)
SELECT year, revenue,
       LAG(revenue) OVER (ORDER BY year) AS prev_revenue,
       ROUND((revenue - LAG(revenue) OVER (ORDER BY year)) * 100.0 / LAG(revenue) OVER (ORDER BY year), 2) AS yoy_growth_pct
FROM yearly;
```

---

## Question 23: Rolling Retention (Cohort Analysis)

Calculate 1-month, 3-month, and 6-month retention for users who signed up in January 2024.

```sql
WITH jan_users AS (
    SELECT DISTINCT user_id FROM signups WHERE signup_date BETWEEN '2024-01-01' AND '2024-01-31'
),
activity AS (
    SELECT DISTINCT u.user_id,
           CASE WHEN EXISTS (SELECT 1 FROM activity a WHERE a.user_id = u.user_id AND a.activity_date BETWEEN '2024-02-01' AND '2024-02-29') THEN 1 ELSE 0 END AS month_1,
           CASE WHEN EXISTS (SELECT 1 FROM activity a WHERE a.user_id = u.user_id AND a.activity_date BETWEEN '2024-04-01' AND '2024-04-30') THEN 1 ELSE 0 END AS month_3,
           CASE WHEN EXISTS (SELECT 1 FROM activity a WHERE a.user_id = u.user_id AND a.activity_date BETWEEN '2024-07-01' AND '2024-07-31') THEN 1 ELSE 0 END AS month_6
    FROM jan_users u
)
SELECT COUNT(*) AS cohort_size,
       ROUND(AVG(month_1) * 100, 2) AS retention_1m_pct,
       ROUND(AVG(month_3) * 100, 2) AS retention_3m_pct,
       ROUND(AVG(month_6) * 100, 2) AS retention_6m_pct
FROM activity;
```

---

## Question 24: Find the "Winning Streak"

Find users who made purchases in 3+ consecutive months.

```sql
WITH monthly AS (
    SELECT DISTINCT customer_id, DATE_TRUNC('month', order_date) AS month
    FROM orders
),
prev AS (
    SELECT customer_id, month,
           LAG(month, 2) OVER (PARTITION BY customer_id ORDER BY month) AS month_minus_2
    FROM monthly
)
SELECT DISTINCT customer_id
FROM prev
WHERE month = DATE_ADD(month_minus_2, INTERVAL 2 MONTH);
```

---

## Question 25: Recursive Date Generation

Generate a calendar table with all dates in 2024 using a recursive CTE.

```sql
WITH RECURSIVE dates(dt) AS (
    SELECT '2024-01-01' AS dt
    UNION ALL
    SELECT DATE_ADD(dt, INTERVAL 1 DAY) FROM dates WHERE dt < '2024-12-31'
)
SELECT dt, DAYOFWEEK(dt) AS day_of_week, MONTH(dt) AS month
FROM dates
OPTION (MAXRECURSION 366);
```

---

## Question 26: Friendship Graph (Mutual Friends)

Find mutual friends between two users.

**Table:** `friendships (user_id, friend_id)`

```sql
-- Alice (user_id=1) and Bob (user_id=2)
SELECT f1.friend_id
FROM friendships f1
WHERE f1.user_id = 1
  AND f1.friend_id IN (SELECT friend_id FROM friendships WHERE user_id = 2);
```

---

## Question 27: Second Most Recent Order

For each customer, find the order details of their second most recent order.

```sql
WITH ranked_orders AS (
    SELECT customer_id, order_id, order_date, total,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rn
    FROM orders
)
SELECT customer_id, order_id, order_date, total
FROM ranked_orders WHERE rn = 2;
```

---

## Question 28: Data Completeness Check

Find days where no transactions were recorded (missing dates in a date range).

```sql
WITH RECURSIVE all_dates AS (
    SELECT MIN(order_date) AS dt FROM orders
    UNION ALL
    SELECT DATE_ADD(dt, INTERVAL 1 DAY) FROM all_dates
    WHERE dt < (SELECT MAX(order_date) FROM orders)
)
SELECT dt AS missing_date
FROM all_dates ad
WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.order_date = ad.dt);
```

---

## Question 29: Moving Median (Advanced)

Calculate a 7-day moving median of transaction amounts.

```sql
WITH numbered AS (
    SELECT txn_date, amount,
           ROW_NUMBER() OVER (ORDER BY txn_date) AS rn
    FROM transactions
),
windows AS (
    SELECT a.txn_date, b.amount,
           ROW_NUMBER() OVER (PARTITION BY a.rn ORDER BY b.amount) AS pos,
           COUNT(*) OVER (PARTITION BY a.rn) AS cnt
    FROM numbered a
    INNER JOIN numbered b ON b.rn BETWEEN a.rn - 3 AND a.rn + 3
)
SELECT txn_date, AVG(amount) AS median_7d
FROM windows
WHERE pos IN (FLOOR((cnt + 1)/2), CEIL((cnt + 1)/2))
GROUP BY txn_date
ORDER BY txn_date;
```

---

## Question 30: Slowly Changing Dimension (SCD Type 2)

Given employee salary changes, find each employee's salary on any given date.

**Table:** `salary_history (emp_id, salary, effective_date)`

```sql
SELECT e.emp_id, e.name, sh.salary
FROM employees e
CROSS JOIN (SELECT DATE '2024-06-15' AS query_date) q
LEFT JOIN salary_history sh ON e.emp_id = sh.emp_id
    AND sh.effective_date <= q.query_date
LEFT JOIN salary_history sh2 ON e.emp_id = sh2.emp_id
    AND sh2.effective_date <= q.query_date
    AND sh2.effective_date > sh.effective_date
WHERE sh2.effective_date IS NULL;
```

---

## Question 31: Finding Overlapping Time Periods

Find pairs of employees whose project assignments overlapped.

**Table:** `assignments (emp_id, project_id, start_date, end_date)`

```sql
SELECT DISTINCT a1.emp_id, a2.emp_id, a1.project_id
FROM assignments a1
INNER JOIN assignments a2 ON a1.project_id = a2.project_id
    AND a1.emp_id < a2.emp_id
    AND a1.start_date <= a2.end_date
    AND a2.start_date <= a1.end_date;
```

---

## Question 32: Employee Retention by Manager

For each manager, calculate the average tenure of their direct reports.

```sql
SELECT m.emp_id AS manager_id, m.name AS manager_name,
       AVG(DATEDIFF(e.leave_date, e.hire_date)) AS avg_tenure_days,
       COUNT(*) AS team_size
FROM employees e
INNER JOIN employees m ON e.manager_id = m.emp_id
WHERE e.leave_date IS NOT NULL
GROUP BY m.emp_id, m.name;
```

---

## Question 33: Hierarchical Permission Propagation

In a role hierarchy, find all permissions for each user including inherited ones.

**Tables:** `users (user_id, role_id)`, `roles_hierarchy (role_id, parent_role_id)`, `role_permissions (role_id, permission)`

```sql
WITH RECURSIVE role_tree AS (
    SELECT role_id, role_id AS effective_role_id FROM roles_hierarchy
    UNION ALL
    SELECT rt.role_id, rh.parent_role_id
    FROM role_tree rt
    INNER JOIN roles_hierarchy rh ON rt.effective_role_id = rh.role_id
)
SELECT u.user_id, rp.permission
FROM users u
INNER JOIN role_tree rt ON u.role_id = rt.role_id
INNER JOIN role_permissions rp ON rt.effective_role_id = rp.role_id;
```

---

## Question 34: Version Comparison

Compare two versions of a configuration table and show what changed.

**Table:** `config (key VARCHAR PRIMARY KEY, value VARCHAR, version INT)`

```sql
SELECT COALESCE(a.key, b.key) AS key,
       a.value AS old_value, b.value AS new_value
FROM config a
FULL JOIN config b ON a.key = b.key AND b.version = 2
WHERE a.version = 1 OR a.version IS NULL
  AND (a.value IS NULL OR b.value IS NULL OR a.value <> b.value);
```

---

## Question 35: IP Address Validation

Find rows with invalid IP addresses (not in correct IPv4 format).

```sql
SELECT * FROM users
WHERE ip_address NOT REGEXP '^([0-9]{1,3})\\.([0-9]{1,3})\\.([0-9]{1,3})\\.([0-9]{1,3})$'
   OR CAST(SUBSTRING_INDEX(ip_address, '.', 1) AS UNSIGNED) > 255
   OR CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(ip_address, '.', 2), '.', -1) AS UNSIGNED) > 255
   OR CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(ip_address, '.', 3), '.', -1) AS UNSIGNED) > 255
   OR CAST(SUBSTRING_INDEX(ip_address, '.', -1) AS UNSIGNED) > 255;
```

---

## Question 36: Join Without Using JOIN Keyword

Write a query equivalent to an INNER JOIN without using the JOIN keyword.

```sql
SELECT o.*, c.name
FROM orders o, customers c
WHERE o.customer_id = c.customer_id;
```

---

## Question 37: Find Maximum Without MAX

Find the highest salary without using MAX aggregate.

```sql
SELECT salary FROM employees
WHERE salary >= ALL (SELECT salary FROM employees);

-- Alternative:
SELECT salary FROM employees ORDER BY salary DESC LIMIT 1;
```

---

## Question 38: Recursive Fibonacci

Generate the first 20 Fibonacci numbers using a recursive CTE.

```sql
WITH RECURSIVE fib(n, fib_n, fib_next) AS (
    SELECT 1, 0, 1
    UNION ALL
    SELECT n + 1, fib_next, fib_n + fib_next
    FROM fib WHERE n < 20
)
SELECT n, fib_n AS fibonacci_number FROM fib;
```

---

## Question 39: Split Delimited String

Split a comma-separated string into individual rows without a split function.

**Table:** `tags (id INT, tag_list VARCHAR)`

```sql
WITH RECURSIVE split AS (
    SELECT id,
           SUBSTRING_INDEX(tag_list, ',', 1) AS tag,
           SUBSTRING(tag_list, LENGTH(SUBSTRING_INDEX(tag_list, ',', 1)) + 2) AS remainder
    FROM tags
    UNION ALL
    SELECT id,
           SUBSTRING_INDEX(remainder, ',', 1),
           SUBSTRING(remainder, LENGTH(SUBSTRING_INDEX(remainder, ',', 1)) + 2)
    FROM split WHERE remainder != ''
)
SELECT id, tag FROM split ORDER BY id;
```

---

## Question 40: Time Series Interpolation

Fill in missing values in a time series by carrying forward the last known value.

**Table:** `stock_prices (date DATE, price DECIMAL)`

```sql
WITH date_series AS (
    SELECT MIN(date) AS dt FROM stock_prices
    UNION ALL
    SELECT DATE_ADD(dt, INTERVAL 1 DAY) FROM date_series WHERE dt < (SELECT MAX(date) FROM stock_prices)
),
last_known AS (
    SELECT ds.dt, sp.price,
           MAX(sp.date) OVER (ORDER BY ds.dt) AS last_filled_date
    FROM date_series ds
    LEFT JOIN stock_prices sp ON ds.dt = sp.date
)
SELECT dt, FIRST_VALUE(price) OVER (PARTITION BY last_filled_date ORDER BY dt) AS filled_price
FROM last_known;
```

---

## Question 41: JSON Field Extraction

Extract specific values from a JSON column.

**Table:** `events (event_id INT, payload JSON)`

```sql
SELECT event_id,
       JSON_EXTRACT(payload, '$.user.email') AS email,
       JSON_EXTRACT(payload, '$.action.type') AS action_type
FROM events
WHERE JSON_EXTRACT(payload, '$.action.type') = 'purchase';
```

---

## Question 42: Recursive Path Counting

Count the number of paths from root to each leaf in a tree.

```sql
WITH RECURSIVE paths AS (
    SELECT id, parent_id, 1 AS path_count
    FROM categories WHERE parent_id IS NULL
    UNION ALL
    SELECT c.id, c.parent_id, p.path_count
    FROM categories c
    INNER JOIN paths p ON c.parent_id = p.id
)
SELECT COUNT(*) AS total_paths
FROM paths p
WHERE NOT EXISTS (SELECT 1 FROM categories c WHERE c.parent_id = p.id);
```

---

## Question 43: Conditional Early Exit

Find the first transaction where an account balance went negative (running balance < 0).

**Table:** `transactions (account_id, txn_id, amount, txn_date)`

```sql
WITH running AS (
    SELECT account_id, txn_id, amount, txn_date,
           SUM(amount) OVER (PARTITION BY account_id ORDER BY txn_date, txn_id) AS balance
    FROM transactions
),
flagged AS (
    SELECT *,
           LAG(balance) OVER (PARTITION BY account_id ORDER BY txn_date, txn_id) AS prev_balance
    FROM running
)
SELECT account_id, txn_id, txn_date, balance
FROM flagged
WHERE balance < 0 AND (prev_balance IS NULL OR prev_balance >= 0);
```

---

## Question 44: Friend-of-a-Friend (2nd Degree)

Find friends of friends (not direct friends) in a social network.

**Table:** `friendships (user_id, friend_id)`

```sql
WITH direct_friends AS (
    SELECT friend_id FROM friendships WHERE user_id = 1
)
SELECT DISTINCT f2.friend_id AS second_degree
FROM friendships f1
INNER JOIN friendships f2 ON f1.friend_id = f2.user_id
WHERE f1.user_id = 1
  AND f2.friend_id NOT IN (SELECT friend_id FROM direct_friends)
  AND f2.friend_id <> 1;
```

---

## Question 45: Balanced Parentheses Check

Given strings of parentheses, find which strings are balanced.

**Table:** `strings (id INT, str VARCHAR)`

```sql
WITH RECURSIVE check_balance(id, str, pos, count) AS (
    SELECT id, str, 1, 0 FROM strings
    UNION ALL
    SELECT id, str, pos + 1,
           CASE WHEN SUBSTRING(str, pos, 1) = '(' THEN count + 1
                WHEN SUBSTRING(str, pos, 1) = ')' THEN count - 1
                ELSE count END
    FROM check_balance WHERE pos <= LENGTH(str) AND count >= 0
),
final AS (
    SELECT id, MAX(pos) AS last_pos,
           MAX(CASE WHEN pos = LENGTH(str) + 1 THEN count END) AS final_count
    FROM check_balance GROUP BY id
)
SELECT id, str,
       CASE WHEN final_count = 0 THEN 'Balanced' ELSE 'Unbalanced' END AS status
FROM final;
```

---

## Question 46: Election Winner

Given votes table, find the candidate with the most votes. If tie, pick the one that appears first alphabetically.

```sql
WITH vote_counts AS (
    SELECT candidate, COUNT(*) AS votes
    FROM votes GROUP BY candidate
)
SELECT candidate
FROM vote_counts
ORDER BY votes DESC, candidate ASC
LIMIT 1;
```

---

## Question 47: Tournament Winners (Round Robin)

In a tournament where each pair plays twice, find the team with the most wins.

**Table:** `matches (home_team, away_team, home_score, away_score)`

```sql
WITH team_scores AS (
    SELECT home_team AS team,
           CASE WHEN home_score > away_score THEN 1 ELSE 0 END AS win
    FROM matches
    UNION ALL
    SELECT away_team,
           CASE WHEN away_score > home_score THEN 1 ELSE 0 END
    FROM matches
)
SELECT team, SUM(win) AS total_wins
FROM team_scores
GROUP BY team
ORDER BY total_wins DESC;
```

---

## Question 48: Cumulative GPA Calculation

Calculate cumulative GPA from course grades.

**Table:** `grades (course_id, student_id, grade CHAR, credits INT)`

```sql
WITH grade_points AS (
    SELECT student_id, credits,
           CASE grade
               WHEN 'A' THEN 4.0 WHEN 'A-' THEN 3.7
               WHEN 'B+' THEN 3.3 WHEN 'B' THEN 3.0
               WHEN 'B-' THEN 2.7 WHEN 'C+' THEN 2.3
               WHEN 'C' THEN 2.0 WHEN 'C-' THEN 1.7
               WHEN 'D' THEN 1.0 WHEN 'F' THEN 0.0
           END AS points
    FROM grades
)
SELECT student_id,
       ROUND(SUM(credits * points) / SUM(credits), 2) AS cumulative_gpa
FROM grade_points
GROUP BY student_id;
```

---

## Question 49: Matching Without a Self-Join

Given a table of coordinates, find points that are symmetric (a,b) and (b,a) without using a self-join.

```sql
SELECT DISTINCT LEAST(x, y) AS a, GREATEST(x, y) AS b
FROM coordinates
GROUP BY LEAST(x, y), GREATEST(x, y)
HAVING COUNT(*) >= 2;
```

---

## Question 50: Longest Increasing Sequence

Find the longest streak where each day's sales were higher than the previous day.

```sql
WITH streaks AS (
    SELECT sale_date, sales_amount,
           CASE WHEN sales_amount > LAG(sales_amount) OVER (ORDER BY sale_date)
                THEN 0 ELSE 1 END AS streak_break
    FROM daily_sales
),
groups AS (
    SELECT sale_date, sales_amount,
           SUM(streak_break) OVER (ORDER BY sale_date) AS grp
    FROM streaks
)
SELECT grp, MIN(sale_date) AS start_date, MAX(sale_date) AS end_date,
       COUNT(*) AS streak_length, MAX(sales_amount) AS peak_sales
FROM groups
GROUP BY grp
ORDER BY streak_length DESC
LIMIT 1;
```

---

## Question 51: Row Pattern Matching (Data Quality)

Find rows where a "start" event is not followed by an "end" event within 60 minutes.

**Table:** `events (session_id, event_type, event_time)`

```sql
SELECT s.session_id, s.event_time AS start_time
FROM events s
WHERE s.event_type = 'start'
  AND NOT EXISTS (
    SELECT 1 FROM events e
    WHERE e.session_id = s.session_id
      AND e.event_type = 'end'
      AND e.event_time BETWEEN s.event_time AND DATE_ADD(s.event_time, INTERVAL 60 MINUTE)
  );
```

---

## Question 52: Self-Join for Graph Distances

Given a graph, find pairs of nodes connected by exactly one intermediate node.

**Table:** `edges (node_a, node_b)`

```sql
SELECT DISTINCT e1.node_a AS start_node, e2.node_b AS end_node
FROM edges e1
INNER JOIN edges e2 ON e1.node_b = e2.node_a
  AND e1.node_a <> e2.node_b
  AND NOT EXISTS (
    SELECT 1 FROM edges e3
    WHERE (e3.node_a = e1.node_a AND e3.node_b = e2.node_b)
  );
```

---

## Question 53: Conditional Subtotal (Rollup)

Create a report with subtotals by region and grand total.

```sql
SELECT
    COALESCE(region, 'Grand Total') AS region,
    COALESCE(product, 'Subtotal') AS product,
    SUM(sales) AS total_sales
FROM sales_data
GROUP BY region, product WITH ROLLUP;
```

---

## Question 54: Regressive Counting (Reverse Sequential)

For each user, assign a sequential number counting backward from their total count.

**Table:** `actions (user_id, action_time)`

```sql
SELECT user_id, action_time,
       COUNT(*) OVER (PARTITION BY user_id) - ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY action_time) + 1 AS reverse_seq
FROM actions;
```

---

## Question 55: Shortest Path in Graph (BFS)

Find the shortest path between two nodes in a directed graph using recursive CTE.

**Table:** `graph (from_node, to_node)`

```sql
WITH RECURSIVE bfs AS (
    SELECT from_node, to_node, 1 AS depth,
           CAST(CONCAT(from_node, '->', to_node) AS VARCHAR(500)) AS path
    FROM graph WHERE from_node = 'A'
    UNION ALL
    SELECT b.from_node, g.to_node, b.depth + 1,
           CAST(CONCAT(b.path, '->', g.to_node) AS VARCHAR(500))
    FROM bfs b
    INNER JOIN graph g ON b.to_node = g.from_node
    WHERE b.depth < 10
      AND b.path NOT LIKE CONCAT('%', g.to_node, '%')
)
SELECT path, depth
FROM bfs WHERE to_node = 'G'
ORDER BY depth
LIMIT 1;
```
