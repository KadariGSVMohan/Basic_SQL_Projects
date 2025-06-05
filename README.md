 
# 🛒 Retail Sales Analysis using PostgreSQL

## 📌 Project Objective

This project is focused on analyzing transactional retail data using SQL in PostgreSQL. The goal is to extract actionable business insights through data cleaning, exploration, and analytical queries. The project simulates a real-world sales environment — helping stakeholders understand customer behavior, product category performance, and time-based sales trends.


## 🧱 Dataset Structure – `retail_sales`

The dataset captures transactional-level retail sales information with the following schema:

| Column Name       | Data Type   | Description                                      |
|-------------------|-------------|--------------------------------------------------|
| `transaction_id`  | INT         | Unique identifier for each transaction           |
| `sale_date`       | DATE        | Date of the transaction                          |
| `sale_time`       | TIME        | Time of the transaction                          |
| `customer_id`     | INT         | Unique customer ID                               |
| `gender`          | VARCHAR(15) | Gender of the customer                           |
| `age`             | INT         | Age of the customer                              |
| `category`        | VARCHAR(15) | Product category (e.g., Clothing, Beauty)        |
| `quantity`        | INT         | Quantity purchased                               |
| `price_per_unit`  | FLOAT       | Price per product unit                           |
| `cogs`            | FLOAT       | Cost of goods sold                               |
| `total_sale`      | FLOAT       | Total sale amount for the transaction            |



## ⚙️ Project Workflow

### 1️⃣ Table Creation

Created a table `retail_sales` to store transactional data.

### 2️⃣ Data Cleaning

- Checked for NULL values across all key fields
- Deleted incomplete records to ensure data quality

```sql
DELETE FROM retail_sales
WHERE 
  transaction_id IS NULL OR
  sale_date IS NULL OR
  sale_time IS NULL OR
  gender IS NULL OR
  age IS NULL OR
  category IS NULL OR
  quantity IS NULL OR
  cogs IS NULL OR
  total_sale IS NULL;
````

### 3️⃣ Data Exploration

* **Total Sales Transactions**
* **Unique Customers**
* **Distinct Product Categories**

---

## 💡 Business Problem Statements & Insights

### 📌Retrieve all sales on a specific date

```sql
SELECT * FROM retail_sales WHERE sale_date = '2022-11-05';
```

### 📌Clothing sales with quantity > 4 in November 2022

```sql
SELECT * FROM retail_sales
WHERE category = 'Clothing'
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantity >= 4;
```

### 📌Total sales and order count per category

```sql
SELECT category, SUM(total_sale) AS Net_Sale, COUNT(*) AS Total_Orders
FROM retail_sales
GROUP BY category;
```

### 📌Average age of customers in 'Beauty' category

```sql
SELECT ROUND(AVG(age)) AS avg_age
FROM retail_sales
WHERE category = 'Beauty';
```

### 📌High-value transactions (sales > 1000)

```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000;
```

### 📌Transactions by gender and category

```sql
SELECT category, gender, COUNT(*) AS Total_Transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

### 📌Best-selling month in each year by average sales

```sql
SELECT year, month, avg_sale
FROM (
  SELECT 
    EXTRACT(YEAR FROM sale_date) AS year,
    EXTRACT(MONTH FROM sale_date) AS month,
    AVG(total_sale) AS avg_sale,
    RANK() OVER (PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
  FROM retail_sales
  GROUP BY year, month
) AS monthly_ranked
WHERE rank = 1;
```

### 📌Top 5 customers by total purchase value

```sql
SELECT customer_id, SUM(total_sale) AS Total_Sales
FROM retail_sales
GROUP BY customer_id
ORDER BY Total_Sales DESC
LIMIT 5;
```

### 📌Unique customers per product category

```sql
SELECT category, COUNT(DISTINCT customer_id) AS Unique_Customers
FROM retail_sales
GROUP BY category;
```

### 📌Number of orders by time-of-day shift

```sql
WITH Hourly_Sale AS (
  SELECT *,
    CASE
      WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
      WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
      ELSE 'Evening'
    END AS shift
  FROM retail_sales
)
SELECT shift, COUNT(*) AS total_orders
FROM Hourly_Sale
GROUP BY shift;
```

---

## 📊 Key Takeaways

* Identified **top-performing categories** and **peak transaction periods**.
* Highlighted **high-value customers** for potential loyalty campaigns.
* Detected **most profitable months** to guide inventory and marketing efforts.
* Observed **purchase trends by gender and time** for targeted promotions.


## 🛠️ Tools & Technologies

* **Database**: PostgreSQL
* **Concepts Used**:

  * DDL & DML (CREATE, DELETE, INSERT)
  * Aggregation & Filtering
  * Window Functions (RANK)
  * Date-Time Functions (EXTRACT, TO\_CHAR)
  * Subqueries & CTEs




