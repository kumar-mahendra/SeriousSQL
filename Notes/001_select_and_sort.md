### Welcome to the SERIOUS-SQL 

<ins>Refereces to learn about writing markdown docs </ins>
* https://www.markdownguide.org/basic-syntax/
* https://www.markdownguide.org/extended-syntax/

*Lets Start* 

First step of exploring any dataset is looking at the big picture 

**Query Dataset** using `SELECT` Command 

*syntax of sql query in most basic form*
```sql
SELECT
  column_name_1,
  column_name_2
FROM schema_name.table_name;
```
*to select all columns use \* instead of column name(s) .*
```sql
SELECT * 
FROM schema_name.table_name;
```

*limit output rows*
```sql
-- limit rows to 10 
SELECT *
FROM schema_name.table_name
LIMIT 10;  
```
---

**Sorting Dataset** using `ORDER BY`

*sort by text column*

```sql
SELECT * 
FROM schema_name.table_name 
ORDER BY column_name_with_text
LIMIT 5; 
-- `ORDER BY` must come after `FROM`
```

*sort by numeric/data column*
```
Sorting for any column with numbers, dates, timestamps is done from
lowest to highest or latest to earliest for date time related columns.
```

```sql
SELECT *
FROM schema_name.table_name 
ORDER BY numeric_column_name DESC  
--  sort in reverse order using DESC at end of column name  
LIMIT 5; 
```

`Instead of providing column name in ORDER BY we can also provide column position.`

```sql
SELECT column_1, column_2 
FROM schema_name.table_name 
ORDER BY 1 
-- this will sort output by column_1 
LIMIT 5; 
```
`we can also do sorting using multiple columns seperated by "," after ORDER BY` 

```sql
SELECT column_1, column_2 
FROM schema_name.table_name 
ORDER BY column_2 DESC, column_1   
LIMIT 5; 
```










