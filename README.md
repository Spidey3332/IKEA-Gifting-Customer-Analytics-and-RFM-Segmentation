# 🛋️ IKEA Gifting: Customer Analytics & RFM Segmentation

**An end-to-end data analytics project using SQL, Python, and Power BI**

![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?logo=microsoftsqlserver&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?logo=powerbi&logoColor=black)
![pandas](https://img.shields.io/badge/pandas-150458?logo=pandas&logoColor=white)

> ⚠️ **Disclaimer:** "IKEA Gifting" is a **fictional company name** created for this educational portfolio project. It is not affiliated with, endorsed by, or representative of the real IKEA corporation. All figures come from a public, anonymized dataset and do not reflect any real company's performance. The IKEA-style branding is used for design and storytelling only.

---

## 📑 Table of Contents

1. [Project Overview](#-project-overview)
2. [Business Problem](#-business-problem)
3. [Business Questions](#-business-questions-answered)
4. [Key Insights](#-key-insights)
5. [Data Source](#-data-source)
6. [Tech Stack](#-tech-stack)
7. [Project Workflow](#-project-workflow)
8. [Dashboard Structure](#-dashboard-structure)
9. [Code Samples](#-code-samples)
10. [How to Reproduce](#-how-to-reproduce)

---

## 📌 Project Overview

This project simulates a real-world analytics engagement for a fictional UK-based gifting and home décor e-commerce brand. The goal was to take **raw, messy transactional data** all the way to a **decision-ready Power BI dashboard**, following the workflow a data analyst would use inside a real company:

**Define the problem → Clean the data in SQL → Build the logic in Python → Deliver insights in Power BI**

It was built as part of a public tutorial series that teaches SQL, Python, and Power BI as **one connected skillset** rather than three isolated topics.

### 🧠 My Thinking Process

- **Question first, data second.** Most portfolio projects start with a dataset and work backward. I wrote a **Business Requirements Document** before touching any data, so every technical step traces back to a defined business question.
- **A less common dataset.** I chose *Online Retail II* instead of the heavily used Olist dataset.
- **Real data cleaning.** I cleaned the raw data myself instead of using a pre-cleaned version, so the project covers real problems: mixed date formats, invalid row types, duplicate transactions, and missing customer IDs.

---

## 🎯 Business Problem

The business wants to **identify its most valuable customers** and **reduce churn** among customers at risk of leaving. Leadership has no structured view of customer value, so retention efforts are reactive instead of targeted.

## ❓ Business Questions Answered

1. Who are the most valuable customers, and how much revenue do they contribute?
2. Which customers have gone quiet and are at risk of churning?
3. How do customer value and retention trend by month, quarter, and year?
4. How do customer value and churn differ across countries?
5. What concrete actions should the business take for each customer segment?

---

## 💡 Key Insights

| Metric | Value |
|---|---|
| Total customers | **5,881** |
| Total revenue | **~$17M** |
| Overall churn rate | **50.9%** (inactive for 90+ days) |
| Churn trend | Peaked at **56.3% in 2010**, fell to **31.5% in 2011** |

<!-- TODO: add the segment-level insight here after re-running the RFM scoring (see note in chat about the R_Score labels) -->

---

## 📦 Data Source

| | |
|---|---|
| **Dataset** | [Online Retail II](https://archive.ics.uci.edu/dataset/502/online+retail+ii), UCI Machine Learning Repository |
| **Donated by** | Daqing Chen |
| **Description** | Real, anonymized transactions from a UK-based, non-store online retailer selling all-occasion gift ware |
| **Period** | December 2009 to December 2011 |
| **Size** | 1,067,371 raw rows across two sheets |
| **License** | Creative Commons Attribution 4.0 International (CC BY 4.0) |

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **SQL Server / SSMS** | Data import, cleaning, deduplication, and exploration on the full 1M+ row dataset |
| **Python (pandas)** | RFM calculation, customer scoring, segmentation, and churn flagging |
| **Jupyter Notebook** | Interactive development environment for the Python analysis |
| **Power BI Desktop** | Interactive dashboard with slicers, bookmarks, and page navigation |
| **DAX** | Measures for customer counts, revenue totals, and average customer value |

### Core Concepts Demonstrated

- **RFM Analysis:** scoring customers on Recency, Frequency, and Monetary value on a comparable scale
- **Customer Segmentation:** turning RFM scores into business groups (Champions, Loyal, At Risk, New, Need Attention, Lost)
- **Churn Flagging:** applying a fixed 90-day inactivity threshold
- **Data Quality Handling:** fixing mixed date formats, invalid type conversions, and duplicates using SQL window functions
- **Dimensional Modeling for BI:** one unified table shared by all report pages, with no manual relationships
- **Interactive BI Design:** slicers, bookmark-based toggles, and multi-page navigation

---

## 🔄 Project Workflow

| Step | Stage | What I did |
|---|---|---|
| **1** | **Business Requirements** | Defined scope, business questions, key definitions, and success criteria before any data work |
| **2** | **SQL: Clean & Explore** | Imported both raw sheets, fixed data types, resolved a mixed date format affecting over half the dataset, removed **32,907 exact duplicate rows**, and validated row counts at every step against the published UCI total |
| **3** | **Python: RFM Segmentation** | Calculated Recency, Frequency, and Monetary value per customer, scored each on a 1 to 5 scale, assigned all **5,881 customers** to one of six segments, and added a 90-day churn flag |
| **4** | **Power BI: Dashboard** | Merged segmentation results back onto transaction-level data to create one analysis-ready table, then built the report |

---

## 📊 Dashboard Structure

### Page 1: Business Overview
- Headline KPIs: **Total Customers, Total Revenue, Churn %, Avg Customer Value**
- **Customer / Revenue toggle** (bookmark navigator)
- Segment breakdowns and month / quarter / year trends
- Slicers: Month, Quarter, Year, Country, Segment, Churn status

### Page 2: RFM Analysis
- **Segment Performance Summary** table: customers, revenue, and average R / F / M scores per segment
- Average customer value by segment
- Churn % trended by month, quarter, and year
- **Segment / Country toggle**

> 📸 *Add dashboard screenshots here, for example `![Business Overview](images/overview.png)`*

---

## 💻 Code Samples

### SQL: Finding missing and duplicate data

```sql
USE ikka_sales_analysis;

-- Check 1: Missing Customer_ID
SELECT COUNT(*) AS failed_csid
FROM combine_sales_data
WHERE Customer_ID IS NULL;

-- Check 2: Negative and zero quantity
SELECT COUNT(*) AS negative_quantity
FROM combine_sales_data
WHERE Quantity <= 0;

-- Check 3: Missing description
SELECT COUNT(*) AS missing_des
FROM combine_sales_data
WHERE Description IS NULL;

-- Check 4: Exact duplicate rows
SELECT Invoice, StockCode, Description, Quantity,
       InvoiceDate, Price, Customer_ID, Country,
       COUNT(*) AS exact_dup_count
FROM combine_sales_data
GROUP BY Invoice, StockCode, Description, Quantity,
         InvoiceDate, Price, Customer_ID, Country
HAVING COUNT(*) > 1;
```

### Python: RFM scoring

```python
# Lower recency (days since last purchase) = better, so it gets the HIGHER score
rfm["R_Score"] = pd.qcut(rfm["Recency"], 5, labels=[5, 4, 3, 2, 1]).astype(int)
rfm["F_Score"] = pd.qcut(rfm["Frequency"].rank(method="first"), 5, labels=[1, 2, 3, 4, 5]).astype(int)
rfm["M_Score"] = pd.qcut(rfm["Monetary"], 5, labels=[1, 2, 3, 4, 5]).astype(int)
```

---

## 🔁 How to Reproduce

1. Download the **Online Retail II** dataset from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii).
2. Run the SQL scripts in `01_sql/` in order to recreate the cleaned `RetailCleaned` table.
3. Export `RetailCleaned` to CSV, then run the notebook in `02_python/` to generate the segmented dataset.
4. Open the Power BI file in `03_powerbi/` and point it to the exported Excel file, then refresh.

---

## 👤 Author

**Shivam Sharma**
🔗 [LinkedIn](#) · 🔗 [GitHub](#)

⭐ If you found this project helpful, please give it a star!
