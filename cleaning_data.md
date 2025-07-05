What issues will you address by cleaning the data?





Queries:
Below, provide the SQL queries you used to clean your data.

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
 
