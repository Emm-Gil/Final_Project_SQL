## Starting with Data

Question 1: Is there a correlation between page views and number of products ordered?

SQL Queries: 
``` sql
SELECT
  CORR(an.pageviews::DOUBLE PRECISION, ss.total_ordered::DOUBLE PRECISION) AS corr_views_orders
-- Both pageviews and total_ordered needed to be cast to double precision before I could succesfully run the `CORR()` function
FROM
  analytics an
JOIN
  all_sessions al 
  USING(full_visitorid)
JOIN
  sales_by_sku ss 
  USING(product_sku)
WHERE
  an.pageviews IS NOT NULL
```

Answer: With a 0.027, it is safe to assume there is essentially no correlation and if anything there is slightly weak positive correlation.

With virtually no correlation between page views and number of products ordered, I wondered if there was any correlation between time spent on the site and number of products ordered. Could longer periods of time spent on the site encourage visitors to buy more products?

``` sql
SELECT
  CORR(an.timeonsite::DOUBLE PRECISION, ss.total_ordered::DOUBLE PRECISION) AS corr_views_orders
FROM
  analytics an
JOIN
  all_sessions al 
  USING(full_visitorid)
JOIN
  sales_by_sku ss 
  USING(product_sku)
WHERE
  an.timeonsite IS NOT NULL
```

Again, we see a 0.028. Virtually no correlation, 


Question 2: How many visitors purchased a unit on their first visit

SQL Queries: 
``` sql
WITH visits AS(
	SELECT
		units_sold,
		visit_number,
		full_visitorid,
		CASE 
			WHEN (units_sold::INTEGER = 0 or units_sold IS NULL) AND visit_number = 1 THEN 'First Time No Buy'
			WHEN (units_sold::INTEGER > 1 and units_sold IS NOT NULL) AND visit_number = 1 THEN 'First Time Buy'
			WHEN (units_sold::INTEGER > 1 and units_sold IS NOT NULL) AND visit_number >= 2 THEN 'Returning Buy'
			ELSE 'Returning No Buy'
		END as status
	FROM
		analytics)
-- creating table that includes: units_sold, visit_number, full_visitorid and defines the type of visit based on purchase activity
SELECT
	COUNT (DISTINCT full_visitorid) as num_of_visitors,
	status
FROM
	visits
GROUP BY
	status
-- counting how many unique visitor ids are found in each previously defined visit category (eliminating duplicate rows)
```

Answer: 2253 visitors purchased a unit on their first visit

Question 3: how many people did not purchase something on their first visit but returned a made a purchase on their subsequent visit?

SQL Queries:
``` sql
-- first cte is to create a table that includes: units_sold, visit_number, full_visitorid and defines the type of visit based on purchase activity
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
),
-- second cte I used the `MAX()` function as a logical tool to check whether a specific condition occurred at least once for each visitor. `MAX()` works effectively here because the `CASE` statements return binary values. `MAX()` scans all visits for that user and returns `1` if the condition is met in any row, or `0` if it's never met.
visit_status AS (
  SELECT
    full_visitorid,
    MAX(CASE WHEN visit_number = 1 AND status = 'First Time No Buy' THEN 1 ELSE 0 END) AS first_no_buy,
    MAX(CASE WHEN visit_number = 2 AND status = 'Returning Buy' THEN 1 ELSE 0 END) AS second_buy
  FROM
    visits
  GROUP BY
    full_visitorid)
SELECT COUNT(*) AS num_visitors
FROM visit_status
WHERE first_no_buy = 1 AND second_buy = 1;
-- Counting the number of visitorids that meet the specific requirements outlined in the `WHERE` function
```

Answer:
2891 visitors made a purchase on their second visit but not their first.


Question 4: Which product was reordered the most?

SQL Queries:
```sql
SELECT
  product_sku,
  product_name,
  COUNT(DISTINCT full_visitorid) AS num_repeat_buyers
FROM (
	  SELECT an.full_visitorid,
		 product_sku,
		 COUNT(*) AS total_purchases
	  FROM analytics an
	  JOIN
		all_sessions al
		ON an.visit_id = al.visitid
	  JOIN
		sales_by_sku
		USING(product_sku)
	  WHERE
		units_sold::INTEGER > 0
	  GROUP BY
		an.full_visitorid, product_sku
	  HAVING
		COUNT(*) > 1
) AS repeated 
JOIN
	products p
	ON p.sku=repeated.product_sku
GROUP BY
	product_sku,
	product_name
ORDER BY
	num_repeat_buyers DESC
LIMIT 1;
```
Answer: Learning Thermostat 3rd Gen-USA - White is the most reordered product

