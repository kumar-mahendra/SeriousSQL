
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


- ***for these solution were given in tutorial iteself***
- `11.4.1.`   Weekly Volume Comparison
  1. What is the average daily volume of Bitcoin for the last 7 days?
  2. Create a 1/0 flag if a specific day is higher than the last 7 days volume average
  3. What is the percentage of weeks (starting on a Monday) where there are 4 or more days with increased volume?
  4. How many high volume weeks are there broken down by year for the weeks with 5-7 days above the 7 day volume average excluding 2021?

<details>
<summary>Question_1 and Question_2 </summary>

```sql
WITH window_calculations AS (
SELECT
  market_date,
  volume,
  AVG(volume) OVER (
    ORDER BY market_date
    RANGE BETWEEN '7 DAYS' PRECEDING and '1 DAY' PRECEDING
  ) AS past_weekly_avg_volume
FROM updated_daily_btc
)
SELECT
  market_date,
  volume,
  CASE
    WHEN volume > past_weekly_avg_volume THEN 1
    ELSE 0
    END AS volume_flag
FROM window_calculations
ORDER BY market_date DESC
LIMIT 10;
```
</details>

<details>
<summary>Question_3</summary>

```sql
WITH window_calculations AS (
  SELECT
    market_date,
    volume,
    AVG(volume) OVER (
      ORDER BY market_date
      RANGE BETWEEN '7 DAYS' PRECEDING and '1 DAY' PRECEDING
    ) AS past_weekly_avg_volume
  FROM updated_daily_btc
),
-- generate the date
date_calculations AS (
  SELECT
    market_date,
    DATE_TRUNC('week', market_date)::DATE AS start_of_week,
    volume,
    CASE
      WHEN volume > past_weekly_avg_volume THEN 1
      ELSE 0
      END AS volume_flag
  FROM window_calculations
),
-- aggregate the metrics and calculate a percentage
aggregated_weeks AS (
  SELECT
    start_of_week,
    SUM(volume_flag) AS weekly_high_volume_days
  FROM date_calculations
  GROUP BY 1
)
-- finally calculate the percentage
SELECT
  weekly_high_volume_days,
  ROUND(100 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage_of_weeks
FROM aggregated_weeks
GROUP BY 1
ORDER BY 1;
```

</details>


<details>
<summary>Question_4</summary>

```sql
WITH window_calculations AS (
  SELECT
    market_date,
    volume,
    AVG(volume) OVER (
      ORDER BY market_date
      RANGE BETWEEN '7 DAYS' PRECEDING and '1 DAY' PRECEDING
    ) AS past_weekly_avg_volume
  FROM updated_daily_btc
),
-- generate the date
date_calculations AS (
  SELECT
    market_date,
    DATE_TRUNC('week', market_date)::DATE AS start_of_week,
    volume,
    CASE
      WHEN volume > past_weekly_avg_volume THEN 1
      ELSE 0
      END AS volume_flag
  FROM window_calculations
),
-- aggregate the metrics and calculate a percentage
aggregated_weeks AS (
  SELECT
    start_of_week,
    SUM(volume_flag) AS weekly_high_volume_days
  FROM date_calculations
  GROUP BY 1
)
-- finally calculate the percentage by year
SELECT
  -- here we demonstrate how to use EXTRACT as an alternative to DATE_TRUNC
  EXTRACT(YEAR FROM start_of_week) AS market_year,
  COUNT(*) AS high_volume_weeks,
  ROUND(100 * COUNT(*) / SUM(COUNT(*)) OVER (), 2) AS percentage_of_total
FROM aggregated_weeks
WHERE weekly_high_volume_days >= 5
AND start_of_week < '2021-01-01'::DATE
GROUP BY 1
ORDER BY 1;
```
</details>

- `11.4.2`. Simple Moving Averages
  For the following time windows: 14, 28, 60, 150 days - calculate the following metrics for the close_price column:

  - Moving average
  - Moving standard deviation
  - The maximum and minimum values
  
  Additionally round all metrics to to the nearest whole number

<details>
<summary>Solution Script for MA, SMA, MAX_AND_MIN calculations </summary>

```sql
SELECT
  market_date,
  close_price,
  -- averages
  ROUND(AVG(close_price) OVER w_14) AS avg_14,
  ROUND(AVG(close_price) OVER w_28) AS avg_28,
  ROUND(AVG(close_price) OVER w_60) AS avg_60,
  ROUND(AVG(close_price) OVER w_150) AS avg_150,
  -- standard deviation
  ROUND(STDDEV(close_price) OVER w_14) AS std_14,
  ROUND(STDDEV(close_price) OVER w_28) AS std_28,
  ROUND(STDDEV(close_price) OVER w_60) AS std_60,
  ROUND(STDDEV(close_price) OVER w_150) AS std_150,
  -- max
  ROUND(MAX(close_price) OVER w_14) AS max_14,
  ROUND(MAX(close_price) OVER w_28) AS max_28,
  ROUND(MAX(close_price) OVER w_60) AS max_60,
  ROUND(MAX(close_price) OVER w_150) AS max_150,
  -- min
  ROUND(MIN(close_price) OVER w_14) AS min_14,
  ROUND(MIN(close_price) OVER w_28) AS min_28,
  ROUND(MIN(close_price) OVER w_60) AS min_60,
  ROUND(MIN(close_price) OVER w_150) AS min_150
FROM updated_daily_btc
WINDOW
  w_14 AS (ORDER BY MARKET_DATE RANGE BETWEEN '14 DAYS' PRECEDING AND '1 DAY' PRECEDING),
  w_28 AS (ORDER BY MARKET_DATE RANGE BETWEEN '28 DAYS' PRECEDING AND '1 DAY' PRECEDING),
  w_60 AS (ORDER BY MARKET_DATE RANGE BETWEEN '60 DAYS' PRECEDING AND '1 DAY' PRECEDING),
  w_150 AS (ORDER BY MARKET_DATE RANGE BETWEEN '150 DAYS' PRECEDING AND '1 DAY' PRECEDING)
ORDER BY market_date DESC

```

</details>

- `11.4.3.`  Statistical Outlier Detection

  - Create outlier flags where the current close_price is outside of the range of the average plus or minus 2 x the standard deviation for each window period.

<details>
<summary>SQL Script</summary>

```sql
WITH base_data AS (
  SELECT
  market_date,
  ROUND(close_price, 2) AS close_price,
  ROUND(AVG(close_price) OVER w_14) AS avg_14,
  ROUND(AVG(close_price) OVER w_28) AS avg_28,
  ROUND(AVG(close_price) OVER w_60) AS avg_60,
  ROUND(AVG(close_price) OVER w_150) AS avg_150,
  ROUND(STDDEV(close_price) OVER w_14) AS std_14,
  ROUND(STDDEV(close_price) OVER w_28) AS std_28,
  ROUND(STDDEV(close_price) OVER w_60) AS std_60,
  ROUND(STDDEV(close_price) OVER w_150) AS std_150
FROM updated_daily_btc
WINDOW
  w_14 AS (ORDER BY MARKET_DATE RANGE BETWEEN '14 DAYS' PRECEDING AND '1 DAY' PRECEDING),
  w_28 AS (ORDER BY MARKET_DATE RANGE BETWEEN '28 DAYS' PRECEDING AND '1 DAY' PRECEDING),
  w_60 AS (ORDER BY MARKET_DATE RANGE BETWEEN '60 DAYS' PRECEDING AND '1 DAY' PRECEDING),
  w_150 AS (ORDER BY MARKET_DATE RANGE BETWEEN '150 DAYS' PRECEDING AND '1 DAY' PRECEDING)
)
SELECT
  market_date,
  close_price,
  CASE
    WHEN close_price BETWEEN
      (avg_14 - 2 * std_14) AND (avg_14 + 2 * std_14)
      THEN 0
    ELSE 1
    END AS outlier_14,
  CASE
    WHEN close_price BETWEEN
      (avg_28 - 2 * std_28) AND (avg_28 + 2 * std_28)
      THEN 0
    ELSE 1
    END AS outlier_28,
  CASE
    WHEN close_price BETWEEN
      (avg_60 - 2 * std_60) AND (avg_60 + 2 * std_60)
      THEN 0
    ELSE 1
    END AS outlier_60,
  CASE
    WHEN close_price BETWEEN
      (avg_150 - 2 * std_150) AND (avg_150 + 2 * std_150)
      THEN 0
    ELSE 1
    END AS outlier_150
FROM base_data
ORDER BY market_date DESC
```
</details>

- `11.4.4.` Weighted Moving Averages

|Time Period|	Weight Factor|
|-|-|
|1-14 days|	0.5|
|15-28 days|	0.3|
|29-60 days|	0.15|
|61-150 days|	0.05|

<details>
<summary>SQL solution</summary>

```sql
WITH base_data AS (
  SELECT
    market_date,
    ROUND(close_price, 2) AS close_price,
    ROUND(AVG(close_price) OVER w_1_to_14) AS avg_1_to_14,
    ROUND(AVG(close_price) OVER w_15_28) AS avg_15_28,
    ROUND(AVG(close_price) OVER w_29_60) AS avg_29_60,
    ROUND(AVG(close_price) OVER w_61_150) AS avg_61_150
FROM updated_daily_btc
WINDOW
  w_1_to_14 AS (ORDER BY MARKET_DATE RANGE BETWEEN
    '14 DAYS' PRECEDING AND '1 DAY' PRECEDING),
  w_15_28 AS (ORDER BY MARKET_DATE RANGE BETWEEN
    '28 DAYS' PRECEDING AND '15 DAY' PRECEDING),
  w_29_60 AS (ORDER BY MARKET_DATE RANGE BETWEEN
    '60 DAYS' PRECEDING AND '29 DAY' PRECEDING),
  w_61_150 AS (ORDER BY MARKET_DATE RANGE BETWEEN
  '150 DAYS' PRECEDING AND '61 DAY' PRECEDING)
)
SELECT
  market_date,
  close_price,
  0.5 * avg_1_to_14 + 0.3 * avg_15_28 + 0.15 * avg_29_60 + 0.05 * avg_61_150 AS custom_moving_avg
FROM base_data
ORDER BY market_date DESC;
```
</details>

- `11.4.5.` Exponential Weighted Moving Average

Calculate the 14 day EWMA close_price using a recursive CTE where smoothing factor lambda - λ=0.13

where SMAt=14 day simple moving average at date t

`EWMAt=λ×SMAt+(1−λ)×EWMAt−1`

<details>
<summary>SQL script</summary>

```sql
DROP TABLE IF EXISTS base_table;
CREATE TEMP TABLE base_table AS
SELECT
    market_date,
    ROUND(close_price, 2) AS close_price,
    ROUND(AVG(close_price) OVER w_1_to_14) AS sma_14,
    ROW_NUMBER() OVER w_1_to_14 AS _row_number
FROM updated_daily_btc
WINDOW
  w_1_to_14 AS (ORDER BY MARKET_DATE RANGE BETWEEN
    '14 DAYS' PRECEDING AND '1 DAY' PRECEDING);

DROP TABLE IF EXISTS ewma_output;
CREATE TEMP TABLE ewma_output AS
WITH RECURSIVE output_table
(market_date, close_price, sma_14, ewma_14, _row_number)
AS (

-- The 1st query: we set initial output row using _row_number = 15
  SELECT
    market_date,
    close_price,
    sma_14,
    sma_14 AS ewma_14,
    _row_number
  FROM base_table
  WHERE _row_number = 15

  UNION ALL

  -- The 2nd query: we need to "shift" our output by 1 row
  SELECT
    base_table.market_date,
    base_table.close_price,
    base_table.sma_14,
    -- Let's round out ewma_14 record to 2 decimal places
    ROUND(
      0.13 * base_table.sma_14 + (1 - 0.13) * output_table.ewma_14,
      2
    ) AS ewma_14,
    base_table._row_number
  FROM output_table
  INNER JOIN base_table
    ON output_table._row_number + 1 = base_table._row_number
    AND base_table._row_number > 15
)
SELECT * FROM output_table;

-- Pivot data for visualisation
SELECT
  market_date,
  'SMA' AS metric_name,
  sma_14 AS metric_value
FROM ewma_output
UNION
SELECT
  market_date,
  'EWMA' AS metric_name,
  ewma_14 AS metric_value
FROM ewma_output
SELECT
  market_date,
  'Price' AS metric_name,
  close_price AS metric_value
FROM ewma_output
ORDER BY market_date, metric_name;
```
</details>
