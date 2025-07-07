##QUALITY ASSURANCE

This database appeared to include many duplicates, around 60% of the analytics table is a duplicate row:
```sql
SELECT
	COUNT(*)
FROM
	analytics
```
Count = 4,301,122
``` sql
SELECT
	COUNT(*) 
FROM (
	SELECT DISTINCT
		* 
  	FROM
		analytics
) AS non_duplicate_rows;
```
Count = 1,739,308
Identifying this early in the process supporting proper data cleaning in order to obtain more accurate responses.

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
This confirms that the only social engagement type present in the table is "Not Socially Engaged", from this I decided that social engagement was not something I could use in a query to provide any insightful imformation.

Some columns also contained entirely null values, like the userid column in the analytics table:
``` sql
SELECT
	userid
FROM
	analytics
WHERE
	userid IS NOT NULL
```
Resulting in 0 rows, to confirm this isnt an error I checked the opposite:
``` sql
SELECT
	userid
FROM
	analytics
WHERE
	userid IS NULL
```
This produced 4,301,122 rows which, as identified before, is the total number of rows in the analytics table. This confirms that the entire userid column in the analytics table is NULL.



