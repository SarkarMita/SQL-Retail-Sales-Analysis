# 🛍️ Retail Sales Analysis — SQL Project

> End-to-End SQL Data Analysis Project
> Database: PostgreSQL | Tool: pgAdmin | April 2026

---

## 📋 Table of Contents
- [About the Project](#about-the-project)
- [Database Setup](#database-setup)
- [Data Cleaning](#data-cleaning)
- [Data Exploration](#data-exploration)
- [Business Questions & Solutions](#business-questions--solutions)
- [Key Findings](#key-findings)
- [What I Learned](#what-i-learned)
- [Tools Used](#tools-used)
- [Project Files](#project-files)
- [Connect With Me](#connect-with-me)

---

## 📌 About the Project

This project demonstrates end-to-end SQL data analysis 
on a retail sales dataset. The goal was to set up a 
database, clean the data, explore it and answer key 
business questions using SQL queries in PostgreSQL.

The project covers real-world business problems including 
sales performance, customer behaviour, category analysis 
and shift-based order patterns.

---

## 🗄️ Database Setup

```sql
CREATE DATABASE "Project1"
    WITH
    OWNER = postgres
    ENCODING = 'UTF8';
```

### Table Structure
```sql
CREATE TABLE retail_sales (
    transactions_id  INT PRIMARY KEY,
    sale_date        DATE,
    sale_time        TIME,
    customer_id      INT,
    gender           VARCHAR(15),
    age              INT,
    category         VARCHAR(15),
    quantiy          INT,
    price_per_unit   FLOAT,
    cogs             FLOAT,
    total_sale       FLOAT
);
```

---

## 🧹 Data Cleaning

Checked for and removed all NULL values across 
every column to ensure data quality.

```sql
-- Identify NULL values
SELECT * FROM retail_sales 
WHERE 
    transactions_id IS NULL
    OR sale_date IS NULL
    OR sale_time IS NULL
    OR customer_id IS NULL 
    OR gender IS NULL
    OR age IS NULL
    OR category IS NULL
    OR quantiy IS NULL
    OR price_per_unit IS NULL
    OR cogs IS NULL
    OR total_sale IS NULL;

-- Delete NULL records
DELETE FROM retail_sales
WHERE 
    transactions_id IS NULL
    OR sale_date IS NULL
    OR sale_time IS NULL
    OR customer_id IS NULL 
    OR gender IS NULL
    OR age IS NULL
    OR category IS NULL
    OR quantiy IS NULL
    OR price_per_unit IS NULL
    OR cogs IS NULL
    OR total_sale IS NULL;
```

---

## 🔍 Data Exploration

```sql
-- Total number of sales
SELECT COUNT(*) AS total_sale 
FROM retail_sales;

-- Total unique customers
SELECT COUNT(DISTINCT customer_id) AS total_customer 
FROM retail_sales;

-- All unique categories
SELECT DISTINCT category 
FROM retail_sales;
```

---

## 💼 Business Questions & Solutions

---

### Q1 — Sales on a Specific Date
**Question:** Retrieve all columns for sales 
made on 2022-11-05.

```sql
SELECT * 
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

---

### Q2 — Clothing Sales Filter
**Question:** Retrieve all transactions where 
category is Clothing, quantity sold is more than 
4 in November 2022.

```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing' 
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantiy >= 4;
```

---

### Q3 — Total Sales by Category
**Question:** Calculate the total sales for 
each category.

```sql
SELECT 
    category, 
    SUM(total_sale) AS net_sale
FROM retail_sales
GROUP BY category;
```

---

### Q4 — Average Customer Age by Category
**Question:** Find the average age of customers 
who purchased from the Beauty category.

```sql
SELECT 
    category, 
    ROUND(AVG(age), 2) AS avg_age
FROM retail_sales
WHERE category = 'Beauty'
GROUP BY category;
```

---

### Q5 — High Value Transactions
**Question:** Find all transactions where 
total sale is greater than 1000.

```sql
SELECT * 
FROM retail_sales
WHERE total_sale > 1000;
```

---

### Q6 — Transactions by Gender and Category
**Question:** Find the total number of 
transactions made by each gender in each category.

```sql
SELECT 
    category, 
    gender, 
    COUNT(*) AS total_transaction
FROM retail_sales
GROUP BY category, gender
ORDER BY 1, 2;
```

---

### Q7 — Best Selling Month Each Year
**Question:** Calculate the average sale for 
each month and find the best selling month 
in each year.

```sql
SELECT year, month, avg_sale 
FROM (
    SELECT 
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER(
            PARTITION BY EXTRACT(YEAR FROM sale_date) 
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales 
    GROUP BY 1, 2
) AS t1
WHERE rank = 1;
```

---

### Q8 — Top 5 Customers by Sales
**Question:** Find the top 5 customers based 
on highest total sales.

```sql
SELECT 
    customer_id, 
    SUM(total_sale) AS highest_sale
FROM retail_sales
GROUP BY customer_id
ORDER BY highest_sale DESC
LIMIT 5;
```

---

### Q9 — Unique Customers per Category
**Question:** Find the number of unique customers 
who purchased items from each category.

```sql
SELECT 
    category,
    COUNT(DISTINCT customer_id) AS unique_customer
FROM retail_sales
GROUP BY category
ORDER BY 2;
```

---

### Q10 — Orders by Shift
**Question:** Create shifts based on sale time 
and count orders per shift.
Morning = before 12, Afternoon = 12 to 17, 
Evening = after 17.

```sql
WITH hourly_sale AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 
                THEN 'Morning' 
            WHEN EXTRACT(HOUR FROM sale_time) 
                BETWEEN 12 AND 17 
                THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT 
    shift, 
    COUNT(*) AS total_orders 
FROM hourly_sale 
GROUP BY shift;
```

---

## 💡 Key Findings

| Question | Finding |
|---|---|
| Q1 Sales on Date | Filtered transactions for specific date |
| Q2 Clothing Filter | Identified high quantity clothing sales in Nov 2022 |
| Q3 Category Sales | Compared total revenue across all categories |
| Q4 Beauty Customers | Found average age of Beauty category shoppers |
| Q5 High Value Sales | Identified all premium transactions over 1000 |
| Q6 Gender Analysis | Breakdown of purchases by gender per category |
| Q7 Best Month | Identified top revenue month for each year |
| Q8 Top Customers | Found top 5 highest spending customers |
| Q9 Unique Customers | Measured customer reach per category |
| Q10 Shift Analysis | Determined busiest shift for orders |

---

## 🎓 What I Learned

- How to create and set up a PostgreSQL database 
  from scratch
- How to perform thorough data cleaning by 
  identifying and removing NULL values
- How to use aggregate functions like SUM, AVG, 
  COUNT for business analysis
- How to use GROUP BY and ORDER BY to organise 
  and rank results
- How to use EXTRACT to work with date and 
  time data
- How to use RANK() window function with 
  PARTITION BY for advanced ranking
- How to use Common Table Expressions (CTE) 
  with WITH clause for complex queries
- How to use TO_CHAR for date formatting 
  and filtering
- How to translate business questions into 
  structured SQL queries
- How to derive actionable insights from 
  raw transactional data

---

## 🛠️ Tools Used

- **PostgreSQL** — Database management system
- **pgAdmin** — SQL query editor and database tool
- **CSV Dataset** — Raw retail sales data source

---

## 📂 Project Files

| File | Description |
|---|---|
| [📄 SQL_Project1_Retail-sales.sql](SQL_Project1_Retail-sales.sql) | Complete SQL script with all queries |
| [📊 SQL_Retail_Sales_Analysis.csv](https://github.com/SarkarMita/SQL-Retail-Sales-Analysis/blob/main/SQL%20-%20Retail%20Sales%20Analysis_utf%20.csv) | Raw dataset used for analysis |

---

## 🔗 Connect With Me

I am always open to discussing data analytics, 
SQL projects and collaboration opportunities.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Mita%20Sarkar-blue?style=for-the-badge&logo=linkedin)](www.linkedin.com/in/mita-sarkar-mba-analyst)
[![GitHub](https://img.shields.io/badge/GitHub-SarkarMita-black?style=for-the-badge&logo=github)](https://github.com/SarkarMita)
[![Email](https://img.shields.io/badge/Email-Contact%20Me-red?style=for-the-badge&logo=gmail)](mitasarkar7727@gmail.com)

---

⭐ If you found this project helpful please 
consider giving it a star!
