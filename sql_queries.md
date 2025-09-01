# 📜 SQL Queries Used in Gold Project Analysis
---
### 1. Data Cleaning
```sql
--- Checking For Missing Values
SELECT 
SUM(CASE WHEN open IS NULL THEN 1 ELSE 0 END) AS missing_open,
SUM(CASE WHEN high IS NULL THEN 1 ELSE 0 END) AS missing_high,
SUM(CASE WHEN low IS NULL THEN 1 ELSE 0 END) AS missing_low,
SUM(CASE WHEN close IS NULL THEN 1 ELSE 0 END) AS missing_close,
FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean` 
```
```sql
--- Converted Date from string to date type
CREATE OR REPLACE
TABLE `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean` AS
SELECT PARSE_DATE('%Y.%m.%d',date) AS date, open, high, low, close
FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025`
```
---
### 2. Yearly Average Close Price
```sql
SELECT 
EXTRACT (YEAR FROM Date(date)) AS year,
ROUND(AVG(close),2)AS avg_close
FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean` 
GROUP BY year
ORDER BY year;
```
---
### 3. Yearly Highest and Lowest Close Price
```sql
SELECT 
EXTRACT (YEAR FROM Date(date)) AS year,
MAX(high) AS yearly_high,
MIN(low) AS yearly_low
FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean` 
GROUP BY year
ORDER BY year;
```
---
### 4. Yearly Volatility
```sql
SELECT 
EXTRACT (YEAR FROM Date(date)) AS year,
ROUND(AVG(high-low),2) AS avg_volatility
FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean` 
GROUP BY year
ORDER BY year;
```
---
### 5. Top 5 Close Price
```sql
SELECT 
date , close
FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean` 
WHERE date>='2015-01-01'
ORDER BY close DESC
LIMIT 5;
```
---
### 6. Monthly Average Close Price
```sql
SELECT 
EXTRACT (YEAR FROM Date(date)) AS year,
EXTRACT (MONTH FROM Date(date)) AS month,
ROUND(AVG(close),2) AS avg_close
FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean` 
GROUP BY year, month
ORDER BY year, month;
```
---
### 7. Top 5 Increases and Bottom 5 Decreases vs Price Change
```sql
WITH changes AS (SELECT 
    date AS month_date,
    ROUND(close, 2) AS close_price,
    ROUND(close - LAG(close) OVER (ORDER BY date), 2) AS price_change
  FROM 
    `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean`)
( SELECT 'Top 5 Increases' AS category, month_date, close_price, price_change
  FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean`
  WHERE price_change IS NOT NULL
  ORDER BY price_change DESC
  LIMIT 5)
UNION ALL
(SELECT 'Bottom 5 Decreases' AS category, month_date, close_price, price_change
  FROM `cool-reach-464612-r4.gold_prices.Gold_Prices_2015_2025_clean`
  WHERE price_change IS NOT NULL
  ORDER BY price_change ASC
  LIMIT 5)
ORDER BY category, price_change DESC;
```
---



