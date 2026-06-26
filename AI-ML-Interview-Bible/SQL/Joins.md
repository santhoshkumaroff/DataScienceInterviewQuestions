# Joins (INNER, LEFT, RIGHT, FULL, CROSS, SELF)

## What is it?
Joins combine rows from two or more tables based on a related column. INNER JOIN returns matching rows. LEFT JOIN returns all rows from left table plus matches from right. RIGHT JOIN is the opposite. FULL JOIN returns all rows from both tables. CROSS JOIN produces a Cartesian product. SELF JOIN joins a table to itself.

## Why do we use it?
Real-world data is normalized across multiple tables. Joins are how you reconstruct the complete picture — linking orders to customers, products to categories, or employees to managers. Every SQL interview includes at least one join question.

## Real-Time Example 1
**E-commerce Order Fulfillment**

An online store needs to generate a report of all orders with customer names and payment status. Even orders without payments should appear.

**Tables:**

```
orders
| order_id | customer_id | order_date | total |
|----------|-------------|------------|-------|
| 101      | 501         | 2024-01-05 | 250   |
| 102      | 502         | 2024-01-06 | 150   |
| 103      | 503         | 2024-01-07 | 300   |

customers
| customer_id | name    | email             |
|-------------|---------|-------------------|
| 501         | Alice   | alice@email.com   |
| 502         | Bob     | bob@email.com     |
| 504         | Diana   | diana@email.com   |

payments
| payment_id | order_id | amount | status  |
|------------|----------|--------|---------|
| 901        | 101      | 250    | Paid    |
| 902        | 102      | 150    | Pending |
```

**Goal:** Show all orders with customer names, even if customer info is missing. Also include payment status.

## Real-Time Example 2
**HR Employee-Manager Hierarchy**

A company wants to display each employee alongside their manager's name using a SELF JOIN on the employees table.

**Table:** `employees (emp_id, name, manager_id, department)`

**Goal:** Build an org chart showing who reports to whom.

## Concept Example 1

**Tables:**

```
students
| student_id | name    |
|------------|---------|
| 1          | Alice   |
| 2          | Bob     |
| 3          | Charlie |

enrollments
| enrollment_id | student_id | course    |
|---------------|------------|-----------|
| 101           | 1          | Math      |
| 102           | 1          | Science   |
| 103           | 2          | Math      |
```

**Goal:** Show all students with their enrolled courses. Include students with no enrollments.

**Venn Diagram Explanation:**
- INNER JOIN: Students AND Enrollments (intersection) → Alice (Math, Science), Bob (Math)
- LEFT JOIN: All Students, matching Enrollments → Alice (Math, Science), Bob (Math), Charlie (NULL)
- RIGHT JOIN: All Enrollments, matching Students → Alice (Math, Science), Bob (Math)
- FULL JOIN: All Students AND all Enrollments → Alice (Math, Science), Bob (Math), Charlie (NULL)
- CROSS JOIN: Every student × every enrollment → 3 × 3 = 9 rows

**SQL:**
```sql
-- LEFT JOIN: all students, even without enrollments
SELECT s.name, e.course
FROM students s
LEFT JOIN enrollments e ON s.student_id = e.student_id
ORDER BY s.name;
```

**Output:**

| name    | course  |
|---------|---------|
| Alice   | Math    |
| Alice   | Science |
| Bob     | Math    |
| Charlie | NULL    |

**Explanation:** Charlie has no enrollments, so course is NULL. LEFT JOIN preserves all rows from the left (students) table.

## Concept Example 2

**Tables:**

```
employees
| emp_id | name    | manager_id |
|--------|---------|------------|
| 1      | CEO     | NULL       |
| 2      | Alice   | 1          |
| 3      | Bob     | 1          |
| 4      | Charlie | 2          |
| 5      | Diana   | 2          |

projects
| project_id | name        | lead_id |
|------------|-------------|---------|
| 101        | Project X   | 2       |
| 102        | Project Y   | 3       |
```

**Goal:** Show each employee with their manager's name (SELF JOIN) and the project they lead (LEFT JOIN).

**SQL:**
```sql
SELECT
    e.name AS employee,
    m.name AS manager,
    p.name AS project
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.emp_id
LEFT JOIN projects p ON e.emp_id = p.lead_id
ORDER BY e.name;
```

**Output:**

| employee | manager | project    |
|----------|---------|------------|
| Alice    | CEO     | Project X  |
| Bob      | CEO     | Project Y  |
| CEO      | NULL    | NULL       |
| Charlie  | Alice   | NULL       |
| Diana    | Alice   | NULL       |

**Explanation:** SELF JOIN links employee to their manager via manager_id. LEFT JOIN to projects shows which projects each employee leads. CEO has no manager (NULL). Charlie and Diana don't lead projects (NULL).

## Common Mistakes

1. **Forgetting join conditions** — Missing ON clause causes CROSS JOIN, producing enormous result sets.
2. **Wrong join type** — Using INNER JOIN when you need LEFT JOIN (losing rows from one table).
3. **NULL matches in joins** — NULL = NULL is FALSE, so joins on NULL columns fail. Use COALESCE or IS NULL checks.
4. **Join order confusion** — A LEFT JOIN B is NOT the same as B LEFT JOIN A.
5. **Mixing LEFT and INNER joins** — An INNER JOIN after a LEFT JOIN can eliminate the LEFT's unmatched rows.
6. **Not aliasing tables** — Causes ambiguity errors when columns share names.

## How Interviewers Ask This

- "Write a query to find customers who never placed an order."
- "What's the difference between INNER and LEFT JOIN?"
- "Explain joins using Venn diagrams."
- "Write a query to show employee names with their manager names."
- "Given tables A and B, what's the difference between A LEFT JOIN B and A RIGHT JOIN B?"
- "How do you join three tables?"
- "What is a CROSS JOIN and when would you use it?"

## How To Impress The Interviewer

- **Draw Venn diagrams** during your explanation — it shows visual communication skills.
- **Know join order matters:** A LEFT JOIN B INNER JOIN C — the INNER JOIN may filter out A's unmatched rows. Use parentheses or CTEs to control order.
- **Use table aliases** (short meaningful ones like `o` for orders, `c` for customers) for readability.
- **Understand join performance:** INNER JOIN is fastest, then LEFT JOIN, FULL JOIN is slowest. Always join on indexed columns.
- **Explain the difference** between `WHERE a.id = b.id` (implicit old-style join) and explicit JOIN syntax — always prefer explicit.
- **Anti-join pattern:** `LEFT JOIN table2 ON ... WHERE table2.id IS NULL` finds rows in table1 not in table2. This is an anti-join.
- **Mention hash vs nested loop vs merge joins** if the role is senior.
- **Filter placement matters:** Conditions in ON vs WHERE can change results with outer joins.

## Most Asked Questions

### Q1: Find customers who never placed an order.
```sql
-- Anti-join pattern
SELECT c.*
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE o.order_id IS NULL;

-- Alternative using NOT EXISTS
SELECT c.*
FROM customers c
WHERE NOT EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id);
```

### Q2: Explain INNER vs LEFT JOIN with examples.
INNER JOIN returns only rows with matches in both tables. LEFT JOIN returns all rows from left table, with NULLs for non-matching right table rows. Example: `students LEFT JOIN enrollments` shows all students; `students INNER JOIN enrollments` shows only enrolled students.

### Q3: Write a query to find employees who earn more than their manager.
```sql
SELECT e.name AS employee, e.salary AS emp_salary, m.salary AS mgr_salary
FROM employees e
INNER JOIN employees m ON e.manager_id = m.emp_id
WHERE e.salary > m.salary;
```

### Q4: How do you join three tables?
```sql
SELECT o.order_id, c.name, p.amount
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
INNER JOIN payments p ON o.order_id = p.order_id;
```

### Q5: What is a CROSS JOIN and when would you use it?
CROSS JOIN produces a Cartesian product — every row from table A paired with every row from table B. Use cases: generating all date-employee combinations for attendance, creating test data, or producing all possible combinations.

```sql
-- Generate all combinations of sizes and colors
SELECT s.size, c.color
FROM sizes s
CROSS JOIN colors c;
```

### Q6: Write a SELF JOIN query to find duplicate records.
```sql
SELECT a.*
FROM users a
INNER JOIN users b ON a.email = b.email AND a.id < b.id;
```

### Q7: What's the difference between ON and WHERE in outer joins?
In LEFT JOIN, conditions in ON are applied before the join. Conditions in WHERE are applied after and can eliminate NULL rows from the right table, effectively converting LEFT JOIN to INNER JOIN.

```sql
-- These are different:
-- 1. Keeps all customers, only filters orders
SELECT * FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id AND o.amount > 100;

-- 2. Converts to INNER JOIN (drops customers with no orders > 100)
SELECT * FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.amount > 100;
```

### Q8: Write a query to show all possible mentor-mentee pairs.
```sql
SELECT mentor.name AS mentor, mentee.name AS mentee
FROM employees mentor
CROSS JOIN employees mentee
WHERE mentor.emp_id <> mentee.emp_id;
```

### Q9: What is a NATURAL JOIN?
NATURAL JOIN automatically joins on columns with the same name in both tables. Avoid it — it's fragile and can produce unexpected results when table schemas change.

### Q10: Write a FULL JOIN example.
```sql
-- All employees and their assigned projects, plus projects with no lead
SELECT e.name AS employee, p.name AS project
FROM employees e
FULL JOIN projects p ON e.emp_id = p.lead_id;
```

### Q11: Find pairs of products frequently bought together (using SELF JOIN).
```sql
SELECT a.product_id AS product_a, b.product_id AS product_b, COUNT(*) AS times_bought_together
FROM order_items a
INNER JOIN order_items b ON a.order_id = b.order_id AND a.product_id < b.product_id
GROUP BY a.product_id, b.product_id
ORDER BY times_bought_together DESC;
```

### Q12: Write a query using LEFT JOIN to identify orphaned records.
```sql
-- Orders with no matching customer (data integrity issue)
SELECT o.*
FROM orders o
LEFT JOIN customers c ON o.customer_id = c.customer_id
WHERE c.customer_id IS NULL;
```
