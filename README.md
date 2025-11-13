# HR Analytics Attrition Project 

Synthetix Solutions, a rapidly growing tech company, is experiencing a concerning rise in it's employee turnover rate. The HR leadership team suspects that a combination of factors, including compensation, job satisfaction, and work-life balance, may be contributing to the issue but do not have a clear idea of where attrition is happening. The goal is to use this dataset to uncover key insights on where attrition is occuring within the organization and present actionable recommendations. The insights provided will be instrumental in helping the company’s leadership make data-driven decisions to improve employee retention, boost morale, and build a more stable and productive workforce.

### Business challenge :
Where is attrition happening within synthetix solutions and what demographics are contributing to the attrition? 

**Insights needed to answer the business challenge**
1. What is the attrition and retention rate within Synthetix Solutions?
2. What is the attrition spread among the departments within Synthetix Solutions?
3. Which gender has higher attrition?
4. What job roles are affected by attrition?
5. What year is attrition occuring in the most among these job roles?
6. What are the potential casues of the attrition within the high risk job roles?

<h2>Understanding the dataset:</h2>

This project was simulated using the IBM HR Analytics Employee Attrition & Performance collected from kaggle. The dataset consisted of 1470 rows of employee information. The information within the dataset highlights their info such as age, gender, job role, job level, commute distance etc. Alongside their performance and self rated satisfaction with their job and environment at the company. The tools used for this project were SQL to collect the data needed for the descriptive analysis, and Tableau to create visualizations and build a dashboard to provide clear insights on where is attrition happening within synthetix solutions and what demographics are contributing to the attrition.

---
# Phase 1: Descriptive Analysis: Uncovering Attrition Hotspots

The initial analysis established a baseline for employee attrition

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
| **Job Level 1 Attrition Rate** | 9.72% | This rate quantifies turnover for employees who are under job level 1 within Synthetix Solutions. |
| **Job Level 2 Attrition Rate** | 3.53% | This rate quantifies turnover for employees who are under job level 2 within Synthetix Solutions. |
| **Job Level 3 Attrition Rate** | 2.17% | This rate quantifies turnover for employees who are under job level 3 within Synthetix Solutions.|
| **Job Level 4 Attrition Rate** | 0.34% | This rate quantifies turnover for employees who are under job level 4 within Synthetix Solutions |
| **Job Level 5 Attrition Rate** | 0.34% | This rate quantifies turnover for employees who are under job level 5 within Synthetix Solutions |

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


### Attrition Disparity by Department 
| Metric | Finding | Context |
| :--- | :--- | :--- |
| **Research and Development** |9.04% | This rate quantifies turnover for employees within the research and development department. |
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
# Phase 2: Diagnostic visualization: Showcasing the Attrition Hotspots Within Synthetix Solutions
This diagnostic step conducts a visualization of areas with high attrition within Tableau, identifying areas that would require intervention and further research to influencing employee retention within Synthetix Solutions.

<h2>Data Analysis & Visualization:</h2>
<p align="center">
Identifying Attrition & Retention baseline <br/>

Analysis of employee data utilizing a visual KPI model reveals findings on Synthetix Solutions original headcount. The analsysis then provided insights into the company's retention and turnover rates. With employee attrition under **20%**, this indicates that a majority of employees are satisfied working for Synthetix Solutions.

<p align="center">
<img width="1336" height="159" alt="image" src="https://github.com/user-attachments/assets/8d010454-cb51-42c1-a473-f54be292bba8" />


<p align="center">
Turnover Demographics <br/>

Analyzing attrition by gender and age reveals that male employees are leaving at a higher rate compared to female employees, with ages of departed employees ranging from **28 - 34** years old. Further investigation is required to unvover the drivers behind the high male attrition rate and departures within the specificed age brackey. This provides insights needed for future projects focused on improving retention among male employees.
Attrition among Genders| Attrition among Age Groups
| :---: | :---: |
| <img src="https://github.com/user-attachments/assets/09384b3e-50b8-4aa1-8846-24daca3f0aaa" alt="Input field screenshot" width="350"> | <img src="https://github.com/user-attachments/assets/6874e499-121a-415a-a3c5-e89ac7c6546d" alt="Results output screenshot" width="650"> |




<p align="center">
Trends in car purchases <br/>

Analyzing historical data revealed a pattern in car transactions in the months of August, September and October. From August to September, car transactions increased by 82%, rising from 810 to 1,475 orders, but then fell in October. The same thing happened in both 2022 and 2023, which shows a clear seasonal pattern. This surge suggests late summer is a key window to stock up on SUVs and push promotions when customers are most active.


1|2
| :---: | :---: |
 | <img width="546" height="398" alt="image" src="https://github.com/user-attachments/assets/facb4ff7-3151-4676-a893-d312f80acc49" />|<img width="903" height="256" alt="Screenshot 2025-11-13 at 2 05 33 PM" src="https://github.com/user-attachments/assets/bb31493b-3c90-4fcf-a8bf-e5d29c99d7fb" />




<p align="center">
Car Transactions by Location  <br/>

When breaking down sales by state, states like Texas, Wisconsin, and Arizona came out on top, while others had lower sales performance. Since the dataset is looking at raw transaction numbers, it works well to show which regions are moving cars at a higher pace. Regions such as Texas, Wisconsin and Arizona have transactions greater than 3,400 while the other regions have transactions under 3,399. Highlighting where performance is lowest, provides opportunity to implement a marketing experiment within one of the low performing locations.

<img width="1118" height="400" alt="image" src="https://github.com/user-attachments/assets/736daeaf-16b1-41a2-9e5d-22dd7aab19c5" />
