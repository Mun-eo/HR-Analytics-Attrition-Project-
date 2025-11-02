# HR Analytics Attrition Project 

Synthetix Solutions, a rapidly growing tech company, is experiencing a concerning rise in it's employee turnover rate. The HR leadership team suspects that a combination of factors, including compensation, job satisfaction, and work-life balance, may be contributing to the issue but do not have a clear idea of where attrition is happening. The goal is to use this dataset to uncover key insights on where attrition is occuring within the organization and present actionable recommendations. The insights provided will be instrumental in helping the company’s leadership make data-driven decisions to improve employee retention, boost morale, and build a more stable and productive workforce.

### Business challenge to uncover:
Where is attrition happening within synthetix solutions and what demographics are contributing to the attrition?

<h2>Understanding the dataset:</h2>

This project was simulated using the IBM HR Analytics Employee Attrition & Performance collected from kaggle. The dataset consisted of 1470 rows of employee information. The information within the dataset highlights their info such as age, gender, salary, commute distance etc. Alongside their performance and self rated satisfaction with their job and environment at the company. The tools used for this project were SQL to collect the data needed for the descriptive analysis and conduct descriptive analysis of how attrition looks in the compan Excel to clean, analyze the dataset, SQL to conduct descriptive analysis of how attrition looks in the company, and Tableau to create visualizations and build a dashboard. 



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

Male attrition (10%) is higher than that of female attrition (6%). This makes gender a high-priority segment for further investigation higlighting potential workplace equity issues.

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
<div align="center">
	
### Attrition Disparity by Job Role
| Metric | Finding | Context |
| :--- | :--- | :--- |
| **Laboratory Technician Attrition Rate** | 4.2% | This rate quantifies turnover for the Laboratory Technician role within Synthetix Solutions. |
| **Sales Executive Attrition Rate** | 3.9% | This rate quantifies turnover for the Sales Executive role within Synthetix Solutions. |
| **Research Scientist Attrition Rate** | 3.2% | This rate quantifies turnover for the Research Scientist role within Synthetix Solutions.|
| **Sales Representative Attrition Rate** | 2.2% | This rate quantifies turnover for the Sales Representative role within Synthetix Solutions |

</div>

The job roles with the highest attrition rates are "Laboratory Technician", "Sales Executive", "Research Scientist", and "Sales Representatice. Compared to the other job roles within Synthetix Solutions which have rates below 1%. This requires further investigations on the affecting factors.

**Query**
```SQL
SELECT 
	emp.job_role,
	CAST(SUM(CASE WHEN per.attrition = 'Yes' THEN 1 ELSE 0 END)AS DECIMAL)/(SELECT COUNT(*) FROM hr_employee_info) *100 AS emp_attrition_rate
FROM 
	hr_employee_info emp
LEFT JOIN 
	hr_performance per ON emp.employeeid = per.employeeid 
GROUP BY 
	emp.job_role
ORDER BY 
	emp_attrition_rate DESC
;
```
<div align="center">

### Attrition Disparity by Job Levels 
 Metric | Finding | Context |
| :--- | :--- | :--- |
| **Job Level 1 Attrition Rate** | 9.72% | This rate quantifies turnover for employees  within Synthetix Solutions. |
| **Job Level 2 Attrition Rate** | 3.53% | This rate quantifies turnover for the Sales Executive role within Synthetix Solutions. |
| **Job Level 3 Attrition Rate** | 2.17% | This rate quantifies turnover for the Research Scientist role within Synthetix Solutions.|
| **Job Level 4 Attrition Rate** | 0.34% | This rate quantifies turnover for the Sales Representative role within Synthetix Solutions |
| **Job Level 5 Attrition Rate** | 0.34% | This rate quantifies turnover for the Sales Representative role within Synthetix Solutions |

</div>

There is a higher rate of attrition among employees who are classified at job level 1 reaching up to 9% compared to employees at higher job levels, which doesn't break over 5. This requires further investigations on the affecting factors.

**Query**
```SQL
SELECT 
	emp.job_level,
	CAST(SUM(CASE WHEN per.attrition = 'Yes' THEN 1 ELSE 0 END)AS DECIMAL)/(SELECT COUNT(*) FROM hr_employee_info) *100 AS emp_attrition_rate
FROM 
	hr_employee_info emp
LEFT JOIN 
	hr_performance per ON emp.employeeid = per.employeeid 
GROUP BY 
	emp.job_level
ORDER BY 
	emp_attrition_rate DESC
;
```

<div align="center">

### Attrition Disparity by Overtime
| Metric | Finding | Context |
| :--- | :--- | :--- |
| **Overtime Attrition Rate** | 8.63% | This rate quantifies turnover for employees who work overime within Synthetix Solutions. |
| **No Overtime Attrition Rate** | 7.48% | This rate quantifies turnover for employees who do not work overime within Synthetix Solutions. |

</div>

There is not a large difference in attrition between employees who work overtime versus those who do not

**Query**
```SQL
SELECT 
	overtime,
	CAST(SUM(CASE WHEN attrition = 'Yes' THEN 1 ELSE 0 END)AS DECIMAL)/(SELECT COUNT(*) FROM hr_employee_info) *100 AS emp_attrition_rate
FROM 
	hr_performance 
GROUP BY 
	overtime
;
```

<div align="center">

### Attriton Disparity by Education Level
| Metric | Finding | Context |
| :--- | :--- | :--- |
| **Bachelor's Degree** |6.73% | This rate quantifies turnover for employees who hold an bachelor's degree within Synthetix Solutions. |
| **Master's Degree** | 3.94% | This rate quantifies turnover for employees who hold an master's degree within Synthetix Solutions. |
| **College** | 2.99% | This rate quantifies turnover for employees who have some college experience within Synthetix Solutions. |
| **Below College** | 2.10% | This rate quantifies turnover for employees who do not have an college degree within Synthetix Solutions. |
| **Doctorate Degree** |0.34% | This rate quantifies turnover for employees who hold an doctorate degree within Synthetix Solutions. |

</div>

There is a higher amount of attrition among employees who hold a bachelor's degree compared to the other categories of education level. This requires further investigations on the affecting factors.

**Query**
```SQL
SELECT 
	emp.education,
	CAST(SUM(CASE WHEN per.attrition = 'Yes' THEN 1 ELSE 0 END)AS DECIMAL)/(SELECT COUNT(*) FROM hr_employee_info) *100 AS emp_attrition_rate
FROM 
	hr_employee_info emp
LEFT JOIN 
	hr_performance per ON emp.employeeid = per.employeeid 
GROUP BY 
	emp.education
ORDER BY 
	emp_attrition_rate DESC
```
<div align="center">

### Attrition Disparity by Department 
| Metric | Finding | Context |
| :--- | :--- | :--- |
| **Research and Development** |9.04% | This rate quantifies turnover for employees within the research and development department . |
| **Sales** | 6.25% | This rate quantifies turnover for employees within the sales department. |
| **Human Resources** | 0.81% | This rate quantifies turnover for employees within the human resources department. |

</div>

There is a attrition rate of 9% within the research and development department and a attriton rate of 6% in the sales department with the Human Resources department a atrrition rate of less than 1%. This requires further insights on the factors affecting the attrition rates of the research and development and sales department within Synthetix Solutions.

**Query**
```SQL
SELECT 
	emp.department,
	CAST(SUM(CASE WHEN per.attrition = 'Yes' THEN 1 ELSE 0 END)AS DECIMAL)/(SELECT COUNT(*) FROM hr_employee_info) *100 AS emp_attrition_rate
FROM 
	hr_employee_info emp
LEFT JOIN 
	hr_performance per ON emp.employeeid = per.employeeid 
GROUP BY 
	emp.department
ORDER BY 
	emp_attrition_rate DESC
;
```



---
# Phase 2: Diagnostic Analysis: Uncovering the Attributing Factors to These Attrition Hotspots
This diagnostic step conducts a deep-dive correlation analysis performed in Excel and visualized within Tableau, identifying factors influencing employee turnover within Synthetix Solutions.
