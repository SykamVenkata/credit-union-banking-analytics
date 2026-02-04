# Credit Union Banking Analytics (SQL + Power BI)

This project showcases an end-to-end analytics workflow using **SQL + Python (data analysis & validation)** and **Power BI (data modeling, DAX, dashboards)** on a realistic credit union banking dataset.

---

## Business Context

A mid-to-large **US Credit Union** wants trusted, consistent reporting across:
- Member growth & engagement
- Deposits and balances
- Consumer lending performance
- Delinquency / risk trends
- Branch productivity

The objective is to produce **audit-friendly KPIs** and interactive dashboards that leadership can use for strategic and operational decisions.

---

## Key Business Questions

1. How is **member growth** trending MoM / YoY?
2. Which **loan products** drive portfolio growth and which drive delinquency risk?
3. How are **deposits** trending by branch and product?
4. Which branches are top / bottom performers and why?
5. Are KPIs consistent across layers (source vs curated vs BI)? (reconciliation)

---

## KPIs (Examples)

- Total Members, New Members (MTD/YTD)
- Total Deposits, Avg Balance, Deposit Growth %
- Total Loan Balance, Avg Loan Balance
- Delinquency Rate %, NPL %
- Branch Productivity Index

---

## Data Model

Star-schema style model with facts and dimensions.

**Facts**
- `fact_membership_monthly`
- `fact_deposits_daily`
- `fact_loans_monthly`
- `fact_transactions`

**Dimensions**
- `dim_member`
- `dim_branch`
- `dim_product`
- `dim_date`

---

## What’s Included

### SQL (Analysis + Validation)
- KPI queries (growth, balances, delinquency)
- Window functions for trends & running totals
- Ranking (top/bottom branches/products)
- Data quality checks (nulls, duplicates, grain issues)

### Python (EDA)
- Profiling (nulls, duplicates, distributions)
- Outlier checks
- Reconciliation support for KPI validation

### Power BI
- Semantic model (relationships, measures)
- DAX measures for KPIs
- Drill-through, tooltips, bookmarks
- Optional: branch-level RLS design

---

## Dashboards (Planned)

1. **Executive Overview**: Members, Deposits, Loans, Trend KPIs  
2. **Lending & Risk**: Delinquency, NPL, product risk breakdown  
3. **Branch Performance**: Ranking, contribution, productivity  

Screenshots will be stored in `/docs`.

---

## Repo Structure

data/ # CSV dataset (or generator)
sql/ # SQL queries (analysis + validation)
python/ # notebooks for EDA and checks
powerbi/ # PBIX file
dax/ # DAX measures (organized by theme)
docs/ # screenshots + KPI definitions
