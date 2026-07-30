# Introduction
Hello, and welcome to my analysis of the data job market as of 2023, focusing on the data analyst role. This project was created out of a desire to improve my python skills, learn about the different tools offered for data analysts via python, and provide useful insights on the job market.

It focuses on top-paying and in-demand skills to help find optimal job opportunities for data analysts.

The data sourced from [Luke Barousse's Python Course](https://www.youtube.com/watch?v=wUSDVGivd-8) which provides a foundation for my analysis, containing detailed information on job titles, salaries, locations and essential skills. Through a series of python scripts, I explore key questions such as the most demanded skills, salary trends, and the intersection of demand and salary in data analytics.

# Background (Questions for the analysis)
The questions I want to answer with my project:
1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand AND High Paying)

# Tools Used

For the project I used several key tools:

- Python: The main tool used, allowing me to analyze the data and find critical insights. I also used the following python libraries:
        
        - Numpy & Pandas - This was used as a central tool in analysing the data in advanced ways.
        - Matplotlib - Visualizing the data and fine-tuning the results.
        - Seaborn - Advanced visuals.
- Jupyter Notebooks: The tool i used to run my python scripts which let me easily include my notes and analysis.
- Visual Studio Code: My go-to for executing my Python scripts.
Git & GitHub: Essential for version control and sharing my Python code and analysis, ensuring collaboration project tracking.

# Data Preperation and Cleanup

This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.

## I start by importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.

```python
#Importing Libraries
import pandas as pd
from datasets import load_dataset
import matplotlib.pyplot as plt
import ast
import seaborn as sns

#Loading Dataset
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

#Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda skill_list: ast.literal_eval(skill_list) if pd.notna(skill_list) else skill_list)
```
*Data clean-up includes turning a string into a list using the `ast library`, as well as turning dates that are read as string into `datetime` using `pandas`*

## Filter US Jobs and/or Data analyst

```python
#Filter for data analyst
df_DA_US = df[(df['job_title_short']=='Data Analyst') & (df['job_country'] == 'United States')].copy()
```

When needed, I apply a filter to the `DataFrame` to extract only jobs in the **US** and/or those that are classified as a **Data Analyst** role.


# The Analysis

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles, I filtered out those positions by witch ones were the most popular, and got the top 5 skills for these top 3 roles.
This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to depending on the role I'm targeting.

View my notebook with detailed steps here:[2_Skills_Count.ipynb](3_Project\2_Skills_Count.ipynb)

### Visualize Data
```python
fig, ax = plt.subplots(len(job_titles),1)

sns.set_theme(style='ticks')
for i,title in enumerate(job_titles):
        df_plot = df_skills_perc[df_skills_perc['job_title_short']==title].head(5)
        #df_plot.plot(kind="barh", x='job_skills', y='skill_percent', ax=ax[i], title=title)
        sns.barplot(data=df_plot, x='skill_percent',y='job_skills',ax=ax[i],hue='skill_count',palette='dark:b_r')
#        ax[i].invert_yaxis()
        ax[i].set_title(title)
        ax[i].set_ylabel('')
        ax[i].set_xlabel('')
        ax[i].legend().set_visible(False)
        ax[i].set_xlim(0,100)
        if i != len(job_titles)-1:
                ax[i].set_xticks([])

        for n,v in enumerate(df_plot['skill_percent']):
                ax[i].text(v+1,n,f'{v:.0f}%',va='center')
fig.suptitle('Likelihood of Skills Requested in US Job Postings', fontsize=15)
plt.tight_layout()
plt.show()
```

### Results
![Likelihood of Skills Requested in US Job Postings](3_Project\Images\skill_demand_all_data_roles.png)

### Insights

- Python is a very sought after skill for both Data Scientists (72%)and Data Engineers (65%). This is probably due to the more technical nature of the roles compared to that required of a Data Analyst, who only required to know python in 27% of roles.
- SQL is the most sought-after skill for Data Analysts (51%) and Data Engineers (68%), and being required for (51%), placing it as a very sought-after skill.
- Data Engineers require a more technical set of skills, focusing more on cloud technology (aws,azure) or data framework tools (Spark). 
- Data Scientists focus more on programming and statistics (r,sas) as their roles require a more mathenatical analysis than the other roles.

## 2. How are in-demand skills trending for Data Analysts?

### Visualize Data
```python
df_plot = df_DA_US_percent.iloc[:,:5]

sns.set_theme(style='ticks')
sns.lineplot(data=df_plot, dashes=False, palette='tab10')
sns.despine()

plt.title('Trending Top Skills for Data Analysts in the US')
plt.ylabel('Likelihood in Job Posting')
plt.xlabel('2023')
plt.legend().remove()

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals=0))

for i in range(5):
    plt.text(11.2,df_plot.iloc[-1,i], df_plot.columns[i]) #we are looking to place the text at the november x value
    #and we are also looking to place the y value of the LAST item (december)

plt.tight_layout()
plt.show()
```

### Results

![Trending Top Skills for Data Analysts in the US](3_Project\Images\skill_demand_trends_DA.png)

### Insights

 - SQL is the most in-demand skill for a DA. A downward trend is visible although it still remains a very sought-after skill.
 - Excel and python are the next most sought-after skills, showing a relative similar trend across the time examined.
 - Tableau and PowerBI are the least sought-after skills, although still very prevalent. They show no skew upwards or downwards across the year.

 ## 3. How well do jobs and skills pay for Data Analysts?

 ### Salary Analysis by median

 ```python
sns.boxplot(data=df_US_top6, x='salary_year_avg', y='job_title_short', order=job_order)

plt.title('Salary Distribution for Data Jobs in the US')
plt.xlabel('Average Yearly Salary $(USD)')
plt.ylabel('Job Name')
plt.xlim(0,600000)

ax = plt.gca()
ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x, loc: f'${int(x/1000)}K'))

plt.tight_layout()
plt.show()
 ```

 ### Results
 ![Salary Analysis by Median](3_Project\Images\Salary_Analysis.png)

 ### Insights
  - Senior roles overtake their junior counterparts with their median salary, as to be expected.
  - A senior data analyst's median salary is lower than that of a junior data engineer/scientist.
  - Overall median pay for data roles fall between 95K$-150K$.

  ### Highest Paid & Most Demanded Skills for Data Analysts in the US

  ```python
fig,ax = plt.subplots(2,1)

sns.set_theme(style='ticks')

sns.barplot(data = df_DA_top_pay, x='median', y = df_DA_top_pay.index, ax=ax[0], hue='median', palette = 'dark:b_r')

#df_DA_top_pay['median'].plot(kind="barh", ax=ax[0], legend=True)
#Can also call the DF in reverse order avoiding need to invert axis
#df_DA_top_pay[::-1].plot'...'
ax[0].set_xlabel('')
ax[0].set_ylabel('')
ax[0].set_title('Top 10 Highest Payed Skills for Data Analysts')
ax[0].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, loc: f'${int(x/1000)}K'))
ax[0].legend().remove()

sns.barplot(data = df_DA_skills, x='median', y = df_DA_skills.index, ax=ax[1],hue='median', palette = 'light:b')
#df_DA_skills['median'].plot(kind="barh", ax=ax[1])
ax[1].set_xlim(ax[0].get_xlim())
ax[1].set_xlabel('')
ax[1].set_ylabel('')
ax[1].set_title('Top 10 Most In-Demand Skills for Data Analysts')
ax[1].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, loc: f'${int(x/1000)}K'))
ax[1].legend().remove()

plt.tight_layout()
plt.show()
```

#### Results
![Top 10 Highest Paid Skills for Data Analysts](3_Project\Images\Highest_paid_skills.png)
*Upper graph details the highest paid skills for a data analyst to have. Lower graph depicts the most sought-after skills for data analysts in the US*

#### Insights
 - The top graph shows specialized technical skills like `Dplyr`, `Bitbucket` and `Gitlab` are associated with higher salaries, some reaching up to 200K$, suggesting that advanced technical proficiency can increase earning potential.
 - The bottom graph highlights that foundational skills like `Excel`, `Powerpoint` and `SQL` are the most in-demand, even though they may not offer the highest salaries. This demonstrates the importance of these core skills for employability in data analysis roles.
- There's a clear distinction between the skills that are highest paid and those that are most in-demand. Data analysts aiming to maximize their career potential should consider developing a diverse skill set that include both high-paying specialized skills and widely demanded foundational skills.

## 4. What is the most optimal skill to learn for a data analyst in the US.

### Visualize Data

```python
sns.scatterplot(
    data = df_plot,
    x='skill_percent',
    y='median_salary',
    hue='technology'
)
sns.despine()
sns.set_theme(style="ticks")
# df_plot.plot(kind='scatter',x='skill_percent',y='median_salary')
#plt.text(x,y,string) example
texts = []
for i, txt in enumerate(df_DA_skills_high_demand.index):
    #trying to break it down into stages, we first want to print out the X,y values per i
    texts.append(plt.text(df_DA_skills_high_demand['skill_percent'].iloc[i],
             df_DA_skills_high_demand['median_salary'].iloc[i],
             txt))
adjust_text(texts,  arrowprops=dict(arrowstyle='->', color='gray', lw=1))
#    print(i,txt)

ax = plt.gca() #gca = get current axis, to access y axis to change values and add '$'
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}K'))
ax.xaxis.set_major_formatter(PercentFormatter(decimals=0))

plt.xlabel('Percent of Job Postings')
plt.ylabel('Median Yearly Salary ($USD)')
plt.title('Most Optimal Skills for Data Analysts in the US')
plt.tight_layout()
plt.show()
```

### Results
![Most Optimal Skills for Data Analysts in the US](3_Project\Images\Optimal_skill_scatter_plot.png)
*The scatter plot also groups the different skills into groups and color-coding them*

### Insights

- The scatter plot shows that most of the `programming` skills (colored blue) tend to cluster at higher salary levels compared to other categories, indicating that programming expertise might offer greater salary benefits within the data analytics field.

- Analyst tools (colored green), including Tableau and PowerBI are prevalent in job postings and offer competitive salaries, showing that visualization and data analysis software are crucial for current data roles. This category not only has good salaries but is also versatile across different types of data tasks.

 - The database skills (colored orange), such as Oracle and SQL server, are associated with some of the highest salries among data analysts tools. This indicates a significant demand and valuation for data management and manipulation expertise in the industry.

# What I Learned

Throughout the project I deepened my knowledge about python, the data analyst job market, enchancing my technical skills with python and similar tools, especially in data manipulation and visualization. In addition I deepened my analytical skills, learning how to ask relevant questions, what sort of data I should extract in order to answer them, transforming and presenting the data in a way that could answer these questions.

- Advanced Python usage: Utilizing libraries such as `Pandas` for data manipulation, `Seaborn` and `Matplotlib` for data visualization, and other libraries helped me perform complex data analysis tasks more efficiently.
- Data Cleaning Importance: I learned that thorough data cleaning and preparation are crucial before any analysis can be conducted, ensuring the accuracy of insights derived from the data.
- Strategic Skill Analysis: The project emphasized the importance of aligning one's skills with the market demand. Understanding the relationship between skill demand, salary, and job availability allows for more strategic career planning in the tech industry.

# Insights

This project provided several general insights into the data job market for data analysts:
- Skill Demand and Salary Correlation: There is a clear correlation between the demand for specific skills and the salaries these skills command. Advanced and specialized skills like `Python` and `Oracle` often lead to higher salaries.
- Market Trend: There are changing trends in skill demand, highlighting the dynamic nature of the data job market.
Keeping up with these trends is essential for career growth in data analytics.
- Economic Value of Skills: Understanding which skills are both in-demand and well-compensated can guide data analysts in prioritizing learning to maximize their economic returns.

# Challenges I Faced
The challenges I faced during the project provided meaningful learning opportunities:
- Data Inconsistencies: Handling missing or inconsistent data entries requires careful consideration and thorough data-cleaning techniques to ensure the integrity of the analysis.
- Complex Data Visualization: Designing effective visual representations of complex datasets was challenging but critical for conveying insights clearly and compellingly.
- Balancing Breadth and Depth: Deciding how deeply to dive into each analysis while maintaining a broad overview of the data landscape required constant balancing to ensure comprehensive coverage without getting lost in the details.

# Conclusion

This exploration into the data analyst job market has been incredibly informative, highlighting the critical skills and trend that shapre this evolving field. The insights I got enhance my understanding and provide actionable guidance for anyone looking to advance their career in data analytics. As the market continues to change, ongoing analysis will be essential to stay ahead in data analytics. This project is a good foundation for future explorations and underscores the importance of continuous learning and adaptation in the data field.

Thank you for reading! :)