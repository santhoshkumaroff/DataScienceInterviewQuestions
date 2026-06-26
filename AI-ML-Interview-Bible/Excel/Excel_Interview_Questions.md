# Excel Interview Questions

## What is it?
This file compiles 30+ Excel interview questions covering formulas, PivotTables, lookups, dashboards, data analysis, shortcuts, and best practices. These are the most commonly asked questions in data analyst, business analyst, and finance interviews.

## Why do we use it?
Excel proficiency is tested in almost every data-related interview. Companies need to know you can handle real data tasks, not just theory. These questions prepare you for technical rounds where you may be asked to solve problems live.

## Real-Time Example 1
**Interview Scenario:** You're asked to analyze a CSV with 50,000 rows of sales data.
**Live Test:** Calculate total revenue by region and month using the fastest method.
**Solution:** Insert a PivotTable with Region in Rows, Date (grouped by Month) in Columns, Revenue in Values. Add a slicer for Product Category. Done in 30 seconds.

## Real-Time Example 2
**Interview Scenario:** Given two tables — employees and departments — you must find the top 3 earners per department.
**Solution:** Use Power Query to merge tables, sort by salary descending per department, group by department with "All Rows" operation, then extract top 3 from each group using Table.FirstN. Alternative: Use array formulas with LARGE and IF.

## Most Asked Questions

**1. What is the difference between a function and a formula in Excel?**
A formula is any expression that starts with `=` (e.g., `=A1+B1`). A function is a predefined operation like SUM or VLOOKUP. All functions are formulas, but not all formulas use functions.

**2. How do you use conditional formatting to highlight duplicate values?**
Select the range > Home > Conditional Formatting > Highlight Cells Rules > Duplicate Values. Choose formatting (light red fill). To find unique values instead, use the built-in "Unique" option or a COUNTIF-based rule.

**3. Explain the difference between TRIM, CLEAN, and PROPER.**
TRIM removes extra spaces (leading, trailing, between words). CLEAN removes non-printable characters (line breaks, tabs). PROPER capitalizes the first letter of each word. These are text cleaning functions used before analysis.

**4. How do you create a combo chart (bar + line) in Excel?**
Select data > Insert > Combo Chart > Clustered Column - Line on Secondary Axis. Select each series and choose chart type. Use secondary axis for the line when scales differ (e.g., revenue in bars, growth % as line).

**5. What is Power Query and why is it important?**
Power Query is an ETL (Extract, Transform, Load) tool inside Excel. It imports data from various sources (files, databases, web), transforms it (remove columns, split, merge, pivot, filter), and loads it to Excel. It's important because it automates data cleaning without VBA.

**6. How do you handle large datasets that slow down Excel?**
(1) Use Power Pivot / Data Model instead of worksheets. (2) Limit volatile functions. (3) Compress data with Power Query. (4) Use Excel Tables for efficient storage. (5) Disable automatic calculations. (6) Use manual PivotTable refresh.

**7. What is the difference between a PivotTable and a regular table?**
A PivotTable summarizes and aggregates data dynamically. A regular table (Ctrl+T) organizes raw data with filtering and structured references. PivotTables are built from regular tables or ranges.

**8. How do you compare two Excel files?**
View > View Side by Side (synchronized scrolling). Or use spreadsheet compare tool (Inquire add-in). For formula differences: Show Formulas (Ctrl+`). For data differences: use conditional formatting or Power Query merge with comparison.

**9. What are array formulas? Give an example.**
Array formulas perform multiple calculations on one or more items. In older Excel, entered with Ctrl+Shift+Enter. Example: `{=SUM(IF(A1:A10>50, A1:A10, 0))}` sums only values > 50. In Excel 365, dynamic arrays are native.

**10. How do you use the TEXT function for date formatting?**
`=TEXT(A1, "dd-mmm-yyyy")` converts a date to "01-Jan-2024". `=TEXT(A1, "mmmm")` returns "January". Useful when you need date parts as text for concatenation or chart labels.

**11. What is the difference between SUMIFS, COUNTIFS, and AVERAGEIFS?**
SUMIFS(SUM_range, criteria_range1, criteria1, ...) adds values matching multiple conditions. COUNTIFS counts matches. AVERAGEIFS averages matches. All support multiple criteria pairs. Example: `=SUMIFS(Sales, Region, "West", Month, "Jan")`.

**12. How do you use Goal Seek for what-if analysis?**
Data > What-If Analysis > Goal Seek. Set a cell to a target value by changing another cell. Example: Find what interest rate gives a monthly payment of $500. Excel iterates to find the answer.

**13. What is the INDIRECT function and when would you use it?**
INDIRECT converts a text string into a cell reference. `=INDIRECT("Sheet2!A1")` returns the value in Sheet2!A1. Use for: dynamic sheet references, creating flexible lookups, and building dependent data validation lists.

**14. How do you create a data model with relationships?**
Go to Power Pivot > Manage > Diagram View. Drag fields between tables to create relationships. This enables PivotTables that use columns from multiple related tables without VLOOKUPs.

**15. What are dynamic arrays in Excel 365?**
New functions (FILTER, SORT, UNIQUE, SEQUENCE, RANDARRAY) that spill results across multiple cells automatically. Example: `=UNIQUE(A1:A100)` returns all unique values in a column. No Ctrl+Shift+Enter needed.

**16. How do you secure sensitive data in an Excel file?**
(1) File > Info > Protect Workbook > Encrypt with Password. (2) Mark as Final (read-only). (3) Restrict access with Information Rights Management. (4) Hide sensitive columns/rows and protect the sheet. (5) Use Power BI with row-level security instead.

**17. What is the difference between a macro and VBA?**
A macro is a recorded set of actions (stored as VBA code). VBA (Visual Basic for Applications) is the programming language behind macros. You can record macros without coding, or write VBA code manually for more complex automation.

**18. How do you create a custom number format?**
Right-click cell > Format Cells > Custom. Pattern: `#,##0.00;(#,##0.00);0;"Text"` — positive; negative; zero; text. Use `0` for forced digits, `#` for optional digits, `@` for text placeholders.

**19. What is the SUBTOTAL function and how is it different from SUM?**
SUBTOTAL(function_num, range) can sum, count, average, etc. Function_num 9=SUM (ignores hidden rows), 109=SUM (ignores hidden rows and manual hiding). SUBTOTAL automatically ignores values from other SUBTOTALs (no double counting).

**20. How do you consolidate data from multiple sheets?**
Data > Consolidate. Select function (SUM, AVERAGE, etc.). Add ranges from each sheet. Check "Top row" and "Left column" labels. Excel merges matching labels. Alternative: use Power Query to append queries.

**21. What are named formulas (defined names that use formulas)?**
Formulas > Name Manager > New. Instead of a range, enter a formula like `=OFFSET(Sheet1!$A$1,0,0,COUNTA(Sheet1!$A:$A),1)`. Named formulas can be used in charts, data validation, and conditional formatting for dynamic ranges.

**22. How do you use the AGGREGATE function?**
`=AGGREGATE(function_num, options, range)` combines SUBTOTAL with error handling. Options: 0=ignore nested SUBTOTAL, 1=ignore hidden rows, 2=ignore errors, 3=ignore hidden + errors, 4-7=combinations. Function 14=LARGE, 15=SMALL, etc.

**23. What is the difference between xlsx, xlsm, and xlsb formats?**
xlxs: standard workbook (no macros, XML-based). xlsm: macro-enabled workbook. xlsb: binary workbook (smaller file size, faster load, supports macros). xls: legacy Excel 97-2003 format (limited rows, no modern features).

**24. How do you create a Pareto chart?**
Select data > Insert > Statistic Chart > Pareto. Shows bars sorted descending with a cumulative percentage line. Used for the 80/20 rule (80% of problems come from 20% of causes).

**25. What are common Excel shortcuts every analyst should know?**
Ctrl+Arrow (navigate), Ctrl+Shift+Arrow (select), Ctrl+Space/Shift+Space (select column/row), Alt+= (AutoSum), F4 (repeat action/toggle absolute reference), Ctrl+T (create table), Ctrl+1 (format cells), F2 (edit cell), Ctrl+; (today's date), Ctrl+Shift+4 (currency format).

**26. How do you use the SWITCH function?**
`=SWITCH(A1, "Red", 1, "Blue", 2, "Green", 3, 0)` — evaluates A1 and returns the matching result. Like a simplified IF without nesting. Works for exact matches only. Default value at the end (optional).

**27. What is the LET function and how does it improve performance?**
`=LET(x, 100, y, A1, x*y)` assigns names to values/calculations. Improves readability and performance by calculating complex expressions once instead of repeatedly. Great for long formulas with repeated sub-calculations.

**28. How do you use the LAMBDA function?**
LAMBDA creates custom functions without VBA. `=LAMBDA(x, x*2)(5)` returns 10. Defined in Name Manager: "Double = LAMBDA(x, x*2)". Then `=Double(5)` works like a built-in function. Reusable across the workbook.

**29. How do you troubleshoot a broken Excel workbook?**
(1) Check for circular references (Formulas > Error Checking > Circular References). (2) Evaluate formulas step-by-step (Formulas > Evaluate Formula). (3) Watch window for monitoring cells. (4) Check for hidden rows/columns. (5) Recalculate with Ctrl+Alt+F9.

**30. How do you export Excel data to other formats?**
File > Export > Change File Type. Options: CSV (comma delimited), PDF, XPS, OpenDocument Spreadsheet. For database use: save as CSV or use Power Query to load to SQL. For sharing: save as PDF. For legacy systems: save as xls or CSV.
