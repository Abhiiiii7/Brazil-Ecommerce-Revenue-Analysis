# Project Documentation

## Brazil E-Commerce Revenue & Demand Analysis (Olist)

**Tools:** PostgreSQL • SQL • Power BI • DAX • Power BI Forecasting (ETS / Exponential Smoothing)

**Project goal:** Build an end-to-end BI solution (data modeling → KPIs → dashboards → forecasting) for a Brazilian multi-vendor e-commerce marketplace.

---

## 1) Business context

Olist is a marketplace model: multiple independent sellers sell products to customers across Brazilian states. A BI solution for a marketplace typically needs to answer:

- **Growth:** Is revenue increasing over time? How strong is momentum?
- **Drivers:** Is growth driven by more orders (volume) or higher spending per order (AOV)?
- **Geography:** Which states contribute most to revenue?
- **Seller health:** Is the marketplace dependent on a small number of sellers (concentration risk)?
- **Seasonality:** Are there repeatable peak months? Are spikes structural or promotion-driven?
- **Forecasting:** Based on historical behavior, what is the short-term expected trajectory?

---

## 2) Dataset

**Source:** *Brazilian E-Commerce Public Dataset by Olist* (commonly hosted on Kaggle).

**Raw CSV files used (9):**

1. `olist_customers_dataset.csv`
2. `olist_orders_dataset.csv`
3. `olist_order_items_dataset.csv`
4. `olist_order_payments_dataset.csv`
5. `olist_order_reviews_dataset.csv`
6. `olist_products_dataset.csv`
7. `olist_sellers_dataset.csv`
8. `olist_geolocation_dataset.csv`
9. `product_category_name_translation.csv`

**Typical row counts (as commonly observed in this dataset):**
- Orders: ~99k
- Order items: ~112k
- Customers: ~99k
- Sellers: ~3k
- Reviews: ~99k
- Geolocation: ~1M

> Your exact counts can vary depending on filters (e.g., delivered orders only) and whether you deduplicate.

---

## 3) Data pipeline (high-level)

### Step A — Load raw data into PostgreSQL
- Create a database (example: `olist_db`).
- Create a schema for raw tables (example: `olist_raw`).
- Create tables matching CSV structures.
- Load data with `COPY` / `\copy`.

### Step B — Transform into an analytics star schema
- Create an analytics schema (example: `olist_analytics`).
- Build dimensions (customer, product, seller, date).
- Build a fact table that supports revenue analytics.

### Step C — Power BI semantic model and dashboards
- Connect Power BI to PostgreSQL.
- Import the analytics tables.
- Create relationships (star schema).
- Create DAX measures.
- Build dashboard pages (executive, sellers, seasonality, forecasting, findings).

---

## 4) Data model

### 4.1 Star schema

**Fact table**
- `olist_analytics_fact_sales`

**Dimension tables**
- `olist_analytics_dim_customer`
- `olist_analytics_dim_product`
- `olist_analytics_dim_seller`
- `olist_analytics_dim_date`

### 4.2 Relationships (Power BI)

Typical relationships:

- `fact_sales[customer_id]` → `dim_customer[customer_id]`
- `fact_sales[product_id]` → `dim_product[product_id]`
- `fact_sales[seller_id]` → `dim_seller[seller_id]`
- `fact_sales[order_date]` → `dim_date[order_date]`

> Use **single-direction filtering** from dimensions → fact for best star-schema performance.

---

## 5) Transformations & feature engineering

### 5.1 Revenue logic
Revenue is derived from order items:

- `item_revenue = price + freight_value`

Then aggregated as needed in Power BI.

### 5.2 Delivered orders filter (data quality)
To keep revenue definitions realistic, analysis can be limited to:

- `order_status = 'delivered'`

This avoids inflating revenue with canceled/unfulfilled orders.

### 5.3 Date dimension
A calendar table enables time intelligence:

- year
- month (numeric)
- month_name
- year-month labels (optional)

Make sure `month_name` is **sorted by** `month` in Power BI to avoid alphabetical ordering.

---

## 6) KPI definitions

### 6.1 Core measures

**Total Revenue**
```DAX
Total Revenue = SUM(olist_analytics_fact_sales[total_revenue])
```

**Total Orders** (delivered/filtered by your model)
```DAX
Total Orders = DISTINCTCOUNT(olist_analytics_fact_sales[order_id])
```

**Average Order Value (AOV)**
```DAX
Average Order Value = DIVIDE([Total Revenue], [Total Orders])
```

### 6.2 Growth measures

**Month-over-Month Growth %**
```DAX
MoM Growth % =
VAR CurrentMonthRevenue = [Total Revenue]
VAR PreviousMonthRevenue =
    CALCULATE(
        [Total Revenue],
        DATEADD('olist_analytics_dim_date'[order_date], -1, MONTH)
    )
RETURN
DIVIDE(CurrentMonthRevenue - PreviousMonthRevenue, PreviousMonthRevenue)
```

### 6.3 Seller concentration

**Top 5 Sellers Revenue %**
```DAX
Top 5 Sellers Revenue % =
VAR Top5Revenue =
    CALCULATE(
        [Total Revenue],
        TOPN(
            5,
            SUMMARIZE(
                'olist_analytics_fact_sales',
                'olist_analytics_fact_sales'[seller_id],
                "SellerRevenue", [Total Revenue]
            ),
            [SellerRevenue],
            DESC
        )
    )
RETURN
DIVIDE(Top5Revenue, [Total Revenue])
```

> Interpreting the metric:
> - Lower % ⇒ more diversified seller base (lower concentration risk)
> - Higher % ⇒ platform dependency risk on a few sellers

---

## 7) Dashboard pages

### Page 1 — Executive Overview
Purpose: fast performance snapshot.

Recommended visuals:
- KPI cards: Total Revenue, Total Orders, AOV, MoM Growth %
- Monthly revenue trend
- Revenue by state (Top N)
- Short executive summary text box

### Page 2 — Seller Performance
Purpose: marketplace health and seller dependency.

Recommended visuals:
- Top 10 sellers by revenue (bar)
- Top 5 sellers revenue contribution % (card)
- Optional: Top 10 sellers contribution %

### Page 3 — Seasonality Analysis
Purpose: determine whether peaks are structural vs event-driven.

Recommended visuals:
- Monthly Orders (all years combined)
- Monthly Revenue (all years combined)
- Monthly AOV (all years combined)
- Revenue by Month with Year as legend (line chart)

How to interpret:
- If Revenue and Orders move together ⇒ volume-driven seasonality
- If AOV spikes while orders remain stable ⇒ pricing/mix-driven seasonality

### Page 4 — Forecasting & Projection
Purpose: demonstrate forecasting skill + forward-looking view.

Approach:
- Use Power BI Analytics pane → **Forecast** on a monthly revenue line chart.
- Power BI uses **ETS / exponential smoothing** and shows confidence intervals.

Suggested settings:
- Forecast length: 6–12 months
- Confidence interval: 95%
- Seasonality: Auto

### Page 5 — Key Findings
Purpose: stakeholder-ready summary.

Recommended bullets:
- Growth pattern (2016–2018)
- Seasonality conclusion (e.g., May structural strength)
- Promotional spike interpretation (e.g., November)
- Seller concentration metric (e.g., Top 5 = 7.4%)
- Recommendations

---

## 8) Reproducibility guide

### 8.1 PostgreSQL setup
1. Install PostgreSQL + pgAdmin.
2. Create database (example): `olist_db`.
3. Create schemas:
   - `olist_raw` (raw ingested tables)
   - `olist_analytics` (star schema)

### 8.2 Load CSV files
Example pattern:

```SQL
-- In psql terminal (recommended for local files):
\copy olist_raw.olist_orders FROM 'C:/path/to/olist_orders_dataset.csv' WITH (FORMAT csv, HEADER true)
```

> Tip: `\copy` reads from your local machine, while `COPY` reads from the database server’s filesystem.

### 8.3 Build analytics tables
Create `dim_*` and `fact_*` tables using SQL transformations.

### 8.4 Connect Power BI
1. Get Data → PostgreSQL.
2. Import `olist_analytics_*` tables.
3. Create relationships (dimension → fact).
4. Add DAX measures.
5. Build visuals and pages.

---

## 9) Assumptions & limitations

- Revenue is approximated as `price + freight_value` (no margin/cost data).
- No marketing spend or campaign dataset included.
- Forecasting assumes historical patterns persist; it will not anticipate major structural changes.

---

## 10) Suggested repo structure

```text
.
├─ README.md
├─ docs/
│  ├─ PROJECT_DOCUMENTATION.md
│  ├─ DATA_DICTIONARY.md         (optional)
│  └─ SCREENSHOTS.md             (optional)
├─ sql/
│  ├─ 01_create_tables.sql
│  ├─ 02_load_data.sql
│  ├─ 03_build_star_schema.sql
│  └─ 04_validation_checks.sql
├─ powerbi/
│  └─ olist_revenue_analysis.pbix
└─ images/
   ├─ executive_overview.png
   ├─ seller_performance.png
   ├─ seasonality.png
   └─ forecasting.png
```

---

## 11) Next improvements (optional)

- Customer retention & repeat purchase rate
- Cohort analysis (first purchase month)
- Category deep-dive (Top categories, category growth)
- Profitability estimates if cost/margin data becomes available

