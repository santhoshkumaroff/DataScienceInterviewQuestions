# Pivot Tables

## What is it?
A PivotTable is an Excel tool that summarizes large datasets into interactive tables. You drag and drop fields to instantly group, count, sum, average, and compare data. It lets you rotate (pivot) rows and columns to see different perspectives of the same data.

## Why do we use it?
PivotTables are the #1 tool for data summarization in business. They eliminate manual formulas, reduce errors, and let non-technical users analyze thousands of rows in seconds. Every data analyst, marketer, and finance professional depends on them.

## Real-Time Example 1
**Business Scenario:** A retail chain wants to analyze monthly sales by product category across 50 stores.
**Step-by-Step:**
1. Source data: Transaction_ID, Date, Store, Category, Product, Quantity, Revenue.
2. Insert > PivotTable > select the data range.
3. Drag Category to Rows, Revenue to Values (SUM).
4. Drag Date to Columns, group by Month.
5. Drag Store to Filters to see individual store performance.
6. Add a Slicer for Product Category to make it interactive.

## Real-Time Example 2
**Industry:** Human Resources
**Use Case:** HR needs to analyze employee demographics and salary distribution.
1. Data: Employee_ID, Department, Gender, Salary, Years_of_Experience, Performance_Rating.
2. Create PivotTable: Department in Rows, Average of Salary in Values.
3. Add Gender to Columns to see gender pay gap by department.
4. Add Performance_Rating as a Report Filter.
5. Use a Timeline to filter by hire year.
6. Add a Calculated Field: Bonus = Salary * 0.1.

## Concept Example 1
**Grouping Dates in a PivotTable**
- **Input:** Transaction data with daily dates from Jan 1 to Dec 31, 2024.
- **Action:** Right-click any date > Group > select Months (and Years if multiple years exist).
- **Output:** Daily data collapses into monthly totals.
- **Explanation:** Excel auto-creates month/year groupings. You can also group by quarters, weeks, or custom ranges. This is faster than adding helper columns.

## Concept Example 2
**Calculated Fields vs Calculated Items**
- **Input:** Sales data with Quantity and Price columns.
- **Calculated Field:** PivotTable Analyze > Fields, Items & Sets > Calculated Field. Formula: `=Quantity * Price`. Adds a new column to the PivotTable.
- **Output:** Revenue calculated automatically for each row.
- **Explanation:** Calculated Fields work on the underlying data (sum of Quantity × sum of Price per cell). Calculated Items work within a field (e.g., "Premium" - "Basic" within Product Type).

## Common Mistakes
- Not formatting source data as a table (new rows not included when refreshing).
- Placing text fields in Values area (PivotTable counts them instead of summing).
- Forgetting to refresh after updating source data.
- Using Calculated Fields when a simple column in source data would work.
- Overcomplicating layouts with too many nested fields.

## How Interviewers Ask This
- "Walk me through creating a PivotTable from scratch."
- "How do you group dates in a PivotTable?"
- "What is the difference between a Calculated Field and a Calculated Item?"
- "How do you show values as percentage of column total?"

## How To Impress The Interviewer
- Mention the Data Model / Power Pivot for handling millions of rows.
- Discuss GETPIVOTDATA function for extracting PivotTable values into formulas.
- Explain that you use "Add this data to the Data Model" for distinct count without helper columns.
- Show knowledge of custom sort orders in PivotTables.
- Mention that PivotTables can connect to external data sources (SQL, Analysis Services).

## Most Asked Questions

**1. What is a PivotTable and when would you use it?**
A PivotTable summarizes large datasets by dragging fields to different areas. Use it when you need to aggregate thousands of rows by categories (e.g., total sales by region, average salary by department).

**2. How do you create a PivotTable?**
Select the data range > Insert > PivotTable > choose New or Existing Worksheet > OK. Drag fields to Rows, Columns, Values, and Filters areas in the PivotTable Fields pane.

**3. How do you refresh a PivotTable when source data changes?**
Right-click the PivotTable > Refresh. To refresh all PivotTables at once: Data > Refresh All. For automatic refresh on file open: PivotTable Options > Data > Refresh data when opening the file.

**4. How do you group data in a PivotTable?**
Right-click a value > Group. For numbers: define starting, ending, and interval size. For dates: group by seconds, minutes, hours, days, months, quarters, or years. For text: select items manually and group them.

**5. What is a Slicer and how does it work?**
A slicer is a visual filter button. Click anywhere in the PivotTable > PivotTable Analyze > Insert Slicer. Select fields. Each slicer shows all unique values as clickable buttons. Multiple fields can be connected and cascading filters applied.

**6. What is a Timeline and how is it different from a Slicer?**
A Timeline filters date fields with a slider. It's visual and shows time periods (years, quarters, months, days). Slicers filter any field type. Timelines only work with date fields.

**7. How do you show values as percentages in a PivotTable?**
Right-click a value in the Values area > Show Value As > % of Column Total, % of Row Total, % of Grand Total, or % of Parent Total. Useful for market share or budget allocation analysis.

**8. What is a Calculated Field in a PivotTable?**
A Calculated Field adds a new field to the PivotTable using a formula based on existing fields. PivotTable Analyze > Fields, Items & Sets > Calculated Field. Example: `=Revenue - Cost` for Profit.

**9. What is the difference between Classic PivotTable layout and Tabular layout?**
Classic layout allows dragging fields directly onto the table. Tabular layout shows all fields in separate columns (like a flat table). Outline layout shows each row field in its own section. Switch via Design > Report Layout.

**10. How do you handle blank cells in a PivotTable?**
Right-click > PivotTable Options > Layout & Format > For empty cells show: enter 0 or "N/A". This replaces blanks with a meaningful placeholder.

**11. What is the GETPIVOTDATA function?**
When you type `=` and click a PivotTable cell, Excel inserts a GETPIVOTDATA formula that returns the value. It's dynamic and updates when the PivotTable changes. Disable via PivotTable Analyze > Options > Generate GetPivotData.

**12. How do you apply conditional formatting to a PivotTable?**
Select cells > Home > Conditional Formatting. Important: Use "All cells showing [field] values" for the rule to apply when the PivotTable layout changes. Otherwise, formatting might not follow the data.

**13. How do you create a Top 10 filter in a PivotTable?**
Click the dropdown on Row Labels > Value Filters > Top 10. Choose Top or Bottom, specify count (10), and select the measure (e.g., Sum of Revenue). Shows only the top performers.

**14. What is Defer Layout Update?**
A checkbox in the PivotTable Fields pane. When checked, changes (dragging fields) don't immediately recalculate. Click "Update" to apply all changes at once. Improves performance with large data.

**15. How do you show multiple summary functions in one PivotTable?**
Drag the same field to Values multiple times. Click each value field > Value Field Settings > change calculation (Sum, Count, Average, Max, Min, Product, etc.). Example: Show both Count and % of Total.

**16. What is the difference between a PivotTable and a PivotChart?**
A PivotChart is the visual representation of a PivotTable. It's interactive—filtering the chart filters the table and vice versa. Create via PivotTable Analyze > PivotChart.

**17. How do you add a running total in a PivotTable?**
Right-click the value > Show Value As > Running Total In. Choose the base field (usually the date or row field). Creates a cumulative total that resets per group if nested.

**18. How do you use the Data Model with PivotTables?**
When creating a PivotTable, check "Add this data to the Data Model". This enables: distinct counts, multiple table relationships, and handling millions of rows via Power Pivot.

**19. How do you change the summary function from SUM to AVERAGE?**
Right-click a value > Summarize Values By > Average. Or click the value field in the pane > Value Field Settings > select Average.

**20. Why is my PivotTable showing "Count of [Field]" instead of "Sum"?**
Excel defaults to COUNT for text fields and blanks. Check for blank cells or text values in the column. If numbers are stored as text, use Text-to-Columns or VALUE() to convert them.

**21. How do you create a PivotTable from multiple sheets?**
Use Alt+D+P (legacy shortcut) to open the PivotTable Wizard. Select "Multiple consolidation ranges". Or better: use Power Query to append sheets, then create a PivotTable from the combined data.

**22. What is the Difference From option in Show Value As?**
Shows the difference between a value and a reference value (previous, next, or a specific base item). Used for month-over-month changes: value - previous month.

**23. How do you sort a PivotTable manually?**
Drag items up/down in the row/column labels. Or apply a custom list via File > Options > Advanced > Edit Custom Lists. PivotTables respect custom sort orders.

**24. How do you drill down into PivotTable values?**
Double-click any value in the Values area. Excel creates a new sheet with the detailed rows that make up that aggregated value. This is also called "Show Details".

**25. How do you remove a field from a PivotTable?**
Uncheck it in the PivotTable Fields pane, or drag it out of its area (Rows, Columns, Values, Filters). The field disappears without affecting the source data.
