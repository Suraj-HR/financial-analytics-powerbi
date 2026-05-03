# Finance Performance Dashboard — Insights Summary

This document captures the business insights generated so far from the Finance Performance Dashboard project. It is written in a simple, interview-ready style.

## 1) Data cleaning insight

The dataset contained inconsistent date formats, so the date column was standardized into a proper `DATE` field before analysis. This made time-based reporting reliable and prevented errors in monthly calculations.

Interview line:
> I standardized mixed date formats into one proper date column and validated the conversion before moving to analysis.

## 2) Total revenue insight

The total revenue from delivered orders is approximately 18.65 million. This represents actual completed business, because cancelled and incomplete transactions were excluded from the calculation.

Interview line:
> I filtered only delivered orders so the revenue reflected completed transactions, not inflated totals from cancelled or unfulfilled orders.

## 3) Monthly revenue trend insight

Monthly revenue peaked in April 2022 and then declined in May and June. This suggests that the business had a strong month in April, but the performance was not sustained in the following months.

Interview line:
> Revenue peaked in April and then declined, which suggests a short-term spike that was not sustained in the next months.

Possible business interpretation:
- seasonality
- one-time demand spike
- temporary operational improvement
- data completeness effect for earlier months

## 4) Category revenue insight

Revenue is concentrated in a few categories. `Set` contributes the highest revenue, followed by `Kurta` and `Western Dress`, while the remaining categories contribute much less.

Interview line:
> The business depends heavily on a few key product categories, especially Set, Kurta, and Western Dress.

Business meaning:
- high category concentration
- dependency risk if top categories slow down
- opportunity to grow weaker categories

## 5) Regional revenue insight

Revenue is highest in Maharashtra, followed by Karnataka and Uttar Pradesh. This shows that sales are concentrated in a few strong regions, while many states contribute much less.

Interview line:
> Maharashtra leads revenue, followed by Karnataka and Uttar Pradesh, showing that business performance is concentrated in a few core states.

Business meaning:
- top-performing states drive most revenue
- low-performing states may need better coverage, promotion, or localized strategy

## 6) Missing location value insight

Some rows had missing state values. Those missing values were handled using `Unknown` so that the analysis remained complete without dropping records.

Interview line:
> I replaced missing location values with `Unknown` to keep the dataset complete and avoid losing records during regional analysis.

## 7) Orders insight

The total number of successfully delivered orders is 28,769. This shows the scale of completed business activity contributing to revenue.

Interview line:
> I counted only delivered orders so the order metric reflects completed transactions contributing to revenue.

## 8) Profit creation insight

The dataset did not contain profit directly, so profit was derived using a fixed percentage assumption for learning and analysis purposes.

Interview line:
> Since the dataset did not include profit, I derived it using an assumed cost structure so I could still analyze profitability patterns.

Important note:
- This is a derived metric, not a true accounting profit.
- It is useful for analysis practice, but the assumption should be stated clearly in interviews.

## 9) Total profit insight

The total profit from delivered orders is approximately 5.6 million. Since profit was derived as 30% of revenue, this metric reflects the assumed profitability of the completed transactions.

Interview line:
> The business generated around 5.6 million in profit from completed transactions, based on the profit assumption used in the project.

## 10) Monthly profit trend insight

Profit peaked in April 2022 and then declined in May and June. This mirrors the revenue trend and shows that the strongest business performance happened in April.

Interview line:
> Profit peaked in April and then declined afterward, which indicates that the business had its strongest profitability in that month.

Important observation:
- March profit was extremely low.
- That may indicate partial data for that month or a short initial period.

## 11) Category profit insight

Profit is highly concentrated in a few categories, with `Set` generating the highest profit, followed by `Kurta` and `Western Dress`. Several categories contribute very little profit.

Interview line:
> Profit is concentrated in a few categories, especially Set and Kurta, which are the strongest contributors to profitability.

Business meaning:
- the same top categories dominate both revenue and profit
- low-profit categories may need better pricing or promotional strategy
- the business is dependent on a limited set of strong products

## 12) Profit margin insight

Profit margin remains constant across categories at 30% because profit was derived using a fixed 30% assumption. This means margin does not provide category-level differentiation in this version of the project.

Interview line:
> Margin is constant across categories because the profit was derived using the same percentage for every row, so it acts more like a validation check than a decision metric here.

Important note:
- This is not a real-world cost model.
- In a true finance dataset, margins would vary by category and product.

## 13) Lowest-profit categories insight

The weakest categories by profit are `Saree`, `Bottom`, `Blouse`, and `Ethnic Dress`. These categories contribute very little compared to the top performers.

Interview line:
> Saree and Bottom are the weakest categories by profit, making them potential areas for review and improvement.

Business meaning:
- lower demand or lower ticket size
- possible opportunity for pricing review
- could be candidates for promotional push or assortment changes

## 14) Regional profit insight

Profit is highest in Maharashtra, followed by Karnataka and Uttar Pradesh. This shows that the same core states also drive the most profitability.

Interview line:
> Maharashtra contributes the most profit, followed by Karnataka and Uttar Pradesh, which means profitability is concentrated in a few strong regions.

Business meaning:
- top regions are the main profit engines
- smaller states contribute very little and may need targeted growth actions

## 15) Business summary so far

The business is driven by:
- completed orders
- a few high-performing categories
- a few strong regions
- a strong April peak in both revenue and profit

The main risks are:
- category concentration
- region concentration
- a profitability view that is currently simulated instead of based on real cost data

## 16) How to say the difference between revenue and profit

> Revenue is the total money generated from sales, while profit is what remains after costs are removed.

## 17) To add after Day 3 and later

Append these insights later:
- profitability trends by page visuals
- running profit
- top profitable products
- profit contribution %
- deeper loss analysis if a real cost field becomes available
