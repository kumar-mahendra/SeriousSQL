

### Table Joins - Some Interview Questions

**Q-1 How would you explain table joins to a senior executive who has zero knowledge of data?**

Answer : 

Suppose you are in a restaurent and you want to order dinner. Waiter gave you two menu cards where first menu contains **main-course** options and second menu card contains **deserts** . You can specify what kind of  food  you want tonight using some criteria.  

In data analytics,  menu cards are seen as data tables and that applying that  certain condition on these tables to generate new table can be seen as **table join** operation.


**Q-2 - What is the difference between a left join, inner join and left-semi join when would you use one over another?**

Answer : 

Use LEFT JOIN when you need to keep all rows on the left table but want to join on additional records from the right table. 
Use INNER JOIN when you need the intersection of both left and right tables and you also need to access columns from the right table.
Use LEFT SEMI JOIN or WHERE EXISTS when you only need to keep records from the left table that exist in the right table


**Q-3 - How do duplicates in the foreign key affect joins - what are some common issues and some strategies to avoid them?**

Answer :  

duplicates in foreign key can produce ouput table with where rows can repeatating and it affects both 
1) Efficieny of sql query as for large dataset presence of duplicate rows may slow down  query which is not good :s 

2) From business point of view if we are unaware of duplicates in output , we may make decision based on these output  which could affect business as whole. 

Best practice is always used `DISTINCT *` on right table before performing table joins . 

**Q-4 - What is the ON condition for table joins and what different varieties are available to use with data?**

Answer : 

ON condition is used to specify criteria based on which two tables will be joined .

There are 5 types of joins with which we can use ON clause they are 

- `INNER JOIN` 
- `LEFT JOIN` 
- `FULL JOIN` 
- `LEFT SEMI JOIN` 
- `ANTI JOIN` 

to refer to syntax visit  to notes section in `serious-sql` repository and see `006_table_joins.md` 


**Q-5 - What are aliases and how might they be used to speedup SQL development in teams?**

Aliases means alternate names for something. In SQL query we can give alise to Table names , CTEs , Subquery etc. using `AS` keyword. 

Based on common agreement in team in workplace , team members can agree on common naming convention of table aliases . generally people use table aliases as t1, t2 t3 ,..so on for active tables in database team are using and keeping a common index which shows to which table each of ti's refer to. such practice can significantly reduce the writing long tables names again and again and make code more readable and faster to write. 


--- 











