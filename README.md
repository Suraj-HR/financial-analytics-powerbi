# Financial Analytics Dashboard (Power BI)

## Overview

This project focuses on analyzing financial performance across products, regions, and channels using Power BI.

The goal was to move beyond raw numbers and build a dashboard that clearly answers:

* Where is revenue coming from?
* Which products and regions drive profit?
* Are we dependent on a few key contributors?

The final output is a 3-page interactive dashboard designed for business-level decision making.

---

## Tools & Technologies

* Power BI → Data modeling and dashboard development
* DAX → KPI calculations and business logic
* Power Query → Data cleaning and transformation
* SQL Server → Data cleaning, transformation, and validation

---

## Data Preparation

* Cleaned missing and inconsistent values
* Standardized fields (State/UT, Category, SKU)
* Built a structured data model (Fact + Dimension tables)
* Handled duplicate and ambiguous entries (State vs UT)

---

## Key KPIs

* Total Revenue
* Total Profit
* Profit Margin %
* Total Orders / Quantity
* Average Revenue per SKU
* Average Profit per SKU

---

## Dashboard Structure

### 1. Financial Overview

* Revenue and profit trend over time
* Category-level revenue distribution
* Revenue vs profit comparison

**Insight:** Revenue peaked in April and declined in the following months
**Insight:** "Set" category contributes nearly half of total revenue

---

### 2. Product Performance Analysis

* Top SKUs by revenue and quantity
* Revenue vs profit scatter analysis
* Size-wise revenue distribution

**Insight:** A small number of SKUs drive the majority of revenue
**Insight:** High revenue does not always translate to high profit
**Insight:** Mid-range SKUs show growth opportunity

---

### 3. Regional Analysis

* Revenue distribution across States/UT
* State-level performance table (Revenue, Profit, Orders)
* Top 3 states contribution

**Insight:** Revenue is concentrated in top states, led by Maharashtra
**Insight:** Top 3 states contribute ~39% of total revenue
**Insight:** Revenue, profit, and order trends follow a similar pattern across regions

---

## Key Business Insights

* Revenue is not evenly distributed — a few states and SKUs dominate
* Profitability varies despite similar revenue levels
* Category concentration suggests dependency risk
* Regional performance consistency indicates strong market alignment

---

## What I Focused On

* Keeping the dashboard clean and structured (no clutter)
* Avoiding redundant visuals across pages
* Focusing on business storytelling instead of just charts
* Balancing design with readability and insights

---

## Files Included

* financial_analytics_dashboard.pbix → Power BI dashboard file
* page1_financial_overview.png → Financial Overview
* page2_product_analysis.png → Product Analysis
* page3_regional_analysis.png → Regional Analysis

---

## Additional Documentation

* Data Cleaning & Validation → [View details](data-cleaning.md)
* SQL Queries → [View queries](sql-queries.sql)
* Detailed Insights → [View insights](insights.md)

---

## What I Learned

* Handling inconsistent real-world data (dates, missing values)
* Structuring dashboards to avoid redundancy
* Connecting SQL logic with Power BI visuals
* Focusing on business questions instead of just metrics

---

## Outcome

This project demonstrates:

* End-to-end analytics workflow (SQL → Power BI → Insights)
* Business-focused dashboard design
* Ability to extract and present meaningful insights

---

## Note

The dataset used is a sample retail dataset.
The focus of this project is on analysis, modeling, and visualization, not data collection.
