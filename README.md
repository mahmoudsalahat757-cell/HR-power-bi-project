# HR Analytics Dashboard — Power BI

A 4-page Power BI report analyzing employee attrition, recruitment, performance, compensation, and absenteeism using a star-schema data model built from scratch.

## Business problem

HR wants a single view to answer: Who is leaving, why, and is it linked to pay, performance, or department? This report turns a flat HR export into a queryable model that surfaces attrition drivers, recruitment channel effectiveness, pay-vs-engagement patterns, and absenteeism trends by department.

## Dataset

- Source: [Human Resources Data Set](https://www.kaggle.com/datasets/rhuebner/human-resources-data-set) (rhuebner, Kaggle) — `HRDataset_v14.csv`
- 311 employees across 6 departments, covering hiring, termination, performance, compensation, engagement, and absence data
- Overall attrition rate: 33.4%

## Data cleaning and modeling

The raw file was rebuilt into a star schema rather than used as a flat table:

- **Data quality issue found and fixed:** the source `DeptID` and `PositionID` columns are unreliable — the same ID maps to multiple department/position names due to trailing whitespace and inconsistent source values. Clean surrogate keys were rebuilt from the trustworthy text fields (`Department`, `Position`) instead.
- Whitespace stripped from all text fields (source had trailing spaces causing false duplicate categories)
- Two-digit birth years parsed with century correction (e.g. `07/10/83` correctly resolved to 1983, not 2083)
- Engineered fields: `TenureDays`, `TenureYears`, `IsActive`

### Tables
| Table | Role |
|---|---|
| `Fact_Employment` | One row per employee — salary, dates, performance, engagement, absences |
| `Dim_Employee` | Demographics |
| `Dim_Department` | 6 clean departments |
| `Dim_Position` | Job titles |
| `Dim_Manager` | Manager lookup |
| `Dim_RecruitmentSource` | Hiring channel lookup |
| `Dim_Calendar` | Full date table |

### Relationships
`Dim_Calendar[Date]` connects to three different fact-table date columns — `DateofHire` (active), `DateofTermination` (inactive), `LastPerformanceReviewDate` (inactive) — switched via `USERELATIONSHIP()` in DAX so the same calendar table can drive hiring, turnover, and review-based analysis without duplicating the date dimension.

## DAX measures

Grouped by purpose:

- **Headcount:** `Total Employees`, `Active Employees`, `Terminated Employees`
- **Attrition:** `Attrition Rate`, `Hires in Period`, `Terminations in Period`, `Attrition Rate (Period)` (period-based, using `USERELATIONSHIP`)
- **Tenure & pay:** `Avg Tenure (Years)`, `Avg Salary`, `Median Salary`
- **Absenteeism:** `Total Absences`, `Avg Absences per Employee`, `Rolling 3-Month Absences` (via `DATESINPERIOD` + `USERELATIONSHIP` on review date)
- **Engagement & performance:** `Avg Engagement Score`, `Avg Satisfaction`, `High Performers %`
- **What-if simulation:** `Salary Increase %` (numeric-range parameter, 0–20%), `Projected Avg Salary`, `Projected Attrition Rate` — models a hypothetical pay-elasticity effect on attrition; this relationship is an illustrative modeling assumption, not derived from the data

## Report pages

1. **Overview** — headcount, attrition rate, avg salary, headcount by year and department, performance score distribution, avg tenure by department
2. **Recruitment & Turnover** — hires/terminations/attrition rate by year, termination reasons, recruitment source effectiveness, department × employment status matrix
3. **Performance & Compensation** — salary vs. engagement scatter (per employee), avg salary by position, median salary, high performers %, and the live What-If salary/attrition simulation
4. **Attendance/Absenteeism** — avg absences per employee, absences by department, rolling 3-month absence trend, absences vs. engagement scatter

## Key findings

- Attrition rate: 33.4% overall, varies notably by department
- Average tenure declines steadily from Executive Office (14 years) down to Production (9.4 years)
- Production has by far the highest total absences of any department
- A hypothetical 19% salary increase projects attrition dropping from 33% to 24% under the modeled elasticity assumption (illustrative, not a data-derived relationship)

## Tools

Power BI Desktop, DAX (`CALCULATE`, `USERELATIONSHIP`, `DATESINPERIOD`, What-If parameters), Python/pandas for the ETL and star-schema build.

## Status / next steps

- Report is functionally complete across all 4 pages
- Remaining polish: business-problem text box on the Overview page, consistent slicer styling across pages, final formatting pass before publishing

## Contact

- Email: mahmoudsalah.at757@gmail.com
- LinkedIn: [linkedin.com/in/mahmoud-salah-al](https://www.linkedin.com/in/mahmoud-salah-al)
