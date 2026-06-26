# Power Query (M Language)

## What is it?
Power Query is Power BI's data connection and transformation engine. It uses the M language behind the scenes. With Power Query, you import data from any source, clean it, reshape it, and load it into your data model. All transformations are recorded as steps.

## Why do we use it?
Real-world data is messy. Power Query automates data cleaning without writing code. Every step is visible and repeatable. It handles merging tables, unpivoting columns, filtering rows, and fixing data types. It's the ETL (Extract, Transform, Load) backbone of Power BI.

## Real-Time Example 1
**Business Scenario:** A marketing team gets weekly CSV exports from Google Ads, Facebook Ads, and LinkedIn Ads with different column names.
**Step-by-Step:**
1. Power Query > Get Data > Folder > point to the folder containing CSVs.
2. Combine Files automatically detects schema and merges all CSVs.
3. Use "Promote Headers" to make first row the column names.
4. Rename columns to standard names: CampaignName, Spend, Impressions, Clicks, Conversions.
5. Change Spend data type to Currency. Remove rows where Spend = 0.
6. Add a custom column: `= [Spend] / [Clicks]` for Cost Per Click.
7. Close & Load. Future CSV files in the folder auto-update on refresh.

## Real-Time Example 2
**Industry:** E-commerce
**Use Case:** Clean and combine orders and returns data from SQL database.
1. Import Orders table and Returns table from SQL Server.
2. Merge queries (Left Outer) on OrderID to add return status.
3. Expand the merged column to include ReturnDate and ReturnReason.
4. Replace null return dates with "Not Returned".
5. Unpivot monthly sales columns (Jan_Sales, Feb_Sales, etc.) into rows: Month, Sales.
6. Group by ProductID and calculate total sales per product.
7. Sort by total sales descending, keep top 100.
8. Load to model for dashboard.

## Concept Example 1
**Unpivoting Columns (Normalizing Data)**
- **Input:** A table with columns: Product, Q1, Q2, Q3, Q4 (quarters as separate columns).
- **Action:** Select Product column > Right-click > Unpivot Other Columns.
- **Output:** Product | Attribute (Q1, Q2...) | Value.
- **Explanation:** Unpivot converts wide (cross-tab) data into tall (normalized) format needed for Power BI visuals. This is one of the most common transformations.

## Concept Example 2
**Merging (Join) Tables**
- **Input:** Table1: Orders (OrderID, CustomerID, Amount). Table2: Customers (CustomerID, Name, Region).
- **Action:** Home > Merge Queries > Select Orders and Customers. Join kind: Left Outer. Match on CustomerID.
- **Output:** Orders table now has a nested column containing matching customer data.
- **Action:** Expand the column to bring in Name and Region.
- **Explanation:** Merge is equivalent to SQL JOIN. Inner, Left Outer, Right Outer, Full Outer, Left Anti, Right Anti are all available.

## Common Mistakes
- Promoting headers when the first row is not actually a header.
- Forgetting to set correct data types (especially Date and Number).
- Removing rows at source that turn out to be needed later.
- Not using "Remove Duplicates" correctly (selecting wrong columns).
- Over-fetching: importing entire tables when only a few columns are needed.

## How Interviewers Ask This
- "Explain the steps you'd take to clean a messy CSV in Power Query."
- "What is the difference between Merge and Append?"
- "When would you use Unpivot instead of Pivot?"
- "What is M language and how is it different from DAX?"

## How To Impress The Interviewer
- Write custom M functions (reusable transformations).
- Use parameters for dynamic queries (file paths, filter values).
- Discuss query folding — pushing transformations to the source database for performance.
- Explain buffering with Table.Buffer for slow custom columns.
- Know how to create a date dimension table entirely in M.

## Most Asked Questions

**1. What is Power Query and how does it fit into Power BI?**
Power Query is the data preparation tool in Power BI. It connects to data sources, applies transformations (filter, rename, merge, unpivot, split, etc.), and loads clean data into the model. It records every step so the process is repeatable.

**2. What is M language?**
M is the functional language behind Power Query. Each applied step in the UI generates M code. You can write M directly in the Advanced Editor. Example: `Table.SelectRows(Source, each [Age] > 18)`. M is case-sensitive and functional (no loops, uses recursion).

**3. What is the difference between Merge and Append?**
Merge combines columns from two tables based on a matching key (like SQL JOIN). Append stacks rows from two tables with the same columns (like SQL UNION). Merge adds columns, Append adds rows.

**4. How do you unpivot data in Power Query?**
Select the columns you want to keep as identifiers (row context) > right-click > Unpivot Other Columns. The selected columns stay, the rest become Attribute-Value pairs. Used to convert cross-tab reports into analysis-ready formats.

**5. How do you pivot data in Power Query?**
Select the column whose values will become new column headers (e.g., Month). Select the values column (e.g., Sales). Transform > Pivot Column. Choose aggregation (Sum, Count, etc.). Pivot is the reverse of Unpivot.

**6. What is query folding?**
Query folding is when Power Query translates M steps into SQL queries that run on the source database. This makes transformations faster because data is filtered/aggregated before it leaves the server. Breaking query folding (e.g., adding a custom column) slows performance.

**7. How do you create a custom column in M?**
Add Column > Custom Column. Enter M expression: `[Quantity] * [Price]`. Or write in Advanced Editor: `Table.AddColumn(Source, "Total", each [Quantity] * [Price])`. The `each` keyword iterates over rows.

**8. How do you handle errors in Power Query?**
Remove errors: Home > Remove Rows > Remove Errors. Replace errors: Replace Errors with a value (0, null). Or use `try` in M: `try [Column] otherwise "Default"`. Error handling is critical for robust refreshes.

**9. What is the difference between Table.NestedJoin and Table.Join?**
`Table.NestedJoin` creates a nested column (requires expansion). `Table.Join` performs the join and immediately flattens the result (equivalent to Expand + NestedJoin). Both do the same thing but with different output structures.

**10. How do you split a column by delimiter?**
Select column > Home > Split Column > By Delimiter. Choose delimiter (comma, space, custom). Options: split at each occurrence or only the leftmost/rightmost. Handles parsing full names, addresses, or concatenated codes.

**11. How do you group by in Power Query?**
Home > Group By. Select grouping column(s) and aggregation (Sum, Count, Average, Min, Max, All Rows). "All Rows" keeps nested detail tables for drill-through. Similar to SQL GROUP BY and PivotTable.

**12. How do you filter rows in Power Query?**
Click the dropdown arrow on a column header. Choose text filters, number filters, or date filters. Or use the filter row in the UI. In M: `Table.SelectRows(Source, each [Amount] > 1000)`.

**13. What is the difference between a step and a query?**
A step is a single transformation in a query (e.g., "Removed Columns", "Filtered Rows"). A query is a complete set of steps that produces a table. Steps are recorded in the Applied Steps pane; queries appear in the Queries pane.

**14. How do you create a date table in Power Query?**
Use `List.Dates(start, count, #duration(1,0,0,0))`. Convert to table, add columns for Year, Month Number, Month Name, Quarter, Weekday, etc. Mark as Date Table in the model. This is a common interview exercise.

**15. How do you reference a query from another query?**
In the Queries pane, right-click a query > Reference. The new query starts from the referenced query's output. Any changes to the source query propagate. Used to avoid duplicating logic. Or use `Table.Distinct(Reference)` to create a dimension.

**16. What is the Advanced Editor?**
The Advanced Editor shows the complete M code for a query. You can edit all steps at once, not just through the UI. Useful for complex transformations, copying queries between files, or writing custom M functions.

**17. How do you use parameters in Power Query?**
Home > Manage Parameters > New Parameter. Name, type, current value. Use `= ParameterName` in M. Useful for dynamic file paths, date ranges, or filter values that change between environments.

**18. What are data types in Power Query?**
Text, Whole Number, Decimal Number, Date, Time, DateTime, Duration, True/False, Binary. Setting correct data types is critical for proper calculations and performance. Power Query auto-detects but often needs manual correction.

**19. How do you replace values in Power Query?**
Home > Replace Values. Replace one value with another (e.g., "N/A" with null). Or use `Table.ReplaceValue(Source, "Old", "New", Replacer.ReplaceText, {"Column1"})`. Replace errors, fill down, or replace multiple values.

**20. What is conditional column in Power Query?**
Add Column > Conditional Column. Define rules: If [Amount] > 1000 then "High" else if [Amount] > 500 then "Medium" else "Low". Generates M: `Table.AddColumn(Source, "Tier", each if [Amount] > 1000 then "High" else if [Amount] > 500 then "Medium" else "Low")`.

**21. How do you combine data from multiple files in a folder?**
Get Data > Folder > enter folder path > Combine & Transform. Power Query detects common file schema and merges all files. Adds a Source.Name column to identify which file each row came from. New files added to the folder are included on refresh.

**22. What is Table.Buffer and when would you use it?**
`Table.Buffer` loads a table into memory. Use when a table is referenced multiple times in subsequent steps to avoid re-querying. Helpful for performance with custom columns referencing other queries. Overuse can waste memory.

**23. How do you create a custom M function?**
In a blank query, define: `(x as number, y as number) => x * y`. Save as "Multiply". Use: `= Table.AddColumn(Source, "Result", each Multiply([Qty], [Price]))`. Functions are reusable and can have multiple parameters.

**24. What is the difference between List, Record, and Table in M?**
List: ordered collection of values `{1, 2, 3}`. Record: named fields `[Name="John", Age=30]`. Table: grid of rows and columns `#table({"Name","Age"}, {{"John",30},{"Jane",25}})`. Understanding these types helps write advanced M.

**25. How do you troubleshoot a slow Power Query?**
(1) Check query folding (right-click step > View Native Query). (2) Remove unnecessary steps. (3) Filter early in the process. (4) Disable background refresh. (5) Use query dependencies view to find bottlenecks. (6) Split complex queries into simpler ones.
