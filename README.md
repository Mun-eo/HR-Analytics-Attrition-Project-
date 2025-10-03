# HR Analytics Attrition Project 

Synthetix Solutions, a rapidly growing tech company, is experiencing a concerning rise in its employee turnover rate. The HR leadership team suspects that a combination of factors, including compensation, job satisfaction, and work-life balance, may be contributing to the issue. The goal is to use this dataset to uncover key insights and present actionable recommendations. The insights provided will be instrumental in helping the company’s leadership make data-driven decisions to improve employee retention, boost morale, and build a more stable and productive workforce.

Main challenge:
What factors are affecting employee retention within Synthtix Solutions?

<h2>Understanding the dataset:</h2>

This project was simulated using the IBM HR Analytics Employee Attrition & Performance collected from kaggle. The dataset consisted of 1470 rows of employee information. The information within the dataset highlights their info such as age, gender, salary, commute distance etc. Alongside their performance and self rated satisfaction with their job and environment at the company. The tools used for this project were Excel to clean, analyze the dataset, SQL to conduct descriptive analysis of how attrition looks in the company, and Tableau to create visualizations and build a dashboard. 



<h2>Data Cleaning walk-through:</h2>

1. After exploring the dataset, every employee works a standard of 80 hours for each pay period meaning they work 40 hours in a week.  I noticed the daily and monthly rate for each employee was not accurate for their hourly pay. An example of this is Employee 1 has an hourly rate of $94 but their daily rate was $1,102 which is inaccurate, if they work 8 hours per day. Their daily rate should instead be $752 with their monthly rate being $15,040.
2. The data tables were then exported into SQL to conduct a brief descriptive analysis on attrition rates within the company to uncover where there is a high amount of attrition. This would allow the project to  transfer into excel to find correlations between these areas with high attrition rate and other factors




---
# Phase 1: Descriptive Analysis: Uncovering Attrition Hotspots

The initial analysis established a baseline for employee attrition and identified key demographic segments driving the rate. This diagnostic step informed the subsequent deep-dive correlation analysis performed in Excel.

<div align="center">
   
### Overall Attrition Rate
| Metric | Finding | Context
|--------------|------------|------------|
| Overall Attrition Rate| 16.12% | This baseline rate quantifies the amount of turnover within Synthetix Solutions

</div>
  
**Query**
```SQL
WITH total_attrition AS (
	SELECT
		COUNT (*) AS total_emp_attrition 
	FROM
		hr_performance
	WHERE 
		attrition = 'Yes'
),
total_headcount AS (
	SELECT 
		COUNT(*) AS total_emp_headcount
	FROM
		hr_performance
)

SELECT 
	CAST(ts.total_emp_attrition AS DECIMAL)/th.total_emp_headcount * 100 AS attrition_rate
FROM
	total_attrition ts
CROSS JOIN
	total_headcount th
;
```

<div align="center">
       
### Attrition Disparity by Gender
| Metric | Finding | Context |
| :--- | :--- | :--- |
| **Male Attrition Rate** | 10.20% | This rate quantifies turnover for male employees within Synthetix Solutions. |
| **Female Attrition Rate** | 5.90% | This rate quantifies turnover for female employees, highlighting a significant disparity. |

</div>

**Query**
```SQL
SELECT 
	emp.gender,
	CAST(SUM(CASE WHEN per.attrition = 'Yes' THEN 1 ELSE 0 END)AS DECIMAL)/(SELECT COUNT(*) FROM hr_employee_info) *100 AS emp_attrition_rate
FROM 
	hr_employee_info emp
LEFT JOIN 
	hr_performance per ON emp.employeeid = per.employeeid 
GROUP BY 
	emp.gender
;
```
