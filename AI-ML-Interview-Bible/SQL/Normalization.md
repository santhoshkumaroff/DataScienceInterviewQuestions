# Normalization (1NF, 2NF, 3NF, BCNF, Denormalization)

## What is it?
Normalization is the process of organizing database tables to reduce redundancy and dependency by dividing tables and defining relationships. 1NF eliminates duplicate columns and requires atomic values. 2NF removes partial dependencies (columns depending on part of a composite key). 3NF removes transitive dependencies (non-key columns depending on other non-key columns). BCNF is a stricter version of 3NF. Denormalization intentionally adds redundancy for read performance.

## Why do we use it?
Normalization prevents data anomalies (insert, update, delete), saves storage, and ensures data integrity. In interviews, normalization questions test your understanding of good database design — critical for building scalable, maintainable systems.

## Real-Time Example 1
**E-commerce Database Design**

An online store starts with a single orders table containing: `order_id, customer_name, customer_email, product_name, product_category, product_price, quantity`. This violates all normal forms.

**Goal:** Design a normalized schema for orders, customers, products, and order_items.

## Real-Time Example 2
**Healthcare Records System**

A hospital stores patient records in one wide table: `patient_id, name, doctor_name, doctor_specialty, diagnosis, treatment, room_number, floor_number`.

**Goal:** Normalize to 3NF to avoid redundant doctor info, room info, and treatment data.

## Concept Example 1

**Unnormalized Table (0NF):**

| student_id | name   | courses                | instructor        |
|------------|--------|------------------------|-------------------|
| 1          | Alice  | Math, Science          | Mr. Smith         |
| 2          | Bob    | Math                   | Mr. Smith         |
| 3          | Charlie| Science, Art           | Mrs. Jones, Ms. Lee|

**Problems:** Multiple values in one cell, inconsistent instructor storage.

**1NF — Atomic values, unique rows:**

| student_id | name   | course   | instructor  |
|------------|--------|----------|-------------|
| 1          | Alice  | Math     | Mr. Smith   |
| 1          | Alice  | Science  | Mr. Smith   |
| 2          | Bob    | Math     | Mr. Smith   |
| 3          | Charlie| Science  | Mrs. Jones  |
| 3          | Charlie| Art      | Ms. Lee     |

**Problems:** Partial dependency — `instructor` depends only on `course`, not on `(student_id, course)`. If Math changes instructor, we must update multiple rows.

**2NF — Remove partial dependencies:**

Students table:
| student_id | name   |
|------------|--------|
| 1          | Alice  |
| 2          | Bob    |
| 3          | Charlie|

Courses table:
| course   | instructor  |
|----------|-------------|
| Math     | Mr. Smith   |
| Science  | Mrs. Jones  |
| Art      | Ms. Lee     |

Enrollments table:
| student_id | course   |
|------------|----------|
| 1          | Math     |
| 1          | Science  |
| 2          | Math     |
| 3          | Science  |
| 3          | Art      |

**Problems:** Transitive dependency — `instructor` depends on `course`, but what if `instructor -> instructor_office`? That creates a transitive dependency through `course`.

**3NF — Remove transitive dependencies:**

Instructors table:
| instructor  | office    |
|-------------|-----------|
| Mr. Smith   | Room 101  |
| Mrs. Jones  | Room 102  |
| Ms. Lee     | Room 103  |

Courses table:
| course   | instructor  |
|----------|-------------|
| Math     | Mr. Smith   |
| Science  | Mrs. Jones  |
| Art      | Ms. Lee     |

**BCNF — Every determinant must be a candidate key:**
If an instructor teaches only one course, and a course has only one instructor, but the dependency `instructor -> course` exists where instructor is not a key, we need to split. In our case, it's already in BCNF because each table's non-key attributes are fully dependent on a candidate key.

## Concept Example 2

**Denormalized Table for Reporting:**

| order_id | customer_name | product_names               | total |
|----------|---------------|-----------------------------|-------|
| 101      | Alice         | T-Shirt, Shoes, Hat         | 150   |
| 102      | Bob           | T-Shirt                     | 50    |
| 103      | Alice         | Jacket, Scarf               | 120   |

**Normalized Version (3NF):**

**customers:** `customer_id, name`
**products:** `product_id, name, price`
**orders:** `order_id, customer_id, order_date`
**order_items:** `order_id, product_id, quantity`

**Normalized Query:**
```sql
SELECT c.name, p.name AS product_name, oi.quantity, oi.quantity * p.price AS line_total
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
INNER JOIN order_items oi ON o.order_id = oi.order_id
INNER JOIN products p ON oi.product_id = p.product_id
WHERE o.order_id = 101;
```

**Output:**

| name  | product_name | quantity | line_total |
|-------|--------------|----------|------------|
| Alice | T-Shirt      | 2        | 50         |
| Alice | Shoes        | 1        | 60         |
| Alice | Hat          | 1        | 40         |

**When to Denormalize:**
If this is a read-heavy reporting system with millions of orders, the 4-table join is slow. A denormalized "order_summary" table with pre-joined data could serve reports 10x faster. Denormalization is a deliberate trade-off: write complexity and storage for read speed.

## Common Mistakes

1. **Over-normalizing** — Splitting into too many tiny tables, making queries complex and slow. 3NF is usually enough.
2. **Confusing 2NF and 3NF** — 2NF is about partial dependencies (non-key depends on part of composite key). 3NF is about transitive dependencies (non-key depends on another non-key).
3. **Ignoring business context** — Designing pure 3NF without considering query patterns. Sometimes 2NF is practical.
4. **Missing surrogate keys** — Using natural keys (like email) as PK can cause problems when they change.
5. **Not understanding BCNF** — BCNF applies when there are overlapping candidate keys. Most 3NF tables are already in BCNF.
6. **Mixing denormalization with bad design** — Denormalization is intentional, not an excuse for lazy schema design.
7. **Forgetting cascade rules** — After normalization, foreign key constraints need proper ON DELETE/ON UPDATE rules.

## How Interviewers Ask This

- "Normalize this table to 3NF."
- "What's the difference between 2NF and 3NF?"
- "When would you denormalize a database?"
- "What is BCNF and how is it different from 3NF?"
- "What are the benefits and drawbacks of normalization?"
- "Design a normalized schema for an e-commerce platform."
- "What is a transitive dependency?"

## How To Impress The Interviewer

- **Memorize the normal forms by their dependency types:**
  - 1NF: No repeating groups, atomic values.
  - 2NF: 1NF + no partial dependency on a composite key.
  - 3NF: 2NF + no transitive dependency (non-key depends on non-key).
  - BCNF: 3NF + every determinant is a candidate key.
- **Use real examples with business context** — Don't just recite definitions. Show you know when to normalize and when to denormalize.
- **Explain the trade-off:** Normalization saves storage and ensures integrity but can hurt read performance. Denormalization boosts reads at the cost of write overhead and redundancy.
- **Know about surrogate vs natural keys** — Surrogate keys (auto-increment IDs) are stable and simple. Natural keys (SSN, email) have business meaning but can change.
- **Mention denormalization patterns in data warehousing:** Star schema (fact + dimension tables) is a denormalized design optimized for analytics. Kimball vs Inmon methodologies.
- **Bring up real-world systems:** Facebook's Unread Count denormalization, Amazon's order history denormalization.
- **Discuss partial updates** — Normalization means one fact in one place, avoiding update anomalies.

## Most Asked Questions

### Q1: What is normalization?
Normalization is a database design technique that organizes tables to minimize redundancy and dependency. It divides larger tables into smaller, related tables and defines relationships between them to avoid data anomalies.

### Q2: Explain 1NF, 2NF, 3NF with examples.
- **1NF:** Each cell contains a single value; no repeating groups. Example: A student can't have "Math, Science" in one course column.
- **2NF:** 1NF + all non-key columns depend on the FULL primary key (no partial dependency). Example: In a Student_Course table with PK (student_id, course_id), instructor depends on course_id alone, violating 2NF.
- **3NF:** 2NF + no transitive dependencies (non-key column depends on another non-key column). Example: In a Courses table with (course_id, instructor_name, instructor_office), instructor_office depends on instructor_name, not course_id.

### Q3: What is the difference between 3NF and BCNF?
BCNF (Boyce-Codd Normal Form) is stricter. A table is in BCNF if for every functional dependency X → Y, X is a superkey. In 3NF, a dependency X → Y is allowed if Y is part of a candidate key. BCNF eliminates all redundancy that 3NF might miss.

### Q4: What is denormalization and when would you use it?
Denormalization intentionally adds redundancy to improve read performance. Use it for read-heavy workloads, reporting/analytics systems, data warehouses, when joins become too slow, or for caching computed values (e.g., order total stored on order table instead of computed from line items).

### Q5: What are the advantages and disadvantages of normalization?
**Advantages:** No data redundancy, consistent data, easier to maintain, no update/insert/delete anomalies, smaller storage.
**Disadvantages:** More tables = more joins = slower reads, complex queries, can hurt performance in read-heavy systems.

### Q6: What is a functional dependency?
A functional dependency X → Y means the value of X uniquely determines the value of Y. Example: employee_id → employee_name (knowing the employee ID gives you the name). If two rows have the same X, they must have the same Y.

### Q7: What is a partial dependency?
A partial dependency occurs when a non-key column depends on only part of a composite primary key. Example: In (student_id, course_id) → student_name, student_name depends only on student_id, not the full key. This violates 2NF.

### Q8: What is a transitive dependency?
A transitive dependency occurs when a non-key column depends on another non-key column. Example: course_id → instructor_id → instructor_office. instructor_office depends on instructor_id, which depends on course_id. This violates 3NF.

### Q9: Design a normalized schema for an e-commerce platform.
```sql
-- Customers
CREATE TABLE customers (customer_id INT PRIMARY KEY, name VARCHAR(100), email VARCHAR(100));

-- Products
CREATE TABLE products (product_id INT PRIMARY KEY, name VARCHAR(100), price DECIMAL(10,2), category_id INT);

-- Categories
CREATE TABLE categories (category_id INT PRIMARY KEY, name VARCHAR(100), parent_category_id INT);

-- Orders
CREATE TABLE orders (order_id INT PRIMARY KEY, customer_id INT, order_date DATE, FOREIGN KEY (customer_id) REFERENCES customers(customer_id));

-- Order Items
CREATE TABLE order_items (order_id INT, product_id INT, quantity INT, price DECIMAL(10,2), PRIMARY KEY (order_id, product_id));
```

### Q10: When is it okay to stay in 2NF instead of going to 3NF?
When the transitive dependency is not causing issues, when query simplicity is more important than perfect normalization, or for reporting tables where historical data shouldn't cascade through key changes.

### Q11: What are update anomalies? Give examples.
- **Insert anomaly:** Can't insert a new instructor without assigning them to a course.
- **Update anomaly:** Changing an instructor's name in one place but not others, causing inconsistency.
- **Delete anomaly:** Deleting the last student in a course deletes the course information.

### Q12: How does normalization relate to data warehousing?
Data warehouses often use star schemas (denormalized) for performance — fact tables with dimension tables. This is intentional denormalization for analytics. OLTP systems use normalized design; OLAP systems use denormalized design.

### Q13: What is 4NF and 5NF?
4NF handles multi-valued dependencies (one attribute determining multiple independent values). 5NF handles join dependencies (lossless join decomposition). These are rarely needed in practice — 3NF/BCNF covers most real-world scenarios.

### Q14: How do you test if a table is in BCNF?
Check every functional dependency X → Y. For each, verify that X is a superkey (contains a candidate key). If any determinant is not a superkey, the table violates BCNF.

### Q15: What is the difference between normalization and partitioning?
Normalization is logical design (splitting tables by dependencies). Partitioning is physical design (splitting a table across files/disks for performance). They are complementary, not alternatives.
