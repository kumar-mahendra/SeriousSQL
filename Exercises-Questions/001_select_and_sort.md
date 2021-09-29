#### Exercise - SELECT and ORDER BY 

- What is the `name` of the category with the highest `category_id` in the `dvd_rentals.category` table?

```sql
SELECT
  name,
  category_id
FROM
  dvd_rentals.category
ORDER BY
  category_id DESC
LIMIT
  5;
```
- For the films with the longest `length`, what is the `title` of the “R” `rating` film with the lowest `replacement_cost` in `dvd_rentals.film` table?

```sql
SELECT
  title,
  length,
  replacement_cost,
  rating
FROM
  dvd_rentals.film ORDERY BY length desc
LIMIT  
  5; 
```

- Who was the `manager` of the store with the highest `total_sales` in the `dvd_rentals.sales_by_store` table?

```sql
SELECT
  manager,
  total_sales
FROM
  dvd_rentals.sales_by_store
ORDER BY
  total_sales DESC
LIMIT
  5;
```

- What is the `postal_code` of the city with the 5th highest `city_id` in `the dvd_rentals.address` table?

```sql
SELECT 
  postal_code,
  city_id
FROM 
  dvd_rentals.address
ORDER BY 
  city_id DESC 
LIMIT 
  5; 
```

<ins>Appendix</ins>

- **;** is known as a “statement terminator” and is used to tell the SQL engine that this is the end of a SQL statement.
- Postgres also allows for an `OFFSET` value to specify the number of rows to skip before taking the records based off the `LIMIT` clause.
 - Text fields will be sorted in alphabetical order, but be careful when some of these text fields start with numbers or non-alphabetical characters.
 - by default NULL value come at last in `ORDER BY` output. When we put the `NULLS FIRST` expression at the end of the `ORDER BY` clause we will see a nulls will appear first.
   

