
### Exercises - Window Functions
---

- `3.2.1. Exercise`
    - Can you identify and implement an alternative method to obtain these aggregated values for our dummy customer_sales dataset using GROUP BY only?

```sql
drop table if exists demo_table;
create temp table demo_table (customer_id, sales_id, sales) as (
  values
    ('A', 1, 300),
    ('A', 1, 150),
    ('A', 2, 100),
    ('B', 3, 200)
);

with t1 as (
  select
    demo_table.customer_id,
    sales_id,
    sum(sales) as sum_1
  from
    demo_table
  group by
    demo_table.customer_id,
    sales_id
),
t2 as (
  select
    demo_table.customer_id,
    sum(sales) as sum_2
  from
    demo_table
  group by
    demo_table.customer_id
  ),
t3 as (
      select
        sum(sales) as sum_3
      from
        demo_table
    )
  select
    demo_table.customer_id,
    demo_table.sales_id,
    t1.sum_1,
    t2.sum_2,
    t3.sum_3
  from
    demo_table
    inner join t1 on demo_table.customer_id = t1.customer_id  and demo_table.sales_id = t1.sales_id
    inner join t2 on demo_table.customer_id = t2.customer_id
    cross join t3;  -- alternatively we can use  inner join on True 
```

Advantage of using Window functions over CTE approach I used is **Efficiency** 

when used `explain analyze` on top of this query execution time was 0.073 ms but if I used window function as below 

```sql
SELECT
  customer_id,
  sales_id,
  sales,
  SUM(sales) OVER (
    PARTITION BY
      customer_id,
      sales_id
  ) AS sum_sales,
  SUM(SALES) OVER (
    PARTITION BY customer_id
  ) AS customer_sales,
  SUM(SALES) OVER () AS total_sales
FROM demo_table;
```
then execution time is 0.048 seconds. Now this is small dataset so effect is not much, for for large dataset such small increase in execution time matters a lot. 

- `4.6.1.1. Exercise` 
  - **Random Seeds**

    What happens when you re-run the RANDOM example - can you think of any method which you can use to make the output deterministic (does not change)?

    <u>Answer</u> on each re-run output of random sample change and also number of rows returned also changes slightly from exact fraction which we specified.

    we can avoid this using `SETSEED` in `RANDOM` function.  

```sql
select setseed(1.); 
select col_1, ..., col_n
from schema_name.table_name 
where random() <= fraction  -- where fraction lies b/w 0 & 1 
```

- `5.4.1. Exercise`
  - Let’s say we need to analyze the `weight` values by using `PERCENT_RANK`, `CUME_DIST` and `NTILE` window functions.

    How would you explain to a non-technical stakeholder what the differences are between each of the 3?
    
    <u>Answer</u> : weight is a numeric columns with 

- `6.2.1. Exercise`
    1. What is the earliest and latest market_date values?
    2. What was the historic all-time high and low values   for the close_price and their dates?
    3. Which date had the most volume traded and what was the close_price for that day?
    4. How many days had a low_price price which was 10% less than the open_price?
    5. What percentage of days have a higher close_price than open_price?
    6. What was the largest difference between high_price and low_price and which date did it occur?
    7. If you invested $10,000 on the 1st January 2016 - how much is your investment worth in 1st of February 2021? Use the close_price for this calculation
   

<u>SQL Script Solutions of above questions</u>

<details>
<summary><u>Question 1</u></summary>

```sql
  'earliest_date' as date_value,
  MIN(market_date) as market_date_

```
</details>

| date_value    | market_date_             |
| ------------- | ------------------------ |
| earliest_date | 2014-09-17 |
| latest_date   | 2021-02-24 |

<details>
<summary><u>Question 2</u></summary>

```sql
WITH table_1 AS ( 
SELECT RANK() over( ORDER BY close_price desc NULLS LAST ) AS rank_index, close_price, market_date FROM trading.daily_btc 
), 
table_2 AS ( 
SELECT RANK() over( ORDER BY close_price NULLS LAST ) AS rank_index, close_price, market_date FROM trading.daily_btc 
)
SELECT 'all_time_low' as all_time_rank , close_price, market_date FROM table_2 WHERE rank_index=1
UNION
SELECT 'all_time_high' as all_time_rank , close_price, market_date FROM table_1 WHERE rank_index=1

```
</details>

| all_time_rank | close_price  | market_date              |
| ------------- | ------------ | ------------------------ |
| all_time_high | 57539.945313 | 2021-02-21 |
| all_time_low  | 178.102997   | 2015-01-14 |

<details>

<summary><u>Question 3</u></summary>

```sql

SELECT
  market_date,
  volume,
  close_price
FROM(
    SELECT
      RANK() OVER(
        ORDER BY
          volume DESC NULLS LAST
      ) as rank_index, 
      market_date,
      volume,
      close_price
    FROM
      trading.daily_btc
  ) as subquery
WHERE
  rank_index = 1;

```
</details>

| market_date              | volume       | close_price  |
| ------------------------ | ------------ | ------------ |
| 2021-01-11 | 123320567399 | 35566.656250 |

<details>
<summary><u>Question 4</u></summary>

```sql
SELECT
  COUNT(*) as required_count
FROM
  trading.daily_btc
WHERE
  low_price <= (1 -0.1) * open_price

```

</details>

| required_count | 
|--|
| 79|

<details>
<summary><u>Question 5</u></summary>

```sql

select
  close_greater_open,
  condition_count,
  total_count,
  ROUND(condition_count :: numeric / total_count * 100)
from
  (
    select
      *,
      count(close_greater_open) over (partition by close_greater_open) as condition_count,
      count(*) over() as total_count
    from
      (
        select
          *,
          CASE
            WHEN close_price > open_price THEN 1
            ELSE 0
          END as close_greater_open
        from
          trading.daily_btc
      ) as subquery
  ) subquery_2
group by
  close_greater_open,
  condition_count,
  total_count

```
</details>

| close_greater_open | condition_count | total_count | round |
| ------------------ | --------------- | ----------- | ----- |
| 0                  | 1070            | 2353        | 45    |
| 1                  | 1283            | 2353        | 55    |

<details>
<summary><u>Question 6</u></summary>

```sql
select
  market_date,
  (high_price - low_price) as difference
from
  trading.daily_btc
order by
  difference desc NULLS LAST
LIMIT
  1;
```
</details>

|market_date |difference |
|--|--|
|2021-02-23|8914.339844 |

<details>
<summary><u>Question 7</u></summary>

```sql

with table_1 as (
  select
    close_price as jan_1_2016
  from
    trading.daily_btc
  where
    market_date = '2016-01-01'
),
table_2 as (
  select
    close_price as feb_1_2021
  from
    trading.daily_btc
  where market_date = '2021-02-01'
)
select
  *,
  ROUND( (1000/jan_1_2016)*feb_1_2021 )  as current_price 
from
  table_1,
  table_2

```
</details>

| jan_1_2016 | feb_1_2021   | current_price |
| ---------- | ------------ | ------------- |
| 434.334015 | 33537.175781 | 77215         |

