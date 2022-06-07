### Multiple Table Joins 

1. Before we know what tables to join in what order we need to find key columns by reverse engineering the output we want at the end . 
2. Mapped out our table journey by linking the tables with our key columns via the foreign keys in the ERD
3. when performing table joins follow this framework : 
    - What is the purpose of joining these two tables?
      - What contextual hypotheses do we have about the data?
      - How can we validate these assumptions?
    - What is the distribution of foreign keys within each table?
    - How many unique foreign key values exist in each table?  
4. We can perform multiple table joins by using join operation say `inner join`  on output of previous table join like this 

```sql 
select t1.col_1, 
       t2.col_2, 
       t3.col_3, 
       ...
       tn.col_n
from t1
inner join t2 on t2.foreign_key_1 = t1.foreign_key_1 
inner join t3 on t3.foreign_key_2 = t2.foreign_key_2 
...
inner join tn on tn.foreien_key_n-1 = tn-1.foregin_key_n-1 

```