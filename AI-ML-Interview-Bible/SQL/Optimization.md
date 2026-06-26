# Optimization (Indexes, EXPLAIN PLAN, Query Optimization, N+1 Problem, Covering Indexes)

## What is it?
SQL optimization is the practice of making queries run faster and use fewer resources. Key concepts: indexes (data structures for fast lookups), EXPLAIN PLAN (execution plan analysis), query optimization (rewriting queries for performance), the N+1 problem (excessive queries in loops), and covering indexes (indexes that satisfy all columns in a query without touching the table).

## Why do we use it?
In production, slow queries crash applications and cost money. A single unoptimized query can bring down a database. Performance optimization separates junior from senior engineers. Most interviewers at top companies (Google, Meta) ask optimization questions to test depth.

## Real-Time Example 1
**E-commerce Slow Dashboard**

An e-commerce dashboard query that aggregates millions of orders runs in 45 seconds — too slow for a real-time dashboard.

**Original Query:**
```sql
SELECT customer_id, COUNT(*) as order_count, SUM(amount) as total_spent
FROM orders
WHERE status = 'completed'
  AND order_date >= '2024-01-01'
GROUP BY customer_id;
```

The `orders` table has 10 million rows. No indexes on `status` or `order_date`. Full table scan.

**Optimization:** Create a composite index on `(status, order_date, customer_id, amount)` and rewrite to use that covering index.

## Real-Time Example 2
**N+1 Problem in a Blog Platform**

A social media app loads a feed of 100 posts. For each post, the app makes a separate query to fetch comments. That's 1 + 100 = 101 queries.

**Problem:**
```python
# N+1: 1 query for posts + N queries for comments
posts = db.query("SELECT * FROM posts WHERE user_id = 123")
for post in posts:
    comments = db.query(f"SELECT * FROM comments WHERE post_id = {post.id}")
```

**Solution:** Use a single JOIN or two queries with IN clause.

## Concept Example 1

**Table:** `employees` (1 million rows)

| emp_id | name | department | salary | hire_date | status |
|--------|------|------------|--------|-----------|--------|

**Slow Query:**
```sql
SELECT emp_id, name, salary
FROM employees
WHERE department = 'Engineering' AND salary > 80000;
```

**EXPLAIN PLAN (Conceptual):**
```
Seq Scan on employees (cost=0.00..17482.00 rows=1000 width=68)
  Filter: ((department)::text = 'Engineering'::text) AND (salary > 80000)
```
Full table scan on 1M rows — slow.

**Optimization — Create Index:**
```sql
CREATE INDEX idx_dept_salary ON employees(department, salary);
```

**EXPLAIN PLAN with Index:**
```
Index Only Scan using idx_dept_salary on employees (cost=0.42..84.50 rows=1000 width=68)
  Index Cond: ((department = 'Engineering'::text) AND (salary > 80000))
```
The index scan reads only the relevant rows. The "Index Only Scan" means it never touches the table (covering index).

**Explanation:** The composite index on `(department, salary)` covers the WHERE clause columns and the SELECT columns. Since all needed data is in the index, the database never reads the table. This is a covering index.

## Concept Example 2

**Tables:** `orders` (5M rows), `customers` (500K rows)

**Slow Query:**
```sql
SELECT c.name, COUNT(o.order_id) as order_count
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
WHERE c.signup_date >= '2023-01-01'
  AND o.status = 'completed'
GROUP BY c.customer_id, c.name;
```

**Problem:** This runs in 30 seconds. The LEFT JOIN with a WHERE condition on the right table forces the database to do a full scan.

**Root Cause:** The LEFT JOIN is effectively an INNER JOIN because `o.status = 'completed'` filters out customers with no completed orders.

**Optimized Query:**
```sql
SELECT c.name, COUNT(o.order_id) as order_count
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
    AND o.status = 'completed'
WHERE c.signup_date >= '2023-01-01'
GROUP BY c.customer_id, c.name;
```

**Additional Optimization — Indexes:**
```sql
CREATE INDEX idx_signup ON customers(signup_date, customer_id, name);
CREATE INDEX idx_customer_status ON orders(customer_id, status) INCLUDE (order_id);
```

**Explanation:** Changing LEFT to INNER JOIN (since WHERE filtered out NULLs anyway). Moving `status` filter to JOIN condition allows the optimizer to use it earlier. Indexes support covering index scans.

## Common Mistakes

1. **No WHERE clause on large tables** — Always filter. Don't `SELECT *` from a 10M row table without conditions.
2. **Using functions on indexed columns** — `WHERE UPPER(name) = 'ALICE'` disables the index. Use `WHERE name = 'Alice'` with case-sensitive collation or a function-based index.
3. **OR conditions that kill index usage** — `WHERE status = 'active' OR status = 'pending'` should be `WHERE status IN ('active', 'pending')`.
4. **NOT IN on large subqueries** — NOT IN is slow with large result sets. Use NOT EXISTS or LEFT JOIN ... IS NULL.
5. **SARGability violation** — Predicates should be "Search ARGument-able": `WHERE salary * 1.1 > 100000` disables index; use `WHERE salary > 100000 / 1.1`.
6. **Over-indexing** — Too many indexes slow down INSERT/UPDATE/DELETE. Each index must be maintained.
7. **Ignoring composite index column order** — Put the most selective column first for equality, then range columns.
8. **SELECT * in production** — Brings unnecessary data. Only select columns you need.
9. **No index on foreign keys** — JOINs on unindexed FK columns are slow.
10. **Missing covering indexes** — An index that contains all needed columns can avoid table access entirely.

## How Interviewers Ask This

- "How would you optimize a slow query?"
- "What is the N+1 problem and how do you fix it?"
- "How do indexes work? B-tree vs Hash?"
- "What's a covering index?"
- "How do you read an EXPLAIN plan?"
- "What is SARGability?"
- "You have a query that runs in 10 seconds. How do you make it run in 1 second?"

## How To Impress The Interviewer

- **Think step-by-step when optimizing:**
  1. Check the EXPLAIN plan — find the most expensive operation.
  2. Look for full table scans on large tables.
  3. Check join order and join methods (nested loop vs hash join vs merge join).
  4. Consider adding/reordering indexes.
  5. Rewrite the query if needed.
  6. Test the fix.
- **Understand index types:** B-tree (default, good for range), Hash (equality only), GiST/GIN (full-text), BRIN (large sequential data).
- **Know clustered vs non-clustered indexes:** Clustered (InnoDB primary key) stores data in index order; non-clustered points to data rows.
- **Composite index column order matters:** For `(a, b, c)`, the index works on `a`, `(a, b)`, `(a, b, c)`, but not `b` or `c` alone. Put equality columns first, then range columns.
- **Cardinality awareness:** High cardinality columns (unique) first in index. Don't index boolean columns with 50/50 distribution.
- **Explain the N+1 problem with a real code example** — It shows you understand application-layer impact.
- **Mention query caching** — Result caching, buffer pool, and how to check cache hit ratios.
- **Database-specific expertise:** Mention MySQL's `EXPLAIN FORMAT=JSON`, PostgreSQL's `EXPLAIN ANALYZE`, SQL Server's query store.

## Most Asked Questions

### Q1: What is an index and how does it work?
An index is a data structure (usually B-tree) that speeds up data retrieval. It stores a copy of selected columns sorted for fast lookup. Instead of scanning the full table, the database uses the index to find rows quickly — like a book's index. Trade-off: indexes slow down writes.

### Q2: What's the difference between clustered and non-clustered index?
Clustered index determines the physical order of data in the table (InnoDB's primary key). A table can have only one clustered index. Non-clustered index is a separate structure pointing to data rows. A table can have many non-clustered indexes.

### Q3: What is the N+1 problem?
The N+1 problem happens when code makes 1 query to fetch N records, then makes N additional queries to fetch related data. Common in ORMs (Hibernate, Entity Framework). Fix: use JOIN or batch queries.

```python
# Bad (N+1)
orders = session.query(Order).all()
for order in orders:
    print(order.customer.name)  # separate query per order

# Good
orders = session.query(Order).options(joinedload(Order.customer)).all()
```

### Q4: What is a covering index?
A covering index contains ALL columns needed by a query, so the database never needs to read the actual table. The index "covers" the query. In EXPLAIN plans, look for "Index Only Scan" (PostgreSQL) or "Using index" (MySQL).

### Q5: How do you read an EXPLAIN plan?
EXPLAIN shows the execution plan: scan type (Seq Scan, Index Scan), join method, estimated cost, rows, and width. Key things to look for:
- Seq Scan on large tables (bad) → needs index
- High cost values → expensive operations
- Nested Loop joins on large datasets → may need hash join
- "Using where" vs "Using index" → covering index or not

### Q6: What is SARGability?
SARGable (Search ARGument-able) means a query predicate can use an index efficiently. Non-SARGable patterns include wrapping columns in functions (`WHERE YEAR(date) = 2024`), using `LIKE '%pattern'`, or calculations on columns (`WHERE salary * 1.1 > 50000`).

### Q7: How do you optimize a query that uses OR?
OR conditions often disable index usage. Solutions:
```sql
-- Instead of:
SELECT * FROM orders WHERE status = 'pending' OR status = 'shipped';

-- Use IN:
SELECT * FROM orders WHERE status IN ('pending', 'shipped');

-- Or use UNION:
SELECT * FROM orders WHERE status = 'pending'
UNION
SELECT * FROM orders WHERE status = 'shipped';
```

### Q8: What columns should you index?
Index columns used in WHERE, JOIN conditions, ORDER BY, and GROUP BY. Index foreign key columns. Prefer high-cardinality columns. Create composite indexes for common query patterns. Avoid indexing columns rarely used in queries.

### Q9: How does a composite index work with column order?
For index `(a, b, c)`:
- Works for: `a` only, `a AND b`, `a AND b AND c`, `a AND c` (uses `a` part).
- Doesn't work for: `b` only, `c` only, `b AND c`.
Put equality conditions first, range conditions second.

### Q10: Write an optimized version of this slow query.
```sql
-- Slow query (bad)
SELECT * FROM orders
WHERE YEAR(order_date) = 2024 AND MONTH(order_date) = 1;

-- Optimized
SELECT * FROM orders
WHERE order_date >= '2024-01-01' AND order_date < '2024-02-01';
```

### Q11: What is a table scan and why is it bad?
A table scan reads every row in the table to find matching rows. On a table with 10M rows, that's 10M reads. With an index, you might read only 100 index entries + 100 table rows. Table scans are bad because they waste I/O and CPU.

### Q12: How do you find slow queries in a production database?
Enable slow query log (`long_query_time` in MySQL), use pg_stat_statements in PostgreSQL, use SQL Server's Query Store. Look for queries with high execution time, high rows examined, or high frequency.

### Q13: What's the difference between a hash join and a nested loop join?
Nested Loop: For each row in outer table, scan inner table. Good for small result sets. Hash Join: Build a hash table from one table, probe with the other. Good for large, unsorted data. Merge Join: Sort both tables, merge. Good for large, pre-sorted data.

### Q14: How does database partitioning affect query optimization?
Partitioning splits a table into smaller pieces based on a partition key (e.g., date range). Queries with WHERE on the partition key only scan relevant partitions (partition pruning). This dramatically reduces I/O for large tables.

### Q15: What is index fragmentation and how do you fix it?
Over time, indexes become fragmented (pages out of order) due to inserts/updates/deletes. Fragmented indexes slow queries. Fix with `ALTER INDEX ... REORGANIZE` (defragment) or `REBUILD` (rebuild from scratch) in SQL Server, or `OPTIMIZE TABLE` in MySQL.
