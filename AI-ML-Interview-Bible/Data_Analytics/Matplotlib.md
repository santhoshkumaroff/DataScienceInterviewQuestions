# Matplotlib

## What is it?
Matplotlib is the foundational visualization library in Python. It creates static, animated, and interactive plots. The core object is the Figure (the overall canvas) containing Axes (individual plots). It supports line plots, bar charts, histograms, scatter plots, and extensive customization.

## Why do we use it?
Every data analysis project needs visualizations. Matplotlib is the most mature and customizable Python plotting library. It integrates with Pandas, NumPy, and Jupyter notebooks. Understanding Matplotlib is essential before learning Seaborn, Plotly, or any other library.

## Real-Time Example 1
**Business Scenario:** A sales team needs a weekly revenue trend chart for their quarterly business review.
**Step-by-Step:**
1. `import matplotlib.pyplot as plt`
2. `plt.figure(figsize=(10, 5))` — set canvas size.
3. `plt.plot(dates, revenue, marker='o', linestyle='-', color='green', label='Revenue')`
4. `plt.plot(dates, target, linestyle='--', color='red', label='Target')`
5. `plt.title('Weekly Revenue Trend - Q1 2024', fontsize=14)`
6. `plt.xlabel('Week')`, `plt.ylabel('Revenue ($)')`
7. `plt.legend()`, `plt.grid(True, alpha=0.3)`
8. `plt.xticks(rotation=45)` for readable date labels
9. `plt.tight_layout()`, `plt.show()`

## Real-Time Example 2
**Industry:** Healthcare
**Use Case:** A hospital wants to visualize patient age distribution and BMI correlation.
1. Create a figure with 2 subplots (1 row, 2 columns).
2. Subplot 1: Histogram of Age — `plt.hist(ages, bins=20, edgecolor='black', alpha=0.7)`.
3. Subplot 2: Scatter plot of Age vs BMI — `plt.scatter(ages, bmis, alpha=0.5, s=30)`.
4. Add a trend line: `plt.plot(ages, np.poly1d(np.polyfit(ages, bmis, 1))(ages), color='red')`.
5. Customize: titles, axis labels, grid.
6. Save: `plt.savefig('patient_analysis.png', dpi=300, bbox_inches='tight')`.

## Concept Example 1
**Creating Subplots**
- **Code:**
  ```python
  fig, axes = plt.subplots(2, 2, figsize=(10, 8))
  axes[0,0].plot(x, y1)
  axes[0,1].scatter(x, y2)
  axes[1,0].bar(categories, values)
  axes[1,1].hist(data, bins=15)
  ```
- **Output:** A 2×2 grid of four different plot types.
- **Explanation:** `plt.subplots(rows, cols)` returns a Figure and an array of Axes objects. Each axes can be customized independently. Common for exploratory analysis.

## Concept Example 2
**Customizing with rcParams**
- **Code:** `plt.rcParams['figure.figsize'] = (12, 6)` and `plt.rcParams['font.size'] = 12`
- **Explanation:** rcParams set global defaults for all plots. Useful for consistent styling across a report. Can be reset with `plt.rcdefaults()`. Common params: figure size, font family, line width, axes grid, color cycle.

## Common Mistakes
- Forgetting `plt.show()` in non-Jupyter environments.
- Not calling `plt.tight_layout()` causing label overlap.
- Using `plt.plot()` repeatedly on the same figure without clearing (`plt.clf()`).
- Not specifying `dpi` when saving for publication quality.
- Overcomplicating — trying to do too much in one figure.

## How Interviewers Ask This
- "How do you create a subplot grid in Matplotlib?"
- "How do you save a figure to a file?"
- "Explain how to customize axis ticks and labels."
- "How do you add a legend to a plot?"

## How To Impress The Interviewer
- Use `plt.style.use('seaborn-v0_8')` or custom styles for polished visuals.
- Know object-oriented API (`fig, ax = plt.subplots()`), not just pyplot.
- Use annotations (`ax.annotate()`) to highlight key data points.
- Create animations with `matplotlib.animation` for time series.
- Know how to create inset axes, twin axes, and shared axes.

## Most Asked Questions

**1. What is the difference between the pyplot and object-oriented API?**
Pyplot (`plt.plot()`) is state-based — uses the current figure/axes automatically. Object-oriented (`fig, ax = plt.subplots()`; `ax.plot()`) is explicit and preferred for complex plots. OO API gives more control and is better for subplots and reusability.

**2. How do you create multiple subplots?**
`plt.subplots(2, 3)` creates 2 rows, 3 columns. Access via `axes[row, col]`. `plt.subplot(2, 3, 1)` for individual subplots (1-indexed). `plt.subplot2grid` for irregular grids. `GridSpec` for the most flexibility.

**3. How do you customize axis ticks and labels?**
`ax.set_xticks([1, 2, 3])` sets tick positions. `ax.set_xticklabels(['A', 'B', 'C'])` sets labels. `ax.tick_params(rotation=45)` rotates. `ax.xaxis.set_major_locator(plt.MaxNLocator(5))` controls tick count.

**4. How do you add a legend to a plot?**
`ax.plot(x, y, label='Series Name')` then `ax.legend()`. Customize: `ax.legend(loc='upper left', frameon=True, fontsize=10)`. `loc` options: 'best', 'upper right', 'lower left', 'center', etc.

**5. How do you save a figure?**
`plt.savefig('plot.png', dpi=300, bbox_inches='tight', facecolor='white', transparent=False)`. `bbox_inches='tight'` removes whitespace. Formats: png, pdf, svg, jpg. Save before `plt.show()` because show clears the figure.

**6. How do you create a bar chart?**
`ax.bar(x, height)` for vertical bars. `ax.barh(x, width)` for horizontal. `ax.bar(x, height, color='blue', edgecolor='black')`. For grouped bars: adjust x positions: `x - width/2`, `x + width/2`. Stacked: `bottom` parameter.

**7. How do you create a histogram?**
`ax.hist(data, bins=30, density=False, alpha=0.7, edgecolor='black')`. `bins`: int (count) or array (bin edges). `density=True` normalizes to probability density. `cumulative=True` for cumulative distribution.

**8. How do you create a scatter plot?**
`ax.scatter(x, y, s=50, c='red', alpha=0.5, marker='o')`. `s` = marker size. `c` = color or array for colormap. `marker` options: 'o', 's', '^', 'D', '+', '.'. `cmap` for color mapping: `c=values, cmap='viridis'`.

**9. How do you add text annotations to plots?**
`ax.text(x, y, 'text', fontsize=12, ha='center')` adds text at data coordinates. `ax.annotate('max', xy=(5, 10), xytext=(6, 11), arrowprops=dict(arrowstyle='->'))` adds annotation with arrow.

**10. How do you change figure size?**
`plt.figure(figsize=(10, 6))` for pyplot. `fig = plt.subplots(figsize=(10, 6))` for OO API. `fig.set_size_inches(12, 8)` after creation. `plt.rcParams['figure.figsize'] = [10, 6]` for global.

**11. How do you create a line plot with multiple lines?**
```python
ax.plot(x, y1, label='Series 1', color='blue', linewidth=2)
ax.plot(x, y2, label='Series 2', color='red', linestyle='--')
ax.legend()
```
`linewidth` (or `lw`), `linestyle` (or `ls`: '-', '--', '-.', ':'), `marker` ('o', 's', etc.), `markersize`.

**12. How do you format axes as dates?**
`import matplotlib.dates as mdates`. `ax.xaxis.set_major_formatter(mdates.DateFormatter('%Y-%m-%d'))`. `ax.xaxis.set_major_locator(mdates.MonthLocator())`. Works with datetime objects.

**13. How do you add a secondary y-axis?**
`ax2 = ax.twinx()` — creates a second y-axis sharing the same x-axis. `ax2.plot(x, y2, color='red')`. `ax2.set_ylabel('Second Y')`. Use when two series have different scales.

**14. How do you create a horizontal line or vertical line?**
`ax.axhline(y=0.5, color='gray', linestyle='--', linewidth=1)`. `ax.axvline(x=10, color='red', linestyle=':')`. `ax.axhline(y=mean, label='Mean')`. Useful for reference lines.

**15. How do you set axis limits?**
`ax.set_xlim(0, 100)` sets x-axis range. `ax.set_ylim(-10, 10)`. `ax.set_xlim(auto=True)` resets to data range. `ax.margins(0.1)` adds 10% padding.

**16. How do you create a pie chart?**
`ax.pie(sizes, labels=labels, autopct='%1.1f%%', startangle=90, explode=(0.1, 0.05, 0, 0))`. `autopct` formats percentages. `explode` separates wedges. Limit to 5-6 categories for readability.

**17. How do you create a histogram with a density curve?**
```python
n, bins, _ = ax.hist(data, bins=30, density=True, alpha=0.6)
ax.plot(bins, np.exp(-(bins - mean)**2 / (2 * std**2)) / (std * np.sqrt(2 * np.pi)), 'r-')
```
Or use Seaborn's `histplot` which combines both.

**18. What is the difference between plt.close() and plt.clf()?**
`plt.clf()` clears the current figure (keeps window open). `plt.close()` closes the figure window entirely. `plt.close('all')` closes all figures. Use `clf()` when generating multiple plots in a loop.

**19. How do you create a stacked bar chart?**
```python
ax.bar(x, values1, label='Category A')
ax.bar(x, values2, bottom=values1, label='Category B')
ax.legend()
```
The `bottom` parameter stacks bars. Works for any number of categories.

**20. How do you add error bars to a plot?**
`ax.errorbar(x, y, yerr=errors, fmt='o', capsize=5, capthick=2)`. `yerr` can be scalar (same for all) or array. `fmt='o'` for markers only (no line). `xerr` for horizontal error bars.

**21. How do you fill between lines?**
`ax.fill_between(x, y1, y2, alpha=0.3, color='gray')` fills area between y1 and y2. `ax.fill_between(x, y, where=y > threshold, color='green', alpha=0.3)` for conditional fill.

**22. How do you create a contour plot?**
`plt.contour(X, Y, Z, levels=20, cmap='viridis')` for lines. `plt.contourf(X, Y, Z, levels=20, cmap='viridis')` for filled contours. X, Y from `np.meshgrid`. Z is the height function.

**23. How do you set colors and colormaps?**
Colors: string 'red', hex '#FF0000', RGB tuple (1,0,0), color names. Colormaps: `cmap='viridis'`, 'plasma', 'inferno', 'magma', 'coolwarm', 'RdBu'. Listed: `plt.get_cmap('viridis', 5)` for discrete colors.

**24. How do you log scale axes?**
`ax.set_xscale('log')`, `ax.set_yscale('log')`. Common for financial data, population sizes, or any data spanning multiple orders of magnitude. `ax.loglog(x, y)` for both axes.

**25. How do you create a box plot?**
`ax.boxplot(data, labels=labels, patch_artist=True)`. `patch_artist=True` fills boxes. `notch=True` for notched boxes. `showfliers=True` to show outliers. `whis=1.5` controls whisker length (in IQR).
