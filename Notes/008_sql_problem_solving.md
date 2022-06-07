### SQL Problem Solving 

`trick` : to make a copy of table use this
```sql 
CREATE TEMP TABLE copy_of_table AS actual_table_name
```

`UPDATE` statement can be used to update tables and their columns 

```sql
UPDATE table_name 

SET  specify_statment(s)_to_modify_column(s)_seperated_by_commas

WHERE specify_condition(s)_to_filter_rows

-- show all updated rows as the query output 
RETURNING *
```

**Checking data types of columns**

```sql
SELECT
  table_name,
  column_name,
  data_type
FROM information_schema.columns
```
`Note` : use above command as it is , don't replace `information_schema` with name of any schema in your data. there is indeed a schema called `information_schema` in your database which contain these details by default. 


