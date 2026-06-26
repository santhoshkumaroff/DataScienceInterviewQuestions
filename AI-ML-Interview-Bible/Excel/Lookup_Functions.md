# Lookup Functions

## What is it?
Lookup functions find and retrieve values from tables. VLOOKUP looks up values vertically. HLOOKUP looks horizontally. INDEX-MATCH is a flexible combination. XLOOKUP (the newest) replaces all of them with a single function. IFERROR handles errors from these lookups.

## Why do we use it?
Lookups connect related data without manual searching. In business, data lives in separate tables (customers, orders, products). Lookups bring them together for analysis, reporting, and dashboarding. They save hours of manual work.

## Real-Time Example 1
**Business Scenario:** You have an Order ID column and need to fetch Customer Names from a separate customer table.
**Step-by-Step (XLOOKUP):**
1. Sheet1: Orders (Order_ID, Amount). Sheet2: Customers (Order_ID, Customer_Name).
2. In Sheet1, enter: `=XLOOKUP(A2, Customers!A:A, Customers!B:B, "Not Found")`.
3. Drag down. Each order now shows its customer name.
4. If an Order_ID is missing, it shows "Not Found" instead of #N/A.

## Real-Time Example 2
**Industry:** Inventory Management
**Use Case:** Look up product prices from a price list.
1. Inventory sheet has Product_ID, Quantity. Price_List sheet has Product_ID, Price.
2. Use INDEX-MATCH: `=INDEX(Price_List!B:B, MATCH(A2, Price_List!A:A, 0))`.
3. Multiplied by quantity: `=INDEX(Price_List!B:B, MATCH(A2, Price_List!A:A, 0)) * B2`.
4. Wrap in IFERROR: `=IFERROR(INDEX(...), 0)` to handle missing products.
5. Result: Total value of inventory calculated automatically.

## Concept Example 1
**VLOOKUP with Exact Match**
- **Input:** Table with Employee IDs (A2:A10) and a lookup table with ID and Department.
- **Formula:** `=VLOOKUP(A2, D2:E10, 2, FALSE)`
- **Output:** Returns the department for each employee ID.
- **Explanation:** VLOOKUP searches the first column of D2:E10 for A2. FALSE means exact match. Column index 2 returns the 2nd column (Department). Limitation: lookup column must be the first column.

## Concept Example 2
**INDEX-MATCH (Two-Way Lookup)**
- **Input:** A sales matrix with Products in rows (A2:A10) and Months in columns (B1:M1).
- **Formula:** `=INDEX(B2:M10, MATCH("Product X", A2:A10, 0), MATCH("Jun", B1:M1, 0))`
- **Output:** Sales value for Product X in June.
- **Explanation:** MATCH finds the row number of "Product X" (vertical). Another MATCH finds the column number of "Jun" (horizontal). INDEX returns the value at that row/column intersection. This is a two-way lookup.

## Common Mistakes
- Forgetting FALSE in VLOOKUP (defaults to TRUE/approximate match).
- Hardcoding column index instead of using MATCH for dynamic columns.
- Not locking the lookup table range with $ (absolute reference).
- Assuming VLOOKUP can look left (it can't—use INDEX-MATCH or XLOOKUP).
- Ignoring trailing spaces that break exact matches (use TRIM).

## How Interviewers Ask This
- "Explain the difference between VLOOKUP and INDEX-MATCH."
- "Why would you choose XLOOKUP over VLOOKUP?"
- "How do you handle #N/A errors in lookups?"
- "How would you do a two-way lookup?"

## How To Impress The Interviewer
- Explain that XLOOKUP supports arrays (return multiple columns at once): `=XLOOKUP(A2, IDs, Table[[Name]:[Salary]])`.
- Show how INDEX-MATCH is faster than VLOOKUP on large datasets (100k+ rows).
- Mention wildcard lookups with `*` and `?` for partial matches.
- Discuss approximate match (TRUE) for bracket/commission rate lookups.
- Show that XLOOKUP can search from bottom to top with search_mode = -1.

## Most Asked Questions

**1. What is VLOOKUP and what are its limitations?**
VLOOKUP searches the first column of a table for a value and returns a value from the same row in a specified column. Limitations: (1) Lookup column must be the leftmost column, (2) column index is hardcoded, (3) breaks if columns are inserted/deleted, (4) can't look to the left.

**2. What is the difference between VLOOKUP with TRUE and FALSE?**
FALSE (0 or exact match) finds an exact value. Returns #N/A if not found. TRUE (1 or approximate match) finds the closest match. Requires the lookup column to be sorted ascending. Used for tiered lookups (tax brackets, commission rates).

**3. What is INDEX-MATCH and why is it better than VLOOKUP?**
INDEX returns a value at a given row/column. MATCH finds the position of a value. Combined: `=INDEX(ReturnRange, MATCH(LookupValue, LookupRange, 0))`. Better because: (1) can look left, (2) column order doesn't matter, (3) inserting columns doesn't break formulas, (4) faster on large data.

**4. How does XLOOKUP differ from VLOOKUP?**
XLOOKUP is newer and simpler: `=XLOOKUP(lookup_value, lookup_array, return_array, [if_not_found], [match_mode], [search_mode])`. Benefits: (1) no column index needed, (2) can look left or right, (3) built-in error handling, (4) supports vertical and horizontal lookups, (5) can return arrays.

**5. How do you handle #N/A errors from lookup functions?**
Use IFERROR: `=IFERROR(VLOOKUP(...), "Not Found")`. Or IFNA (Excel 2013+): `=IFNA(VLOOKUP(...), "Not Found")`. IFNA only catches #N/A, IFERROR catches all errors.

**6. What is the MATCH function? Give examples.**
MATCH returns the relative position of a value in a range.
- `=MATCH(100, A1:A10, 0)` — exact match.
- `=MATCH(100, A1:A10, 1)` — largest value <= 100 (sorted ascending).
- `=MATCH(100, A1:A10, -1)` — smallest value >= 100 (sorted descending).

**7. How do you do a case-sensitive lookup?**
Use EXACT with INDEX and MATCH as an array formula (Ctrl+Shift+Enter in older Excel): `=INDEX(B:B, MATCH(TRUE, EXACT(A2, C:C), 0))`. In newer Excel, XLOOKUP with EXACT works similarly.

**8. What is HLOOKUP and when would you use it?**
HLOOKUP works like VLOOKUP but horizontally (searches the first row, returns from a specified row below). Use when data is organized in rows rather than columns (e.g., lookup tables with headers in the first row).

**9. How do you perform a two-way (matrix) lookup?**
Combine two MATCH functions inside INDEX:
`=INDEX(data_matrix, MATCH(row_value, row_range, 0), MATCH(col_value, col_range, 0))`. Used for looking up values at the intersection of a row and column in a table.

**10. How do you look up multiple criteria?**
Use XLOOKUP with concatenation: `=XLOOKUP(A2&B2, Criteria1&Criteria2, ReturnRange)`.
In older Excel: `=INDEX(Return, MATCH(1, (Criteria1=Range1)*(Criteria2=Range2), 0))` as an array formula.

**11. What is the difference between IFERROR and IFNA?**
IFERROR catches all errors (#N/A, #VALUE!, #DIV/0!, #REF!, etc.). IFNA only catches #N/A errors. Use IFNA when you want to see other errors (helpful for debugging lookup formulas).

**12. How do you use VLOOKUP with approximate match for tiered calculations?**
Example: Commission brackets. Table: 0→2%, 10000→3%, 50000→5%. `=VLOOKUP(A1, BracketTable, 2, TRUE)`. With TRUE, Excel finds the largest value ≤ lookup value. No IF nesting needed.

**13. What happens if VLOOKUP finds multiple matches?**
VLOOKUP returns the first match (topmost). For multiple matching values, use INDEX-MATCH with array formulas or FILTER function (Office 365) to return all matches.

**14. Can VLOOKUP return values to the left?**
No. VLOOKUP always searches the first column and returns from a column to the right. Use INDEX-MATCH or XLOOKUP to return values to the left of the lookup column.

**15. How do you use CHOOSE with VLOOKUP to simulate a left lookup?**
`=VLOOKUP(A2, CHOOSE({1,2}, B:B, A:A), 2, FALSE)`. CHOOSE creates a virtual array with A as the first column and B as the second, allowing VLOOKUP to "look left".

**16. What is the FILTER function and how is it related to lookups?**
FILTER (Office 365) returns all matching records: `=FILTER(Table, Criteria)`. It's more powerful than lookups when you need multiple results. Not technically a lookup, but often used for the same purpose.

**17. How do you use wildcards in lookup functions?**
Use `*` (any characters) and `?` (single character) with exact match (FALSE).
- `=VLOOKUP("*John*", A:B, 2, FALSE)` — finds any cell containing "John".
- `=XLOOKUP("A?C", A:A, B:B, , 2)` — match_mode 2 enables wildcards.

**18. How do you look up values across multiple sheets?**
Use INDIRECT with MATCH: `=INDEX(INDIRECT("'" & A1 & "'!B:B"), MATCH(Lookup, INDIRECT("'" & A1 & "'!A:A"), 0))`. Where A1 contains the sheet name. This creates flexible cross-sheet lookups.

**19. What is the OFFSET function and how is it used with lookups?**
OFFSET returns a range offset from a starting cell. `=SUM(OFFSET(A1, MATCH(Lookup, A:A, 0)-1, 0, 1, 3))` returns 3 columns from the matched row. OFFSET is volatile (recalculates on every change).

**20. How do you handle spaces or formatting issues in lookup values?**
Wrap the lookup value in TRIM: `=VLOOKUP(TRIM(A2), Table, 2, FALSE)`. Use CLEAN to remove non-printable characters. Use TEXT to match number formats: `=VLOOKUP(TEXT(A2,"000"), Table, 2, FALSE)`.

**21. What is the LOOKUP function (legacy)?**
LOOKUP is an older function (not VLOOKUP). `=LOOKUP(lookup_value, lookup_vector, result_vector)`. It always does approximate match (sorted ascending). Rarely used today but may appear in legacy spreadsheets.

**22. How do you return the last match in a lookup?**
XLOOKUP with search_mode -1: `=XLOOKUP(A2, A:A, B:B, , , -1)`. This searches from bottom to top. With INDEX-MATCH: array formula using MAX(IF(...)) for the last occurrence.

**23. What is the difference between column index number 2 and 2 in VLOOKUP?**
They're the same—the column number within the table array. Column index 1 is the first column (the lookup column), index 2 is the second, etc. If you insert a column in the table array, the index doesn't automatically update.

**24. How do you make VLOOKUP dynamic when columns might be added?**
Use MATCH to find the column number dynamically: `=VLOOKUP(A2, Table, MATCH("Sales", Table[#Headers], 0), FALSE)`. This automatically adjusts if "Sales" moves columns.

**25. What is a self-referencing lookup?**
When you look up a value in the same table. Example: Looking up an employee's manager ID in the same employee table to get the manager's name. Requires the lookup table to include all values (including self-references need special handling).
