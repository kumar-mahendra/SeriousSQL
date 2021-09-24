#### Cleaning Dirty Data 

**Conditional Querying** using `WHERE` filter 

<ins>Template</ins>

```sql
SELECT
  *
FROM
  schema_name.table_name
WHERE
  (
    (column_1 > 200)
    AND (column_2 is NULL)
  )
  OR (
    (column_3 != 0)
    AND (column_4 = 0)
  )

  OR (
      column_5="text"   
  );
```
**Note** : `WHERE` should come before `GROUP BY` in SQL query else SQL will throw error . 

**Detecting duplicate records**

*first count total number of rows in dataset and then find count of distinct rows in dataset. Easy , isn't it? but wait, how to count distinct rows? that's what I learnt today*

* using `DISTINCT`
```sql
SELECT DISTINCT * -- This will display all rows with unique combination of all columns
FROM schema_name.table_name;
``` 

* `warning` : To count distinct rows we can't use `COUNT( DISTINCT *)` directly in PostgreSQL. 

**3 Ways to count distinct rows in dataset**

*Method 1 - Subquery*

subquery are query inside another query . drawback is they are less readable. 

```sql
SELECT COUNT(*) -- count of distinct rows in table
FROM (
  SELECT DISTINCT *
  FROM schema_name.table_name
) AS subquery
;
```

*Method 2- CTEs* 

CTEs stands for common table expressions . A CTE is a SQL query that manipulates existing data and stores the data outputs as a new reference, very similar to storing data in a new temporary Excel sheet.

```sql
WITH deduped_logs AS (
  SELECT DISTINCT *
  FROM schema_name.table_name
)
SELECT COUNT(*)
FROM deduped_logs;  -- deduped is just another name for duplicate in workplace environment
```

*Method 3- Temporary Table*

before creating temporary table always check if table already exists , if yes then drop it. 

```sql
DROP TABLE IF EXISTS table_name;
```

Now , create new temporary temple

```sql
CREATE TEMP TABLE new_table_name AS  -- AS keyword here tells SQL to use output of query to populate newly created table
SELECT DISTINCT *
FROM schema_name.table_name;
```

temporary tables are called temporary because they gets deleted automatically after current session of database shut down. 

**Duplicate Counts** using `GROUP BY` and `HAVING` clause
  
```sql
CREATE TEMP TABLE new_table_name AS
SELECT
  *
FROM
  schema_name.table_name
GROUP BY
  column_1,
  column_2,
  ... 
  column_n      --- We can't use * in GROUP BY like we did in SELECT so, write all column names manually
HAVING COUNT(*) > 1;  -- HAVING is used to apply a condition on an expression without including expression in output table 
```

alternatively, if you want COUNT(*) column in output then do this instead 

```sql
CREATE TEMP TABLE new_table_name AS
SELECT
  *,
  COUNT(*) AS frequency 
FROM
  schema_name.table_name
GROUP BY
  column_1,
  column_2,
  ... 
  column_n      --- We can't Use * in GROUP BY so, write all column names manually
WHERE frequency > 1 
ORDER BY frequency DESC;
```

**Note** *SQL don't allow using aggregate function like `COUNT` with `WHERE` statment and that's the reason why we need `HAVING` caluse.* <br>

Thats it for today. Now Its time to do exercises. 



















