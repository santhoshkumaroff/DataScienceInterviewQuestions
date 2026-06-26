# DAX (Data Analysis Expressions)

## What is it?
DAX is a formula language used in Power BI, Power Pivot, and Analysis Services. It's similar to Excel formulas but designed for working with relational data. DAX includes functions for aggregation, filtering, time intelligence, and creating calculated columns and measures.

## Why do we use it?
DAX is the heart of Power BI calculations. Measures and calculated columns let you create custom metrics that Excel formulas alone cannot handle. DAX works with filtered contexts and relationships, making it essential for dynamic reporting.

## Real-Time Example 1
**Business Scenario:** A retail company needs Year-over-Year sales growth percentage.
**Step-by-Step:**
1. Create a base measure: `Total Sales = SUM(Sales[Amount])`.
2. Create a time intelligence measure: `Sales PY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))`.
3. Create growth measure: `YoY Growth = DIVIDE([Total Sales] - [Sales PY], [Sales PY])`.
4. Format as percentage. Drop into a matrix with Year and Category.
5. Result: Dynamic YoY growth that updates with any slicer selection.

## Real-Time Example 2
**Industry:** SaaS / Subscription Business
**Use Case:** Calculate Monthly Recurring Revenue (MRR) and Churn Rate.
1. Data: Customer, Plan, Monthly_Price, Start_Date, End_Date (null if active).
2. Measure: `Active Customers = CALCULATE(COUNTROWS(Customers), Customers[End_Date] = BLANK())`.
3. Measure: `MRR = CALCULATE(SUM(Customers[Monthly_Price]), Customers[End_Date] = BLANK())`.
4. Churn: `Churn Rate = DIVIDE(COUNTROWS(FILTER(Customers, Customers[End_Date] >= EDATE(TODAY(), -1))), [Active Customers])`.
5. Visual: Card for MRR, line chart for trend, gauge for churn vs target.

## Concept Example 1
**CALCULATE with FILTER**
- **Input:** Sales table with Amount, Product[Category], Date[Year].
- **Measure:** `High Value Sales = CALCULATE(SUM(Sales[Amount]), FILTER(Sales, Sales[Amount] > 1000))`
- **Output:** Sum of sales only for transactions exceeding $1,000.
- **Explanation:** CALCULATE changes the filter context. FILTER iterates over each row and keeps only those where Amount > 1000. CALCULATE wraps the filter and applies the aggregation.

## Concept Example 2
**Time Intelligence: Running Total**
- **Input:** Sales table with Date and Amount columns.
- **Measure:** `Running Total = CALCULATE(SUM(Sales[Amount]), DATESYTD('Date'[Date]))`
- **Output:** Cumulative sum from January 1 to the latest selected date.
- **Explanation:** DATESYTD returns all dates from the start of the year to the max date in current context. CALCULATE applies this filter to the SUM. Works across years in matrix visual automatically.

## Common Mistakes
- Confusing calculated columns (row context) with measures (filter context).
- Using SUM on a column that should be COUNT or DISTINCTCOUNT.
- Forgetting that CALCULATE changes filter context (it's not the same as FILTER).
- Not using a separate Date table for time intelligence.
- Writing verbose DAX when simpler functions like SUMX or AVERAGEX suffice.

## How Interviewers Ask This
- "Explain the difference between a measure and a calculated column."
- "What is filter context and row context in DAX?"
- "How does CALCULATE work?"
- "Write a DAX formula for Year-over-Year growth."

## How To Impress The Interviewer
- Explain context transition — when row context becomes filter context inside CALCULATE.
- Discuss iterator functions (X functions) vs aggregator functions (SUM vs SUMX).
- Show understanding of expanded tables and how relationships affect filter propagation.
- Mention performance considerations: avoid FILTER over entire tables; use CALCULATE with simple conditions.
- Know ALL, ALLEXCEPT, ALLSELECTED for advanced filter manipulation.

## Most Asked Questions

**1. What is the difference between a measure and a calculated column?**
A calculated column is computed row-by-row during data refresh and stored in the model. A measure is evaluated at query time based on filter context and is not stored. Use calculated columns for row-level calculations, measures for dynamic aggregations.

**2. What is CALCULATE and why is it important?**
CALCULATE is the most powerful DAX function. It evaluates an expression in a modified filter context. Syntax: `CALCULATE(expression, filter1, filter2, ...)`. It can add, remove, or override filters. Almost all time intelligence and dynamic measures use CALCULATE.

**3. Explain the difference between SUM and SUMX.**
SUM adds a single column: `SUM(Sales[Amount])`. SUMX is an iterator that evaluates an expression for each row and sums the results: `SUMX(Sales, Sales[Quantity] * Sales[Price])`. Use SUMX when you need row-by-row calculation before summation.

**4. What is filter context vs row context?**
Filter context comes from slicers, visuals, and query filters — it determines which rows are included. Row context exists inside iterators (X functions) and calculated columns — it refers to the current row. CALCULATE converts row context to filter context (context transition).

**5. How do you use the FILTER function?**
FILTER returns a table of rows that meet a condition. Used inside CALCULATE: `CALCULATE(SUM(Sales[Amount]), FILTER(Products, Products[Category] = "Electronics"))`. FILTER is powerful but can be slow on large tables — prefer simple filter conditions when possible.

**6. What is the ALL function used for?**
ALL removes filters from a table or column. `CALCULATE(SUM(Sales[Amount]), ALL(Sales))` returns total sales ignoring all filters. Used for percentages of total: `DIVIDE([Total Sales], CALCULATE([Total Sales], ALL(Products)))`.

**7. What does ALLEXCEPT do?**
ALLEXCEPT keeps filters on specified columns and removes all others. `CALCULATE(SUM(Sales[Amount]), ALLEXCEPT(Sales, Sales[Region]))` removes all filters except Region. Useful for calculating % of region total.

**8. What is the RELATED function?**
RELATED fetches a value from the related table on the "one" side of a one-to-many relationship. Used in calculated columns: `= RELATED(Products[Category])`. Similar to VLOOKUP in Excel. The relationship must exist in the model.

**9. What is the difference between COUNT, COUNTA, COUNTROWS, and DISTINCTCOUNT?**
COUNT counts numeric values in a column. COUNTA counts non-blank values (any type). COUNTROWS counts rows in a table. DISTINCTCOUNT counts unique values in a column. DISTINCTCOUNT is commonly used for customer counts.

**10. What are time intelligence functions in DAX?**
Functions that work with date periods: TOTALYTD, TOTALQTD, TOTALMTD, SAMEPERIODLASTYEAR, DATEADD, DATESBETWEEN, DATESYTD, PREVIOUSMONTH, NEXTMONTH, PARALLELPERIOD. They require a properly marked Date table.

**11. How do you calculate Year-over-Year growth in DAX?**
```dax
Total Sales = SUM(Sales[Amount])
Sales PY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR('Date'[Date]))
YoY Growth = DIVIDE([Total Sales] - [Sales PY], [Sales PY])
```
DIVIDE handles division by zero gracefully (returns BLANK instead of error).

**12. What is the DIVIDE function and why use it instead of /?**
`DIVIDE(numerator, denominator, [alternate_result])` safely divides. If denominator = 0, it returns BLANK (or alternate_result). Better than `/` because it avoids #DIV/0! errors and is more performant.

**13. How do you create a measure for running total?**
```dax
Running Total = CALCULATE(
    SUM(Sales[Amount]),
    FILTER(ALL('Date'), 'Date'[Date] <= MAX('Date'[Date]))
)
```
This sums all sales up to the current date in context, ignoring date filters but respecting other filters.

**14. What is the difference between SELECTEDVALUE and VALUES?**
SELECTEDVALUE returns a single value when a column has exactly one value in context (or alternate result). VALUES returns a table of unique values. Use SELECTEDVALUE for single-selection scenarios (slicers with one item).

**15. How do you handle many-to-many relationships in DAX?**
Use CROSSFILTER to modify relationship direction temporarily: `CALCULATE(SUM(Sales[Amount]), CROSSFILTER(T1[Key], T2[Key], BOTH))`. Or use TREATAS to inject filter context from one table to another without a physical relationship.

**16. What is the EARLIER function?**
EARLIER returns the earlier row context when iterating nested tables. Used in calculated columns for "for each row, look at other rows" logic. Example: rank within a group. Modern DAX often replaces EARLIER with variables or RANKX.

**17. How do you use variables in DAX?**
```dax
YoY Growth =
VAR CurrentSales = SUM(Sales[Amount])
VAR PreviousSales = CALCULATE(SUM(Sales[Amount]), SAMEPERIODLASTYEAR('Date'[Date]))
RETURN DIVIDE(CurrentSales - PreviousSales, PreviousSales)
```
Variables improve readability, performance (evaluated once), and debugging.

**18. What is USERELATIONSHIP?**
USERELATIONSHIP activates an inactive relationship for the duration of a calculation. `CALCULATE(SUM(Sales[Amount]), USERELATIONSHIP(Sales[ShipDate], 'Date'[Date]))`. Useful when a table has multiple date columns (order date, ship date, delivery date).

**19. How do you rank items in DAX?**
```dax
Rank = RANKX(ALL(Products[Category]), [Total Sales], , DESC, Dense)
```
RANKX ranks values within a table. Parameters: table, expression to rank by, value, order, ties method (Skip or Dense). ALL removes filters to rank globally.

**20. What is the difference between HASONEVALUE and ISFILTERED?**
HASONEVALUE returns TRUE if a column has exactly one distinct value. ISFILTERED returns TRUE if a column is being filtered directly. HASONEVALUE is safer for checking if a single selection was made (returns TRUE even if only one value exists in data).

**21. How do you create a dynamic top N measure?**
```dax
Top N Sales =
VAR N = SELECTEDVALUE('Top N'[Value], 10)
VAR TopProducts = TOPN(N, ALL(Products[Name]), [Total Sales])
RETURN CALCULATE([Total Sales], TopProducts)
```
Use a disconnected parameter table with values 5, 10, 20, etc. TOPN dynamically selects top products.

**22. What is context transition?**
Context transition happens when CALCULATE is used inside a row context (calculated column or iterator). CALCULATE converts the current row context into an equivalent filter context. This is why measures can be used in calculated columns but not vice versa.

**23. How do you calculate percentage of total in DAX?**
```dax
% Total = DIVIDE([Total Sales], CALCULATE([Total Sales], ALL(Products)))
```
ALL removes product filters while keeping other filters. For % of row total, use ALLSELECTED. For % of column total, use ALL(Column).

**24. What is the CROSSFILTER function?**
CROSSFILTER modifies the filter propagation direction between two tables for the duration of an expression. Options: NONE (no filter), SINGLE (one way), BOTH (bidirectional). Use when default relationship direction is insufficient.

**25. How do you debug a DAX measure?**
(1) Use a matrix visual showing the measure broken down by relevant dimensions. (2) Create separate measures for intermediate calculations. (3) Use EVALUATE in DAX Studio. (4) Check filter context with HASONEVALUE and ISFILTERED. (5) Simplify by removing CALCULATE to see base values.
