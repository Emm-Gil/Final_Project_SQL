## Starting with Questions

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```sql
--1.
SELECT
  city,
  SUM(total_transaction_revenue::INTEGER) AS total_revenue
-- Casting total_trasaction_revenue as an integer to be able to run the `SUM()` function
FROM
  all_sessions
WHERE
  total_transaction_revenue IS NOT NULL
AND city != '(not set)' AND city != 'not available in demo dataset'
-- Removing irrelevant/unnecessary data
GROUP BY
  city
ORDER BY
  total_revenue DESC;
-- Formatting the table to have the highest values first
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
-- Rounding the average of total_ordered to do decimal places for improved readability
FROM
	all_sessions al
JOIN
	sales_by_sku ss
	USING(product_sku)
-- Both tables have the exact same column name, chose to use `USING()` function here for simplicity
WHERE
    city != '(not set)' AND city != 'not available in demo dataset'
GROUP BY
	city
ORDER BY
	average_orders DESC
-- Formatting the table to have the highest values first
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
	average_orders DESC
```


Answer: 

CITY:

![city2](./final_project_sql/images/cityq2/cityq2.png)

COUNTRY

![country2](./final_project_sql/images/countryq2/countryq2.png)



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
-- Counts how many orders there were for each city-category combo
    RANK() 
		OVER (PARTITION BY city
		ORDER BY COUNT(*) DESC) AS city_rank
--  Ranks the number of orders within each city
  FROM
    all_sessions
  JOIN
    sales_by_sku 
	USING(product_sku)
  WHERE
    city !='(not set)' AND city != 'not available in demo dataset'
  	AND v2_product_category != '(notset)'
-- Filters out unhelpful or incomplete city/category values
  GROUP BY
    city, v2_product_category
)
WHERE city_rank = 1
-- Displays only the top ranked product categories for each city
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

CITY:

![city3](./final_project_sql/images/cityq3/cityq3.png)

COUNTRY

![country3](./final_project_sql/images/countryq3/countryq3.png)


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:
```sql
1--
WITH rank_tbl AS(
SELECT
    product_name,
    city,
    COUNT(*) AS num_ordered,
-- Counts how many times each product was ordered in each city
    RANK() OVER (
      PARTITION BY city
      ORDER BY COUNT(*) DESC
    ) AS rank
-- Ranks the products within each city by number of orders
  FROM
    products p
  JOIN
    sales_by_sku ss ON p.sku = ss.product_sku
  JOIN
    all_sessions USING(product_sku)
  WHERE 
    city !='(not set)' AND city != 'not available in demo dataset'
-- Filters out unhelpful or incomplete city/category values
  GROUP BY
    city, product_name
)
SELECT *
FROM rank_tbl
WHERE rank = 1
-- Displays only the top ranked product categories for each city
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

CITY:

![city4](./final_project_sql/images/cityq4/cityq4.png)

COUNTRY

![country](./final_project_sql/images/countryq4/countryq4.png)


**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```sql
-- 1
WITH revenue AS
	(SELECT
		country,
		SUM((unit_price / 1000000)*total_ordered) AS total_revenue
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
-- Calculates revenue for each row by multiplying unit_price × total_ordered
-- We were advised to divide unit price by 1000000
SELECT
	country,
	TO_CHAR(total_revenue, 'FM999,999,999,999,990.00') AS dollar_revenue
-- Formatting revenue to display it more similarly to currency
FROM
	revenue
ORDER BY
	total_revenue DESC
--  Displays highest revenue first

-- 2
WITH revenue AS
	(SELECT
		city,
		SUM((unit_price / 1000000)*total_ordered) AS total_revenue
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

CITY:

![country5](./final_project_sql/images/countryq5/countryq5.png)

COUNTRY

![city5](./final_project_sql/images/cityq5/cityq5.png)






