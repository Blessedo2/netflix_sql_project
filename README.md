# Netflix Movies and TV Shows Data Analysis using SQL

![Netflix Logo](https://github.com/Blessedo2/netflix_sql_project/blob/main/7124274_netflix_logo_icon.png)

## Objective
SELECT * FROM netflix;

SELECT 
     COUNT(*) as total_content
FROM netflix;

SELECT 
    DISTINCT type 
FROM netflix;

SELECT * FROM netflix;


--15 Business Problems


--1. Count the number of movies vs Tv shows

SELECT
    type,
	COUNT(*) as total_content
FROM netflix
GROUP BY type


2. Find the most common rating for movies and Tv shows

SELECT
     type,
	 rating
FROM
(


  WITH ranked_netflix AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS count,
        RANK() OVER (PARTITION BY type ORDER BY COUNT(*) DESC) AS ranking
    FROM netflix
    GROUP BY type, rating
)
SELECT *
FROM ranked_netflix
WHERE ranking = 1

) t1
WHERE
   ranking = 1



3. List all movies released in a specific year (e.g, 2020)

-- filter 2020
-- movies

SELECT * FROM netflix
WHERE 
    type = 'Movie'
	AND
	release_year = 2020


4. Find the top 5 countries with the most content on netflix


SELECT 
   UNNEST(STRING_TO_ARRAY(country, ', ')) as new_country,
   COUNT(show_id) as total_content
FROM netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5


5.Identify the longest movie?

SELECT 
    title,  
    CAST(SUBSTRING(duration, 1, POSITION('m' IN duration) - 1) AS INT) AS duration
FROM 
    netflix
WHERE 
    type = 'Movie' 
    AND duration IS NOT NULL
ORDER BY 
    duration DESC
LIMIT 1


6.Find the content added in the last 5 years
     

SELECT 
    *
FROM netflix
WHERE 
    TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years'



SELECT CURRENT_DATE - INTERVAL '5 years'

7.Find all the movies/TV shows by director 'Rajiv Chilaka'

SELECT * FROM netflix
WHERE director ILIKE 'Rajiv Chilaka'



8.List all TV shows with more than 5 seasons

SELECT 
    *
FROM netflix
WHERE
   type = 'TV Show'
   AND
   SPLIT_PART(duration, ' ', 1)::numeric > 5


9.Count the number of content items in each genre

SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')), 
	COUNT(show_id) as total_content
FROM netflix
GROUP BY 1


10. Find each year and average of numbers of content release by india on netflix, 
return top 5 year with highest avg content release.


total content 333/972

SELECT 
   EXTRACT(YEAR FROM TO_DATE(date_added, 'Month DD, YYYY')) as year,
   COUNT(*),
   ROUND(
   COUNT(*)::numeric/(SELECT COUNT(*) FROM netflix WHERE country = 'India')::numeric * 100
   ,2)as avg_content_per_year
FROM netflix
WHERE COUNTRY = 'India'
GROUP BY 1


11. List all movies that are documentaries

SELECT * FROM netflix
WHERE 
    listed_in ILIKE '%documentaries%'



12. Find all content without a director

SELECT * FROM netflix
WHERE 
    director is NULL 


13.Find how many movies actor 'Salman Khan' appeard in last 10 years

SELECT * FROM netflix
WHERE 
   casts ILIKE '%Salman Khan%'
   AND
   release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10



14.Find the top 10 actors who have appeared in the highest number of movies produced in india.


SELECT
--show_id,
--casts,
UNNEST(STRING_TO_ARRAY(casts, ',')) as actors,
COUNT(*) as total_content
FROM netflix
WHERE country ILIKE '%india'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10



15.
Categorize the content based on the presence of the keywords 'Kill' and 'violence' in
the description field. label content containing these keywords as 'Bad' and all other
content as 'Good', count how many items fall into each category.


SELECT 
    CASE 
        WHEN LOWER(description) LIKE '%kill%' 
          OR LOWER(description) LIKE '%violence%' THEN 'Bad'
        ELSE 'Good'
    END AS category,
    COUNT(*) AS count
FROM 
    netflix
GROUP BY 
    category
