Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```sql
--1.
SELECT
  city,
  country,
  SUM(total_transaction_revenue::INTEGER) AS total_revenue
FROM
  all_sessions
WHERE
  total_transaction_revenue IS NOT NULL
  AND city != 'not available in demo dataset'
GROUP BY
  city,
  country
ORDER BY
  total_revenue DESC;
--2.
SELECT
  country,
  SUM(total_transaction_revenue::INTEGER) AS total_revenue
FROM
  all_sessions
WHERE
  total_transaction_revenue IS NOT NULL
GROUP BY
  country
ORDER BY
  total_revenue DESC;
```


Answer:
TOP THREE
Cities = San Francisco, Atlanta, Sunnyvale
Countries = United States, Israel, Australia


**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
```sql
-- 1.
SELECT
	city,
	ROUND(AVG(total_ordered),2) AS average_orders
FROM
	all_sessions al
JOIN
	sales_by_sku ss
	USING(product_sku)
WHERE
    city != '(not set)' AND city != 'not available in demo dataset'
GROUP BY
	city
ORDER BY
	city
-- 2.
SELECT
	country,
	ROUND(AVG(total_ordered),2) AS average_orders
FROM
	all_sessions al
JOIN
	sales_by_sku ss
	USING(product_sku)
WHERE
	country != '(not set)'
GROUP BY
	country
ORDER BY 
	country
```


Answer: Theres 225 rows for city alone, but code does run.


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```sql
1-- 
SELECT *
FROM (
  SELECT
    city,
    v2_product_category,
    COUNT(*) AS total_orders,
    RANK() 
		OVER (PARTITION BY city
		ORDER BY COUNT(*) DESC) AS city_rank
  FROM
    all_sessions
  JOIN
    sales_by_sku 
	USING(product_sku)
  WHERE
    city !='(not set)' AND city != 'not available in demo dataset'
  	AND v2_product_category != '(notset)'
  GROUP BY
    city, v2_product_category
)
WHERE city_rank = 1
2--
SELECT *
FROM (
  SELECT
    country,
    v2_product_category,
    COUNT(*) AS total_orders,
    RANK() 
		OVER (PARTITION BY country
		ORDER BY COUNT(*) DESC) AS country_rank
  FROM
    all_sessions
  JOIN
    sales_by_sku 
	USING(product_sku)
  WHERE
    country !='(not set)' AND v2_product_category != '(notset)'
  GROUP BY
    country, v2_product_category
)
WHERE country_rank = 1
```

Answer:
While some countries/cities have a more varied array of top categories, certain countries like Canada for example has a distinct leader


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
```sql
1--
WITH rank_tbl AS(
SELECT
    product_name,
    city,
    COUNT(*) AS num_ordered,
    RANK() OVER (
      PARTITION BY city
      ORDER BY COUNT(*) DESC
    ) AS rank
  FROM
    products p
  JOIN
    sales_by_sku ss ON p.sku = ss.product_sku
  JOIN
    all_sessions USING(product_sku)
  WHERE 
    city !='(not set)' AND city != 'not available in demo dataset'
  GROUP BY
    city, product_name
)
SELECT *
FROM rank_tbl
WHERE rank = 1
2-- 
WITH rank_tbl AS(
SELECT
    product_name,
    country,
    COUNT(*) AS num_ordered,
    RANK() OVER (
      PARTITION BY country
      ORDER BY COUNT(*) DESC
    ) AS rank
  FROM
    products p
  JOIN
    sales_by_sku ss ON p.sku = ss.product_sku
  JOIN
    all_sessions USING(product_sku)
  WHERE 
    country !='(not set)'
  GROUP BY
    country, product_name
)
SELECT *
FROM rank_tbl
WHERE rank = 1
```
Answer:




**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```sql
-- 1
WITH revenue AS
	(SELECT
		country,
		SUM(unit_price*total_ordered) AS total_revenue
	FROM
		analytics an
	JOIN
		all_sessions al
		ON an.visit_id = al.visitid
	JOIN
		sales_by_sku
		USING(product_sku)
	WHERE
		country != '(not set)'
	GROUP BY
		country)
SELECT
	country,
	TO_CHAR(total_revenue, 'FM999,999,999,999,990.00') AS dollar_revenue
FROM
	revenue
ORDER BY
	total_revenue DESC

-- 2
WITH revenue AS
	(SELECT
		city,
		SUM(unit_price*total_ordered) AS total_revenue
	FROM
		analytics an
	JOIN
		all_sessions al
		ON an.visit_id = al.visitid
	JOIN
		sales_by_sku
		USING(product_sku)
	WHERE
		city != '(not set)'
	GROUP BY
		city)
SELECT
	city,
	TO_CHAR(total_revenue, 'FM999,999,999,999,990.00') AS dollar_revenue
FROM
	revenue
ORDER BY
	total_revenue DESC
```


Answer:







