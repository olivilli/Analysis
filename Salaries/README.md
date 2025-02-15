The dataset used for this analysis was sourced from [Kaggle](https://www.kaggle.com/datasets/arnabchaki/data-science-salaries-2023/data) and is freely available for public use

I want to put my statistics knowledge into practice. I'll start with the minimum, maximum, and mean, then find the standard deviation and z-score, and recalculate them. Of course, I'll also look for new clues in the process.

 Count  | 3 755   |
--------|---------|
 Max    | 450 000 |  
 Min    | 5 132   |  
 Avg    | 137 570 |
 Median | 135 000 |



I also calculated the median in two ways: one that I came up with myself and another using the special PERCENTILE_CONT function (thanks Google).
The result is the same - 135,000.

```sql
/*
SELECT salary_in_usd
FROM (SELECT salary_in_usd, 
ROW_NUMBER() OVER (ORDER BY salary_in_usd)
FROM salaries)
WHERE ROW_NUMBER = 1878
*/

SELECT PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY salary_in_usd) 
FROM salaries;
```

Next, I calculated the standard deviation and the z-score. 

```sql
WITH a AS (
SELECT 
AVG(salary_in_usd),
STDDEV(salary_in_usd)
FROM salaries
)

SELECT 
(s.salary_in_usd - a.avg)/a.stddev as z_score
FROM salaries s, a
WHERE ABS((s.salary_in_usd - a.avg)/a.stddev) > 2
ORDER BY z_score DESC
```

First, I checked where z-score > 2 and then where z-score < -2. I found 109 very high and 28 very low salaries (maximum Z-score: 4.95, minimum: -2.1). Most of them are from the US in 2023 and belong to senior employees from mid-sized US companies. This accounts for 3.6% of the entire sample. Let's see how everything changes if I exclude them.

```sql
WITH a AS (
SELECT 
AVG(salary_in_usd),
STDDEV(salary_in_usd)
FROM salaries
)

SELECT COUNT (*), MIN(salary_in_usd),
MAX(salary_in_usd), ROUND(AVG(salary_in_usd)),
PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY salary_in_usd) 
FROM ( SELECT salary_in_usd, (s.salary_in_usd - a.avg)/a.stddev as z_score
FROM salaries s, a
WHERE ABS((s.salary_in_usd - a.avg)/a.stddev)<= 2 
ORDER BY z_score DESC )
```

 Count  | 3 755   | 3 618   |
--------|---------|---------|
 Max    | 450 000 | 262 500 |
 Min    | 5 132   | 12 000  |
 Avg    | 137 570 | 133 411 |
 Median | 135 000 | 133 250 |

 The maximum and minimum changed a lot, but the mean and median changed by less than 5% and standard deviation decreased by 12.5%. These changes are not significant.


