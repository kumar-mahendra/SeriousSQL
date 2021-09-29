**Difference between** `ROW_NUMBER`, `RANK`, `DENSE_RANK`  window functions 

```sql

SELECT
  ROW_NUMBER() OVER(
    ORDER BY
      numeric_column
  ) AS row_number_value -- row number of output
  RANK() OVER(
    ORDER BY
      numeric_column
  ) AS rank_vaue 
  -- rank 'r' means after (r-1)rows , if rth row value is 
  -- not equal to (r-1)th row value then it is assigned rank 
  -- r otherwise rth row will have rank (r-1).
  DENSE_RANK() OVER (
    ORDER BY
      numeric_column
  ) AS dense_rank_value 
  -- rank values may not be continuous numbers. dense_rank is just one-one 
  -- mapping from natural number to unique rank values.
FROM
  schema_name.table_name
ORDER BY
  numeric_column;
```

**Example**

numeric_column = {2, 4, 4, 7, 7, 7, 10, 11} 

`ROW_NUMBER` will return   { 1, 2, 3, 4, 5, 6, 7, 8}  i.e. row numbers of each data value

`RANK` will return {1, 2, 2, 4, 4, 4, 7, 8}

`DENSE_RANK` will return { 1, 2, 2, 3, 3, 3, 4, 5}



