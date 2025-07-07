Question 1: Is there a correlation between page views and products ordered?

SQL Queries: 
``` sql
SELECT
  CORR(an.pageviews::DOUBLE PRECISION, ss.total_ordered::DOUBLE PRECISION) AS corr_views_orders
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



Question 2: 

SQL Queries:

Answer:



Question 3: 

SQL Queries:

Answer:



Question 4: 

SQL Queries:

Answer:



Question 5: 

SQL Queries:

Answer:
