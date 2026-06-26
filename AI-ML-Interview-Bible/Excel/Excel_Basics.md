# Excel Basics

## What is it?
Excel is a spreadsheet application from Microsoft that organizes data into rows and columns. It provides tools for calculations, data analysis, charting, and automation. Excel Basics include understanding formulas, cell references, data types, sorting, and filtering.

## Why do we use it?
Excel is the most widely used data tool across all industries. It requires no coding, is accessible to everyone, and handles everything from simple lists to complex financial models. Almost every business role uses Excel daily.

## Real-Time Example 1
**Business Scenario:** A sales manager needs to calculate monthly commissions for 200 sales reps.
**Step-by-Step:**
1. Open a spreadsheet with columns: Sales Rep, Region, Total Sales, Commission Rate.
2. Use a formula in the Commission column: `=C2*D2` (Total Sales × Commission Rate).
3. Drag the fill handle down to copy the formula for all rows.
4. Use AutoSum to calculate total commissions: `=SUM(E2:E201)`.
5. Sort data by Region using Data > Sort.
6. Filter to see only "West" region using Data > Filter.

## Real-Time Example 2
**Industry:** HR / Recruitment
**Use Case:** An HR manager tracks 500 job applicants.
1. Columns: Name, Position Applied, Interview Score, Status, Date Applied.
2. Use data validation to restrict Status column to "Pending", "Shortlisted", "Rejected", "Hired".
3. Sort by Score descending to find top candidates.
4. Filter Status = "Shortlisted" to send offer letters.
5. Use Conditional Formatting to highlight scores above 80 in green.

## Concept Example 1
**Basic Formula Calculation**
- **Input:** Cells A1=10, B1=20, C1=30
- **Formula:** `=AVERAGE(A1:C1)`
- **Output:** 20
- **Explanation:** The AVERAGE function adds 10+20+30=60 and divides by 3. Cell references (A1:C1) tell Excel which cells to use.

## Concept Example 2
**Mixed Cell References**
- **Input:** A1=100, B1=10, B2=20, B3=30. Formula in C1: `=$A$1*B1`
- **Output when dragged down:** C1=1000, C2=2000, C3=3000
- **Explanation:** `$A$1` is an absolute reference (always points to A1). B1 is relative (changes to B2, B3 as you drag). Mixed references ($A1 or A$1) lock only the row or column.

## Common Mistakes
- Forgetting the equals sign (=) before formulas.
- Using absolute vs relative references incorrectly.
- Storing numbers as text (green triangle in corner).
- Merging cells when using filters or pivot tables.
- Not using Tab to auto-complete function names.

## How Interviewers Ask This
- "What is the difference between relative and absolute cell reference?"
- "How do you remove duplicates in Excel?"
- "Explain the difference between COUNT, COUNTA, and COUNTBLANK."
- "How would you sort a table by multiple columns?"

## How To Impress The Interviewer
- Mention keyboard shortcuts (Ctrl+Arrow, Ctrl+Shift+End, Alt+= for AutoSum).
- Explain that you use Excel Tables (Ctrl+T) for dynamic ranges.
- Discuss structured references and how they auto-expand.
- Show knowledge of Flash Fill (Ctrl+E) for quick data cleaning.
- Mention Power Query for data transformation beyond basic Excel.

## Most Asked Questions

**1. What is the difference between a workbook and a worksheet?**
A workbook is the entire Excel file (.xlsx). A worksheet is a single tab/sheet within that workbook. One workbook can have many worksheets.

**2. Explain the difference between COUNT, COUNTA, COUNTBLANK, and COUNTIF.**
COUNT counts cells with numbers only. COUNTA counts cells that are not empty (any data type). COUNTBLANK counts empty cells. COUNTIF counts cells meeting a condition (e.g., `=COUNTIF(A1:A10,">50")`).

**3. What are relative, absolute, and mixed cell references?**
Relative reference (A1): changes when copied. Absolute ($A$1): stays fixed when copied. Mixed ($A1 or A$1): locks only column or row. Example: `=B1*$C$1` locks C1 but lets B1 change.

**4. How do you sort data by multiple columns?**
Select the data range, go to Data > Sort. Add levels: first by Department, then by Salary descending. Excel sorts by the first level, then the second within ties.

**5. What is data validation and how do you use it?**
Data > Data Validation restricts what users can enter. You can create dropdown lists, set number ranges, date limits, or custom formulas. Prevents invalid data entry.

**6. How do you remove duplicate values?**
Select the range, go to Data > Remove Duplicates. Choose which columns to check for duplicates. Excel keeps the first occurrence and removes the rest.

**7. What is the difference between a filter and a sort?**
Sort rearranges data order (ascending/descending). Filter hides rows that don't match criteria without changing order. Filters can be applied on multiple columns simultaneously.

**8. How do you use Conditional Formatting?**
Home > Conditional Formatting. Rules can highlight cells based on values, top/bottom items, data bars, color scales, or icon sets. Example: Highlight all cells > 100 in green.

**9. What is the IF function? Give an example.**
`=IF(logical_test, value_if_true, value_if_false)`. Example: `=IF(A1>50, "Pass", "Fail")` returns "Pass" if A1 > 50, otherwise "Fail".

**10. How do you freeze panes in Excel?**
View > Freeze Panes. Freeze Top Row keeps row 1 visible while scrolling. Freeze First Column keeps column A visible. Freeze Panes keeps rows above and columns left of the selected cell visible.

**11. What are named ranges and why use them?**
Named ranges give a name to a cell or range (e.g., name A1:A100 as "Sales"). They make formulas readable: `=SUM(Sales)` instead of `=SUM(A1:A100)`. Create via Formulas > Define Name.

**12. How do you create a drop-down list?**
Data > Data Validation > List. Enter items separated by commas (Yes,No,Maybe) or select a range. The cell shows a dropdown arrow.

**13. What is the TEXT function used for?**
`=TEXT(value, format_text)` converts numbers to formatted text. Example: `=TEXT(A1, "mm/dd/yyyy")` displays a date. `=TEXT(A1, "$#,##0.00")` formats currency.

**14. Explain the difference between SUMIF and SUMIFS.**
SUMIF adds cells based on one condition: `=SUMIF(range, criteria, sum_range)`. SUMIFS adds with multiple conditions: `=SUMIFS(sum_range, criteria_range1, criteria1, criteria_range2, criteria2)`.

**15. How do you transpose data?**
Copy the data, right-click where you want it, select Paste Special > Transpose. This flips rows to columns and columns to rows.

**16. What is Flash Fill?**
Flash Fill (Ctrl+E) automatically fills data based on a pattern. Type an example, press Ctrl+E, and Excel detects and applies the pattern. Great for extracting first names, formatting phone numbers, etc.

**17. How do you protect a worksheet or workbook?**
Review > Protect Sheet (password protects editing). Review > Protect Workbook (protects structure like adding/deleting sheets). File > Info > Protect Workbook for encryption.

**18. What is a clustered column chart?**
A chart that groups bars side-by-side for comparing multiple categories. Each cluster represents one category, each bar within represents a sub-category. Insert > Column Chart.

**19. How do you create a PivotTable from data?**
Select the data range, go to Insert > PivotTable. Choose where to place it. Drag fields to Rows, Columns, Values, and Filters areas. Refresh with right-click > Refresh.

**20. What are keyboard shortcuts for common tasks?**
Ctrl+C/V/X (copy/paste/cut), Ctrl+Z/Y (undo/redo), Ctrl+Arrow (jump to edge of data), Ctrl+Shift+End (select to last cell), Alt+= (AutoSum), Ctrl+T (create table), Ctrl+1 (format cells), F2 (edit cell).

**21. What is the difference between a table and a range?**
A table (Ctrl+T) has built-in filtering, structured references, automatic expansion, and consistent formatting. A range is just raw cells without these features.

**22. How do you handle errors like #DIV/0! or #N/A?**
Use IFERROR: `=IFERROR(A1/B1, 0)` returns 0 instead of #DIV/0!. Use IFNA for #N/A specifically. Check for blank divisor or missing lookup values.

**23. What is the CONCATENATE function (or & operator)?**
Joins text from multiple cells. `=A1 & " " & B1` combines A1 and B1 with a space. CONCAT (newer) and TEXTJOIN (with delimiter options) are modern alternatives.

**24. Explain the difference between PMT, FV, and NPV.**
PMT calculates loan payments. FV calculates future value of an investment. NPV calculates net present value of cash flows. These are financial functions used in modeling.

**25. How do you record a macro?**
View > Macros > Record Macro. Perform actions, then stop recording. Macros automate repetitive tasks. Saved in the workbook as VBA code. Assign to buttons for easy access.
