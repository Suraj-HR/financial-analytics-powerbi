# Finance Performance Dashboard — Data Cleaning and Verification Log

This file documents the exact cleaning and validation steps completed in SQL Server.

## 1) Raw data import

The dataset was imported into SQL Server without changing the original date type at import time. This allowed the raw values to be inspected safely before making transformations.

Why this mattered:
- prevented accidental data loss
- allowed format inspection first
- made the cleaning process safer


> I imported the raw file first and inspected the source values before applying any transformation.

## 2) Date inspection

The raw date values were checked with a simple sample query.

Observed formats:
- `04-30-22`
- `04-12-2022`

This showed that the date column had mixed formats.


> I first inspected the raw date values and found that the column had mixed formats, so I did not convert it blindly.

## 3) Date cleaning logic

A new column was created to store a standardized date.

```sql
ALTER TABLE [Amazon Sale Report]
ADD Clean_Date DATE;
```

Then conditional conversion was used:

```sql
UPDATE [Amazon Sale Report]
SET Clean_Date =
    CASE
        WHEN LEN(Date) = 8 THEN TRY_CONVERT(date, Date, 1)
        WHEN LEN(Date) = 10 THEN TRY_CONVERT(date, Date, 110)
    END;
```

What this did:
- handled `MM-DD-YY`
- handled `MM-DD-YYYY`
- stored the result in one consistent `DATE` column

> I standardized mixed date formats using conditional conversion logic so that all records could be used reliably in monthly trend analysis.

## 4) Date validation

The cleaned date column was checked for nulls.

Validation query used:

```sql
SELECT COUNT(*) AS NULL_COUNT
FROM [Amazon Sale Report]
WHERE Clean_Date IS NULL;
```

What happened:
- the first conversion attempt showed many nulls
- the mixed-format issue was then handled with conditional logic
- after that, the date cleaning was finalized properly

Why this mattered:
- confirmed whether the conversion logic worked
- exposed hidden format inconsistency
- prevented incorrect monthly analysis

## 5) Replace the original date column

After validation, the original text date column was removed and the cleaned column was renamed.

```sql
ALTER TABLE [Amazon Sale Report]
DROP COLUMN Date;

EXEC sp_rename '[Amazon Sale Report].Clean_Date', 'Order_Date', 'COLUMN';
```

Why this mattered:
- removed duplicate columns
- kept the final table clean
- made the model easier to use later in Power BI


> I created a new date column first to avoid data loss, validated it, and only then replaced the original column.

## 6) Amount cleaning

A new numeric column was created for revenue values.

```sql
ALTER TABLE [Amazon Sale Report]
ADD Clean_Amount FLOAT;
```

Then the text amount was converted:

```sql
UPDATE [Amazon Sale Report]
SET Clean_Amount = TRY_CAST(Amount AS FLOAT);
```

Why this mattered:
- text values cannot be summed properly
- numeric conversion was required for revenue analysis

Validation result:
- many rows could not be converted
- only a very small number of delivered rows were affected


> I converted the amount column into a numeric field so I could calculate revenue correctly.

## 7) Amount validation

Two checks were used:

```sql
SELECT COUNT(*) AS Null_Count
FROM [Amazon Sale Report]
WHERE Clean_Amount IS NULL;
```

```sql
SELECT COUNT(*) AS Null_Count
FROM [Amazon Sale Report]
WHERE Clean_Amount IS NULL
AND Status = 'Shipped - Delivered to Buyer';
```

What the checks showed:
- there were many non-convertible rows in the raw amount column
- only a very small number of delivered rows were affected

How it was handled:
- the analysis continued because the impact on delivered-order revenue was negligible
- in a stricter production setting, those few rows should be reviewed manually


> I checked how many amount values failed conversion and then confirmed that the impact on delivered orders was very small.

## 8) Business filter logic

For all revenue and profit calculations, only delivered orders were included.

```sql
WHERE Status = 'Shipped - Delivered to Buyer'
```

Why this mattered:
- cancelled orders should not be counted as revenue
- shipped-but-not-delivered orders are not completed business
- delivered orders give a more accurate business picture


> I filtered only delivered orders so the analysis reflected actual realized revenue instead of incomplete transactions.

## 9) Missing location values

Missing `Ship_state` values were replaced with `Unknown` in the analysis queries.

```sql
ISNULL(Ship_state, 'Unknown')
```

Why this mattered:
- avoids dropping records
- keeps analysis complete
- makes missing data visible instead of invisible


> I replaced missing location values with `Unknown` so I could keep all records in the regional analysis.

## 10) Profit creation

A derived profit column was added because the source dataset did not include a true profit field.

```sql
ALTER TABLE [Amazon Sale Report]
ADD Profit FLOAT;
```

The profit was then populated using a fixed assumption:

```sql
UPDATE [Amazon Sale Report]
SET Profit = Clean_Amount * 0.3;
```

Why this mattered:
- allowed profitability analysis even though actual cost was missing
- made it possible to build a finance-focused dashboard
- useful for learning, but not a substitute for a true accounting cost model


> Since profit data was not available, I derived it using a percentage assumption so I could still analyze profitability patterns.

## 11) Profit validation

Profit values were checked against the cleaned amount values:

```sql
SELECT TOP 10 Clean_Amount, Profit
FROM [Amazon Sale Report];
```

What this confirmed:
- profit was exactly 30% of clean amount
- the calculation was applied consistently


> I validated the derived profit field by checking sample rows to ensure the calculation was consistent.

## 12) Profit margin note

Profit margin was calculated in SQL using:

```sql
SUM(Profit) * 1.0 / SUM(Clean_Amount)
```

Important observation:
- because profit was derived using a fixed 30% assumption, the margin is identical across categories
- this means margin is not a differentiator in this version of the project

## 13) Final verification summary

Completed checks:
- raw data inspection
- date format inspection
- date standardization
- amount conversion
- null validation
- delivered-order filtering
- missing region handling
- profit derivation
- profit validation

## 14) Final summary

> In SQL Server, I cleaned the dataset by standardizing mixed date formats, converting the amount field into a numeric type, handling missing location values, and deriving a profit column because the dataset did not include one. I also validated the transformed fields before using them in revenue and profitability analysis.

## 15) To add after Day 3 and later

Append later:
- profit contribution %
- running profit
- top profitable products
- deeper loss analysis if a true cost field becomes available
