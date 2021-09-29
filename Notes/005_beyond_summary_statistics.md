#### Beyond Summary Statistics 

**Reverse Engineering** 
 
 *In this you know what output looks like now try to make an algorithm to know how to write sql-query for printing that output.*


 **Cumulative Distribution Functions**

 ***percentile*** : the probability that any value  lie between minimum value of dataset X and value V . In mathematical terms this is called `cumulative distribution`. 

 $ F(V) = \int_{min(X)}^{V} f(X)dx $ =$Pr(min(X) <= X <= V)$

**Bucketing** or `NTILE`-ing 

```sql
SELECT
  numeric_column,
  NTILE(100) OVER( -- assign number from 1 to 100 to data rows by dividing numeric_column into 100 equal bucket as all numbers in given bucket are assigned same number. 
    ORDER BY
      numeric_column  -- sort numeric_column in ascending order before assigning tile number 
  ) AS percentile
FROM
  schema_name.table_name
ORDER BY
  numeric_column
LIMIT
  10;
```

**Window functions** `ROW_NUMBER`, `RANK`, `DENSE_RANK`

```sql
SELECT
  ROW_NUMBER() OVER(
    ORDER BY
      numeric_column
  ) AS row_number_value -- row number of output
  RANK() OVER(
    ORDER BY
      numeric_column
  ) AS rank_vaue -- rank 'r' means after (r-1)rows , if rth row value is not equal to (r-1)th row value then it is assigned rank r otherwise rth row will have rank (r-1).
  DENSE_RANK() OVER (
    ORDER BY
      numeric_column
  ) AS dense_rank_value -- rank values may not be continuous. It is just one-one mapping from natural number to unique rank values.
FROM
  schema_name.table_name
ORDER BY
  numeric_column;
```

<br>

`WIDTH_BUCKET` **function in PostgreSQL** 

```sql
SELECT
  WIDTH_BUCKET(
    numeric_column,
    min_value,
    max_value,
    no_of_buckets
  ) AS bucket,
  MIN(numeric_column) AS floor_value,
  MAX(numeric_column) AS ceiling_value,
  COUNT(*) AS frequency
FROM
  schema_name.table_name
GROUP BY
  bucket
ORDER BY
  bucket
```

**Note** - *Number of buckets may vary from `no_of_buckets` to `no_of_buckets`+2 depending on wheter there are values less than `min_value` and/or greater than `max_value` in numeric_column , If yes then they will form seperate bucket .*
<br>

**`CUME_DIST` Function**

This function will calculate cumulative distribution i.e. probability using formula we gave above. i.e output will be a value between 0 and 1 . 

```sql
SELECT
  CUME_DIST() OVER (
    ORDER BY
      numeric_column
  ) AS probability
FROM
  schema_name.table_name
```

**Note** - we can't use  `CUME_DIST` with group by to form group . That's why we needed  `NTILE` function so we can group data. 

Thats it for today. With this we are done with basic statistics. 





















