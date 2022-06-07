
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



