### Table Joins ;

Table joins help us access rows from multiple tables into single query. 

**Note** : Do you know  table joins uses low level relational algebra transformation? *Mahendra do it in near future !!!*

---

#### 1. `INNER JOIN` 

Inner join perform intersection of two tables based on a common column between two tables. 

```sql
SELECT
  table1.common_col,
  table1.col_1,
  table2.col_2,
  table2.col_3,
  table3.col_4
FROM
  table1
  INNER JOIN table2 ON table1.common_col = table2.common_col;
```

*Observations on `INNER JOIN`*

- It is not compulsory to take common_col for performing innner join . You can take two different column for inner join too but in that case they may be one-to-many or many-to-one mapping from table 1 to table2.
- Also you can put any condition inside ON to map two tables rows (need not be ONE-ONE mapping)
- If you provide condition which involves only 1 table then all selected rows from first table will be matched to every row of second table.
- You cannot perform inner join on two tables with same same i.e. table1 name should be different from table2 although they may have same content as SQL only checks for table names. 


**Note** - <ins>*Table and column references*</ins>

- If you use `SELECT *` OR `SELECT table1.* , table2.*` then even columns which is used for inner join in two tables will be come in output twice which is  not recommended most of times. It is advised to alaways use table references/aliases to improve readability and quality of your code. 

#### 2. `LEFT JOIN`

`LEFT JOIN` keeps all rows of table on left (also called **base** table) and if matching records are not found for columns corresponding to right table, rows corresponding to column entries are filled with `null` . 


```sql
SELECT
  table1.common_col,
  table1.col_1,
  table2.col_2,
  table2.col_3,
  table3.col_4
FROM
  table1
  LEFT JOIN table2 ON table1.common_col = table2.common_col;
```

**Note** - *Output of `LEFT JOIN` depends on order of tables they appear. I repeat all rows of left table will be kept.*



#### 3. `FULL JOIN`

It is similar to `LEFT JOIN` but only difference is this time both tables are used as **base** table i.e. all rows of both tables will be there in output and where their is no matching , corresponding columns will be filled with `null`.

```sql
SELECT
  table1.common_col,
  table1.col_1,
  table2.col_2,
  table2.col_3,
  table3.col_4
FROM
  table1
  FULL JOIN table2 ON table1.common_col = table2.common_col;
```


#### 4. `CROSS JOIN`

Catersian Join ( similar to catesian product) of two tables. 
Every row in table 1 will be mapped to every other row in table 2 . There is no `ON` condition in `CROSS JOIN` 


```sql
SELECT
  table1.common_col,
  table1.col_1,
  table2.col_2,
  table2.col_3,
  table3.col_4
FROM
  table1
  CROSS JOIN table2 ;
```

Alternatively, we can also do cross join **without** using `CROSS JOIN` by seperating tables by comms(",") like this


```sql
SELECT
  table1.common_col,
  table1.col_1,
  table2.col_2,
  table2.col_3,
  table3.col_4
FROM
  table1, table2;
```

**Note**  - *Not recommended in practice. Just for knowledge*

- We can also perform `INNER JOIN` implicitly using `WHERE` clause and above implementation of cross join 


```sql
SELECT
  table1.common_col,
  table1.col_1,
  table2.col_2,
  table2.col_3,
  table3.col_4
FROM
  table1, table2
WHERE 
  table1.common_col = table2.common_col; 
```

**Note** *Table Aliases* 

- [ ]  using first letter of big table names as alises 
   Example - `customer_sales_prices AS csp `
- [ ] using logical aliases names for tables 
   Example - `customer_sales_prices AS sales` 
- [x] giving a number to each table starting from 1 to N=total_no_of_tables and assign table alias as `t1`, `t2`, .. so on. 



**Note** the `||` Operator 

This can be used to concatenate multiple column containing strings into single column 

<ins>*table1*</ins>

| person_name | animal_name | 
|--|--|
| Mahendra  | parrots | 
| Naveen | dogs | 

```sql
SELECT person_name || ' loves ' || animal_name || ' !! ' AS text_output 
FROM table1
```

<ins>*Output*</ins>

| text_output |
|--|
| Mahendra loves parrots !! | 
| Naveen loves dogs !! | 


#### 5. `WHERE EXISTS` OR `LEFT SEMI JOIN`

It is similar to Left join except it only returns rows from left table . No rows or columns from right table will be included in output .



In PostgreSQL can `WHERE EXISTS` clause is used to perform left semi join operation as `LEFT SEMI JOIN` don't work but it may work in other flavors of SQL. 


```sql
SELECT
  table1.common_col,
  table1.col_1
FROM table1 
WHERE EXISTS ( --- This won't return any data , just check for WHERE condition
  SELECT 1     
  FROM table2 
  WHERE table1.common_col = table2.common_col
);
```

Also you can perform `LEFT SEMI JOIN` as combining `LEFT JOIN` with `DISTINCT` keyword on joining column as `WHERE` clause as follows

```sql
SELECT
  DISTINCT table1.common_col,
  table1.col_1
FROM table1 
LEFT JOIN table2 ON table2.common_col = table1.common_col 
WHERE table2.common_col is NOT NULL;  
```

Also you can perform `LEFT SEMI JOIN` as combining `INNER JOIN` with `DISTINCT` keyword on joining columns with `WHERE` clasue as follows 

```sql
SELECT
  DISTINCT table1.common_col,
  table1.col_1
FROM table1 
INNER JOIN table2 ON table2.common_col = table1.common_col 
-- Note we removed NULL check condition as it is INNER JOIN
```

Alternatively, if you are using any sql environment which support `LEFT SEMI JOIN` use following 

```sql
SELECT
  table1.common_col,
  table1.col_1
FROM table1 
LEFT SEMI JOIN table2  ON table1.common_col = table2.common_col; 
```

#### 6. `WHERE NOT EXISTS` OR `ANTI JOIN`

This is exactly opposite of `LEFT SEMI JOIN` 

It is expressed as a `WHERE NOT EXISTS` clause in PostgreSQL but similar to the `LEFT SEMI JOIN` some SQL flavours support a direct `ANTI JOIN` syntax also.



```sql
SELECT
  table1.common_col,
  table1.col_1
FROM table1 
WHERE NOT EXISTS ( --- This won't return any data , just check for WHERE condition
  SELECT 1     
  FROM table2 
  WHERE table1.common_col = table2.common_col
);
```

Another popular alternative for `ANTI JOIN` using `LEFT JOIN` and `WHERE` clause is 


```sql
SELECT
  DISTINCT table1.common_col,
  table1.col_1
FROM table1 
LEFT JOIN table2 ON table2.common_col = table1.common_col 
WHERE table2.common_col is NULL;  
```


The regular `ANTI JOIN` syntax also works for some SQL flavours too - this won’t run in PostgreSQL though!


```sql
SELECT
  table1.common_col,
  table1.col_1
FROM table1 
ANTI JOIN table2  ON table1.common_col = table2.common_col; 
```



**Note** - *Joining on columns with null entries*

- `INNER JOIN` does not join `NULL` columns  
- `LEFT JOIN`  does join  `NULL` columns when joining column is NOT `NULL` . i.e. It can't map left table joining column entry = `NULL` to right table with joining column entry = `NULL` 
- `LEFT SEMI JOIN` also does not join `NULL` columns like `INNER JOIN` 
- `ANIT JOIN` behave similar to `LEFT JOIN` when treating `NULL` values i.e. does not consider joining column entry `NULL` as identical entries in left and right table. 


**Note** - `IN` and `NOT IN` clauses 

- `IN` as an alternative of `WHERE EXISTS` / `LEFT SEMI JOIN` 

```sql
SELECT
  table1.common_col,
  table1.col_1
FROM table1 
WHERE table1.common_col IN (
  SELECT common_col 
  FROM table2 
);
```

- `NOT IN` as an alternative of `WHERE NOT EXISTS` / `ANTI JOIN` 

```sql
SELECT
  table1.common_col,
  table1.col_1
FROM table1 
WHERE table1.common_col NOT IN ( 
  SELECT common_col     
  FROM table2 
);

```
***conclusion*** It is recommended not to use `IN` and `NOT IN` when there are null values in rows as `NOT IN` does not return anything if there are `NULL` value(s) in joining column (an unexpected behaviour)


#### 7. `UNION` ( & `UNION ALL` ) ,  `INTERSECTION`  & `EXCEPT`

These 3 methods are pretty straightforward however as it matches directly with set notation, however the only condition is that all of the `SELECT` result sets must have columns with the same data types - the column names have no impact.

Difference between `UNION` and `UNION ALL` is that `UNION` runs `DISTINCT` on top of union but `UNION ALL` does not . 


writing commands is simple just use set operations between various queries you want to join. 

**dvidie**


















