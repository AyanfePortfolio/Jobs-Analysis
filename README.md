## Background 
Motivated by the need to navigate the data analyst job market more strategically, this project was developed to identify the most in-demand and highest-paying skills, thereby simplifying the job search process for aspiring professionals.

The dataset utilized for this analysis contains comprehensive information on job titles, salary ranges, geographic locations, and the key skills required across various roles.

The questions to answer through my SQL queries are as follows:
- What are the top paying data analyst jobs?
- What skills are required for these top paying jobs?
- What skills are most in demand for Data Analysts?
- Which skills are associated with higher salaries?
- What are the most optimal skills to learn?

## Tools Utilized 

To conduct a comprehensive analysis of the data analyst job market, I leveraged a combination of essential tools:

- SQL: Served as the foundation of the analysis, enabling efficient querying of the database and extraction of meaningful insights from large datasets.
- Microsoft SQL Server Management Studio (SSMS): Functioned as the primary database management environment, providing a reliable platform for organizing, managing, and analyzing job posting data.
- GitHub: Used for version control and documentation, facilitating seamless tracking of changes, collaboration, and the sharing of SQL scripts and analytical outputs.

## The Analysis 

Each query within this project was designed to examine a specific dimension of the data analyst job market. The following outlines the methodological approach and key insights derived from each analysis:


**1. Top-Paying Data Analyst Roles**

To identify the highest-paying opportunities, I filtered data analyst job postings by average annual salary and location, with a particular focus on remote roles. This approach enabled a clear view of premium compensation trends within the field.

```sql
SELECT TOP 10
    job_id,
    job_title,
    salary_year_avg AS Average_Salary,
    job_posted_date,
    name AS company_name
FROM 
    job_postings_fact
LEFT JOIN 
    company_dim 
    ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_location = 'Anywhere' 
    AND salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
```

The analysis revealed that the top 10 highest-paying data analyst roles in 2023 ranged from $184,000 to $650,000, highlighting substantial earning potential. These opportunities were distributed across a diverse set of organizations, including companies such as SmartAsset, Meta, and AT&T, demonstrating cross-industry demand. Additionally, the variety of job titles from Data Analyst to Director of Analytics reflects the breadth of roles and specialization levels within the profession.

| job_id | job_title                                      | Average_Salary | job_posted_date     | company_name                          |
|--------|------------------------------------------------|----------------|----------------------|----------------------------------------|
| 226942 | Data Analyst                                   | 650000         | 2/20/2023 15:13      | Mantys                                 |
| 547382 | Director of Analytics                          | 336500         | 8/23/2023 12:04      | Meta                                   |
| 552322 | Associate Director- Data Insights              | 255829.5       | 6/18/2023 16:03      | AT&T                                   |
| 99305  | Data Analyst, Marketing                        | 232423         | 12/5/2023 20:00      | Pinterest Job Advertisements           |
| 1021647| Data Analyst (Hybrid/Remote)                   | 217000         | 1/17/2023 0:17       | Uclahealthcareers                      |
| 168310 | Principal Data Analyst (Remote)                | 205000         | 8/9/2023 11:00       | SmartAsset                             |
| 731368 | Director, Data Analyst - HYBRID                | 189309         | 12/7/2023 15:00      | Inclusively                            |
| 310660 | Principal Data Analyst, AV Performance Analysis| 189000         | 1/5/2023 0:00        | Motional                               |
| 1749593| Principal Data Analyst                         | 186000         | 7/11/2023 16:00      | SmartAsset                             |
| 387860 | ERM Data Analyst                              | 184000         | 6/9/2023 8:01        | Get It Recruit - Information Technology|

**2. Skills for Top Paying Jobs **

To determine the competencies associated with high-paying positions, I joined job postings data with corresponding skill requirements. This provided insight into the capabilities most valued by employers offering premium compensation.

```sql
WITH top_paying_jobs AS (

SELECT TOP 10
    job_id,
    job_title,
    salary_year_avg AS Average_Salary,
    name AS company_name
FROM 
    job_postings_fact
LEFT JOIN 
    company_dim 
    ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short = 'Data Analyst' 
    AND job_location = 'Anywhere' 
    AND salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
)

SELECT top_paying_jobs.*, skills 
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY 
Average_Salary DESC;
```

The findings show that SQL is the most prominent skill, Python follows closely. Other relevant skills, including Tableau, Databricks, and Jupyter demonstrate moderate but notable demand.

<img width="490" height="356" alt="image" src="https://github.com/user-attachments/assets/f9ac67f2-455f-4c9f-a2d1-67aad24a4bf0" />


**3.	In-Demand Skills for Data Analysts** 
This analysis focused on identifying the most frequently requested skills across job postings, thereby highlighting core competencies required in the market.

```sql
SELECT TOP 10
Skills, 
COUNT(skills_job_dim.job_id) as Demand_Count 
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short = 'Data Analyst' AND job_no_degree_mention = 'FALSE' AND job_country = 'United States'
GROUP BY Skills 
ORDER BY Demand_Count DESC;
```

The results indicate that SQL and Excel remain foundational skills, emphasizing the importance of data manipulation and spreadsheet proficiency. Additionally, programming and visualization tools such as Python, Tableau, and Power BI are increasingly essential, reflecting the growing emphasis on data storytelling and decision support capabilities.

| Skills  | Demand_Count |
|---------|--------------|
| sql     | 26177        |
| excel   | 21979        |
| tableau | 15480        |
| python  | 15062        |
| sas     | 11426        |
| R       | 10321        |
| Power BI| 9452         |
| Word    | 6344         |
| powerpoint  | 6120     |
| oracle  | 4042         |

**4.  Skills Based on Higher Salaries** 
By analyzing average salaries across different skill sets, I identified which competencies are linked to higher earning potential.

```sql
SELECT TOP 25
    Skills, 
    ROUND(AVG(Salary_year_avg), 0) AS Average_Salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE 
    job_title_short = 'Data Analyst'  
    AND Salary_year_avg IS NOT NULL 
    AND job_work_from_home = 'True'
GROUP BY 
    Skills 
ORDER BY 
    Average_Salary DESC;
```
The results highlight strong demand for expertise in big data technologies (e.g., PySpark, Couchbase), machine learning tools (e.g., DataRobot, Jupyter), and Python libraries (e.g., Pandas, NumPy). These skills command higher salaries due to their relevance in advanced data processing and predictive analytics.

Furthermore, proficiency in software development and deployment tools such as GitLab, Kubernetes, and Airflow indicates a valuable overlap between data analysis and data engineering. Similarly, expertise in cloud platforms and data engineering tools including Elasticsearch, Databricks, and Google Cloud Platform (GCP) significantly enhances earning potential, reflecting the increasing adoption of cloud-based analytics environments.

| Skills        | Average_Salary |
|---------------|----------------|
| pyspark       | 208,172        |
| bitbucket     | 189,155        |
| couchbase     | 160,515        |
| watson        | 160,515        |
| datarobot     | 155,486        |
| gitlab        | 154,500        |
| swift         | 153,750        |
| jupyter       | 152,777        |
| pandas        | 151,821        |
| elasticsearch | 145,000        |

*Table of the Average Salary for the Top 10 paying skills for Data Analysts*

**5. Most Optimal Skills to Learn**
To identify the most strategic skills for career growth, I combined demand and salary data to determine which competencies offer both high demand and strong compensation.

```sql
WITH skills_demand AS (
    SELECT 
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count 
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id 
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst'
        AND job_work_from_home = 'TRUE'  
    GROUP BY 
        skills_dim.skill_id,
        skills_dim.skills                   
),                                       

average_salary AS ( 
    SELECT 
        skills_dim.skill_id, 
        skills_dim.skills, 
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact  
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
    WHERE 
        job_title_short = 'Data Analyst'
        AND salary_year_avg IS NOT NULL 
        AND job_work_from_home = 'TRUE' 
    GROUP BY 
        skills_dim.skill_id,
        skills_dim.skills            
)

SELECT 
    sd.skill_id,
    sd.skills,
    sd.demand_count,
    asal.avg_salary
FROM skills_demand sd
INNER JOIN average_salary asal ON sd.skill_id = asal.skill_id
ORDER BY 
    sd.demand_count DESC,
    asal.avg_salary DESC;
```

The analysis shows that programming languages such as Python and R are highly sought after, with strong demand levels and competitive average salaries (approximately $101,397 and $100,499, respectively). Cloud technologies—including Snowflake, Azure, AWS, and BigQuery—also demonstrate strong demand and attractive compensation, underscoring their importance in modern data workflows.

Business intelligence and visualization tools such as Tableau and Looker remain critical for translating data into actionable insights, while database technologies (e.g., Oracle, SQL Server, and NoSQL systems) continue to be essential for effective data management and retrieval.


## Key Learnings

- Advanced SQL Proficiency: Strengthened my ability to write complex queries, including the use of Common Table Expressions (CTEs) and multi-table joins.
- Data Aggregation Techniques: Gained practical experience using GROUP BY clauses and aggregate functions such as COUNT() and AVG() to summarize data effectively.
- Analytical Problem-Solving: Enhanced my ability to translate business questions into structured, insight-driven SQL queries.


## Conclusions

**Key Insights:**
- Top-Paying Roles: Remote data analyst positions offer a wide salary range, with top earnings reaching $650,000.
- Critical Skills for High Pay: Advanced proficiency in SQL is consistently associated with higher-paying roles.
- Most In-Demand Skill: SQL remains the most sought-after skill in the data analyst job market.
- High-Value Specialized Skills: Niche technical skills, such as SVN and Solidity, are linked to higher average salaries.
- Optimal Skill for Market Value: SQL stands out as both highly demanded and well-compensated, making it a key skill for maximizing career opportunities.
 

**Closing Remarks**

This project significantly strengthened my SQL capabilities while providing valuable insights into the evolving data analyst job market. The findings offer a clear framework for prioritizing skill development and guiding job search strategies. By focusing on high-demand and high-value competencies, aspiring data analysts can position themselves more competitively. Ultimately, this analysis reinforces the importance of continuous learning and adaptability in the rapidly evolving field of data analytics.
