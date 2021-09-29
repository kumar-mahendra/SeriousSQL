

### Table Joins - Some Interview Questions

**Q-1 How would you explain table joins to a senior executive who has zero knowledge of data?**

Answer : 

Suppose you are in a restaurent and you want to order dinner. Waiter gave you two menu cards where first menu contains **main-course** options and second menu card contains **deserts** . You can specify what kind of  food  you want tonight using some criteria.  

In data analytics,  menu cards are seen as data tables and that applying that  certain condition on these tables to generate new table can be seen as **table join** operation.


**Q-2 - What is the difference between a left join, inner join and left-semi join when would you use one over another?**

Answer : 

In Left join all rows of left table will be retained in final ouptut  and if there are rows on right table which satiefies matching criterion then they will be displayed in ouput table otherwise columns corresponding to columns of second table will be filled with `null`. 

In inner join only rows in left table which satisfies matching criterion will be displayed along with corresponding matching selected columns of right table. (Think of this as intersection of two tables)

left semi join is similar to inner join except it only retain columns of left table and not right table. 


Use LEFT JOIN when you need to keep all rows on the left table but want to join on additional records from the right table. 
Use INNER JOIN when you need the intersection of both left and right tables and you also need to access columns from the right table.
Use LEFT SEMI JOIN or WHERE EXISTS when you only need to keep records from the left table that exist in the right table






