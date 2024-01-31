# Table of Contents
1. Creating and Dropping Temp Table
2. Calculating Frequency and Distribution (%)
3. Duplicate Records: Processing and Calculations


 Key Tasks and Commands
## 1. Creating and Dropping a Temp Table

```sql
DROP TABLE IF EXISTS sample_table;
CREATE TEMP TABLE sample_table AS
WITH raw_data (id, column_a, column_b) AS (
 VALUES
 (1, 0, 'A'),
 (2, 0, 'B'),
 (3, 1, 'C'),
 (4, 1, 'D'),
 (5, 2, 'D'),
 (6, 3, 'D')
)
SELECT * FROM raw_data;
```

## 2. Calculating Frequency and Distribution (%)

Version 1:
```sql
SELECT
  rating,
  COUNT(*) AS frequency,
  COUNT(*)::NUMERIC / SUM(COUNT(*)) OVER () AS percentage
FROM dvd_rentals.film_list
GROUP BY rating
ORDER BY frequency DESC;
```
Version 2:
```sql
SELECT
  rating,
  COUNT(*) AS frequency,
  ROUND(
    100 * COUNT(*)::NUMERIC / SUM(COUNT(*)) OVER (),
    2
  ) AS percentage
FROM dvd_rentals.film_list
GROUP BY rating
ORDER BY frequency DESC;
```

## 3. Duplicate Records: Processing and Calculations
### Counting of Duplicates - Using Frequency and Group By
```sql
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
ORDER BY frequency DESC
```
### Filtering # of Duplicates - Using Having, Frequency and Group By

> To get the duplicate records to be returned - we would like that COUNT(*) value to be greater than 1.

```sql
-- Don't forget to clean up any existing temp tables!
DROP TABLE IF EXISTS unique_duplicate_records;

CREATE TEMPORARY TABLE unique_duplicate_records AS
SELECT *
FROM health.user_logs
GROUP BY
  id,
  log_date,
  measure,
  measure_value,
  systolic,
  diastolic
HAVING COUNT(*) > 1;

-- Finally let's inspect the top 10 rows of our temp table
SELECT *
FROM unique_duplicate_records
LIMIT 10;
```
### Viewing Duplicate Records/Rows - Using Having, Frequency and Group By

> To get the duplicated records to be returned - we would like the COUNT(*) or Frequency value to be greater than 1.

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
SELECT *
FROM groupby_counts
WHERE frequency > 1
ORDER BY frequency DESC
LIMIT 10;
```
### Example Calculations - Percentage of Table Records Duplicates
> What percentage of records measure_value = 0 when measure = 'blood_pressure' in the health.user_logs table? How many records are there also for this same condition?
```sql
WITH all_measure_values AS (
  SELECT
    measure_value,
    COUNT(*) AS total_records,
    SUM(COUNT(*)) OVER () AS overall_total
  FROM health.user_logs
  WHERE measure = 'blood_pressure'
  GROUP BY 1
)
SELECT
  measure_value,
  total_records,
  overall_total,
  ROUND(100 * total_records::NUMERIC / overall_total, 2) AS percentage
FROM all_measure_values
WHERE measure_value = 0;
```
Output:
<img width="425" alt="image" src="https://github.com/ShaheerKhan200/SQL-Guide-by-Shaheer-Khan/assets/69527800/e7bdb3af-8602-4d43-99bd-ff8bb5058a3d">

### Example Calculations - Percentage of Duplicates


---

---
