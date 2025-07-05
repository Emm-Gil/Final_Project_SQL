Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
--1.
SELECT
  c.city,
  al.country,
  SUM(al.total_transaction_revenue::INTEGER) AS total_revenue
FROM
  all_sessions al
JOIN
  all_sessions c
  USING(full_visitorid)
WHERE
  al.total_transaction_revenue IS NOT NULL
  AND c.city != 'not available in demo dataset'
GROUP BY
  c.city,
  al.country
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



Answer:





**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:



Answer:





**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:



Answer:





**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:



Answer:







