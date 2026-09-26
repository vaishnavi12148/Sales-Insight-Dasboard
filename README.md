# 📊 Sales Insight Dashboard

An interactive sales analytics dashboard built in **Microsoft Power BI** to analyze sales performance across time, markets, zones, products, and customers.

**Project note:** This project was built as a learning/portfolio project following and extending the structure of a Codebasics Power BI beginner tutorial. The dashboard, data preparation, cleaning steps, currency normalization, measures, and documentation shown here reflect my implementation and learning work.
---

## 🖼️ Dashboard Preview

![Sales Insight Dashboard](screenshots/dashboard.png)

---

## ⚡ At a Glance

| Metric                       |      Value |
| ---------------------------- | ---------: |
| Total Transactions           |    **52K** |
| Total Sales                  |  **₹336M** |
| Avg. Order Value             | **₹6.49K** |
| YoY Growth (selected period) | **-18.8%** |

> **Note:** Figures reflect the currently selected year/zone filter and update dynamically as the report is interacted with.

---
## 🎯 Why This Project
 
Raw transactional data rarely arrives ready to analyze — mixed currencies, blank categories, and invalid values all had to be resolved before a single chart could be trusted. This project's real value isn't the visuals alone; it's the **cleaning, validation, and modeling work behind them** that makes those visuals trustworthy.
 
The dashboard answers the questions a sales team actually asks:
- What's our total sales, and how many transactions drove it?
- How is performance trending year over year, and month by month?
- Which markets and zones are winning — or falling behind?
- Who are our top customers, and where's the average order value heading?
---
 
## 🧩 Data Model
 
A star-schema-style structure: one fact table, four supporting dimensions.
 
| Table | Role |
|---|---|
| `sales transactions` | Fact table — transaction-level sales data |
| `sales customers` | Customer names, codes, and types |
| `sales products` | Product codes and types |
| `sales markets` | Market codes, names, and zones |
| `sales date` | Calendar table for time intelligence |
 
Relationships run through `product_code`, `customer_code`, `market_code`, and the date table — so year and zone filters propagate consistently across every visual.
 
---
 
## 🧹 Data Cleaning & Transformation
 
Four real issues had to be resolved before the numbers could be trusted:
 
**1. Blank zones removed** — so the dashboard never shows an "undefined" category.
 
**2. Invalid sales amounts filtered** — `sales_amount` values of `0` or `-1` were excluded from analysis.
 
**3. Currency values standardized** — values that *looked* identical (`USD` vs `USD` with a hidden character) were actually different strings:
```powerquery
Text.Trim(...)   // removes stray whitespace
Text.Clean(...)  // removes non-printable characters
```
 
**4. Currency normalized to a single basis** — a `norm_sales_amount` column converts every USD transaction to INR (× 95.39), leaving INR transactions unchanged, then casts the result to a numeric type:
```powerquery
Table.TransformColumnTypes(
    #"Added norm_sales_amount column",
    {{"norm_sales_amount", type number}}
)
```
 
---
 
## 🧮 DAX Measures
 
```DAX
Total Sales = SUM('sales transactions'[norm_sales_amount])
 
Total Transactions = COUNTROWS('sales transactions')
 
Total Sales PY =
CALCULATE([Total Sales], SAMEPERIODLASTYEAR('sales date'[date]))
 
Avg Order Value = DIVIDE([Total Sales], [Total Transactions])
 
YoY Growth = DIVIDE([Total Sales] - [Total Sales PY], [Total Sales PY])
```
 
---
 
## 🖥️ Dashboard Features
 
**KPI Cards** — Total Transactions · Total Sales · Avg Order Value · YoY Growth
**Interactive Filters** — Year · Zone
**Visual Analysis** — Sales by Market · Monthly Sales Trend · Sales by Zone · Top 5 Customers
 
Every visual responds live to the selected year and zone.
 
---
 
## ✅ Validation
 
Source data was cross-checked directly in **MySQL Workbench** — transaction counts, zero/negative sales amounts, and aggregate totals were independently verified against the Power BI output before the dashboard was trusted for analysis.
 
---
 
## 🔄 Project Workflow
 
```
Raw Sales Data
      ↓
MySQL Source → Power BI Data Loading
      ↓
Data Cleaning
      ├── Remove blank zones
      ├── Filter invalid sales values
      ├── Trim & clean currency strings
      ↓
Data Transformation
      ├── Currency normalization (USD → INR)
      └── Data type conversion
      ↓
Data Modeling (star schema)
      ↓
DAX Measures
      ↓
Interactive Dashboard → Validation
```
 
---
 
## Repository Structure
 
```
sales-insight-dashboard/
├── README.md
├── powerbi/
│   └── Sales_Insight_Dashboard.pbix
├── screenshots/
│   └── dashboard.png
└── docs/
    └── data_cleaning_and_measures.md
```
 
---
 
## What I'd Add Next
 
- Sales quantity and profit as additional KPIs
- Drill-through pages for individual markets and customers
- Dynamic, filter-aware KPI titles
- Deeper time-intelligence measures (rolling averages, MTD/QTD)
- Scheduled/automated data refresh
---
 
*Built as a hands-on extension of a Codebasics Power BI tutorial — the data preparation, cleaning logic, currency normalization, and measures reflect my own implementation and learning process.*
 