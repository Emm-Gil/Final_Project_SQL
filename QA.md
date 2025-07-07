What are your risk areas? Identify and describe them.



QA Process:
Describe your QA process and include the SQL queries used to execute it.


With the number of rows in these tables, visually identifying a pattern proves difficult. Many rows contained null values, inconsistent data or irrelevant data.

For example, lets say you are looking to identify if different tiers of "social engagement" produced a higher number of products ordered. You could run a query like this:

``` sql
  SELECT
	social_engagement_type,
	SUM(total_ordered)
FROM
	analytics a
JOIN
	all_sessions al
	USING(full_visitorid)
JOIN
	sales_by_sku ss
	USING(product_sku)
GROUP BY
	social_engagement_type;
```
However, you'd find that only one social engagement type appears in the table, "Not Socially Engaged".
To confirm this is not an error in the code you could narrow your scope and identify how many types are available in the table:

```sql
SELECT
	DISTINCT social_engagement_type
FROM
	analytics
```
This confirmes that the only social engagement type available in the table is "Not Socially Engaged".


