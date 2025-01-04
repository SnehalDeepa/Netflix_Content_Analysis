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

-- 1)create table columns

DROP TABLE IF EXISTS netflix;

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

-- 2)select entire data 

select * from netflix;

select distinct(type) from netflix;

-- 3)conunt the total of movies and TV Show

SELECT COUNT(type) FROM netflix;
	
-- 4)Count the Number of Movies vs TV Shows

SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY type;

-- 5)Find the Most Common Rating for Movies and TV Shows

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

-- 6)List All Movies Released in a Specific Year (e.g., 2021)

SELECT count(*) 
FROM netflix
WHERE release_year = 2021;

-- 7)Find the Top 5 Countries with the Most Content on Netflix

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

-- 8)Find each year and the average numbers of content release in India on netflix.

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

-- 9)Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;

-- 10)Content Production Trends

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

-- 11)Regional Preferences

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

-- 12)Release Patterns

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

-- 13)Diversity in Content

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

-- 14)Ratings Analysis

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

-- 15)Genre Dominance

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

-- 16)Content Variety by Country

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
