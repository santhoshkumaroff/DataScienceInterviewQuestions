# Power BI Interview Questions

## What is it?
This file covers 30+ interview questions spanning Power BI architecture, DAX, data modeling, Power Query, visualization best practices, performance optimization, and deployment. These are the most commonly asked questions in Power BI developer and analyst interviews.

## Why do we use it?
Power BI is the #1 business intelligence tool in the market. Companies adopt it for self-service analytics. Interviewers test both conceptual understanding (star schema, filter context) and practical skills (DAX formulas, report design, deployment).

## Real-Time Example 1
**Interview Scenario:** The interviewer gives you sales data with customers, products, and orders. They ask you to design a report showing top products by region with YoY growth.
**Solution Outline:**
1. Model: Star schema — Sales (fact), Products (dim), Customers (dim), Date (dim).
2. DAX measures: Total Sales = SUM(Sales[Amount]), Sales PY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(Date[Date])), YoY Growth = DIVIDE([Total Sales]-[Sales PY], [Sales PY]).
3. Visuals: Matrix with Products in rows, Region in columns, YoY Growth as values. A bar chart for Top 10 products. Slicers for date and category.

## Real-Time Example 2
**Interview Scenario:** You're asked to optimize a slow Power BI report that takes 5 minutes to refresh.
**Solution Approach:**
1. Check the data model — reduce number of columns, remove unnecessary tables.
2. Move transformations to Power Query (reduce DAX calculated columns).
3. Check query folding — ensure filters push to the source database.
4. Reduce cardinality of columns used in relationships (use integers instead of text keys).
5. Change some visuals to not use high-cardinality fields.
6. Schedule refresh during off-hours and use incremental refresh.

## Most Asked Questions

**1. What are the main components of Power BI?**
Power BI Desktop (authoring), Power BI Service (cloud sharing), Power BI Mobile (apps), Power BI Report Builder (paginated reports), Power BI Report Server (on-premises), and Power BI Embedded (custom apps).

**2. Explain the difference between Power BI Desktop and Power BI Service.**
Desktop is the free authoring tool where you create reports and data models. Service is the cloud platform (app.powerbi.com) where you publish, share, schedule refresh, create dashboards, and manage security. Desktop = build. Service = distribute.

**3. What is a measure in Power BI?**
A measure is a DAX formula used for aggregation that changes based on filter context. It's evaluated at query time and not stored. Example: `Total Sales = SUM(Sales[Amount])`. Measures are the primary way to create calculations in Power BI.

**4. What is the difference between calculated columns and measures?**
Calculated columns are computed row-by-row during refresh and stored. Measures are computed at query time and respond to filter context. Use columns for row-level operations, measures for aggregations. Measures are preferred for most scenarios.

**5. How does filter context work in Power BI?**
Filter context is the set of filters applied when a measure is evaluated. Filters come from: slicers, visual level filters (rows, columns), page-level filters, report-level filters, cross-filtering between visuals, and DAX filter functions (CALCULATE, FILTER).

**6. What is a DAX measure vs a calculated table?**
A calculated table is created with DAX and stored in the model (like a regular table). Example: `Date Table = CALENDAR(DATE(2020,1,1), DATE(2025,12,31))`. A measure is dynamic. Calculated tables increase model size but can simplify certain patterns.

**7. Explain the difference between Import, DirectQuery, and Composite models.**
Import: data copied into Power BI, fast queries, requires scheduled refresh, max ~1GB per dataset. DirectQuery: queries source database live, real-time, but slower. Composite: mix of Import and DirectQuery tables in one model.

**8. How do you schedule data refresh in Power BI Service?**
In Power BI Service, go to dataset settings > Scheduled Refresh. Toggle on, set frequency (daily, weekly), and times. Need an on-premises data gateway for on-premises data sources. The dataset must be in a shared capacity or Premium.

**9. What is Row-Level Security (RLS) in Power BI?**
RLS restricts data access at the row level based on user roles. Define roles in Power BI Desktop (Manage Roles) with DAX filters. Example: `Salesperson = UserPrincipalName()`. After publishing, assign users to roles in the Service using Manage Permissions.

**10. How do you create a parameter in Power BI?**
Parameters are created in Power Query (Manage Parameters) for dynamic queries. They can control file paths, filter values, or connection strings. DAX parameters (What-If parameters) create slicer-adjustable values for scenario analysis.

**11. What is a composite model in Power BI?**
A composite model combines Import and DirectQuery tables in a single dataset. It also enables many-to-many relationships and calculations across sources. Requires Power BI Premium or Premium Per User. More flexible but needs careful performance management.

**12. How do you optimize Power BI performance?**
(1) Reduce data size — remove unused columns, filter early. (2) Star schema modeling — avoid flat tables. (3) Avoid complex DAX with FILTER over large tables. (4) Reduce cardinality of columns in relationships. (5) Turn off cross-highlighting between unnecessary visuals. (6) Use Performance Analyzer to find slow visuals.

**13. What is the Performance Analyzer in Power BI?**
View > Performance Analyzer. It records the time taken by each visual and DAX query. Helps identify slow charts, excessive query counts, and inefficient DAX. Start recording, interact with the report, review timings, and optimize accordingly.

**14. How do you create a drill-through page in Power BI?**
Create a new report page. Add the drill-through field(s) to the Drill-through filter pane (e.g., Product Category). Set "Enable drill-through" to On. On another page, right-click a data point > Drill-through > target page. The target page filters based on the selected value.

**15. What are bookmarks and how are they used?**
Bookmarks capture the current state of a report page (filters, slicers, visual visibility). Use them for: show/hide elements, custom navigation, scenario toggling, and presentation mode. Combine with buttons for interactive behaviors.

**16. What is a tooltip page?**
A tooltip page is a custom page (small size) that appears when you hover over a data point. Create a page, set page size to Tooltip (320x240), add visuals. On the target visual, set Tooltip to the custom page. Great for drill-over details.

**17. How do you create a hierarchy in Power BI?**
In the Fields pane, right-click a field > New Hierarchy. Add child fields by dragging. Example: Category > SubCategory > Product Name. Hierarchies allow users to drill up/down in visuals. Works in matrix, bar charts, and decomposition trees.

**18. What is the difference between a KPI and a metric in Power BI?**
A KPI visual shows a value against a target (with status indicator). A metric (Power BI Goals) is a higher-level feature in the Service for tracking objectives over time with check-ins and notes. KPI = visual. Metric = Service feature.

**19. How do you implement dynamic formatting for measures?**
Use calculation groups (Tabular Editor) or SWITCH with FORMAT. Example: `Dynamic Format = SWITCH(SELECTEDVALUE(Format[Format]), "Currency", FORMAT([Value], "$#,##0"), "Percentage", FORMAT([Value], "0.0%"))`.

**20. What is a gateway and why is it needed?**
An on-premises data gateway is software installed on a server that bridges Power BI Service with on-premises data sources (SQL Server, files, etc.). Required for scheduled refresh of import data and DirectQuery to on-premises databases.

**21. How do you share a Power BI report without giving edit access?**
Publish to Power BI Service. Create an app (workspace > Create App) and share the app link. Or use "Share" from the report page and set permission to "View only". Sharing requires Premium or Pro license for the viewer.

**22. What are Power BI Apps?**
Apps are collections of dashboards and reports packaged together for consumers. Created from a workspace. Provide a simplified, focused experience. Consumers don't see the workspace structure — they see the curated app with its own navigation.

**23. How do you handle what-if analysis in Power BI?**
Create a What-If parameter (Modeling > What-If Parameter). It generates a calculated table and a measure. Use the measure in calculations: `Sales Adjusted = [Total Sales] * (1 + Parameter[Parameter Value])`. Slicer lets users adjust the parameter.

**24. What is the difference between a paginated report and a Power BI report?**
Paginated reports (Report Builder) are designed for printing and pixel-perfect layouts (invoices, statements). Power BI reports are interactive, visual-first, and optimized for on-screen analysis. Paginated reports use SQL Server Reporting Services.

**25. How do you implement multi-language reports in Power BI?**
Create a translation table in Power Query (Language, FieldName, Translation). Use a disconnected slicer for language selection. Write DAX measures that return the translation based on the selected language using SELECTEDVALUE.

**26. What is a calculated table in DAX?**
A table created with DAX: `Top Products = TOPN(10, Products, Products[Sales])`. Calculated tables are evaluated during refresh and stored. Useful for static reference tables, disconnected parameter tables, and date dimension tables.

**27. How do you set up cross-report drill-through?**
Requires Power BI Premium or Premium Per User. Create a connection from the source report to the target dataset. Use a measure like `Drillthrough = SELECTEDVALUE(Table[Column])`. Configure drill-through in the target report.

**28. What are the limitations of Power BI?**
(1) Import mode dataset size limit (1GB default, 400GB with Premium). (2) No native undo button (only Ctrl+Z). (3) Limited custom visualizations without marketplace. (4) No real-time streaming without additional setup. (5) DAX learning curve.

**29. How do you manage version control for Power BI reports?**
Power BI Desktop doesn't have native version control. Options: (1) Save .pbix files in Git (not diff-friendly). (2) Use Power BI Project (.pbip) format for text-based source control. (3) Use ALM Toolkit for comparing models. (4) Manually archive versions.

**30. What is XMLA endpoint and when would you use it?**
XMLA endpoint allows programmatic management of Power BI datasets (creation, update, refresh). Used for CI/CD pipelines, automated deployments, and third-party tool integration. Available for Premium and Premium Per User workspaces.
