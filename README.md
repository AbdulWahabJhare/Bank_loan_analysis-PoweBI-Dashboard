# Bank Loan Performance & Risk Analysis Dashboard

> **Transforming raw financial lending data into actionable risk management insights through SQL analysis and interactive Power BI visualizations.**

---

## 📌 Overview
This project provides an end-to-end analytics solution for bank lending data. By processing raw financial records through SQL and modeling them in Power BI, this repository demonstrates how to track key performance indicators (KPIs), evaluate portfolio health, monitor loan profitability, and assess risk distributions.

---

## 🎯 The Challenge
Financial institutions require clear visibility into their lending portfolios to mitigate default risk and optimize capital deployment. The goal of this analysis was to aggregate high-volume loan application trends, monitor funded amounts, and evaluate borrower creditworthiness to support data-driven lending and risk strategy decisions.

---

## 💡 The Solution
A robust analytical pipeline was built using SQL for data extraction, aggregation, and business logic implementation, followed by Power BI for dynamic modeling and visualization.

### Key Insights & Metrics Extracted
* **Overall Portfolio Health:** Analyzed 38,576 total loan applications representing over **$435 million** in total funded capital.
* **Risk Classification:** Categorized portfolio performance into **Good Loans (86.18%)** vs. **Bad Loans (13.82%)**, measuring total cash received against principal funded for each tier.
* **Borrower Profiles:** Evaluated portfolio health across a **12.05% average interest rate** and a **13.33% average Debt-to-Income (DTI)** ratio.
* **Dynamic Segmentation:** Granularly sliced performance across Month-to-Date (MTD), State, Loan Term, Employment Duration, Loan Purpose, and Homeownership status.

---

## 🛠️ Tools & Technologies Used
* **SQL:** Data querying, CTEs, window functions, and KPI calculation logic (MTD, Previous Month MTD).
* **Power BI:** Star schema data modeling (`bank_loan_dashboard.pbix`), DAX measures, interactive filtering, and dynamic visualization.
* **Excel / CSV:** Source data validation and preliminary analysis.

---

## 📊 Dashboard Preview

![Bank Loan Performance Summary](Bank_loan_project_summary.png)
*Figure 1: High-Level Portfolio Performance & Summary View.*

<br>

![Bank Loan Dashboard Overview](Bank_laon_project_Overview.png)
*Figure 2: Monthly Trends & Key Metric Overview.*

<br>

![Bank Loan Granular Details](Bank_loan_project_Details.png)
*Figure 3: Granular Borrower & Loan Details Breakdown.*

---

## 🛢️ SQL Queries & Business Logic

The core metrics and calculations displayed in the dashboard were structured and validated using SQL Server (T-SQL) queries.

### 1. Key Performance Indicators (KPIs)
```sql
-- Total Applications, MTD, and PMTD
SELECT COUNT(id) AS Total_Applications FROM bank_loan_data;
SELECT COUNT(id) AS MTD_Applications FROM bank_loan_data WHERE MONTH(issue_date) = 12;
SELECT COUNT(id) AS PMTD_Applications FROM bank_loan_data WHERE MONTH(issue_date) = 11;

-- Portfolio Funding & Amount Received
SELECT SUM(loan_amount) AS Total_Funded_Amount FROM bank_loan_data;
SELECT SUM(total_payment) AS Total_Amount_Collected FROM bank_loan_data;

-- Good Loan Metrics (Fully Paid & Current)
SELECT
    (COUNT(CASE WHEN loan_status IN ('Fully Paid', 'Current') THEN id END) * 100.0) / COUNT(id) AS Good_Loan_Pct,
    COUNT(id) AS Good_Loan_Applications,
    SUM(loan_amount) AS Good_Loan_Funded_Amount,
    SUM(total_payment) AS Good_Loan_Amount_Received
FROM bank_loan_data;

-- Bad Loan Metrics (Charged Off)
SELECT
    (COUNT(CASE WHEN loan_status = 'Charged Off' THEN id END) * 100.0) / COUNT(id) AS Bad_Loan_Pct,
    COUNT(id) AS Bad_Loan_Applications,
    SUM(loan_amount) AS Bad_Loan_Funded_Amount,
    SUM(total_payment) AS Bad_Loan_Amount_Received
FROM bank_loan_data;

-- Loan Performance Breakdown by Month
SELECT 
    MONTH(issue_date) AS Month_Number, 
    DATENAME(MONTH, issue_date) AS Month_Name, 
    COUNT(id) AS Total_Applications,
    SUM(loan_amount) AS Total_Funded_Amount,
    SUM(total_payment) AS Total_Amount_Received
FROM bank_loan_data
GROUP BY MONTH(issue_date), DATENAME(MONTH, issue_date)
ORDER BY MONTH(issue_date);


