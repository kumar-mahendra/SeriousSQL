#### Exercies - Cleaning Dirty Data

- Which `id` value has the most number of duplicate records in the `health.user_logs` table?

```sql
WITH deduplicated_table AS (
  SELECT
    *,
    COUNT(*) AS duplicates_count
  FROM
    health.user_logs
  GROUP BY
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
SELECT
  id,
  SUM(duplicates_count) AS output
FROM
  deduplicated_table
WHERE
  duplicates_count > 1  
GROUP BY
  id
ORDER BY
  output DESC;
```

- Which `log_date` value had the most duplicate records after removing the max duplicate `id` value from question 1?

```sql
WITH deduplicated_table AS (
  SELECT
    *,
    COUNT(*) AS duplicates_count
  FROM
    health.user_logs
  WHERE id!='054250c692e07a9fa9e62e345231df4b54ff435d'
  GROUP BY
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
SELECT
  log_date,
  SUM(duplicates_count) AS output
FROM
  deduplicated_table
WHERE
  duplicates_count > 1  
GROUP BY
  log_date
ORDER BY
  output DESC;
```

- Which ``measure_value` had the most occurences in the `health.user_logs` value when `measure` = 'weight'?

```sql
SELECT
  measure_value,
  COUNT(*) as measure_value_frequencies
FROM
  health.user_logs
WHERE
  measure = 'weight'
GROUP BY
  measure_value
ORDER BY
  measure_value_frequencies DESC;
```

- How many single duplicated rows exist when `measure` = 'blood_pressure' in the `health.user_logs`? How about the total number of duplicate records in the same table?

```sql
WITH deduplicated_table AS (
  SELECT
    *,
    COUNT(*) as frequency
  FROM
    health.user_logs
  WHERE
    measure = 'blood_pressure'
  GROUP BY
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
SELECT
  COUNT(*) AS single_duplicated_rows,
  SUM(frequency) as total_duplicated_records
FROM
  deduplicated_table
WHERE
  frequency > 1
```

- What percentage of records `measure_value` = 0 when `measure` = 'blood_pressure' in the `health.user_logs` table? How many records are there also for this same condition?

```sql
WITH deduplicated_table AS (
  SELECT
    *
  FROM
    health.user_logs
  WHERE
    measure = 'blood_pressure'
)
SELECT
  measure_value,
  SUM(COUNT(*)) OVER() AS total_records
  ROUND(100 * COUNT(*) :: NUMERIC / SUM(COUNT(*)) OVER(), 2) AS percentage 
FROM
  deduplicated_table
GROUP BY
  measure_value
ORDER BY measure_value -- measure_value=0 will be on top 
LIMIT 1 ; 
```
- What percentage of records are duplicates in the `health.user_logs` table?

```sql
WITH groupby_counts AS (
  SELECT
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic,
    COUNT(*) AS frequency
  FROM health.user_logs
  GROUP BY
    id,
    log_date,
    measure,
    measure_value,
    systolic,
    diastolic
)
SELECT
  -- Need to subtract 1 from the frequency to count actual duplicates!
  -- Also don't forget about the integer floor division!
  ROUND(
    100 * SUM(CASE
        WHEN frequency > 1 THEN frequency - 1
        ELSE 0 END
    )::NUMERIC / SUM(frequency),
    2
  ) AS duplicate_percentage
FROM groupby_counts;
```

alternate way using subqueries 

```sql
WITH deduped_logs AS (
  SELECT DISTINCT *
  FROM health.user_logs
)
SELECT
  ROUND(
    100 * (
      (SELECT COUNT(*) FROM health.user_logs) -
      (SELECT COUNT(*) FROM deduped_logs)
    )::NUMERIC /
    (SELECT COUNT(*) FROM health.user_logs),
    2
  ) AS duplicate_percentage;
```

