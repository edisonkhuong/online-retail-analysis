# Online Retail II — Business Insights Analysis

**An end-to-end data analysis of 1M+ transactions from a UK-based online retailer, demonstrating data cleaning, SQL analysis, and interactive visualization.**

![Dashboard](Dashboard.png)

**[Interactive Tableau Dashboard](https://public.tableau.com/app/profile/edison.khuong/viz/OnlineRetailIIBusinessInsightsDashboard/OnlineRetailIIBusinessInsightsDashboard)**

---

## Project Overview

This project takes the [UCI Online Retail II dataset](https://archive.ics.uci.edu/dataset/502/online+retail+ii) — a real-world, famously messy dataset of e-commerce transactions from December 2009 to December 2011 — and walks it through a complete analytics pipeline: extraction, cleaning, transformation, SQL-based analysis, and interactive visualization.

The work answers five business questions a retail analyst would actually ask:

1. **Revenue trends** — How does revenue evolve over time? Is there seasonality?
2. **Customer segmentation** — Who are the VIPs? Who's at risk of churning?
3. **Product performance** — Which products drive revenue? Is there a Pareto pattern?
4. **Geographic reach** — Which markets matter, and which are untapped?
5. **Cancellation behavior** — What gets returned, and when?

## Business Questions & Findings

### 1. How does revenue evolve over time? Is there seasonality?

Revenue is heavily concentrated around the Christmas gift-buying season, with **November driving roughly 2x a typical month** in both 2010 and 2011. December and January are the weakest months.

→ **Recommendation:** Inventory planning, ad spend, and hiring should be tuned to a November peak. Suppliers should be secured by September. Any system outage in November would be disproportionately costly.

### 2. Who are our best customers — and who's at risk?

Applying RFM (Recency-Frequency-Monetary) segmentation across 5,852 known customers revealed:
- **VIPs** (~22% of customers) drive a disproportionate share of revenue
- **"Lost"** is the largest segment by count — customers who used to buy but haven't recently
- **"At Risk"** customers still rank high on Frequency/Monetary but haven't purchased recently

→ **Recommendation:** Launch a retention campaign targeted at the "At Risk" segment. These are customers with proven purchase history who are currently disengaged — far higher ROI than acquiring new customers. A lightweight re-engagement email (15% off, free shipping) could recover meaningful revenue.

### 3. Which products drive revenue? Is there a Pareto pattern?

Yes, and strongly. **Roughly the top 20% of SKUs (~980 products) account for 80% of revenue.** The revenue curve is steep at the top and flat at the tail.

→ **Recommendation:** Focus inventory, marketing, and merchandising resources on the top revenue SKUs. Periodically audit the bottom 50% of SKUs (which together contribute <5% of revenue) for discontinuation, as they likely carry storage and complexity costs that exceed their contribution.

### 4. Which markets matter, and which are untapped?

The UK dominates at ~85% of revenue. Among non-UK markets, **Netherlands, EIRE, and Germany** have notably high average order values — suggestive of wholesale or bulk-buyer customers rather than retail end-consumers.

→ **Recommendation:** Investigate the identity of high-AOV buyers in these three countries. If they're confirmed wholesale/trade buyers, the retailer should consider a dedicated B2B channel with volume pricing, which typically improves margins and customer lifetime value compared to treating these buyers through the retail flow.

### 5. When do customers buy, and what gets cancelled?

Orders concentrate in **weekday business hours (Tuesday–Thursday, 10am–3pm)**. Saturday activity is negligible — likely operational closure. The overall cancellation rate hovers around 2–3% monthly, with specific high-value products (Paper Craft Little Birdie, Medium Ceramic Top Storage Jar) driving disproportionate cancellation value from single large orders that fell through.

→ **Recommendation:** Promotional emails should be sent Monday afternoon or early Tuesday morning to catch the Tuesday–Thursday peak. Saturday closure should be reviewed — either accept the gap as a cost-of-operations choice or investigate enabling Saturday order processing to capture an additional day of potential revenue. The top cancellation products warrant investigation: are they quality issues, shipping issues, or single-customer wholesale failures?

## Stack

- **Python (pandas, numpy)** — data cleaning and feature engineering
- **SQLite + SQL** — structured storage and analytical queries (CTEs, window functions, joins)
- **Jupyter Lab** — exploration, cleaning, and analysis notebooks
- **Tableau Public** — interactive dashboard hosted online
- **matplotlib** — supplementary in-notebook charting

## Data Cleaning — Decisions Made

The raw dataset required several judgment calls, each documented in `02_clean.ipynb`:

| Decision | Rationale |
|---|---|
| Remove 'A'-prefix invoices (6 rows) | Identified as accounting adjustments (bad-debt write-offs), not customer transactions |
| Separate 'C'-prefix invoices (~22K rows) into their own table | Cancellations are real events worth analyzing, but distort sales-side metrics if mixed in |
| Remove non-product stock codes (POST, DOT, M, BANK CHARGES, AMAZONFEE, etc.) | Allowlist approach initially tried with regex; switched to explicit exclusion list after discovering legitimate products with 2-3 letter variant codes (e.g., `15056BL`) and non-standard code prefixes (`DCGS*`) were being incorrectly filtered out |
| Drop 4,369 rows with null Description | Inspection revealed these are inventory adjustments (all zero-price, null customer, extreme quantities) — not customer transactions |
| Drop rows with Price ≤ 0 or Quantity ≤ 0 (after cancellation split) | Data entry errors after cancellations are already isolated |
| Remove exact duplicates | Same invoice + product + quantity + price + timestamp indicates double-insertion, not a second purchase |
| **Keep** rows with missing Customer ID (~25% of data) but flag them | Valid sales for revenue/product analysis; filtered out only when customer segmentation requires known IDs |

**Final dataset:** 1,003,425 clean sales rows across 5,852 known customers, 4,904 unique products, and 43 countries.

## Reproducing This Analysis

1. Download the dataset from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/502/online+retail+ii) (free, no login)
2. Place `online_retail_II.xlsx` in `data/`
3. Run notebooks in order: `01_explore.ipynb` → `02_clean.ipynb` → `03_sql_analysis.ipynb`
4. The cleaning notebook produces `sales_clean.csv` and `cancellations.csv`
5. The SQL notebook loads them into `retail.db` (SQLite) and exports summary tables to `data/tableau_exports/`

Required Python packages: `pandas`, `numpy`, `matplotlib`, `openpyxl`, `sqlalchemy`.

## About the Dataset

The UCI Online Retail II dataset represents transactions from a non-store online retailer based in the UK, primarily selling unique all-occasion giftware. Data covers December 2009 to December 2011. The dataset is widely used in academic and industry data analysis case studies.

---

*This project is a portfolio piece demonstrating end-to-end data analytics skills — from raw data ingestion through interactive dashboarding. Open to feedback or questions.*
└── README.md
