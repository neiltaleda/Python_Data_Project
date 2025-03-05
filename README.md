# Introduction
Welcome to my analysis of the data job market, specifically for data analyst roles. This project explores the highest-paying and most in-demand skills to help find the best job opportunities.

# Questions to Answer
Below are the questions I want to answer in my project:

1. What are the skills most in demand for the top 3 most popular data roles?
2. How are in-demand skills trending for Data Analysts?
3. How well do jobs and skills pay for Data Analysts?
4. What are the optimal skills for data analysts to learn? (High Demand AND High Paying)

# Tools Used
For my deep dive into the data analyst job market, I harnessed the power of several key tools:

- Python: The backbone of my analysis, allowing me to analyze the data and find critical insights.I also used the following Python libraries.

- **Python**: The backbone of my analysis, allowing me to analyze the data and find critical insights. I also used the following Python libraries:
  
  - **Pandas**: Used to analyse the data
  - **Matplotlib**: For data visualization.
  - **Seaborn**: For advanced visuals.
- **Jupyter Notebooks**: The tool used to run Python scripts
- **Visual Studio Code**:Code editor used for running the Python code.
- **Git and Github**: Essential for version control and sharing my code, thereby ensuring collaboration and project tracking.

# Data Preparation and Cleanup
Importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality

```Python
# Importing libraries
import ast
import pandas as pd
from datasets import load_dataset
import matplotlib.pyplot as plt
import seaborn as sns

# Loading data
dataset = load_dataset('lukebarousse/data_jobs')
df = dataset['train'].to_pandas()

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```

# Filter Jobs
To focus my analysis on the U.S. job market, I apply filters to the dataset, narrowing down to roles based in the United States

```Python
df_US = df[df['job_country'] == 'United States']
```

# Analysis

## 1. What are the most demanded skills for the top 3 most popular data roles?

To find the most demanded skills for the top 3 most popular data roles. I filtered out those positions by which ones were the most popular, and got the top 5 skills for these top 3 roles. This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to depending on the role I'm targeting.

View my notebook here:
[2_Skill_Demand.ipynb](3_Project/2_Skill_Demand.ipynb)

```python
fig, ax = plt.subplots(len(job_titles), 1)

sns.set_theme(style='ticks')

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head()
    sns.barplot(data=df_plot, x='skill_percent', y='job_skills', ax=ax[i], hue='skill_count', palette='dark:b_r')
    ax[i].set_title(job_title)
    ax[i].set_ylabel('')
    ax[i].set_xlabel('')
    ax[i].get_legend().remove()
    ax[i].set_xlim(0, 78)

    for n, v in enumerate(df_plot['skill_percent']):
        ax[i].text(v+1, n, f'{v:.0f}%', va='center')

    if i != len(job_titles) - 1:
        ax[i].set_xticks([])    

fig.suptitle('Likelihood of Skills Requested in US Job Postings', fontsize=15)
fig.tight_layout(h_pad=0.5)
plt.show()

```
### Results
![Visualization of Top Skills](3_Project/images/skill_demand_all_data_roles.png)

### Insights
- Python is a versatile and widely demanded skill across all 3 roles but most prominently for Data Scientists (72%) and Data Engineers (65%)
- SQL is the most demanded skill for Data Analysts and Data Scientists. For Data Analysts Python is the most sought after skill.
- Data Engineers require more specialized technical skills (AWS, Azure, Spark) compared to Data Analysts and Data Scientists who are required to be more proficient in data management and analysis.


## 2. How are in-demand skills trending for data analysis?

### Visualize data

```python
df_plot = df_DA_US_percent.iloc[:, :5]

sns.lineplot(data=df_plot, dashes=False, palette='tab10')
sns.set_theme(style='ticks')
sns.despine()

plt.title('Trending Top Skills for Data Analysts in the US')
plt.ylabel('Likelihood in Job Posting')
plt.xlabel('2023')
plt.legend().remove()

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(decimals=0))

for i in range(5):
    plt.text(11.2, df_plot.iloc[-1, i], df_plot.columns[i])
```

![Trending Top Skills for Data Analysts in the US](3_Project\images\skill_Trend_DA.png)

### Insights
- SQL remains the most consistent demanded skill but shows a gradual decrease in demand.
- Exccel noticed an increase in demand, starting around September
- Both Python and Tableau show relatively stable demand throughout the year with some fluctuations but remain essential skills for data analysts.
- PowerBI is demanded less but shows a slight upward trend toward's the end of the year.

## 3. How well do jobs and skills pay for data analysts?

### Salary Analysis

#### Visualize the data
```python
sns.boxplot(data=df_US_top6, x='salary_year_avg', y='job_title_short', order=job_order)
sns.set_theme(style='ticks')

plt.title('Salary Distribution in the United States')
plt.xlabel('Yearly Salary ($USD)')
plt.ylabel('')
plt.xlim(0, 600000)
ticks_x = plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}K')
plt.gca().xaxis.set_major_formatter(ticks_x)
plt.show()
```

#### Results
![Salary Distribution of Data Jobs in the US](3_Project\images\salary_boxplot.png)
*Box plot visualizing the salary distributions for the top 6 data job titles.*

### Insights
- Semior Data Scientists tend to have the highest salaries indicating the importance of advanced data skills.

- Senior Data Engineers and Senior Data Scientists have a considerable number of outliers on the higher end of the salary spectrum whereas Data Analysts roles demonstrate more consistency in salaries, with fewer outliers.

- The median salaries increase with the seniority and specialization of the roles.


## 3. How well do jobs and skills pay for data?

### Highest Paid and Most Demanded Skills for Data
#### Visualize Data

```python
fig, ax = plt.subplots(2, 1)

sns.set_theme(style="ticks")

# Top 10 Highest Paid Skills for Data Analysts
sns.barplot(data=df_DA_top_pay, x='median', y=df_DA_top_pay.index, ax=ax[0], hue='median', palette='dark:b_r')
# df_DA_top_pay[::-1].plot(kind='barh', y='median', ax=ax[0], legend=False)
ax[0].set_title('Top 10 Highest Paid Skills for Data Analysts ')
ax[0].set_ylabel('')
ax[0].set_xlabel('')
ax[0].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'${int(x/1000)}K'))
ax[0].legend().remove()




# Top 10 Most In Demand Skills for Data Analysts
sns.barplot(data=df_DA_skills, x='median', y=df_DA_skills.index, ax=ax[1], hue='median', palette='light:b')
# df_DA_skills[::-1].plot(kind='barh', y='median', ax=ax[1], legend=False)
ax[1].set_title('Top 10 Most In-Demand Skills for Data Analysts')
ax[1].set_ylabel('')
ax[1].set_xlabel('Median Salary($USD)')
ax[1].set_xlim(ax[0].get_xlim())
ax[1].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'${int(x/1000)}K'))
ax[1].legend().remove()

plt.tight_layout()
plt.show()
```
#### Results
![Salary Distribution of Data Jobs in the US](3_Project\images\HighestPaidIndemandSkills.png)

#### Insights:
- The top graph shows that specialised technical skills like `dplyr`, `Bitbucket`, and `Gitlab` are associated wth higher salaries.

- The bottom graph suggests that skills like `Python`, `Tableau` are most in demand even though they may not have the highest salaries.

- There is a clear distinction between the skills that are highest paid and those that are in most demand. So to maximise their career earnign potential one should consider developing a diverse skill set.

## 4. What is the most optimal skill to learn for data analysts?

#### Visualize the data

```python
from adjustText import adjust_text
from matplotlib.ticker import PercentFormatter

# df_plot.plot(kind='scatter', x='skill_percent', y='median_salary')
sns.scatterplot(
    data=df_plot,
    x='skill_percent',
    y='median_salary',
    hue='technology'
)

sns.despine()
sns.set_theme(style='ticks')

texts=[]
for i, txt in enumerate(df_DA_skills_high_demand.index):
    texts.append(plt.text(df_DA_skills_high_demand['skill_percent'].iloc[i], df_DA_skills_high_demand['median_salary'].iloc[i], txt))

adjust_text(
    texts, 
    arrowprops=dict(arrowstyle="->", color='gray', lw=1),
    only_move={'points': 'y', 'text': 'y'},
    expand_text=(1.5, 1.5)
    )

for text in texts:
    if text.get_text() == "power bi":
        text.set_y(text.get_position()[1] + 600)  # Move Power BI higher
        text.set_x(text.get_position()[0] - 1)   # Slightly shift left
    if text.get_text() == "sas":
        text.set_y(text.get_position()[1] - 100)  # Move SAS lower
        text.set_x(text.get_position()[0] + 0.1)   # Slightly shift right

plt.xlabel('Percent of Data Analyst Jobs')
plt.ylabel('Median Yearly Salary')
plt.title('Most Optimal Skills for Data Analysts in the US')

ax = plt.gca()
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, _: f'${int(y/1000)}K'))
ax.xaxis.set_major_formatter(PercentFormatter(decimals=0))


plt.tight_layout()
plt.show()
```

#### Results
![Most Optimal Skills for Data Analysts in the US](3_Project\images\optimalskills.png)

#### Insights
- Most programming skills tend to cluster at higher salary levels, indicating that programming offers greater benefits.

- Database skills like Oracle and SQL server are associated with some of the highest paying salaries.

- Analyst tools (colored orange), including Tableau and Power BI, are prevalent in job postings and offer competitive salaries, showing that visualization and data analysis software are crucial for current data roles. This category not only has good salaries but is also versatile across different types of data tasks.

# Insights
This project provided several general insights into the data job market for analysts:

- **Skill Demand and Salary Correlation**: There is a clear correlation between the demand for specific skills and the salaries these skills command. Advanced and specialized skills like Python and Oracle often lead to higher salaries.
- **Market Trends**: There are changing trends in skill demand, highlighting the dynamic nature of the data job market. Keeping up with these trends is essential for career growth in data analytics.
- **Economic Value of Skills**: Understanding which skills are both in-demand and well-compensated can guide data analysts in prioritizing learning to maximize their economic returns

# Conclusion
This project provided valuable insights into the key skills and trends in the data analyst job market. It highlights the need for continuous learning and ongoing analysis to stay ahead in the evolving field of data analytics.
