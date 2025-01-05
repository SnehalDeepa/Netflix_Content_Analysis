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

## Python Code

### Import Libraries

```python
import pandas as pd
import numpy as np
import seaborn as sns 
import matplotlib.pyplot as plt
```

## Discribing and SOrting Data

```python
df = pd.read_csv("netflix_titles (1).csv")
df
```

```python
df.head()
```

```python
df.info()
```

```python
df.describe()
```

### Find The Most Common Rating For Movies and TV Shows

```python
rating_counts.plot(kind='bar', figsize=(10, 6))
plt.title('Ratings Distribution for Movies and TV Shows')
plt.xlabel('Rating')
plt.ylabel('Count')
plt.legend(title='Type')
plt.xticks(rotation=45)
plt.show()
```

### Top 5 Countries With Most Content On Netflix

```python
# Group by 'country' and count the number of titles for each country
country_content_count = df['country'].value_counts().reset_index()
country_content_count.columns = ['country', 'content_count']

# Get the top 5 countries
top_5_countries = country_content_count.head(5)

# Display the result
print("Top 5 Countries with Most Content on Netflix:")
print(top_5_countries)

sns.barplot(data=top_5_countries, x='content_count', y='country', palette='viridis')
plt.title('Top 5 Countries with Most Content on Netflix')
plt.xlabel('Number of Titles')
plt.ylabel('Country')
plt.show()
```

### Each Year and avg Numbers of Content Release in India on Netflix

```python
sns.lineplot(data=content_by_year, x='release_year', y='content_count', marker='o')
plt.title('Content Releases in India by Year on Netflix')
plt.xlabel('Year')
plt.ylabel('Number of Releases')
plt.show()
```

### Content Production Trends 

```python
content_trends = df.groupby(['release_year', 'type']).size().reset_index(name='content_count')
content_trends['cumulative_count'] = content_trends.groupby('type')['content_count'].cumsum()

sns.lineplot(data=content_trends, x='release_year', y='cumulative_count', hue='type', marker='o')
plt.title('Content Production Trends')
plt.show()
```

### Diversity in Content

```python
content_diversity = df.groupby('country')['listed_in'].nunique().reset_index(name='unique_genres')
content_diversity = content_diversity.sort_values('unique_genres', ascending=False)

content_diversity.head(10).plot(kind='bar', x='country', y='unique_genres', legend=False)
plt.title('Diversity in Content by Country')
plt.show()
```

## Ratings Analysis

### Plot overall ratings distribution

```python
plt.figure(figsize=(10, 6))
sns.barplot(data=rating_distribution, x='count', y='rating', palette='viridis')
plt.title('Overall Ratings Distribution on Netflix')
plt.xlabel('Number of Titles')
plt.ylabel('Rating')
plt.show()
```

### Plot ratings distribution by type

```python
plt.figure(figsize=(12, 7))
sns.barplot(data=rating_by_type, x='count', y='rating', hue='type', palette='magma')
plt.title('Ratings Distribution by Type (Movies vs TV Shows)')
plt.xlabel('Number of Titles')
plt.ylabel('Rating')
plt.legend(title='Type')
plt.show()
```

### Genre Dominance

```python
# Overall Genre Dominance
plt.figure(figsize=(12, 7))
sns.barplot(data=genre_counts.head(10), x='count', y='genre', palette='coolwarm')
plt.title('Top 10 Dominant Genres on Netflix')
plt.xlabel('Number of Titles')
plt.ylabel('Genre')
plt.show()

# Genre Dominance by Type
plt.figure(figsize=(14, 8))
sns.barplot(data=genre_by_type, x='count', y='genre', hue='type', palette='magma')
plt.title('Genre Dominance by Type (Movies vs TV Shows)')
plt.xlabel('Number of Titles')
plt.ylabel('Genre')
plt.legend(title='Type')
plt.show()
```

### Content Variety by country

```python
plt.figure(figsize=(12, 8))
sns.barplot(data=content_variety.head(10), x='unique_genre_count', y='country', palette='coolwarm')
plt.title('Top 10 Countries by Content Variety on Netflix')
plt.xlabel('Number of Unique Genres')
plt.ylabel('Country')
plt.show()
```


