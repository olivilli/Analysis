The dataset used for this analysis was sourced from [Kaggle](https://www.kaggle.com/datasets/arnabchaki/data-science-salaries-2023/data) and is freely available for public use

I want to put my statistics knowledge into practice. I'll start with the minimum, maximum, and mean, then find the standard deviation and z-score, and recalculate them. Of course, I'll also look for new clues in the process.

![screenshot](https://github.com/olivilli/Analysis/blob/main/Salaries/ordinary.png)

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
MAX(salary_in_usd), ROUND(AVG(salary_in_usd)) as avg,
PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY salary_in_usd) 
FROM ( SELECT salary_in_usd, (s.salary_in_usd - a.avg)/a.stddev as z_score
FROM salaries s, a
WHERE ABS((s.salary_in_usd - a.avg)/a.stddev)<= 2 
ORDER BY z_score DESC )
```

![screenshot](https://github.com/olivilli/Analysis/blob/main/Salaries/with_z_score.png)

Despite the fact that the minimum and maximum values changed by almost a factor of two, the mean and median remained nearly unchanged. This means that outliers affected the range but did not influence the overall data trends. This may indicate that the data distribution was relatively stable and that the outliers were isolated cases rather than a systemic issue.


