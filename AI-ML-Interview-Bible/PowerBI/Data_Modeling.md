# Data Modeling (Star Schema & Relationships)

## What is it?
Data modeling is the process of structuring data tables and their relationships for efficient reporting. Star schema organizes data into fact tables (measurements/transactions) and dimension tables (descriptive attributes). Relationships connect tables using keys with defined cardinality and filter direction.

## Why do we use it?
A good data model is the foundation of fast, accurate Power BI reports. Without it, DAX formulas are slow, relationships are confusing, and reports produce wrong numbers. Star schema reduces data redundancy, improves query performance, and makes the model easy to understand.

## Real-Time Example 1
**Business Scenario:** A retail company needs a sales report combining orders, customers, products, and dates.
**Step-by-Step Schema Design:**
1. Fact Table: Sales (OrderID, CustomerID, ProductID, DateKey, Quantity, Amount).
2. Dimension Tables: Customers (CustomerID, Name, Region, Segment), Products (ProductID, Name, Category, Price), Date (DateKey, Date, Year, Month, Day, Quarter).
3. Create relationships: Sales[CustomerID] → Customers[CustomerID] (many-to-one), Sales[ProductID] → Products[ProductID], Sales[DateKey] → Date[DateKey].
4. Set all filter directions to single (dimension filters fact, not vice versa).
5. Result: Users can slice sales by customer region, product category, or any date period.

## Real-Time Example 2
**Industry:** Finance / Banking
**Use Case:** Analyze loan portfolio performance across branches, products, and time.
1. Fact Table: LoanTransactions (TransactionID, LoanID, BranchID, DateKey, Amount, InterestRate, Status).
2. Dimensions: Loans (LoanID, ProductType, Term, CustomerSegment), Branches (BranchID, Region, City, Manager), Date (DateKey date dimension).
3. Add a Bridge Table for many-to-many: LoanGuarantors (LoanID, GuarantorID).
4. Use role-playing dimensions: Date[DateKey] connected to LoanTransactions[DateKey] and also to LoanTransactions[DisbursementDate] via inactive relationships.
5. Use USERELATIONSHIP in DAX to activate alternate date relationships.

## Concept Example 1
**Star Schema vs Snowflake Schema**
- **Star:** Product dimension is a single table with Category, SubCategory as columns.
- **Snowflake:** Category is a separate table, SubCategory is another, linked to Product via foreign keys.
- **Preference in Power BI:** Star schema is better. Snowflake normalization adds relationship overhead. Power BI's columnar storage handles denormalized dimensions efficiently.
- **Explanation:** Star schema has fewer joins, simpler DAX, and better performance. Snowflake is rarely needed in Power BI unless source system enforces it.

## Concept Example 2
**Bidirectional Cross-Filtering**
- **Setup:** Sales → Products (single direction, many-to-one). Products → Category (single direction, many-to-one).
- **Default:** Filtering Category filters Products filters Sales. But filtering Sales does NOT filter Category.
- **Bidirectional:** Enabling "Cross filter direction: Both" on Sales → Products allows filtering Sales to propagate to Products to Category.
- **Risk:** Ambiguous filter context (circular filtering) — use sparingly.
- **Better alternative:** Use a denormalized dimension with all attributes in one table.

## Common Mistakes
- Not having a separate Date table (essential for time intelligence).
- Creating a giant flat table with all columns (no star schema).
- Using bidirectional filters everywhere without understanding the impact.
- Forgetting to set correct cardinality (many-to-one vs one-to-one).
- Creating relationships on columns with duplicate values (should be unique on one side).

## How Interviewers Ask This
- "Explain star schema and why it's important in Power BI."
- "What is the difference between a fact table and a dimension table?"
- "How do you handle many-to-many relationships?"
- "When would you use bidirectional cross-filtering?"

## How To Impress The Interviewer
- Discuss bridge tables for many-to-many relationships with proper DAX (SUMX with CROSSFILTER).
- Explain role-playing dimensions and USERELATIONSHIP.
- Talk about degenerate dimensions (order header attributes in fact table).
- Mention calculation groups for reusing measures across time intelligence scenarios.
- Understand how to handle slowly changing dimensions (Type 1 vs Type 2) in Power BI.

## Most Asked Questions

**1. What is star schema in data modeling?**
A star schema has one central fact table (measurable events: sales, transactions) surrounded by dimension tables (descriptive: products, customers, dates). The fact table contains foreign keys to dimensions and numeric measures. Dimension tables are denormalized.

**2. What is the difference between a fact table and a dimension table?**
Fact tables store quantitative data (measures, metrics) and foreign keys to dimensions. They have many rows and are updated frequently. Dimension tables store descriptive attributes (names, categories, hierarchies). They have fewer rows and change slowly.

**3. What are the types of fact tables?**
(1) Transactional — one row per event (sales transaction). (2) Periodic snapshot — regular summary (monthly inventory levels). (3) Accumulating snapshot — tracks process (order-to-delivery lifecycle). (4) Factless — records events without measures (student attendance).

**4. What is a snowflake schema and why avoid it in Power BI?**
Snowflake normalizes dimensions into multiple related tables. It saves storage but adds join complexity. In Power BI, storage is efficient and joins cost performance. Star schema is preferred — denormalize dimensions.

**5. What is cardinality in relationships?**
Cardinality describes how rows in one table relate to another: Many-to-One (most common), One-to-One (rare), Many-to-Many (requires bridge table). One-to-Many is the same as Many-to-One depending on direction.

**6. What is cross-filter direction?**
Single direction: the dimension table filters the fact table. Both (bidirectional): facts can also filter dimensions. Default is single. Bidirectional can cause ambiguity. Use it only when necessary and understand the impact on filter context.

**7. How do you handle many-to-many relationships?**
Option 1: Add a bridge table with unique combinations. Option 2: Use CROSSFILTER in DAX. Option 3: Denormalize if possible. The bridge table approach is preferred: two one-to-many relationships from dimensions to the bridge.

**8. What is a Date table and why is it required?**
A Date table contains one row per date with columns for Year, Month, Quarter, Day, Weekday, etc. It must be marked as a Date Table in Power BI. Required for time intelligence functions (TOTALYTD, SAMEPERIODLASTYEAR, etc.). Continuously covers the date range of your data.

**9. What are inactive relationships?**
A table can have multiple relationships with another table (e.g., Sales has OrderDate and ShipDate). One is active (default, solid line), others are inactive (dashed line). Use `USERELATIONSHIP` in DAX to activate an inactive one for a specific measure.

**10. What is a surrogate key?**
A surrogate key is an artificial unique identifier (often an auto-incrementing integer) used as the primary key in a dimension table. It's preferred over natural keys (like ProductCode) because it's stable even if the natural key changes.

**11. How do you create calculated columns in a data model?**
Calculated columns are created in Power Query (M) or in DAX (calculated column). They add a new column to a table. Use DAX for row-level calculations: `Profit = Sales[Revenue] - Sales[Cost]`. They are evaluated during refresh and stored.

**12. What is a degenerate dimension?**
A degenerate dimension is an attribute in the fact table that doesn't belong to a separate dimension table. Example: Order Number stored in the Sales fact table. It's kept in the fact when it doesn't have its own descriptive attributes.

**13. What is a role-playing dimension?**
A dimension used multiple times with different meanings. Example: Date dimension used for Order Date, Ship Date, and Delivery Date. Each usage is a different "role". Create multiple inactive relationships or duplicate the dimension.

**14. How do you handle slowly changing dimensions (SCD)?**
Type 1: Overwrite the attribute (no history). Type 2: Add a new row with effective dates (tracks history). In Power BI, Type 2 creates multiple rows per entity. Use Power Query to manage SCD logic during data loading.

**15. What are the best practices for naming tables and columns?**
Use clear, business-friendly names (not database codes). Avoid spaces in column names (use underscores). Prefix fact tables with "Fact_" and dimensions with "Dim_". Hide technical keys and foreign keys from report view. Organize into display folders.

**16. How do you hide fields in Power BI?**
Right-click a field > Hide in Report View. Hidden fields remain available for DAX calculations but don't appear in the Fields pane for report authors. Useful for keys, technical columns, and intermediate measures.

**17. What is the difference between Import mode, DirectQuery, and Dual mode?**
Import: data copied into Power BI (fast, scheduled refresh). DirectQuery: queries source database live (real-time, slower). Dual: used in composite models (some tables import, some DirectQuery). Import is preferred for most scenarios.

**18. How do you organize measures in a model?**
Create a separate "Measures" table (Create Table with empty list, then move measures there). Use display folders to group related measures. Name measures consistently (Total Sales, Sales PY, Sales YoY %). Keep related measures together.

**19. What is the difference between a calculated column and a measure in modeling context?**
Calculated columns are stored in the model and increase file size. Measures are not stored — they're computed at query time. Use calculated columns sparingly. Prefer measures for aggregations and calculations that change with filter context.

**20. How do you detect and fix circular dependencies?**
Power BI shows a warning during save. Trace the chain: A calculated column in Table1 references Table2, which references Table1. Fix by removing one of the dependencies, using a measure instead, or restructuring the model.

**21. What is the proper way to handle a date range filter across multiple date columns?**
Use a disconnected date slicer table. Create a measure that checks if the selected date range overlaps with the event: `CALCULATE(SUM(...), FILTER(Sales, Sales[StartDate] <= MAX(Date[Date]) && Sales[EndDate] >= MIN(Date[Date])))`.

**22. How do you model hierarchical data (e.g., Org Chart)?**
Use a self-referencing table (EmployeeID, ManagerID). Create a calculated column for the hierarchy path in Power Query. Use Parent-Child functions (PATH, PATHITEM, PATHLENGTH) or unroll hierarchy into multiple columns.

**23. What are calculation groups?**
Calculation groups (Tabular Editor only) define reusable calculation logic (e.g., Time Intelligence: YTD, QTD, MTD, YoY). They appear as a slicer in reports and automatically apply to all measures. Reduces measure duplication significantly.

**24. What is the difference between a regular table and a disconnected table?**
A regular table has relationships to other tables. A disconnected table has no relationships — used for parameters (Top N selector, dynamic measure switching). Connected via DAX using TREATAS, LOOKUPVALUE, or user relationship functions.

**25. How do you test if your data model is correct?**
(1) Create a matrix visual with dimensions and measures; verify totals. (2) Use DAX Studio to run EVALUATE queries and check row counts. (3) Validate relationships in Diagram View. (4) Cross-check with source system totals. (5) Test edge cases (empty dimensions, NULL values).
