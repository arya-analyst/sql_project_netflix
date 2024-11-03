# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/arya-analyst/sql_project_netflix/blob/main/logo.png)

## Objective
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

## Objectives
- Analyze the distribution of content types (movies vs TV shows).
- Identify the most common ratings for movies and TV shows.
- List and analyze content based on release years, countries, and durations.
- Explore and categorize content based on specific criteria and keywords.

## Business Problems and Solutions

### Q1. Count the number of Movies vs TV Shows
```sql
Select type, count(*)
from netflix
Group By type;
```
### Q2.  Find the most common rating for movies and TV shows
```sql
With common_rating AS
(
    Select type, rating, count(rating) as count,
    rank() over (partition by type order by count(rating) desc ) as rnk
    From netflix
    Group By type, rating
    Order by type, count(rating) desc
)

Select type, rating, count
from common_rating 
Where rnk = 1
```
