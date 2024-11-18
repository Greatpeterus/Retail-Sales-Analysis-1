# Retail Sales Analysis 1 SQL Project

## Project Overview

**Project Title**: Retail Sales Data Analysis  


This project is designed to demonstrate SQL skills and techniques typically used by data analysts to explore, clean, and analyse retail sales data. The project involves setting up a retail sales database from a given dataset, performing exploratory data analysis (EDA), and answering specific business questions through SQL queries. This project is ideal for those who are starting their journey in data analysis and want to build a solid foundation in SQL.

## Objectives

1. **Set up a retail sales database**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null values.
3. **Exploratory Data Analysis (EDA)**: Perform basic exploratory data analysis to understand the dataset.
4. **Business Analysis**: Use SQL to answer specific business questions and derive insights from the sales data.


## Tools Used 
**MySQL Workbench



## Project Structure



### 1. Database Setup

- **Database Creation**: The project starts by creating a database named `sales_project`.
- **Table Creation**: A table named `retail_sales` is created by importing the dataset provided. The table structure includes columns for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sale amount.

Note: MySQL Workbench dropped 13 transactions with null values; 10 with blank age and 3 with blank total_sales. This reduced the transactions from 2000 to 1987
 
```sql
CREATE DATABASE sales_projects;

```

### 2. Data Exploration & Cleaning

- **Record Count**: How many sales do we have in the dataset?
- **Customer Count**: How many unique customers do we have in the dataset?
- **Category Count**: How many unique product category do we have in the dataset.
- **Null Value Check**: is there any blank cell or null values in the dataset? Delete records with missing data.

```sql
SELECT COUNT(*) 
FROM sales_projects.retail_sales;


SELECT COUNT(DISTINCT customer_id) 
FROM sales_projects.retail_sales;


SELECT DISTINCT category 
FROM sales_projects.retail_sales;


SELECT * 
FROM sales_projects.retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR gender IS NULL
    OR age IS NULL OR category IS NULL 
    OR quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

```
#### RESULTS
	1. total sale record in the dataset is 1987
	2. unique customer_id is 155
	3. unique product category are  Clothing, Beauty and Electronics
	4. NULL transaction- there was no null value and no value to delete. Recall that 13 null values were earlier removed upon importing dataset




### 3. Data Analysis & Findings

The following SQL queries were developed to answer specific business questions:
- Q.1 What was the sales record on 05-11-2022?  How much was the revenue on this day? 
- Q.2 In November 2022, what are the transactions that sold 4 or more cloth? 
- Q.3 What was the revenue from each category in 2022?
- Q.4 How old were customers who purchased beauty products?
- Q.5 What were the details of transactions where the total sale is greater than 1500?
- Q.6 What are the revenue made by each gender in each category?
- Q.7 What is the average sale for each month. Find out best selling month in each year?
- Q.8 Who are the top 5 customers who spent most on shopping with us?
- Q.9 Who are the top 10 customers who purchased most from each category?
- Q.10 What is the average sale per shift? (suppose Morning <12, Afternoon Between 12 & 17, Evening >17)

-



1. **What was the sales record on 05-11-2022? How much was the revenue on this day?**
```sql
SELECT *
FROM sales_projects.retail_sales
WHERE sale_date = '2022-11-05';

SELECT SUM(total_sale) as revenue
FROM sales_projects.retail_sales
WHERE sale_date = '2022-11-05';

```
##### Result: £6,830.00 was made as sales revenue on 2022-11-05



2. **In November 2022, what are the transactions that sold 4 or more cloth?**
```sql
SELECT *
FROM sales_projects.retail_sales
WHERE category = 'Clothing'
     AND quantity >= 4 
     AND sale_date LIKE '2022-11-%';

SELECT COUNT(Transactions_id) 
FROM sales_projects.retail_sales
WHERE category = 'Clothing'
     AND quantity >= 4 
     AND sale_date LIKE '2022-11-%';
```
##### Result: There were 17 transactions in Nov 2022 that sold 4 or mor clothes


3. **What was the revenue from each category in 2022?**
```sql
SELECT 
    category,
     COUNT(*) AS total_orders,
     SUM(total_sale) AS revenue
FROM sales_projects.retail_sales
WHERE sale_date LIKE '2022%'
GROUP BY category; 

```
##### Result: category, total_orders, revenue: Clothing, 330, 148780; Beauty, 311, 151460; and Electronics, 325, 149095

4. **How old were customers who purchased beauty products?**  
```sql
SELECT
    ROUND(AVG(age), 2) AS avg_age,
    MAX(age) AS max_age,
    MIN(age) AS min_age
FROM sales_projects.retail_sales
WHERE category = 'Beauty';

```
##### 5Result age of customer who purchased beauty products was between 18 and 64 with the average of 40.42 years


5. **What were the details of transactions where the total_sale is greater than 1500?**:
```sql
SELECT * 
FROM sales_projects.retail_sales
WHERE total_sale > 1500;

```
##### Result: see table



6. **What are the revenues made by each gender in each category.**:
```sql
SELECT 
    category,
    gender,
    SUM(total_sale) AS revenue
FROM sales_projects.retail_sales
GROUP BY 
    category,
    gender
ORDER BY category;
```
##### RESULT: see table



7. **What is the average sale for each month? Find out best selling month in each year**:
```sql
SELECT 
    YEAR (sale_date) AS year,
    MONTH (sale_date) AS month,
    AVG(total_sale) AS avg_sale,
    RANK() OVER(PARTITION BY YEAR (sale_date) ORDER BY AVG(total_sale) DESC) AS position
FROM sales_projects.retail_sales
GROUP BY year, month;

##### find the highest for each year

SELECT 
     year,
     month,
     ROUND(avg_sale, 2)
FROM 
	( SELECT 
		YEAR (sale_date) AS year,
		MONTH (sale_date) AS month,
		AVG(total_sale) AS avg_sale,
		RANK() OVER(PARTITION BY YEAR (sale_date) ORDER BY AVG(total_sale) DESC) AS position
	FROM sales_projects.retail_sales
	GROUP BY year, month
	) AS table1
WHERE position = 1;

```
##### Result: # year, month, avg_sale: 2022, 7, 541.34 and 2023, 2, 535.53



8. **Who are the top 5 customers who spent most on shopping with us?**:
```sql
SELECT 
    SUM(total_sale) as amount_spent, 
    customer_id
FROM sales_projects.retail_sales
GROUP BY customer_id
ORDER BY amount_spent DESC
LIMIT 5
```
##### Result: Customers with customer_id 1 to 5

9. **Who are the top 10 customers who purchased most from each category?**:
```sql

SELECT
	category,
	amount_spent,
	positions,
   	customer_id
FROM 
	(SELECT 
		category, 
        SUM(total_sale) AS amount_spent,
		customer_id, 
	RANK() OVER(PARTITION BY category ORDER BY SUM(total_sale) DESC) AS positions
	FROM sales_projects.retail_sales
	GROUP BY 
		category,
		customer_id
    ) as table2
WHERE positions <=10;
```
##### Result: see table


10. **What is the average sale per shift? (suppose Morning <12, Afternoon Between 12 & 17, Evening >17)**:
```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM sales_projects.retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift;
```




## Findings

- **Customer Demographics**: The dataset includes customers from various age groups, with sales distributed across different categories such as Clothing and Beauty.
- **High-Value Transactions**: Several transactions had a total sale amount greater than 1000, indicating premium purchases.
- **Sales Trends**: Monthly analysis shows variations in sales, helping identify peak seasons.
- **Customer Insights**: The analysis identifies the top-spending customers and the most popular product categories.

## Reports

- **Sales Summary**: A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis**: Insights into sales trends across different months and shifts.
- **Customer Insights**: Reports on top customers and unique customer counts per category.

## Action
There is a need to add visualisation  to this report to aid further understanding of trends


## Conclusion

This project serves as a comprehensive introduction to SQL for data analysts, covering database setup, data cleaning, exploratory data analysis, and business-driven SQL queries. The findings from this project can help drive business decisions by understanding sales patterns, customer behaviour, and product performance.


### Acknolegement 
This project was inspired by [najirh](https://github.com/najirh/Retail-Sales-Analysis-SQL-Project--P1/) on github


