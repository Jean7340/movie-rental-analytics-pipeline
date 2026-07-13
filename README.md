# Modern Data Stack for Customer Analytics

## Overview

In this project, I used **MySQL** and **dbt** to transform a raw movie rental dataset ([Sakila Movie Rental Database](https://dev.mysql.com/doc/sakila/en/)) containing **15,986 rental transactions and 599 customers** into a reusable analytics layer. The objective was to create scalable analytical assets that support customer, revenue, and demand analysis.

---

## Business Problem

The raw transactional dataset contained customer activity, rental events, film information, and revenue data at the transaction level.

While suitable for operational processes, the structure made it difficult to efficiently answer questions such as:

- Who are the highest-value customers?
- Which categories contribute the most revenue?
- How does demand vary across categories and time periods?
- How can business metrics be standardized and reused across analyses?

Without a modeling layer, analysts would need to repeatedly rebuild the same logic for every new report or dashboard.

---

## Solution Architecture

<p align="center">
  <img src="docs/dbt_architecture.png" width="850">
</p>

The project follows a layered analytics architecture that transforms raw transactional data into reusable business-ready analytics assets.

```text
Raw Data
    ↓
Staging Layer
    ↓
Business Data Marts
    ↓
Business Insights
```

---

## Data Overview

### Source Dataset

The source dataset contains rental transaction activity for a DVD rental business.

Key attributes include:

- Customer information
- Film information
- Film category
- Rental activity
- Revenue metrics
- Rental month

### Scale

| Metric | Value |
|----------|----------|
| Transactions | 15,986 |
| Customers | 599 |
| Categories | 16 |
| Data Source | MySQL |

### Grain Transformation

The project transforms rental transaction records into multiple business-oriented analytical grains. Each mart is designed around a specific business question, enabling reusable reporting and analysis without repeatedly querying raw transaction data.

<p align="center">
  <img src="docs/grain_transformation.png" width="1100">
</p>

---

## What I Built

### 1. Staging Layer

Model:

```text
stg_customer_film_analytics
```

Purpose:

- Standardize source fields
- Create a consistent analytical foundation
- Separate raw operational data from business logic

---

### 2. Customer Lifetime Value Mart

Model:

```text
mart_customer_ltv
```

Business Questions:

- Who are the highest-value customers?
- How engaged are they with the platform?

Key Metrics:

- Customer Lifetime Value (LTV)
- Total Rentals
- Films Watched
- Categories Watched
- Average Spend per Rental

Example Output:

<p align="center">
  <img src="docs/customer_ltv_mart.png" width="900">
</p>

---

### 3. Revenue Performance Mart

Model:

```text
mart_category_revenue
```

Business Questions:

- Which categories drive the most revenue?
- How concentrated is revenue across categories?

Key Metrics:

- Total Revenue
- Rental Volume
- Revenue per Rental
- Film Count

Example Output:

<p align="center">
  <img src="docs/category_revenue_mart.png" width="900">
</p>

---

### 4. Seasonal Demand Mart

Model:

```text
mart_seasonal_demand
```

Business Questions:

- How does demand change over time?
- Which categories perform best during different periods?

Key Metrics:

- Active Customers
- Rental Volume
- Monthly Revenue

Example Output:

<p align="center">
  <img src="docs/seasonal_demand_mart.png" width="900">
</p>

---

## Key Findings

### Revenue Concentration

Revenue was concentrated among a subset of film categories.

Top-performing categories included:

- Sports
- Sci-Fi
- Animation

### Customer Value Differences

Customer lifetime value varied significantly across the customer base, suggesting opportunities for customer segmentation and retention-focused analysis.

### Demand Patterns

Rental activity showed meaningful variation across months and categories, indicating seasonal demand behavior that could influence future inventory and pricing decisions.

---

## Business Impact

This project mirrors a common analytics engineering workflow: **Raw Data → dbt Models → Business Marts → Insights**, and demonstrates how dbt can be used to transform operational data into scalable analytics infrastructure.

To be specific, the project created a reusable analytics layer that:

- Centralizes business logic
- Standardizes metric definitions
- Reduces repeated SQL development
- Supports future reporting and analysis

---

## Repository Structure

```text
movie-analytics-dbt/
│
├── models/
│   ├── staging/
│   └── marts/
│
├── docs/
│
└── README.md
```

---

### Skills Demonstrated

`SQL`
`MySQL`
`dbt`
`ELT`
`Data Modeling`
`Business Intelligence`
`Analytics Engineering`
`Tableau`
