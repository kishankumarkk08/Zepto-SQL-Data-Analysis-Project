
# 🛒 Zepto SQL Data Analysis Project

<p align="center">
  <img src="Zepto-logo.png" alt="Zepto Logo" width="300" />
</p>

## Overview
This project performs data exploration, cleaning, and analysis on a dataset from [Zepto](https://www.zepto.com/)—a grocery delivery service. The dataset was imported from Kaggle and contains information about product pricing, availability, and weights.

---

## Dataset

The data for this project is sourced from the Kaggle dataset:

- **Dataset Link:** [Zepto Dataset](https://www.kaggle.com/datasets/palvinder2006/zepto-inventory-dataset)

## 🧾 Table Schema

| Column Name               | Data Type          | Description                                     |
|--------------------------|--------------------|-------------------------------------------------|
| CATEGORY                 | VARCHAR(100)       | Product category                                |
| NAME                     | VARCHAR(100)       | Product name                                    |
| MRP                      | INT                | Maximum Retail Price (in paise, later converted)|
| DISCOUNTPERCENT          | INT                | Discount offered in percentage                  |
| AVAILABLEQUANTITY        | INT                | Units available in inventory                    |
| DISCOUNTEDSELLINGPRICE   | INT                | Price after discount (in paise, later converted)|
| WEIGHTINGMS              | INT                | Weight of product in milligrams                 |
| OUTOFSTOCK               | ENUM('TRUE','FALSE')| Whether product is out of stock                |
| QUANTITY                 | INT                | Quantity in product unit (unused in analysis)   |

---


## Schema

```sql
-- CREATION OF DATABASE
CREATE DATABASE ZEPTO_DB;
USE ZEPTO_DB;

-- TABLE CREATION
CREATE TABLE ZEPTO_V2 (
CATEGORY VARCHAR(100),
NAME VARCHAR(100),
MRP INT,
DISCOUNTEDPERCENTAGE INT,
AVAILABLEQUANTITY INT,
DISCOUNTEDSELLINGPRICE INT,
WEIGHTINGMS INT,
OUTOFSTOCK ENUM('TRUE','FALSE'),
QUANTITY INT
);
```

## Business Problems and Solutions

### 1. Sample Data

```sql
SELECT * FROM ZEPTO_V2 LIMIT 20;
```


### 2. Data Exploration

```sql
SELECT DISTINCT CATEGORY FROM ZEPTO_V2; -- DIFFERENT PRODUCT CATEGORIES
SELECT COUNT(OUTOFSTOCK) FROM ZEPTO_V2 GROUP BY OUTOFSTOCK; -- COUNT OF OUTOFSTOCK AND INSTOCK
SELECT NAME,COUNT(NAME) AS OCCURENCE FROM ZEPTO_V2 GROUP BY NAME HAVING OCCURENCE > 1; -- PRODUCT HAVING MORE THAN 1 OCCURENCE
```

### 3. Data Cleaning - Cleaning unnecessary data and modifying data

```sql
SELECT * FROM ZEPTO_V2 WHERE DISCOUNTEDSELLINGPRICE = 0 OR MRP = 0; -- CHECKING IF ANY PRODUCT'S COST IS 0
DELETE FROM ZEPTO_V2 WHERE MRP = 0 OR DISCOUNTEDSELLINGPRICE = 0; -- DELETING THE ENTRY WHERE MRP OR SELLING PRICE IS 0

UPDATE ZEPTO_V2 
SET MRP = MRP/100,
	DISCOUNTEDSELLINGPRICE = DISCOUNTEDSELLINGPRICE/100 WHERE MRP IS NOT NULL; -- CONVERTING AMOUNT (PAISE -> RUPEES)
```

### 4. TOP 10 BEST VALUE PRODUCTS BASED ON THE DISCOUNTE PERCENTAGE

```sql
SELECT DISTINCT * FROM ZEPTO_V2 ORDER BY DISCOUNTPERCENT DESC LIMIT 10;
```



### 5. PRODUCTS WITH HIGH MRP BUT OUTOFSTOCK

```sql
SELECT * FROM ZEPTO_V2 WHERE OUTOFSTOCK = 'TRUE' AND MRP > (SELECT AVG(MRP) FROM ZEPTO_V2);
```

### 6. ESTIMATED REVENUE FROM EACH CATEGORY

```sql
SELECT CATEGORY, SUM(DISCOUNTEDSELLINGPRICE * AVAILABLEQUANTITY) AS ESTIMATED_REVENUE FROM ZEPTO_V2 GROUP BY CATEGORY;
```


### 7.  FIND ALL PRODUCTS WHERE MRP IS GREATER THAN 500 AND DISCOUNT IS LESS THAN 10 %

```sql
SELECT DISTINCT * FROM ZEPTO_V2 WHERE MRP > 500 AND DISCOUNTPERCENT < 10;
```


### 8. FIND PRICE PER GRAM FOR PRODUCTS ABOVE 100G AND SORT BY BEST VALUE

```sql
SELECT DISTINCT CATEGORY,NAME,DISCOUNTEDSELLINGPRICE,WEIGHTINGMS, ROUND(DISCOUNTEDSELLINGPRICE/WEIGHTINGMS,2) AS PRICE_PER_GM
FROM ZEPTO_V2 WHERE WEIGHTINGMS >= 100;
```

### 9. GROUP ITEMS IN LOW, MEDIUM OR BULK

```sql
SELECT DISTINCT CATEGORY,NAME,WEIGHTINGMS,
CASE WHEN WEIGHTINGMS < 1000 THEN 'LOW'
	 WHEN WEIGHTINGMS < 5000 THEN 'MEDIUM'
     ELSE 'BULK'
     END AS WEIGHT
FROM ZEPTO_V2;
```

### 10. TOTAL INVENTORY WEIGHT PER CATEGORY

```sql
SELECT CATEGORY, SUM(WEIGHTINGMS * AVAILABLEQUANTITY) AS TOTAL_INVENTORY_WEIGHT FROM ZEPTO_V2 GROUP BY CATEGORY;
```


## Findings and Conclusion

- **Products with zero MRP or price were identified and removed for accuracy.
- **Top 10 products were sorted based on discount percentage to showcase best value items.
- **Out-of-stock high MRP items could indicate high demand or stock issues.
- **Category-wise estimated revenue and inventory weight were calculated to inform logistics and pricing strategy.
- **Products were classified as Low, Medium, or Bulk based on weight, supporting better categorization and stock planning.

## Author - Kishan

This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!

Thank you for your support, and I look forward to connecting with you!
