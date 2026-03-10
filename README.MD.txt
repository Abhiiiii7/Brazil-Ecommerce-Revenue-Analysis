# 🇧🇷 Brazil E-Commerce Revenue & Demand Analysis

## 📌 Project Overview

This project analyzes Brazilian e-commerce transactional data (2016–2018) to evaluate revenue growth, seller performance, seasonality patterns, and demand forecasting.

The objective was to design a scalable Business Intelligence solution that transforms raw transactional data into strategic insights using SQL and Power BI.

---

## 📊 Dataset Description

- Time Period: 2016–2018
- Orders Analyzed: ~99,000+
- Order Items: ~112,000+
- Geographic Coverage: Brazil (multiple states)
- Granularity: Transaction-level data

---

## 🛠 Tools & Technologies

- SQL (PostgreSQL)
- Star Schema Data Modeling
- Power BI
- DAX (Advanced Measures)
- Time Series Forecasting (Exponential Smoothing)

---

## 🏗 Data Modeling Architecture

Implemented a Star Schema:

**Fact Table**
- fact_sales

**Dimension Tables**
- dim_customer
- dim_product
- dim_seller
- dim_date

This structure improves performance, scalability, and analytical flexibility.

---

## 🧹 Data Preparation

- Imported and structured 9 raw CSV datasets
- Resolved encoding issues (WIN1252 → UTF-8)
- Filtered only delivered orders for revenue accuracy
- Created calculated measures:
  - Total Revenue
  - Total Orders
  - Average Order Value
  - MoM Growth %
  - Top Seller Contribution %
  - Forecast Model

---

## 📊 KPI Definitions

- **Total Revenue** = SUM(price + freight_value)
- **Total Orders** = COUNT(DISTINCT order_id)
- **Average Order Value (AOV)** = Revenue / Orders
- **MoM Growth %** = (Current Month Revenue - Previous Month Revenue) / Previous Month Revenue
- **Top Seller %** = Revenue contribution of top sellers vs total revenue

---

## 📈 Key Analysis Performed

### 1️⃣ Executive Overview
- Revenue growth trend
- Order volume expansion
- Stability of AOV
- Month-over-Month growth tracking

### 2️⃣ Seller Performance
- Top 10 revenue contributors
- Revenue concentration evaluation
- Seller distribution impact

### 3️⃣ Seasonality Analysis
- Monthly demand comparison across 3 years
- Identification of structural demand (May peak)
- Detection of event-driven spikes (November promotions)

### 4️⃣ Demand Forecasting
Applied Power BI’s exponential smoothing model:
- Forecast horizon: 6–12 months
- Confidence interval: 95%
- Observed stable upward trajectory
- No extreme volatility detected

---

## 🔎 Key Insights

- Revenue shows consistent upward growth (2016 → 2018)
- Growth is volume-driven rather than price-driven
- May consistently demonstrates strong structural demand
- November spikes are promotional and non-recurring
- Seller revenue distribution is moderately diversified

---

## 🎯 Business Recommendations

- Prepare inventory ahead of May seasonal peaks
- Optimize promotional margins in November
- Strengthen high-performing seller partnerships
- Introduce loyalty programs to improve AOV

---

## 💡 Skills Demonstrated

- End-to-End BI Development
- Data Modeling (Star Schema)
- Advanced DAX Calculations
- KPI Engineering
- Time Series Forecasting
- Business Insight Communication
- Dashboard Design & Storytelling

---

## 📷 Dashboard Pages

1. Executive Overview
2. Seller Performance Analysis
3. Seasonality Analysis
4. Forecasting & Projection
5. Strategic Key Findings

---

## 🚀 Project Outcome

Successfully built a complete analytical solution from raw data to business-level forecasting insights.

This project demonstrates readiness for:
- Data Analyst roles
- Business Intelligence roles
- Analytics & Reporting positions

---

## 👤 Author

KONDERU ABHINAV VARMA
Data Analyst | SQL | Power BI | Forecasting | Business Intelligence