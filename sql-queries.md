# Finance Performance Dashboard — SQL Query Log

This document captures the SQL work completed for the Finance Performance Dashboard. It is written so you can explain each query clearly in interviews and later extend it with additional Day 3+ work if needed.

## Project flow used here

1. Import raw CSV into SQL Server
2. Inspect the source data before modifying anything
3. Clean date and amount fields
4. Validate the cleaned columns
5. Run analysis queries for revenue, profit, trend, category, and region
6. Use the SQL outputs later in Power BI visuals and storytelling

---

## 1) Data preparation and validation

### 1.1 Inspect the raw date values
Purpose: confirm the imported table and inspect the raw date format before any transformation.

```sql
SELECT TOP 20 Date
FROM [Amazon Sale Report];
```

### 1.2 Create a cleaned date column
Purpose: store a proper SQL `DATE` value without touching the raw column first.

```sql
ALTER TABLE [Amazon Sale Report]
ADD Clean_Date DATE;
```

### 1.3 Standardize mixed date formats
Purpose: handle both `MM-DD-YY` and `MM-DD-YYYY` values safely.

```sql
UPDATE [Amazon Sale Report]
SET Clean_Date =
    CASE
        WHEN LEN(Date) = 8 THEN TRY_CONVERT(date, Date, 1)
        WHEN LEN(Date) = 10 THEN TRY_CONVERT(date, Date, 110)
    END;
```

### 1.4 Validate the date cleaning
Purpose: confirm whether the conversion succeeded.

```sql
SELECT COUNT(*) AS NULL_COUNT
FROM [Amazon Sale Report]
WHERE Clean_Date IS NULL;
```

### 1.5 Review rows that still failed conversion
Purpose: inspect remaining bad values and verify the mixed-format issue.

```sql
SELECT TOP 20 Date
FROM [Amazon Sale Report]
WHERE Clean_Date IS NULL;
```

### 1.6 Replace the original date column
Purpose: keep the final table clean and remove the text-based date column after validation.

```sql
ALTER TABLE [Amazon Sale Report]
DROP COLUMN Date;

EXEC sp_rename '[Amazon Sale Report].Clean_Date', 'Order_Date', 'COLUMN';
```

### 1.7 Confirm the final date column
Purpose: verify the final column is usable for reporting.

```sql
SELECT TOP 20 Order_Date
FROM [Amazon Sale Report];
```

### 1.8 Create a cleaned amount column
Purpose: convert the revenue values from text to a numeric type.

```sql
ALTER TABLE [Amazon Sale Report]
ADD Clean_Amount FLOAT;
```

### 1.9 Convert Amount to numeric
Purpose: make revenue usable for `SUM`, trend analysis, and profit calculations.

```sql
UPDATE [Amazon Sale Report]
SET Clean_Amount = TRY_CAST(Amount AS FLOAT);
```

### 1.10 Validate amount conversion
Purpose: count how many rows could not be converted into a numeric value.

```sql
SELECT COUNT(*) AS Null_Count
FROM [Amazon Sale Report]
WHERE Clean_Amount IS NULL;
```

### 1.11 Inspect non-convertible amount values
Purpose: identify bad values in the original amount column.

```sql
SELECT TOP 20 Amount
FROM [Amazon Sale Report]
WHERE TRY_CAST(Amount AS FLOAT) IS NULL;
```

### 1.12 Check whether bad amounts affect delivered orders
Purpose: verify the impact on the rows that matter for revenue analysis.

```sql
SELECT COUNT(*) AS Null_Count
FROM [Amazon Sale Report]
WHERE Clean_Amount IS NULL
  AND Status = 'Shipped - Delivered to Buyer';
```

### 1.13 Handle missing states in analysis queries
Purpose: replace NULL location values with a readable label in grouping queries.

```sql
ISNULL(Ship_state, 'Unknown')
```

---

## 2) Day 1 revenue and order analysis

### 2.1 Total revenue
Business question: how much revenue was generated from completed transactions?

```sql
SELECT SUM(Clean_Amount) AS Total_Revenue
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer';
```

### 2.2 Monthly revenue trend
Business question: how is revenue changing month by month?

```sql
SELECT
    YEAR(Order_Date) AS Year,
    MONTH(Order_Date) AS Month,
    SUM(Clean_Amount) AS Monthly_Revenue
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer'
GROUP BY
    YEAR(Order_Date),
    MONTH(Order_Date)
ORDER BY
    Year,
    Month;
```

### 2.3 Revenue by category
Business question: which product categories drive revenue?

```sql
SELECT
    Category,
    SUM(Clean_Amount) AS Revenue
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer'
GROUP BY Category
ORDER BY Revenue DESC;
```

### 2.4 Revenue by state
Business question: which states contribute the most revenue?

```sql
SELECT
    ISNULL(Ship_state, 'Unknown') AS Ship_state,
    SUM(Clean_Amount) AS State_Revenue
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer'
GROUP BY ISNULL(Ship_state, 'Unknown')
ORDER BY State_Revenue DESC;
```

### 2.5 Total orders
Business question: how many completed orders contributed to revenue?

```sql
SELECT COUNT(Order_ID) AS Total_Orders
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer';
```

---

## 3) Day 2 finance layer

### 3.1 Create a profit column
Purpose: derive profit because the source dataset did not include a real cost/profit field.

Assumption used in this project:
- Cost is assumed to be 70% of revenue
- Profit is assumed to be 30% of revenue

```sql
ALTER TABLE [Amazon Sale Report]
ADD Profit FLOAT;
```

### 3.2 Populate the profit column
Purpose: calculate a derived profit value for analysis.

```sql
UPDATE [Amazon Sale Report]
SET Profit = Clean_Amount * 0.3;
```

### 3.3 Validate the profit calculation
Purpose: confirm that profit is 30% of the cleaned amount.

```sql
SELECT TOP 10 Clean_Amount, Profit
FROM [Amazon Sale Report];
```

### 3.4 Total profit
Business question: how much profit was generated from completed transactions?

```sql
SELECT ROUND(SUM(Profit), 2) AS Total_Profit
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer';
```

### 3.5 Profit by category
Business question: which categories generate the most profit?

```sql
SELECT
    Category,
    SUM(Profit) AS Profit_Category
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer'
GROUP BY Category
ORDER BY Profit_Category DESC;
```

### 3.6 Monthly profit trend
Business question: how is profit changing month by month?

```sql
SELECT
    YEAR(Order_Date) AS Year,
    MONTH(Order_Date) AS Month,
    SUM(Profit) AS Monthly_Profit
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer'
GROUP BY
    YEAR(Order_Date),
    MONTH(Order_Date)
ORDER BY
    Year,
    Month;
```

### 3.7 Profit by state
Business question: which states generate the most profit?

```sql
SELECT
    ISNULL(Ship_State, 'Unknown') AS Ship_State,
    SUM(Profit) AS State_Profit
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer'
GROUP BY ISNULL(Ship_State, 'Unknown')
ORDER BY State_Profit DESC;
```

### 3.8 Profit margin by category
Business question: which category has the best efficiency?

Important note:
- In this project, profit is derived using a fixed 30% assumption.
- That means the margin will be identical for all categories and is mainly useful as a validation check here, not as a differentiator.

```sql
SELECT
    Category,
    SUM(Profit) * 1.0 / SUM(Clean_Amount) AS Profit_Margin
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer'
GROUP BY Category
ORDER BY Profit_Margin DESC;
```

### 3.9 Lowest-profit categories
Business question: which categories are the weakest performers?

```sql
SELECT
    Category,
    SUM(Profit) AS Profit_Category
FROM [Amazon Sale Report]
WHERE Status = 'Shipped - Delivered to Buyer'
GROUP BY Category
ORDER BY Profit_Category ASC;
```

---

## 4) Query design notes you can mention in interviews

### Revenue queries
- Revenue analysis was always filtered to `Shipped - Delivered to Buyer` so only completed transactions were included.
- `ORDER BY Revenue DESC` was used for rankings like category or region.
- `ORDER BY Year, Month` was used for time trend queries so the chart would read chronologically.

### Profit queries
- Profit was created because the dataset did not contain a native profit field.
- A fixed assumption was used for learning purposes.
- Profit margin stayed constant across categories because profit was derived from revenue using the same percentage for every row.

### Data quality handling
- Mixed date formats were standardized before any monthly trend analysis.
- Amount values were converted into numeric form before using `SUM`.
- Missing states were labeled as `Unknown` so regional analysis would not lose rows.

---

## 5) Power BI mapping

- `Total_Revenue` → KPI card
- `Monthly_Revenue` → line chart
- `Revenue by Category` → bar chart
- `Revenue by State` → bar chart or map
- `Total_Orders` → KPI card
- `Total_Profit` → KPI card
- `Monthly_Profit` → line chart
- `Profit by Category` → bar chart
- `Profit by State` → bar chart or map
- `Profit_Margin` → validation metric, not a strong differentiator in this project because it is constant

