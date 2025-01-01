# salary_case_study

**Introduction**

In this case study, I will be analyzing a dataset containing information about individuals' salaries, demographics, education levels, job titles, and years of experience to uncover key factors influencing salary trends and potential disparities. By examining the relationships between age, experience, and education, this analysis aims to provide actionable insights for workforce management, career development, and promoting social equity. Understanding these dynamics is critical in today’s data-driven world, where salary information offers valuable insights into economic trends and workforce dynamics.

**Objective**

This analysis aims to uncover salary trends and key factors influencing earnings, such as education, experience, and gender. By identifying these patterns, it provides insights to promote equitable pay practices and guide career growth.

Through a combination of statistical methods and visual exploration, this analysis will address questions like:
  1.	What are the most influential factors driving salary differences?
  2.	How does education level correlate with earning potential?
  3.	Is there evidence of gender pay disparity in this dataset?

**The Data: Exploration and Cleaning**

This dataset comprises 6,704 data points, encompassing key variables such as age, gender, education level, job title, years of experience, and salary. Sourced from surveys, job posting platforms, and publicly available records, it provides a broad and diverse perspective on salary trends. However, potential sampling bias may result in underrepresentation of certain groups or industries, and while the dataset is shared under the Community Data License Agreement – Sharing, Version 1.0, its reliability depends on the quality of its sources. Gaps, inconsistencies in reporting, and the absence of detailed source citations may limit its overall credibility.

Let's explore the dataset to understand its structure and the relationships between different variables.

|Column #|Column Name|Data Tye|Description|
|----|---------------|----------|------------------|
|1 | age | INTEGER | Age of the individual |
|2 | gender | STRING | Gender of the individual |
|3 | education_level | STRING | Highest level of education attained |
|4 | job_title | STRING | Job title or position of the individual |
|5 | years_of_experience | FLOAT | Years of work experience |
|6 | salary | INTEGER | Annual salary of the individual |


Next, I will use SQL in BigQuery to clean and refine the dataset, addressing inconsistencies, duplicates, and missing values to ensure data integrity and accuracy for analysis.

```SQL
--Understand the dataset, review the schema 
SELECT *
FROM salary_annual;

--Check for null or missing values and identify columns with null values
SELECT 
  COUNT(*) - COUNT(age) AS age,
  COUNT(*) - COUNT(gender) AS gender,
  COUNT(*) - COUNT(education_level) AS education_level,
  COUNT(*) - COUNT(job_title) AS job_title,
  COUNT(*) - COUNT(years_of_experience) AS years_of_experience,
  COUNT(*) - COUNT(salary) AS salary
FROM salary_annual;

--Output rows with null values 
SELECT *
FROM salary_annual
WHERE age IS NULL
  OR gender IS NULL
  OR education_level IS NULL
  OR job_title IS NULL
  OR years_of_experience IS NULL
  OR salary IS NULL;
  
--Delete rows with null values in each column and missing years of experience and salary
DELETE FROM salary_annual
WHERE age IS NULL
  AND gender IS NULL
  AND education_level IS NULL
  AND job_title IS NULL
  AND years_of_experience IS NULL
  AND salary IS NULL;

DELETE FROM salary_annual
WHERE years_of_experience IS NULL
  AND salary IS NULL;

DELETE FROM salary_annual
WHERE salary IS NULL;

--Find the most frequent education_level for a Developer in order to replace the null value
SELECT education_level, COUNT(*) AS education_count
FROM salary_annual
WHERE job_title = 'Developer'
GROUP BY education_level
ORDER BY education_count DESC;

--Unfortunately, there is only 1 developer in the whole dataset. Unable to replace the value, so remove the row with the null value
DELETE FROM salary_annual
WHERE education_level IS NULL;

--Identify logical inconsistencies by standardizing categorical data with spelling and formatting for gender column
SELECT DISTINCT gender
FROM salary_annual;

--Delete the 14 rows with the gender listed as "Other" because the small amount of data in this category does not provide enough representation to allow for a meaningful or accurate analysis 
DELETE FROM salary_annual
WHERE gender = 'Other';

--Identify logical inconsistencies by standardizing categorical data with spelling and formating for education_level column
SELECT DISTINCT education_level
FROM salary_annual;

--Modify education_level data. Bachelor's = Bachelor's Degree, Master's = Master's Degree and phD = PhD
UPDATE salary_annual
SET education_level = "Bachelor's Degree"
WHERE education_level = "Bachelor's";

UPDATE salary_annual
SET education_level = "Master's Degree"
WHERE education_level = "Master's";

UPDATE salary_annual
SET education_level = 'PhD'
WHERE education_level = 'phD';

--Identify logical inconsistencies by standardizing categorical data with spelling and formatting for education_level column
SELECT DISTINCT job_title
FROM salary_annual;

--Since there are many job_title categories, count the job titles and determine if they need modifying
SELECT job_title, COUNT(job_title) as job_title_count
FROM salary_annual
GROUP BY job_title_count ASC;

--Validate data ranges. Verify numerical values are within realistic bounds
SELECT *
FROM salary_annual
WHERE age < 18 OR age > 65
  OR years_of_experience < 0
  OR years_of_experience > age
  OR salary < 0;

--Delete rows where salary is less than 1000 annually
DELETE FROM salary_annual
WHERE salary < 1000

--Reveiw, verify data and export clean sheet
CREATE TABLE 'salary_annual_cleaned' AS
SELECT *
FROM salary_annual
```

**Analyze & Share**

By utilizing SQL for detailed data analysis and Tableau for visualizations, I uncovered valuable insights into salary trends driven by education, experience, and gender.

Here is the correlation between salary and key factors such as education, experience, and age using SQL for statistical calculations.
```SQL
--Measure the linear relationship between each variable and salary to identify influencing factors to salary
SELECT 
  ROUND(CORR(years_of_experience, salary), 2) AS experience_salary_corr,
  ROUND(CORR(age, salary), 2) AS age_salary_corr,
  ROUND(CORR(
    CASE 
      WHEN education_level = 'High School' THEN 1
      WHEN education_level = "Bachelor's Degree" THEN 2
      WHEN education_level = "Master's Degree" THEN 3
      WHEN education_level = 'Phd' THEN 4
      ELSE NULL
    END, salary),2) AS education_salary_corr
FROM salary_annual;
```
![image](https://github.com/user-attachments/assets/d20ffd30-2e97-4ffc-b380-14fe3cded9a9)

![](https://github.com/marvin-gomez/salary_case_study/blob/5bb24054180f36b127e9b6452bb6760f882101cb/Data%20Visuals/Plot%20Age%20to%20salary.png)

![](https://github.com/marvin-gomez/salary_case_study/blob/5bb24054180f36b127e9b6452bb6760f882101cb/Data%20Visuals/Plot%20Yrs%20Exp%20to%20salary.png)

Experience and age show the strongest correlation to salary, as highlighted in these scatter plot graphs with linear trendlines, illustrating how earnings increase predictably with greater experience and age.

The distribution of salaries across different education levels highlights how earning potential varies with educational attainment.
```SQL
--Salary Average, Min, Max and STDDEV distribution by Education Level
SELECT education_level,
  AVG(salary) AS avg_salary,
  MIN(salary) AS min_salary,
  MAX(salary) AS max_salary,
  STDDEV(salary) AS salary_std_dev
FROM daprojects-2025.salary_data.salary_annual
GROUP BY education_level
ORDER BY avg_salary DESC;
```
![image](https://github.com/user-attachments/assets/6a0c8fc3-497d-43c3-8024-6c27594009b7)

![](https://github.com/marvin-gomez/salary_case_study/blob/4a8afaccf147e6f252c58e67c1f37d9d525ac579/Data%20Visuals/BoxPlot%20salary%20at%20edu.png)

This box plot shows how salaries vary across education levels, with higher degrees like Master's and PhDs leading to higher median salaries and broader ranges. However, some outliers from lower education levels, such as High School or Bachelor's, still earn as much as individuals with advanced degrees, highlighting unique exceptions.

Discrepancies in salaries between genders, with a focus on how earnings differ based on education levels, experience, and job types.
```SQL
--Average salary differences between genders
SELECT 
  gender, 
  AVG(salary) AS avg_salary, 
  COUNT(*) AS count
FROM daprojects-2025.salary_data.salary_annual
GROUP BY gender
ORDER BY avg_salary DESC;
```
![image](https://github.com/user-attachments/assets/26d06f7e-8a6e-44ef-bb1f-3fcc57fbfb05)

![](https://github.com/marvin-gomez/salary_case_study/blob/1874bb13627aeaffaf30bdcf68013bd14e3de825/Data%20Visuals/Bar%20Gender%20salary%20at%20edu.png)

Despite similar educational attainment, this graph reveals a gender salary discrepancy, with males consistently earning higher average salaries than females across all education levels.

Diving deeper, I need to analyze how other variables, such as experience and job type, contribute to these gender salary discrepancies.
```SQL
SELECT 
  education_level,
  ROUND(AVG(CASE WHEN gender = 'Male' THEN salary ELSE NULL END),2) AS avg_salary_male,
  ROUND(AVG(CASE WHEN gender = 'Male' THEN years_of_experience ELSE NULL END),2) AS avg_experience_male,
  ROUND(AVG(CASE WHEN gender = 'Male' THEN age ELSE NULL END),2) AS avg_age_male,
  COUNT(CASE WHEN gender = 'Male' THEN 1 ELSE NULL END) AS count_male,
  ROUND(AVG(CASE WHEN gender = 'Female' THEN salary ELSE NULL END),2) AS avg_salary_female,
  ROUND(AVG(CASE WHEN gender = 'Female' THEN years_of_experience ELSE NULL END),2) AS avg_experience_female,
  ROUND(AVG(CASE WHEN gender = 'Female' THEN age ELSE NULL END),2) AS avg_age_female,
  COUNT(CASE WHEN gender = 'Female' THEN 1 ELSE NULL END) AS count_female
FROM daprojects-2025.salary_data.salary_annual
GROUP BY education_level
ORDER BY education_level, avg_salary DESC;
```
![image](https://github.com/user-attachments/assets/b8eeff5b-415b-4e9f-bf13-6667d49044d8)

![](https://github.com/marvin-gomez/salary_case_study/blob/1874bb13627aeaffaf30bdcf68013bd14e3de825/Data%20Visuals/Bar%20Gender%20salary%20at%20age%20and%20exp.png)

This chart illustrates that age and years of experience contribute to higher salaries, which may explain why males tend to earn more than females at the same education level, as seen in the previous chart. The trend suggests that males might have, on average, slightly more experience and age within each education category, contributing to the observed salary disparities.





