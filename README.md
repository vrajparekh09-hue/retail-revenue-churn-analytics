# Retail Revenue, Churn & Profitability Analytics

An end-to-end Python analysis of a retail / e-commerce business: forecasting revenue, predicting which customers will churn, measuring customer value, and finding where profit actually comes from.

**Notebook:** `retail_operation.ipynb` · **Platform:** Google Colab (Python 3.10+) · **Author:** Vraj

---

## Business Problem

A retail business wants to know where its revenue is heading, who is about to stop buying, and which customers and products are worth investing in. This project answers 15 business questions across four themes:

| Theme | Questions answered |
|---|---|
| Revenue forecasting | What is the expected revenue for the next 12 months? Is there seasonality? What is the growth trend? |
| Churn | Which customers are likely to churn? What drives it? What does it cost? |
| Cohorts & value | Which cohorts retain best? How does CLV vary by segment? How long is the payback period? |
| Segmentation | Are there distinct customer groups? Which are most profitable? Where should resources go? |

## Dataset

Synthetic retail data (1,000 customers, Turkish cities and regions, orders from Jan 2021 to Jun 2026), loaded from CSV files:

| File | Contents |
|---|---|
| `customers.csv` | CustomerID, gender, age, city, region, segment (Standard / Premium / VIP), sign-up date |
| `orders.csv` | OrderID, CustomerID, OrderDate |
| `order_details.csv` | Line items: quantity, unit price, unit cost, discount rate, return flag |
| `products.csv` | Product and category information |

Derived tables built in the notebook: a line-item transactions table (revenue, cost, profit per line), a per-customer summary (revenue, profit, order count, recency, churn flag), and a monthly revenue series.

**Key definitions**
- **Revenue** = Quantity × UnitPrice × (1 − DiscountRate)
- **Profit** = Revenue − (Quantity × UnitCost)
- **Churned customer** = no order in the 90 days before the dataset's last order date (2026-06-01)

## Methods

1. **Data preparation & EDA**: joining the relational tables, feature derivation, missing-value checks, initial visuals
2. **Time series forecasting**: decomposition, ADF stationarity test, ACF/PACF, ARIMA grid search (AIC), Facebook Prophet, Holt-Winters exponential smoothing, train/test comparison
3. **Churn prediction**: feature engineering, Logistic Regression vs Random Forest vs Gradient Boosting, feature importance, customer risk tiers
4. **Cohort & retention analysis**: monthly retention matrix, revenue cohorts, RFM segmentation, CLV and payback period
5. **Profitability analysis**: segment, category and regional profitability using real cost data, K-Means clustering (K=5)
6. **Executive reporting**: KPI summary, written executive summary, one-page dashboard

## Key Findings

**Revenue & profit**
- Total revenue is **$761,951** with **$211,629 gross profit** (27.8% margin), across 1,000 customers.
- Seasonality is weak (strength 0.33) while the trend is strong (0.87). The series is non-stationary and becomes stationary after one round of differencing.

**Forecasting**
- Best ARIMA model: **ARIMA(2,1,1)**, selected by AIC, with a 12-month forecast of about **$249.9K** (test MAPE 19.5%).
- **Holt-Winters (additive) had the lowest test error** (MAPE 16.1%) and forecasts about **$236.6K**, so the ARIMA figure is likely the optimistic end of the range. Prophet is included as a third view.

**Churn**
- **56.9%** of customers (569) are classed as churned, and they account for **50.8%** of historical revenue.
- Churn rate is nearly identical across segments (Standard 57.2%, Premium 57.1%, VIP 54.3%), so the segment label alone does not explain who leaves.
- Best model: **Logistic Regression, ROC AUC 0.71, accuracy 66.5%**. That is a useful ranking signal, not a precise predictor. Top drivers are customer age (days since sign-up), average revenue per month, and average order value.
- **549 customers** are flagged at-risk, representing roughly **$57K** of annualized revenue.

**Cohorts, RFM & CLV**
- Only about **24%** of a sign-up cohort orders in month 1, and monthly activity then stays flat at roughly 18–25%.
- **VIP** customers have the highest CLV (**$1,848**) versus Premium ($965) and Standard ($487), yet all three segments earn a similar ~28% margin.
- In RFM, the **"At Risk" group (78 customers) holds the most revenue ($107K)**, more than Champions, which makes it the first retention target.

**Products & regions**
- **Meat & Poultry drives 33% of revenue**; Household Cleaning has the best margin (31.4%) and Produce the lowest (24.2%).
- Marmara is the largest region with about 56% of customers and 56% of revenue.

## Assumptions & Limitations

- **CAC of $50 is a placeholder** (no acquisition cost data exists), so the 15.2x CLV:CAC ratio and 6.3-month payback are illustrative only.
- The **90-day churn threshold is an assumption**, not derived from the data, and it directly drives every churn number.
- CLV is historical revenue to date, not a forward-looking predicted value.
- The final month of order data is partial and was excluded from the time series.
- Churn models were trained on 800 customers and tested on 200, so results will vary with the split.
- Data is synthetic, so findings demonstrate the method rather than describe a real business.

## How to Run

1. Open `retail_operation.ipynb` in [Google Colab](https://colab.research.google.com).
2. Upload the four CSV files to Google Drive and update the file paths in the data loading cell (currently `/content/drive/MyDrive/files for python/...`).
3. Install extra libraries in their own cell, then restart the runtime if Colab asks:
   ```python
   !pip install -q prophet lifelines statsmodels
   ```
4. Run all cells top to bottom.

**Libraries:** pandas, numpy, matplotlib, seaborn, scipy, statsmodels, scikit-learn, prophet

## Outputs Generated

| File | Description |
|---|---|
| `financial_viz/` | 16 charts: EDA, decomposition, forecasts, churn, cohorts, RFM, CLV, profitability, final dashboard |
| `at_risk_customers.csv` | Customers ranked by churn probability |
| `rfm_segmentation.csv` | RFM segment for every customer |
| `kpi_summary.txt` | Headline KPIs |
| `EXECUTIVE_SUMMARY_RETAIL.txt` | Written summary for stakeholders |

## About the Author

**Vraj** is an aspiring Business / Data Analyst with a background in Electrical Engineering, working with SQL, Tableau, Power BI , Excel and Python.

- LinkedIn: www.linkedin.com/in/vraj-parekh-67a588271
