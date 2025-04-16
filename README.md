
# HR Absenteeism Dashboard – Power BI + SQL Project

## Project Overview

This Power BI project analyzes employee absenteeism using HR data. The goal is to uncover patterns in absentee behavior, identify opportunities for employee wellness programs, and recommend HR policy changes. SQL was used extensively for data cleaning, transformation, and feature engineering before connecting to Power BI for dashboarding.
![Image](https://github.com/user-attachments/assets/ee9a9175-ab61-4064-9fc8-b720a2d4c8e6)
---

## Files Included

- `Absent.csv`: Main HR dataset with absenteeism records.
- `compensation.csv`: Compensation details of employees.
- `Reason.csv`: Mapping of absenteeism reason codes to their descriptions.
- `SQLQuery4.sql`: SQL scripts used to clean, join, and transform the data.
- `employeesabsentproj.pbix`: Power BI file with the final interactive dashboard.

---

## Tools & Technologies Used

- **SQL Server Management Studio (SSMS)**
- **Power BI Desktop**
- **CSV Files as Source Data**
- **SQL Joins, CTEs, CASE Statements**
- **DAX for KPIs and calculated fields**

---

## Data Preparation (SQL)

The entire data transformation process was performed in SQL before connecting to Power BI:

### 1. Data Integration
```sql
-- Join tables to enrich absenteeism data
SELECT * FROM Absenteeism_at_work a
LEFT JOIN compensation b ON a.ID = b.ID
LEFT JOIN Reasons r ON a.Reason_for_absence = r.Number;
```

### 2. Identifying Healthy Employees
```sql
-- Find non-smokers, non-drinkers, healthy BMI, and below-average absence time
SELECT * FROM Absenteeism_at_work 
WHERE Social_drinker = 0 AND Social_smoker = 0 
AND Body_mass_index < 25 
AND Absenteeism_time_in_hours < (
    SELECT AVG(Absenteeism_time_in_hours) FROM Absenteeism_at_work
);
```

### 3. Budget Proposal: Nonsmoker Incentives
```sql
-- Calculate number of nonsmokers to allocate bonus budget
SELECT COUNT(*) AS nonsmokers 
FROM Absenteeism_at_work 
WHERE Social_smoker = 0;
```

### 4. Optimized Query for Power BI
```sql
-- Final transformed table for Power BI dashboard
SELECT 
  a.ID, 
  r.Reason,
  Month_of_absence,
  Body_mass_index,
  CASE 
    WHEN Body_mass_index < 18.5 THEN 'Underweight'
    WHEN Body_mass_index BETWEEN 18.5 AND 25 THEN 'Healthy'
    WHEN Body_mass_index BETWEEN 25 AND 30 THEN 'Overweight'
    WHEN Body_mass_index > 30 THEN 'Obese'
    ELSE 'Unknown'
  END AS BMI_Category,
  CASE 
    WHEN Month_of_absence IN (12,1,2) THEN 'Winter'
    WHEN Month_of_absence IN (3,4,5) THEN 'Spring'
    WHEN Month_of_absence IN (6,7,8) THEN 'Summer'
    WHEN Month_of_absence IN (9,10,11) THEN 'Fall'
    ELSE 'Unknown'
  END AS Season_Names,
  Seasons,
  Day_of_the_week,
  Transportation_expense,
  Education,
  Son,
  Social_drinker,
  Social_smoker,
  Pet,
  Disciplinary_failure,
  Age,
  Work_load_Average_day,
  Absenteeism_time_in_hours
FROM Absenteeism_at_work a
LEFT JOIN compensation b ON a.ID = b.ID 
LEFT JOIN Reasons r ON a.Reason_for_absence = r.Number;
```

---

## Dashboard Features (Power BI)

- **Absenteeism KPIs**: Total hours missed, average BMI, % smokers, % drinkers.
- **Monthly Absence Trend**: Patterns by season and month.
- **BMI & Absenteeism Analysis**: Correlation between BMI category and absentee hours.
- **Smoking & Drinking Analysis**: Impact on productivity and HR cost.
- **Custom Filters**: By Reason for Absence, Education Level, Age Group, and Season.

---

## Key Insights

- Employees who are non-smokers, non-drinkers, and have a healthy BMI tend to take fewer hours off.
- Specific months and seasons show spikes in absenteeism—useful for forecasting.
- Bonus allocation plan proposed based on healthy behavior to improve employee wellness and reduce absenteeism cost.

---


