# Introduction
This project is intended to explore the data job maket, focusing primarily on Data Analyst roles in the UK. This project explores the top paying jobs, the top paying job skills, skills with the highest demand, top paying skills and the most optimal skills

SQL Quesries: [project_sqlfolder](/project_sql/)
# Background
The data job market has experienced significant growth in recent years, driven by the increasing reliance on data-driven decision-making across industries. Among the various roles in this sector, Data Analyst positions are particularly critical, serving as the bridge between data collection and actionable insights. Understanding the dynamics of this job market, especially in specific regions such as the United Kingdom, is essential for both job seekers and employers.

 1.	**Top Paying Jobs**: Identifying which Data Analyst roles offer the highest salaries. This involves analyzing job postings to determine which positions are most lucrative and understanding the factors that contribute to higher pay, such as industry, company size, and location.

 2.	**Top Paying Job Skills**: Exploring the specific skills that command the highest salaries in the Data Analyst market. This includes examining which technical and non-technical skills are most sought after by employers and how proficiency in these skills impacts earning potential.

 3.	**Skills with the Highest Demand**: Investigating which skills are most frequently requested in Data Analyst job postings. Understanding demand trends helps job seekers focus on acquiring the most marketable skills and assists employers in identifying critical skill gaps within their organizations.
       
 4.	**Top Paying Skills**: Beyond just demand, this project examines which skills not only are in high demand but also lead to higher salaries. This analysis helps in identifying the most valuable skills in the job market, offering insights into which competencies provide the greatest return on investment for both job seekers and employers.
       
 5.	**Most Optimal Skills**: Determining the optimal skills that balance high demand and high pay. This involves a strategic analysis of the skills landscape to pinpoint which skills offer the best opportunities for career advancement and salary growth.

# Tools I used

- **SQL**: Utilized for querying and managing data within relational databases, enabling complex data analysis and retrieval.
- **PostgreSQL**: A powerful, open-source relational database system used to store and manage the project’s data securely and efficiently.
- **Visual Studio Code**: A versatile code editor employed for writing and debugging SQL queries and other project-related code, enhancing productivity with its robust features and extensions.
- **Git & GitHub**: Version control tools used for tracking changes, collaborating with others, and managing the project’s source code repository, ensuring efficient and organized development.


# The analysis

### 1. Top Paying Data Analyst Jobs

To find the highest paying roles, I filtered data analyst positions by average yearly salary and location, focusing on job postings in the United kingdon. This query hihglights those high paying opportunities:
```sql
SELECT 
    job_id,
    job_title,
    job_location, 
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name

FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id

WHERE job_title = 'Data Analyst' AND 
job_location = 'United Kingdom' AND 
salary_year_avg IS NOT NULL

ORDER BY salary_year_avg DESC

LIMIT 10;
```

![Top Paying Roles](assets/image.png)

### Findings with Explanation

    •	Nominet:
    •	Job ID: 227038
    •	Average Salary: £77,017.5
    •	Nominet offers the highest average salary among the listed Data Analyst positions, indicating that this company values this role highly, possibly due to the complexity or critical nature of the tasks involved.
    •	Job Schedule: Full-time and Temp work
    •	The job schedule is flexible, including both full-time and temporary work options, which might appeal to a broader range of candidates.

    •	GWI:
    •	Job ID: 166362
    •	Average Salary: £53,014.0
    •	GWI offers a mid-range salary for Data Analysts, which is substantial but significantly lower than Nominet’s offering. This suggests a different level of responsibility or company budget allocation for this role.•	Job Schedule: Full-time
    •	The job is full-time, indicating a stable, long-term position which could attract candidates looking for job security.

    •	Humanity:
    •	Job ID: 1185135
    •	Average Salary: £30,000.0
    •	Humanity offers the lowest average salary in this comparison. This might reflect either an entry-level position, fewer required qualifications, or budget constraints.
    •	Job Schedule: Full-time
    •	Despite the lower salary, the job is full-time, providing a consistent work schedule for the employee.

### 2.Top Paying Job Skills

To find the highest paying jobs with the skills associated with them, I used the the first query to filter out the highest paying jobs and joined that data with the skills table to hihglight which skills were the most sought out for those jobs. This query highlights those findings: 

``` sql
WITH top_paying_jobs AS ( 

    SELECT 
        job_id,
        job_title,
        salary_year_avg,
        job_posted_date,
        name AS company_name

    FROM job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id

    WHERE job_title = 'Data Analyst' AND 
    job_location = 'United Kingdom' AND 
    salary_year_avg IS NOT NULL

    ORDER BY salary_year_avg DESC

    LIMIT 10
)


SELECT top_paying_jobs.*,
skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC 
    
```

![Top Paying Job Skills](assets/top_paying_jobs_skill.png)

### Findings with Explanation

	•   Nominet offers the highest average salary (£77,017.5) and requires skills in SQL, Python, and R.

	•	GWI offers a mid-range salary (£53,014.0) and requires a diverse set of skills including SQL, Python, GCP, Express, Linux, Terminal, and Jira.

	•	Humanity offers the lowest salary (£30,000.0) and requires skills in SQL, Excel, and Tableau.

 ### 3. Top Demanded Skills

 To find the highest demanded skill within the data job market I decided to use the COUNT agregate function to filter the demand and utilizing the skills data from the join in my last query. This query highlights demand per skill: 

```sql
  SELECT
   skills,
   COUNT (skills_job_dim.job_id) AS demand_count

   FROM job_postings_fact

INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id

WHERE job_title_short = 'Data Analyst' AND 
    job_location = 'United Kingdom'

GROUP BY 
skills_dim.skills

ORDER BY demand_count DESC
LIMIT 5;
```

![Top Demanded Skills](assets/top_demanded_skills.png)

### Findings with Explanation

	1.SQL:
	•	Demand Count: 867
	•	SQL is the most in-demand skill for Data Analysts in the UK, indicating its critical role in managing and querying databases.
	2.Excel:
	•	Demand Count: 776
	•	Excel remains a highly demanded skill, essential for data manipulation, analysis, and reporting.
	3.	Power BI:
	•	Demand Count: 557
	•	Power BI is a popular business analytics tool, highlighting the need for data visualization and business intelligence capabilities.
	4.Python:
	•	Demand Count: 455
	•	Python is a versatile programming language, valued for its applications in data analysis, machine learning, and automation.
	5.Tableau:
	•	Demand Count: 361
	•	Tableau, another key data visualization tool, though less in demand compared to Power BI, is still significant for its advanced visual analytics capabilities.

### 4. Top Paying Skills

To find the skills with the highest salary, I had to find the average yearly salary and associate that data with the skills that correspond with them. I filtered for specifically Data Analyst role that are found in the UK. This query highlights those top paying skills: 
```sql
SELECT
   skills,
   ROUND (AVG(salary_year_avg),0) AS avg_salary

   FROM job_postings_fact

INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id

WHERE job_title_short = 'Data Analyst' 
    AND salary_year_avg IS NOT NULL
    AND job_location = 'United Kingdom'

GROUP BY 
skills

ORDER BY avg_salary DESC
LIMIT 25;
```
![Top Paying Skills](assets/top_paying_skills.png)

### Findings with Explanation

    The provided data showcases the average salaries associated with various skills. Here’s a summary of the key findings:

	1.	Top Paying Skills:
	•	Shell and Flow lead the list with an average salary of $156,500.
	•	Looker is next with $118,140, followed by SAS at $109,000.
	2.	Mid-Range Salaries:
	•	Skills like Express ($104,757), Jupyter ($103,620), Unify and Git (both $89,100), and SAP, Azure, and Databricks (each $86,400) fall into this category.
	•	Python ($83,968) and Excel ($82,494) also feature prominently.
	3.	Lower Range Salaries:
	•	Skills like SQL ($65,818), Terminal, Linux, and Jira (each $53,014) are on the lower end of the salary spectrum.


### 5. Optimal Skills

To find the most optimal skills it was important that I utilize the highest paying skills query and the skill that is highest in demand query. This required using CTEs to combine both those queries while filtering for Data Analyst roles in the UK. This query highlights the most optimal skills to have: 
```sql
WITH skill_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM 
        job_postings_fact
    INNER JOIN 
        skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN 
        skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst' 
        AND salary_year_avg IS NOT NULL
        AND job_location = 'United Kingdom'
    GROUP BY 
        skills_dim.skill_id,
        skills_dim.skills
), 
average_salary AS (
    SELECT
        skills_job_dim.skill_id,
        skills_dim.skills,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM 
        job_postings_fact
    INNER JOIN 
        skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN 
        skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst' 
        AND salary_year_avg IS NOT NULL
        AND job_location = 'United Kingdom'
    GROUP BY 
        skills_job_dim.skill_id,
        skills_dim.skills
)
SELECT 
    skill_demand.skill_id,
    skill_demand.skills,
    demand_count,
    avg_salary
FROM 
    skill_demand
INNER JOIN 
    average_salary ON skill_demand.skill_id = average_salary.skill_id

    ORDER BY demand_count DESC,
    avg_salary DESC

    LIMIT 25;
```
![Optimal Skills](assets/new_top_paying_skills.png)

### Findings with Explanation

    The provided data presents the demand counts and average salaries for various skills. Here’s a summary:

	1.	High Demand Skills:
	•	Excel has the highest demand with 11 job listings, accompanied by an average salary of $82,494.
	•	Python and SQL both have 8 job listings, with average salaries of $83,968 and $65,818 respectively.
	2.	Moderate Demand Skills:
	•	Tableau is in moderate demand with 5 job listings and an average salary of $78,428.
	•	Power BI, Outlook, and Word each have 3 job listings, with respective average salaries of $74,563, $46,171, and $46,171.
	3.	Low Demand but High Salary Skills:
	•	Shell and Flow have only 1 job listing each, but they command the highest average salary of $156,500.
	•	Looker also has 1 job listing with an impressive average salary of $118,140.
	•	SAS, Express, and Jupyter follow with high salaries but low demand.

This data highlights that while some skills like Excel, Python, and SQL are in high demand, others like Shell and Flow offer significantly higher salaries despite lower demand. This suggests that specialized skills may command higher salaries even with fewer job openings. ￼

# What I learned

### Complex Queries

One of the key areas I focused on was mastering complex queries, which included:

	•	Table Merges: I extensively practiced merging tables using INNER JOIN to combine data from multiple sources effectively.

	•	Common Table Expressions (CTEs): By utilizing WITH clauses, I created CTEs to simplify and structure my queries better. This approach allowed for improved readability and maintainability.

### Aggregation

I also delved deeply into aggregation techniques, including:

	•	Aggregate Functions: I used functions such as COUNT() and AVG() to summarize and derive meaningful insights from large datasets. These functions enabled me to quickly compute statistics and metrics that are crucial for data analysis.

	•	GROUP BY Clause: After applying aggregate functions, I employed the GROUP BY clause to organize the results based on specific columns. This was essential for generating grouped summaries and understanding data distribution across different categories.

### Analytics

In the analytics domain, I aimed to apply my SQL skills to real-world problem-solving scenarios:

	•	Real-World Problem Solving: I designed and executed queries to answer the questions that I formulared, such as identifying trends and extracting actionable insights from raw data. This required a combination of logical thinking and practical SQL knowledge.




# Conclusions



### Insights

1. **Top Paying Data Analyst Jobs**: Nominet offers the highest average salary (£77,017.5) in the UK among the listed Data Analyst positions. This suggests that Nominet places a high value on this role, likely due to the complexity and critical importance of the tasks Data Analysts are required to perform within the company.
2. **Skills for Top Paying Jobs**: Nominet offers the highest average salary (£77,017.5) and requires skills in SQL, Python, and R.
3. **Top Demanded Skills** : SQL, Excel, and Power BI are the top three in-demand skills for Data Analysts in the UK, highlighting the critical importance of database management, data manipulation, and data visualization.
4. **Top Paying Skills**: The top paying skills for Data Analysts include Shell and Flow, both commanding an impressive average salary of $156,500. Looker follows with $118,140, and SAS at $109,000. Mid-range salaries are seen for skills such as Express ($104,757), Jupyter ($103,620), Unify and Git (both $89,100), and SAP, Azure, and Databricks (each $86,400), with Python ($83,968) and Excel ($82,494) also standing out. On the lower end of the salary spectrum, skills like SQL ($65,818), Terminal, Linux, and Jira (each $53,014) are observed.
5. **Optimal Skills**: Excel has the highest demand with 11 job listings and an average salary of $82,494, while Python and SQL both have 8 job listings, with average salaries of $83,968 and $65,818 respectively.

### Final Take-Away 
By integrating these advanced SQL techniques into my project, I not only solidified my understanding of intermediate SQL but also significantly improved my ability to analyze and manipulate data in a meaningful way. This project has been instrumental in honing my SQL skills and preparing me for more complex data challenges in the future.