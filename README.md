# Movie Rental Analytics Pipeline

**Building reusable analytics marts with MySQL, dbt, and Tableau**

15,986 raw rental transactions (Sakila dataset, 599 customers, 16 categories) transformed into a layered analytics stack — from operational data to business dashboards, with a data-quality bug caught and fixed along the way.

---

## 📊 Interactive Dashboards

🔗 **[View on Tableau Public](YOUR_TABLEAU_LINK_HERE)**

[![Dashboard Preview](docs/dashboard_overview.png)](YOUR_TABLEAU_LINK_HERE)

| Dashboard | Question it answers | Key finding |
|---|---|---|
| **Executive Overview** | How is the business doing? | $67,407 revenue over 16,044 rentals; demand peaked in July 2005, led by Sports, Sci-Fi, Animation |
| **Customer Insights** | Who are our customers? | Avg LTV $112.53; value is driven by rental frequency — only a small segment exceeds $180 |
| **Revenue Drivers** | What drives revenue? | Top 5 categories = 35.2% of revenue; high volume ≠ high per-rental value (Animation: #3 in revenue, #12 in revenue/rental) |

*LTV is calculated within the observed period (May 2005 – Feb 2006).*

---

## Business Questions

- Who are the highest-value customers, and how engaged are they?
- Which film categories drive the most revenue — and is revenue concentrated?
- How does demand vary across categories and time periods?
- How can business metrics be standardized and reused instead of rebuilt per report?

---

## Architecture

![dbt Architecture](docs/dbt_architecture.png)

One staging model standardizes the raw table; three marts each answer a distinct business question at a different grain.

### Grain Transformation

![Grain Transformation](docs/grain_transformation.png)

Transaction-level records are re-aggregated into three business-oriented grains — customer, category, and month × category — so each mart directly serves its question without re-querying raw data.

---

## Data Marts

| Model | Grain | Key metrics |
|---|---|---|
| `stg_customer_film_analytics` | transaction | standardized fields, single source for all marts |
| `mart_customer_ltv` | customer | LTV, total rentals, films watched, avg spend/rental |
| `mart_category_revenue` | category | revenue, rental volume, revenue per rental, film count |
| `mart_seasonal_demand` | month × category | active customers, rentals, monthly revenue |

### `mart_customer_ltv` — example output (top 5 by LTV)

| customer | total_rentals | customer_ltv | films_watched | categories_watched | avg_spend_per_rental |
|---|---|---|---|---|---|
| Karl Seal | 45 | $221.55 | 44 | 15 | $4.92 |
| Eleanor Hunt | 46 | $216.54 | 46 | 14 | $4.71 |
| Clara Shaw | 42 | $195.58 | 42 | 16 | $4.66 |
| Marion Snyder | 39 | $194.61 | 38 | 14 | $4.99 |
| Rhonda Kennedy | 39 | $194.61 | 38 | 15 | $4.99 |

### `mart_category_revenue` — example output (top 5 by revenue)

| category | film_count | total_rentals | total_revenue | revenue_per_rental |
|---|---|---|---|---|
| Sports | 73 | 1,179 | $5,314.21 | $4.51 |
| Sci-Fi | 59 | 1,101 | $4,756.98 | $4.32 |
| Animation | 64 | 1,166 | $4,656.30 | $3.99 |
| Drama | 61 | 1,060 | $4,587.39 | $4.33 |
| Comedy | 56 | 941 | $4,383.58 | $4.66 |

### `mart_seasonal_demand` — example output (top 5 by monthly revenue)

| month | category | active_customers | total_rentals | total_revenue |
|---|---|---|---|---|
| 2005-07 | Sports | 340 | 497 | $2,281.03 |
| 2005-07 | Sci-Fi | 312 | 462 | $2,013.38 |
| 2005-07 | Drama | 318 | 463 | $1,978.37 |
| 2005-08 | Sports | 304 | 432 | $1,962.68 |
| 2005-07 | Animation | 325 | 489 | $1,954.11 |

---

## 🐛 Validation: Bug Found & Fixed

While validating dashboard numbers against raw data, I found that `mart_customer_ltv` used `MAX(total_spent)` — but `total_spent` in the source table is a **per-rental value**, not a customer total. The mart was reporting each customer's *single most expensive rental* as their "lifetime value" (top "LTV" ≈ $17, yet 19 films watched — logically impossible).

**Fix:** aggregate with `SUM` instead of `MAX`, then re-validate against raw transactions:

```sql
SUM(film_revenue)      AS customer_ltv,   -- was: MAX(total_spent)
SUM(film_rental_count) AS total_rentals   -- was: MAX(total_rentals)
```

After the fix, top customer Karl Seal shows $221.55 across 45 rentals — matching a direct aggregation of the raw data. Takeaway: **always validate mart output against source data before shipping.**

---

## Key Insights

- **Revenue is volume-driven.** Revenue per rental is nearly constant (~$4.20), so the July peak ($28.4K, 5.9× May) reflects transaction volume, not pricing — confirmed by perfectly overlapping revenue/volume trends.
- **Volume and unit economics diverge by category.** Comedy earns the highest revenue per rental ($4.66) on modest volume; Animation ranks #3 in total revenue but #12 in revenue per rental ($3.99).
- **Customer value is concentrated.** Most customers generate $80–140 in the period; the top 5 customers each exceed $190, and rentals-vs-LTV correlation shows frequency (not spend per visit) drives value.

---

## Business Impact

The marts turn one-off SQL into shared infrastructure: business logic is centralized, metric definitions are standardized (one definition of LTV, one of revenue per rental), and every future report or dashboard starts from validated, business-ready tables instead of raw transactions.

---

## Repository Structure

```
movie-analytics-dbt/
├── models/
│   ├── staging/     # stg_customer_film_analytics
│   └── marts/       # mart_customer_ltv, mart_category_revenue, mart_seasonal_demand
├── docs/            # architecture diagram, dashboard screenshots
└── README.md
```

## Skills Demonstrated

`SQL` `MySQL` `dbt` `ELT` `Data Modeling` `Data Validation` `Business Intelligence` `Tableau` `Analytics Engineering`
