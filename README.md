# Netflix_Content_Analysis

## Objective

The objective of the Netflix Content Analysis project is to explore and analyze Netflix's global content library to derive meaningful insights about content distribution, genre variety, country-wise contributions, and viewer preferences. Using Python, advanced data analysis techniques, and visualizations, this project aims to uncover trends that can assist in understanding Netflix's content strategies and inform decision-making for business and viewers alike.

## Overview

This project delves into Netflix's vast content library to uncover patterns, trends, and regional dynamics that shape its offerings. With a dataset containing details about movies and TV shows—including genres, countries, ratings, release years, and key contributors—the analysis provides a multifaceted view of Netflix’s content strategy. The findings are presented using visualizations that highlight the diversity and strategic focus of Netflix's content. This project not only sheds light on Netflix’s global reach but also demonstrates the ability to extract actionable insights from complex datasets.

## Tools Used

- PostgreSQL
- python
- power bi

## Business Statement

### 1)create table columns

```sql
DROP TABLE IF EXISTS netflix;
```
```sql
CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
```
Objective: Create Columns in table

### 2)select entire data 

```sql
select * from netflix;
```
```sql
select distinct(type) from netflix;
```
objective: count total shows

### 3)conunt the total of movies and TV Show

```sql
SELECT COUNT(type) FROM netflix;
```
Ojective: Count the total number of Movies and TV Shows

### 4)Count the Number of Movies vs TV Shows

```sql
SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY type;
```
 
### 5)Find the Most Common Rating for Movies and TV Shows

```sql
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
```
Objective: Identify the most frequently occurring rating for each type of content.

### 6)List All Movies Released in a Specific Year (e.g., 2021)

```sql
SELECT count(*) 
FROM netflix
WHERE release_year = 2021;
```
Objective:  Retrieve all movies released in a specific year.

### 7)Find the Top 5 Countries with the Most Content on Netflix

```sql
SELECT * 
FROM
(
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
```
Objective: Identify the top 5 countries with the highest number of content items.

### 8)Find each year and the average numbers of content release in India on netflix.

```sql
SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
```
Objective: Calculate and rank years by the average number of content releases by India.

### 9)Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

```sql
SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
```
Objective: Identify the top 10 actors with the most appearances in Indian-produced movies.

 ### 10)Content Production Trends

```sql
SELECT 
    release_year, 
    type, 
    COUNT(*) AS content_count
FROM 
    netflix
GROUP BY 
    release_year, type
ORDER BY 
    release_year ASC;
```
Objective: Identify the trends in Netflix content production across different years. Analyze the distribution of content types (movies vs. shows) and identify which genres are growing or declining.

### 11)Regional Preferences

```sql
SELECT 
    country, 
    listed_in AS genre, 
    COUNT(*) AS genre_count
FROM 
    netflix
GROUP BY 
    country, listed_in
ORDER BY 
    country, genre_count DESC;
```
Objective: Explore regional content preferences by analyzing the most popular genres, directors, or actors in different countries.

### 12)Release Patterns

```sql
SELECT 
    TO_CHAR(TO_DATE(date_added, 'Month DD, YYYY'), 'Month') AS month, 
    COUNT(*) AS content_count,
    RANK() OVER (ORDER BY COUNT(*) DESC) AS popularity_rank
FROM 
    netflix
GROUP BY 
    TO_CHAR(TO_DATE(date_added, 'Month DD, YYYY'), 'Month')
ORDER BY 
    popularity_rank;
```
Objective: Determine the best time of year to release content based on patterns observed in Netflix's release schedule.

### 13)Diversity in Content

```sql
SELECT 
    country, 
    COUNT(DISTINCT listed_in) AS unique_genres,
    RANK() OVER (ORDER BY COUNT(DISTINCT listed_in) DESC) AS diversity_rank
FROM 
    netflix
GROUP BY 
    country
ORDER BY 
    diversity_rank;
```
Objective: Analyze the diversity of content in terms of country, genres, and languages to identify gaps in the library.

### 14)Ratings Analysis

```sql
SELECT 
    rating, 
    COUNT(*) AS rating_count,
    PERCENT_RANK() OVER (ORDER BY COUNT(*) DESC) AS percentile
FROM 
    netflix
GROUP BY 
    rating
ORDER BY 
    rating_count DESC;
```
Objective: Evaluate the distribution of content ratings and analyze which categories dominate family-friendly vs. adult content.

### 15)Genre Dominance

```sql
SELECT 
    listed_in AS genre, 
    COUNT(*) AS genre_count,
    DENSE_RANK() OVER (ORDER BY COUNT(*) DESC) AS rank
FROM 
    netflix
GROUP BY 
    listed_in
HAVING 
    COUNT(*) > 50
ORDER BY 
    rank;
```
Objective: Identify the most commonly listed genres and determine the combinations that are most frequent.


### 16)Content Variety by Country

```sql
SELECT 
    country, 
    COUNT(DISTINCT title) AS content_variety,
    AVG(COUNT(*)) OVER (PARTITION BY country) AS avg_variety
FROM 
    netflix
GROUP BY 
    country
ORDER BY 
    content_variety DESC;
```
Objective: Analyze the variety of content offerings in each country and determine whether Netflix's strategy aligns with regional demands.

## Data Visualisation


<a href="Netflix_Content_Analysis.pbix">view Dashboard<a/>
