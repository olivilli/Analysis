Let's find **[Netflix Movies and TV Shows ](https://www.kaggle.com/datasets/shivamb/netflix-shows)** dataset on Kaggle

Create a CTE for girls and boys

  ```sql
WITH girls AS (
SELECT *
FROM movies
WHERE description ILIKE '%girl%' 
 	OR description ILIKE '%woman%'
    OR description ILIKE '%women%'
 	OR description ILIKE '%she%'
),  
	boys AS (
SELECT *
FROM movies
WHERE description ILIKE '%boy%' 
	OR description ILIKE '%man%'
    OR description ILIKE '%men%'
	OR description ILIKE '%he%'
)
```
First, just count

>**Girls 1676**
>
>**Boys 8283**

Now I'll count the movies where there are only girls and only boys.

```sql
SELECT COUNT(*)
FROM movies
WHERE show_id IN ( SELECT show_id FROM girls)
AND show_id NOT IN ( SELECT show_id FROM boys)

SELECT COUNT(*)
FROM movies
WHERE show_id IN ( SELECT show_id FROM boys)
AND show_id NOT IN ( SELECT show_id FROM girls)

```

>**Girls 18**
>
>**Boys 6625**

What? 

![screenshot](https://github.com/olivilli/Analysis/blob/main/Netflix/Netflix_girls1.png)

Half of them are comedies...

![screenshot](https://github.com/olivilli/Analysis/blob/main/Netflix/Netflix_girls2.png)

Well, it wasn't long, but it was interesting.
I expected much less difference and planned to analyze each genre like this

```sql
SELECT COUNT(*) 
FROM movies
WHERE show_id IN ( SELECT show_id FROM girls)
	AND listed_in ILIKE '%dramas%'
```

Maybe play with years, descriptions, percentages. 
But 18 is very little and very sad.

Thanks for watching.
