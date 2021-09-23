#### Exercies - COUNT and DISTINCT

- Which `actor_id` has the most number of unique `film_id` records in the `dvd_rentals.film_actor` table?`

```sql
SELECT
  actor_id,
  COUNT(DISTINCT film_id)
FROM
  dvd_rentals.film_actor
GROUP BY
  actor_id
ORDER BY
  2 DESC
```
- How many distinct `fid` values are there for the 3rd most common `price` value in the `dvd_rentals.nicer_but_slower_film_list` table?

```sql
-- all fid values are unique in this dataset. 
SELECT
  price,
  COUNT(*) as fid_counts
FROM
  dvd_rentals.nicer_but_slower_film_list
GROUP BY price 
ORDER BY 2 DESC
OFFSET 2 -- to get 3rd most common price as first row
```

- How many unique `country_id` values exist in the `dvd_rentals.city table`?

```sql
SELECT
  COUNT(DISTINCT country_id)
FROM
  dvd_rentals.city
```

- What percentage of overall `total_sales` does the Sports `category` make up in the `dvd_rentals.sales_by_film_category` table?

```sql
SELECT
  category,
  ROUND(
    100 * SUM(total_sales) :: NUMERIC / SUM(SUM(total_sales)) OVER(),
    2
  ) AS percentage
FROM
  dvd_rentals.sales_by_film_category
GROUP BY
  category
```

alternately, (If categories are not repeated which is there in this case)

```sql
SELECT
  category,
  ROUND(
    100 * total_sales::NUMERIC / SUM(total_sales) OVER (),
    2
  ) AS percentage
FROM dvd_rentals.sales_by_film_category;
```

- What percentage of unique `fid` values are in the Children `category` in the `dvd_rentals.film_list` table?

```sql
SELECT
  category,
  ROUND(
    100 * COUNT(DISTINCT fid) :: NUMERIC / SUM(COUNT(DISTINCT fid)) OVER(),
    2
  )
FROM
  dvd_rentals.film_list
GROUP BY
  category
```


Thats it for Today! Bye. 