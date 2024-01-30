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

```sql

```

---

---
