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
### Q3. List all movies released in a specific year (e.g., 2020)
```sql
SELECT * FROM netflix
Where release_year = 2020
```
### Q4. Find the top 5 countries with the most content on Netflix
```sql
Select country, count (*) as content_count
from netflix
Where country IS NOT NULL
Group By country
Order By content_count desc
FETCH FIRST 5 ROWS WITH TIES;
```
### Q5. Identify the longest movie
```sql
With netflix_movies as
(
    Select title, duration, regexp_substr (duration, '[^ ]+') as mins
    from netflix
    Where type = 'Movie'
)

Select title, duration, to_number (mins, 999) as total_duration_in_mins from netflix_movies
order by total_duration_in_mins desc
FETCH FIRST ROW WITH TIES
```
### Q6. Find content added in the last 5 years
```sql
Select * from netflix
Where date_added >= sysdate - NUMTOYMINTERVAL (5, 'year')

-- Alternate

Select * from netflix
Where date_added >= add_months (sysdate, -60)

```
### Q7. Find all the movies/TV shows by director 'Rajiv Chilaka'!
```sql
Select type, title, director
From netflix
where director LIKE '%Rajiv Chilaka%'
```
### Q8. List all TV shows with more than 5 seasons

```sql
With tv_shows as 
(    Select title, regexp_substr (duration, '[^ ]+') as mod
     from netflix
     Where type = 'TV Show'
)

Select title from tv_shows where mod > 5

-- Alternative

Select * from
(    Select title, regexp_substr (duration, '[^ ]+') as no_of_seasons
     from netflix
     Where type = 'TV Show'
) x
where x.no_of_seasons > 5
```

### Q9. Find how many movies actor 'Salman Khan' appeared in last 10 years!
```sql
SELECT * FROM netflix
Where cast LIKE '%Salman Khan%'
And date_added >= sysdate - NUMTOYMINTERVAL (10, 'year')
```

### Q10. Find all content without a director
```sql
Select * from netflix
Where director is null
```

### Q11. Find each year and the average numbers of content release in India on netflix. Return top 5 year with highest avg content release!
```sql
Select 
    Extract (year from date_added) as year,
    count (*),
    round (count (*)/(Select count(*) from netflix where country = 'India') * 100, 2) as avg_content_per_year
From netflix
Where country = 'India'
Group By Extract (year from date_added)
order by avg_content_per_year desc
fetch first 5 rows only
```

### Q12. Categorize the content based on the presence of the keywords 'kill' and 'violence' in the description field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.
```sql
With new_view as
(
Select title, description,
    CASE WHEN 
        description LIKE '%kill%' OR
        description LIKE '%violence%' THEN 'Bad_Content'
        ELSE 'Good_Content'
    End category  
From netflix
)

Select category, count (*) from new_view
Group by category
```














