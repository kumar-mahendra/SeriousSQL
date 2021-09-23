### Exploring Dataset 

What's it really mean by exploring a dataset? *Lets See*


**How many records** using `COUNT` 

```sql
SELECT
  COUNT(*) AS row_count 
FROM
  schema_name.table_name
LIMIT
  10;
```

*`AS` is used to give alternate name(aliases) for expression , a table in database, CTEs and many more which will be covered later in depth.*

*You can also use an integer instead of * in `COUNT` function e.g. `COUNT(1)`*


**Unique Values in a Column** using `DISTINCT`

```sql
SELECT
  DISTINCT column_name
FROM
  schema_name.table_name
LIMIT
  10;
```


*Now lets find count of unique values*  
```sql
SELECT 
  COUNT(DISTINCT column_name) AS count_unique
FROM
  schema_name.table_name
LIMIT
  10;
```

*What if we want to count freq of rows having unique combination of more than 1 column than just unique values in one column? For this we have `GROUP BY` clause in SQL*

**Frequency Count** using `GROUP BY` 

*One way to think of the `GROUP BY` is to imagine our dataset being divided into different groups based off the values of selected columns.*

```sql
SELECT
  column_name, 
  COUNT(*) AS group_count
FROM
  schema_name.table_name
GROUP BY
  column_name
LIMIT
  10;
```

**Note** *If you want to group by multiple columns just select all those column in `SELECT` command and then also in mention them in `GROUP BY` like this*

```sql
SELECT
  column_1,
  column_2,
  COUNT(*) AS group_count
FROM
  schema_name.table_name
GROUP BY
  column_1,
  column_2
LIMIT
  10;
```









