# HR Workforce Dynamics & Attrition Cost Analysis

![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Data_Analysis_Expressions-blue?style=for-the-badge)
![Excel](https://img.shields.io/badge/Microsoft_Excel-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

An executive Power BI analytics portfolio project evaluating organizational turnover dynamics, flight-risk exposure, and the financial impact of workforce attrition using the IBM HR Analytics dataset and SHRM replacement cost benchmarks.

---

## Dashboard Preview

![Executive Dashboard Preview](assets/dashboard_preview.png)

---

## Executive Summary & Core Metrics

Organizational attrition is commonly analyzed as a simple operational percentage. This project bridges human capital operations and corporate finance by modeling the true replacement cost across seniority levels (encompassing direct recruitment, onboarding friction, and lost productivity).

* **Total Workforce Headcount:** 1,470 employees
* **Active Headcount:** 1,233 employees
* **Total Departures:** 237 exits
* **Baseline Attrition Rate:** 16.12%
* **Total Turnover Financial Impact:** **$11.27M ($11,267,943)**
* **Identified Flight-Risk Employees:** 9 active personnel currently exhibiting high-risk flight criteria

---

## Key Business Insights

1. **The Early-Tenure Flight Cliff:**
   * **42% of all company exits occur within the first 2 years** of employment (`0–1 year` and `1–2 years`). 
   * This points to early onboarding friction, mismatched role expectations, or initial ramp-up dissatisfaction rather than late-career exits.

2. **The Overtime Burnout Driver:**
   * Employees working regular overtime experience an attrition rate of **30.53%**, compared to only **10.44%** among non-overtime staff.
   * Overtime workload nearly triples the exit rate and accounts for **74.52% of total departure exposure**.

3. **High-Cost Concentration in Sales:**
   * While Laboratory Technicians lead in absolute exit counts (62 exits), **Sales Executives represent $4.63M (41.1%)** of the company's total financial loss.
   * Mid-level and senior replacement costs amplify financial exposure, making retention in the Sales division the highest-ROI opportunity.

---

## Data Model & Architecture

The analytical model uses a clean **Star Schema** linking employee records with external financial benchmarks:

* **Fact Table (`employee_data`):** 1,470 rows containing demographic records, job role, department, tenure, overtime status, compensation, and exit flags.
* **Dimension Table (`job_levelcost`):** Seniority replacement benchmark table linked to `employee_data[JobLevel]` via a **1-to-Many** relationship.
  * **Level 1 (Entry):** 0.50x Annual Salary
  * **Level 2 (Junior):** 0.75x Annual Salary
  * **Level 3 (Mid-Level):** 1.00x Annual Salary
  * **Level 4 (Senior):** 1.25x Annual Salary
  * **Level 5 (Executive):** 1.50x Annual Salary

---

## Key DAX Measures

### 1. Volume & Baseline Metrics
```dax
Total Headcount = COUNTROWS(employee_data)
```
```dax
Total Departures = SUM(employee_data[Attrition flag])
```
```dax
Active Employees = [Total Headcount] - [Total Departures]
```
```dax
Attrition Rate % = DIVIDE([Total Departures], [Total Headcount], 0)
```

### 2. Financial Impact Model
Calculates turnover loss by multiplying each departing employee's annual compensation by their seniority-specific cost multiplier:
```dax
Turnover Financial Impact = 
SUMX(
    FILTER(employee_data, employee_data[Attrition flag] = 1),
    (employee_data[MonthlyIncome] * 12) * RELATED(job_levelcost[CostMultiplier])
)
```

### 3. Proactive Flight-Risk Filter
Identifies currently active staff exhibiting compounding risk factors:
```dax
High Risk Employees = 
CALCULATE(
    COUNTROWS(employee_data),
    employee_data[Attrition flag] = 0,
    employee_data[OverTime] = "Yes",
    employee_data[YearsSinceLastPromotion] >= 3,
    employee_data[JobSatisfaction] IN {"Low", "Medium"},
    employee_data[income_band] IN {"Low (< $3,000)", "Medium ($3,000–$6,000)"}
)
```

---

## Department & Role Vulnerability Matrix

| Job Role | Total Headcount | Departures | Attrition Rate % | Turnover Financial Impact |
| :--- | :---: | :---: | :---: | :---: |
| **Sales Executive** | 326 | 57 | 17.48% | $4,632,696.00 |
| **Manager** | 102 | 5 | 4.90% | $1,359,012.00 |
| **Laboratory Technician** | 259 | 62 | 23.94% | $1,194,513.00 |
| **Healthcare Representative** | 131 | 9 | 6.87% | $898,587.00 |
| **Research Scientist** | 292 | 47 | 16.10% | $812,439.00 |
| **Manufacturing Director** | 145 | 10 | 6.90% | $807,858.00 |
| **Research Director** | 80 | 2 | 2.50% | $698,238.00 |
| **Sales Representative** | 83 | 33 | 39.76% | $474,474.00 |
| **Human Resources** | 52 | 12 | 23.08% | $390,126.00 |
| **Total** | **1,470** | **237** | **16.12%** | **$11,267,943.00** |

---

## Strategic HR Recommendations

1. **Protect the Sales Core:** Restructure retention bonuses and commission tiers for Sales Executives; curbing attrition in this single title directly addresses 41% of turnover cost exposure.
2. **Onboarding Restructure (0–24 Months):** Overhaul early-career development with formal 90-day check-ins and peer mentoring to flatten the 42% early tenure flight cliff.
3. **Cap Chronic Overtime:** Rebalance workload allocations to mitigate burnout, aiming to compress the 30.5% overtime attrition rate toward the 10.4% baseline.
4. **Direct High-Risk Interventions:** Implement targeted one-on-one stay interviews with the 9 active high-risk personnel to resolve compensation or promotion bottlenecks before exit costs occur.

---

## How to Run This Project

1. Clone this repository:
   ```bash
   git clone [https://github.com/](https://github.com/)<your-username>/hr-attrition-cost-analysis.git
   ```
2. Open the dataset CSV files in the `data/` folder.
3. Launch Microsoft Power BI Desktop and open the report file (`.pbix`).
4. If prompted to refresh data sources, point the directory path to your local `data/` folder.
