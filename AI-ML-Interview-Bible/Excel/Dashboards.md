# Excel Dashboards

## What is it?
An Excel dashboard is a visual summary of key metrics on a single sheet. It combines charts, conditional formatting, PivotTables, slicers, and KPIs to give a quick overview of business performance. Dashboards are interactive and updated automatically when source data changes.

## Why do we use it?
Dashboards replace static reports with real-time visibility. Executives and managers need to make decisions quickly. A dashboard shows trends, outliers, and KPIs at a glance without digging through raw data.

## Real-Time Example 1
**Business Scenario:** A sales director wants a daily dashboard showing revenue, deals closed, pipeline value, and top performers.
**Step-by-Step:**
1. Source data: CRM export with Deal_ID, Sales_Rep, Amount, Stage, Close_Date.
2. Create 4 PivotTables: Revenue by Month, Deals by Stage, Top 10 Reps, Pipeline by Region.
3. Insert a PivotChart for each (line for revenue trend, funnel for stages, bar for reps).
4. Add Slicers for Region and Sales_Rep connected to all PivotTables.
5. Add a scorecard section with KPI cards using formulas referencing PivotTables via GETPIVOTDATA.
6. Apply conditional formatting — data bars on revenue, icon sets on pipeline health.

## Real-Time Example 2
**Industry:** Healthcare / Hospital Administration
**Use Case:** Monitor patient wait times, bed occupancy, and emergency room performance.
1. Data: Patient_ID, Arrival_Time, Wait_Time, Department, Bed_Status, Discharge_Date.
2. Create KPIs: Average Wait Time (formula), Bed Occupancy % (PivotTable), Patients per Day (PivotChart line).
3. Use conditional formatting: Red/Yellow/Green rules on wait times.
4. Add a Timeline slicer to filter by date range.
5. Create a map (Bing Maps add-in or 3D Maps) showing patient origins.
6. Use camera tool to arrange charts into a clean, print-friendly layout.

## Concept Example 1
**Linking Slicers to Multiple PivotTables**
- **Input:** Three PivotTables on one dashboard sheet — Sales by Region, Top Products, Monthly Trend.
- **Action:** Right-click a Slicer > Report Connections > check all three PivotTables.
- **Output:** Selecting "West" in the slicer filters all three PivotTables simultaneously.
- **Explanation:** A single slicer can control multiple PivotTables if they share the same source data field. This creates a unified interactive dashboard.

## Concept Example 2
**Camera Tool for Flexible Dashboard Layout**
- **Input:** A PivotChart and a KPI card that need to be arranged in a non-grid layout.
- **Action:** Select cells/charts > Copy > click a blank area > Paste as Linked Picture (Camera tool from Quick Access Toolbar).
- **Output:** A live picture of the chart that updates when source data changes.
- **Explanation:** The Camera tool creates a floating, resizable image of any range. Useful for creating professional dashboard layouts where standard alignment isn't enough.

## Common Mistakes
- Putting raw data and dashboard on the same sheet.
- Not locking dashboard cells (users accidentally break formulas).
- Using 20 chart types — stick to 3-4 consistent types.
- Overloading the dashboard with too many metrics.
- Forgetting to refresh PivotTables and data connections.

## How Interviewers Ask This
- "Walk me through how you would design a sales dashboard."
- "What chart types work best for dashboards?"
- "How do you make a dashboard interactive?"
- "How do you ensure dashboard performance with large data?"

## How To Impress The Interviewer
- Discuss Power BI integration for enterprise-level dashboards (Excel as data source).
- Mention dynamic named ranges with OFFSET/COUNTA for self-expanding charts.
- Explain how to use camera tool, form controls (scroll bars, option buttons), and macros together.
- Show that you separate data, calculation, and presentation into different sheets.
- Talk about using Excel Online / SharePoint for shared dashboards with real-time collaboration.

## Most Asked Questions

**1. What makes a good Excel dashboard?**
Clear layout, minimal clutter, consistent color scheme, interactive elements (slicers/timelines), real-time data connection, key metrics prominently displayed, and mobile-friendly design.

**2. What chart types are best for dashboards?**
Line charts for trends over time. Bar/column charts for comparisons. Pie/donut charts for proportions (use sparingly). Combo charts for mixed data types. Sparklines for inline mini-trends. KPI cards for key numbers.

**3. How do you make a dashboard interactive?**
Use Slicers (visual filters), Timelines (date filters), form controls (dropdowns, scroll bars), and hyperlinks to navigate between sections. Connect slicers to multiple PivotTables/charts for a unified filter experience.

**4. How do you handle dashboard performance with large datasets?**
(1) Use Power Pivot / Data Model instead of worksheet ranges. (2) Limit volatile functions (INDIRECT, OFFSET, TODAY, NOW). (3) Set PivotTables to manual refresh. (4) Use calculated columns instead of array formulas. (5) Turn off automatic calculations during updates.

**5. What is the difference between a static and dynamic dashboard?**
Static dashboards don't change until the file is manually updated or re-opened. Dynamic dashboards connect to live data sources (databases, APIs, Power BI datasets) and refresh automatically or with a button click.

**6. How do you create KPI cards in Excel?**
Use a cell containing a GETPIVOTDATA or SUMIFS formula for the metric value. Below it, add a comparison (target, prior period) with conditional formatting arrows. Combine with sparklines for trend. Style with borders and background colors.

**7. What are Sparklines and how are they used in dashboards?**
Sparklines are mini-charts inside a single cell. Insert > Sparklines. They show trends compactly (line for trends, column for comparisons, win/loss for binary). Great for dashboards with many rows of KPIs.

**8. How do you use conditional formatting effectively in dashboards?**
(1) Data bars for ranking (like bar charts in cells). (2) Color scales for heat maps. (3) Icon sets for status (green check / yellow ! / red X). (4) Formula-based rules for custom logic. Use carefully — too much is distracting.

**9. What are form controls and how are they used?**
Developer > Insert > Form Controls. Include combo boxes (dropdowns), scroll bars, option buttons, check boxes. Link to a cell that returns the selected value. Use with INDEX-MATCH or CHOOSE to dynamically change chart data.

**10. How do you protect a dashboard without breaking functionality?**
(1) Protect the sheet but allow formatting and sorting. (2) Lock data/calculation sheets with passwords. (3) Hide sheets containing raw data. (4) Use "Very Hidden" (VBA: xlSheetVeryHidden) for sensitive sheets. (5) Leave only slicers and input cells unlocked.

**11. How do you create a drill-down dashboard?**
Use hyperlinks or macros to navigate between summary and detail sheets. Or use PivotTable drill-down (double-click values). Show summary on main dashboard, allow users to click a region to see that region's detail.

**12. What is the camera tool and how do you use it?**
The Camera tool creates a live linked picture of a range. Add to Quick Access Toolbar (File > Options > Quick Access Toolbar > All Commands > Camera). Select range, click Camera, click where you want the image. The image updates automatically.

**13. How do you design dashboards for mobile viewing?**
(1) Use a single-column layout with large fonts. (2) Minimize detail — show only top 3 KPIs. (3) Use large touch-friendly slicer buttons. (4) Avoid tiny charts. (5) Test in Excel mobile app.

**14. What is the best way to handle multiple data sources in a dashboard?**
Use Power Query to combine them into a single data model. Create relationships in Power Pivot. Base all PivotTables and charts on the Data Model. This keeps the dashboard efficient and data sources centralized.

**15. How do you create a dynamic chart range?**
Create a named range with OFFSET and COUNTA: `=OFFSET(Sheet1!$A$2,0,0,COUNTA(Sheet1!$A:$A)-1,1)`. Use this named range as the chart's data source. The chart automatically expands/contracts as data is added or removed.

**16. How do you add a search box to a dashboard?**
Use a cell as a search input. Create a helper column with `=ISNUMBER(SEARCH($SearchCell, A2))`. Filter data based on this. Or use a combo box form control linked to INDEX-FILTER.

**17. What is the best way to layout a dashboard?**
(1) Top row: Title and key global filters (slicers). (2) Below: 3-5 KPI cards in a row. (3) Middle: Main chart (largest element). (4) Bottom: Supporting charts in 2-3 columns. (5) Right sidebar: Filters. Keep critical KPIs above the fold.

**18. How do you use Excel tables for dashboard data?**
Convert data to Excel Tables (Ctrl+T). Tables auto-expand and provide structured references. PivotTables and charts based on tables automatically include new data. Use Table[Column] references in formulas.

**19. How do you create a waterfall chart in Excel?**
Insert > Waterfall Chart. Useful for showing how a starting value increases/decreases to an ending value (e.g., net income). Excel 2016+ has native waterfall. Older versions need stacked column with invisible series.

**20. How do you share a dashboard without revealing source data?**
(1) Copy the dashboard sheet to a new workbook. (2) Paste Values to remove formulas. (3) Remove hidden data sheets. (4) Save as Excel Binary Workbook or PDF for distribution. (5) Use Power BI for controlled access.

**21. What are macros and how do they enhance dashboards?**
Macros (VBA) automate tasks: refresh all data with one click, apply consistent formatting, navigate between sheets, export charts to PowerPoint, or send emails with dashboard snapshots.

**22. How do you create a what-if analysis dashboard?**
Use Data Tables (What-If Analysis > Data Table) with input cells for variables. Create scenario manager. Show outcomes in charts that update when input cells change. Combine with form controls (scroll bars) for interactive inputs.

**23. What are best practices for dashboard colors?**
(1) Use company brand colors. (2) Limit to 3-5 colors max. (3) Use high contrast for important elements. (4) Color-blind friendly palettes (avoid red-green alone). (5) Consistent meaning (red = bad, green = good throughout).

**24. How do you create a heat map in Excel?**
Apply conditional formatting > Color Scales to a matrix of values. The gradient (e.g., green to red) shows relative magnitude. Useful for showing patterns across categories and time periods.

**25. How do you handle errors in dashboard formulas?**
Use IFERROR, IFNA, or ISERROR in all formulas that reference external data. Show "N/A" or 0 instead of #REF! or #N/A. This keeps the dashboard looking professional even when data is missing.
