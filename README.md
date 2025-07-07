# Final SQL Project
⭐*Emma Gilbert*⭐

## Project/Goal

The goal of this project was to challenge my SQL abilities and to create a benchmark for future growth.  
It provided an opportunity to work with an MRD, practice more complex queries and build confidence in data exploration using PostgreSQL.
This project involved analyzing visitor behavior, purchase activity, and product performance across several related tables.  
By cleaning and joining data from different sources, I was able to obtain useful insight from the database.


## Process

### 1. Set Up

- Cloned the starter repo from GitHub and created a new working folder
- Identified data types from the five tables provided: `products`, `sales_by_sku`, `all_sessions`, `analytics`, and `inventory`
- Set up the table and imported the database in pgAdmin
- 
### 2. Data Exploration

- Identified primary and foreign key candidates
- Discovered that `sku` in `products` was the only unique identifier across multiple tables (named `product_sku` in others)

### 3. Data Cleaning

- Removed duplicate rows using `SELECT DISTINCT`
- Cast columns like `visit_start_time` from `VARCHAR` to `TIMESTAMP` using `TO_TIMESTAMP()`
- Checked for missing or inconsistent values such as NULL using `WHERE` and `CASE` statements

### 4. Determined Relationships

- Created a primary key on `products.sku`
- Added foreign key constraints from `sales_by_sku.product_sku` and `all_sessions.product_sku` to `products.sku`
- Created an Entity Relationship Diagram (ERD) to visualize table connections

### 5. Write Queries to Answer Key Questions

- Counted how many visitors purchased on a returning visit
- Found the most reordered product by counting repeat purchases across visits
- Used CTEs and `DISTINCT` logic to simplify and organize complex queries

### 6. Build README and Upload ERD

- Created a `README.md` file to summarize project goals, steps, wins and opportunities
- Used Git and GitHub to track changes and submit the final version
- Uploaded ERD to the repo

## Tools Used

- PostgreSQL
- pgAdmin 4
- Git & GitHub


## Project Wins

- Created a normalized schema and defined foreign key relationships
- Cleaned data and identified duplicates across large datasets (~4 million rows)
- Used `CASE` statements, `JOIN`, `WINDOW FUNCTIONS`, and CTEs to answer layered business questions
- Wrote queries to identify:
  - Visitors who purchased on returning visits
  - Most frequently reordered products
  - First-time vs returning buying behavior


## Project/Personal Opportunities

- Continued to develop time management skills, occasionally spent too long troubleshooting challenges instead of pivoting
- Encountered Mac OS security restrictions that required lengthy workarounds, which impacted overall workflow and consumed significant time
- Faced difficulties using Git and GitHub consistently and correctly, highlighting a key area for further practice and learning

## Future Goals

- Create temporary tables to avoid needing to do mulitple joins frequently in queries
- Address all incorrect data types, set to proper data type
- Analyze visitor behaviour more thoroughly
