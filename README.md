# Case Study #1: LinkedIn : Risky Project

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

All information regarding this case has been sourced from [here](https://platform.stratascratch.com/coding/10304-risky-projects?code_type=1). 


***

## Business Task
Identify projects that are at risk for going overbudget. A project is considered to be overbudget if the cost of all employees assigned to the project is greater than the budget of the project.


You'll need to prorate the cost of the employees to the duration of the project. For example, if the budget for a project that takes half a year to complete is $10K, then the total half-year salary of all employees assigned to the project should not exceed $10K. Salary is defined on a yearly basis, so be careful how to calculate salaries for the projects that last less or more than one year.


Output a list of projects that are overbudget with their project name, project budget, and prorated total employee expense (rounded to the next dollar amount).


_HINT: to make it simpler, consider that all years have 365 days. You don't need to think about the leap years._

***

## Entity Relationship Diagram
![image](https://github.com/colinlim84/StrataScratch/blob/main/LinkedIn_ERD.png)

***

## Question and Solution

### Breakdown the task

#### Request
1. Compute duration of each project
2. SUM expense of individual project
3. Get prorates_expense via (project_expense*project_duration) [rounded to next dollar amount]
4. Keep only prorates_expense > budget

#### What's needed?
1. Project Title
2. Budget
3. Prorates_expense

#### Any specific order?
NA

### Solution
````sql

-- 1. Compute duration of each project
with project as (
SELECT 
    id, 
    title, 
    budget, 
    DATEDIFF(end_date, start_date) as project_length
FROM linkedin_projects),

--2. SUM expense of individual project
expense as (
SELECT 
    project_id, SUM(salary) as expense
FROM linkedin_emp_projects a
LEFT JOIN linkedin_employees b
ON a.emp_id=b.id
GROUP BY project_id)

SELECT 
    title, 
    budget, 
    CEILING(expense/365*project_length) as prorates_expense --3. Get prorates_expense via (project_expense*project_duration) [rounded to next dollar amount]
FROM project p
LEFT JOIN expense e
ON p.id=e.project_id
WHERE CEILING(expense/365*project_length)>budget --4. Keep only prorates_expense > budget

````



### Output:
| title | budget | proprates_expense |
| ----------- | ----------- | ----------- |
| Project6 | 41,611 | 63,230 |
| Project9 | 32,341 | 44,691 |
| Project11 | 11,705 | 31,606 |
| Project21 | 24,330 | 57,310 |
| Project22 | 18,590 | 20,090 |
