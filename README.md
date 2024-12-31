# salary_case_study
uncover salary trends and key factors influencing earnings

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

