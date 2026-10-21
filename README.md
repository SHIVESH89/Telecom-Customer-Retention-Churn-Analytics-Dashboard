# Telecom Customer Retention & Churn Analytics Dashboard

An interactive Power BI executive dashboard analyzing customer attrition patterns, revenue loss, and contract friction across 7,043 telecom accounts. This project isolates operational churn drivers and quantifies monthly recurring revenue (MRR) at risk to inform proactive retention strategies.

![Dashboard Preview](dashboard_screenshot.png)

---

## 📌 Executive Summary & Key KPIs

| Metric | Value | Business Significance |
| :--- | :--- | :--- |
| **Total Customers** | **7,043** | Total customer base analyzed across DSL, Fiber Optic, and Phone services. |
| **Overall Churn Rate** | **26.5%** | Benchmark churn rate; more than 1 in 4 customers discontinue service. |
| **Total Monthly MRR** | **$456.1K** | Gross Monthly Recurring Revenue generated across all active/churned accounts. |
| **Churned MRR** | **$139.1K** | **30.5%** of monthly revenue is lost due to customer attrition. |

---

## 🔍 Key Analytical Insights

1. **Contract Type is the Primary Driver of Churn:**
   * Customers on **Month-to-month contracts** churn at an alarming **42.7%**, compared to **11.3%** on One-year contracts and **2.8%** on Two-year agreements.
   * Long-term commitments reduce churn risk by over 90%.

2. **First-Year Onboarding Vulnerability:**
   * **47.4%** of customer churn occurs during the first 12 months (`0 - 1 Year` cohort).
   * After surviving the first 24 months, churn drops below 20%, indicating that early onboarding and initial service satisfaction are critical retention inflection points.

3. **Payment Method Friction:**
   * Accounts utilizing **Electronic Check** experience a **45.3% churn rate**, more than double that of automated options (Credit Card auto-pay at **15.2%** and Bank Transfer at **16.7%**).

4. **Service Tier Gaps in Fiber Optic:**
   * **Fiber Optic** accounts demonstrate an unexpectedly high churn rate of **41.9%** (vs. **19.0%** for DSL), primarily concentrated among users who do not have attached **TechSupport** or **OnlineSecurity** add-ons.

---

## 🛠️ Data Pipeline & Transformation (Power Query)

* **Source Dataset:** IBM Telco Customer Churn (`WA_Fn-UseC_-Telco-Customer-Churn.csv`) with 7,043 rows and 21 attributes.
* **Data Quality Handling:** Resolved a data type casting bug where 11 rows with `tenure = 0` contained blank whitespace strings (`" "`) in `TotalCharges`. Replaced whitespace with `0` and coerced the column to `Decimal Number`.
* **Feature Engineering:**
  * **`Tenure Cohort`:** Categorized raw tenure months into business cohorts (`0 - 1 Year`, `1 - 2 Years`, `2 - 4 Years`, `4+ Years`) to analyze retention decay.
  * **`Churn Flag`:** Created a binary flag (`1` for `Yes`, `0` for `No`) for row-level numerical calculations.

---

## 📐 Data Modeling & DAX Formulas

All calculations are encapsulated inside a dedicated `_Measures` table:

```dax
// Total Customer Count
Total Customers = COUNTROWS('WA_Fn-UseC_-Telco-Customer-Churn')

// Total Churned Count
Churned Customers = 
CALCULATE(
    COUNTROWS('WA_Fn-UseC_-Telco-Customer-Churn'), 
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "Yes"
)

// Churn Rate Percentage
Churn Rate = 
DIVIDE([Churned Customers], [Total Customers], 0)

// Monthly Recurring Revenue (MRR)
Total MRR = 
SUM('WA_Fn-UseC_-Telco-Customer-Churn'[MonthlyCharges])

// Revenue Lost Monthly to Churn
Churned MRR = 
CALCULATE(
    [Total MRR], 
    'WA_Fn-UseC_-Telco-Customer-Churn'[Churn] = "Yes"
)
