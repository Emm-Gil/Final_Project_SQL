Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
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



Answer:
TOP THREE
Cities = San Francisco, Atlanta, Sunnyvale
Countries = United States, Israel, Australia


**Question 2: What is the average number of products ordered from visitors in each city and country?**


SQL Queries:
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


Answer: Theres 225 rows for city alone, but code does run.


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







