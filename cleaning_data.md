## Data Cleaning
Queries:
Below, provide the SQL queries you used to clean your data.

``` sql
SELECT
  city,
  country,
  SUM(total_transaction_revenue::INTEGER) as total_revenue
-- casting total_transaction_revenue to an Integer after having it as VARCHAR to allow for SUM function
FROM
  all_sessions
WHERE
  total_transaction_revenue IS NOT NULL
  AND city != 'not available in demo dataset'
-- Removing NULLs and inconsistent data
SELECT
	city,
	ROUND(AVG(total_ordered),2) AS average_orders
-- Rounding average  to 2 decimal places to condense size of the row
FROM
	all_sessions al
WITH visits AS (
  SELECT
    units_sold,
    visit_number,
    full_visitorid,
    CASE 
      WHEN (units_sold::INTEGER = 0 OR units_sold IS NULL) AND visit_number = 1 THEN 'First Time No Buy'
      WHEN units_sold::INTEGER > 0 AND visit_number = 1 THEN 'First Time Buy'
      WHEN units_sold::INTEGER > 0 AND visit_number >= 2 THEN 'Returning Buy'
      ELSE 'Returning No Buy'
    END AS status
  FROM analytics
-- renaming and reorganizing information using CASE statement
SELECT
    full_visitorid,
    MAX(CASE WHEN visit_number = 1 AND status = 'First Time No Buy' THEN 1 ELSE 0 END) AS first_no_buy,
    MAX(CASE WHEN visit_number = 2 AND status = 'Returning Buy' THEN 1 ELSE 0 END) AS second_buy
  FROM visits
  GROUP BY full_visitorid
-- using MAX function to identify if a visit occured atleast once and to avoid double counting
```
 
