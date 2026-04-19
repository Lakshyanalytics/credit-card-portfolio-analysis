# Credit Card Portfolio Analysis

## Project Objective
Analyze credit card portfolio performance using customer and transaction data to support profitability, portfolio monitoring, and business insights.

## Import Data to SQL Database

### Steps

### 1. Prepare CSV Files
- customer_master.csv
- customer_supplement.csv
- card_transactions.csv
- card_transactions_supplement.csv

---

### 2. Create Tables in SQL

```sql
CREATE TABLE cust_detail (
 customer_id SERIAL PRIMARY KEY,
 customer_age INT,
 income FLOAT
);

CREATE TABLE cc_detail (
 transaction_id SERIAL PRIMARY KEY,
 customer_id INT,
 annual_fees FLOAT,
 total_trans_amt FLOAT,
 interest_earned FLOAT,
 week_start_date DATE,
 FOREIGN KEY (customer_id) REFERENCES cust_detail(customer_id)
);
```

---

### 3. Import CSV Files into SQL

```sql
COPY cust_detail 
FROM 'customer_master.csv'
DELIMITER ',' CSV HEADER;

COPY cc_detail
FROM 'card_transactions.csv'
DELIMITER ',' CSV HEADER;
```

---

## 4. Analytical SQL Queries

```sql
SELECT card_category,
SUM(total_trans_amt) AS revenue
FROM cc_detail
GROUP BY card_category;
```

```sql
SELECT customer_job,
AVG(interest_earned) AS avg_interest
FROM cc_detail
GROUP BY customer_job;
```

---

## 5. DAX Queries

```DAX
AgeGroup =
SWITCH(
TRUE(),
customer_master[Customer_Age] <30,"20-30",
customer_master[Customer_Age] <40,"30-40",
customer_master[Customer_Age] <50,"40-50",
"50+"
)
```

```DAX
Revenue =
card_transactions[Annual_Fees]
+ card_transactions[Total_Trans_Amt]
+ card_transactions[Interest_Earned]
```

```DAX
Current_Week_Revenue =
CALCULATE(
SUM(card_transactions[Revenue]),
FILTER(
ALL(card_transactions),
card_transactions[week_num2]=MAX(card_transactions[week_num2])
))
```

---

## Project Insights
- Identified high-revenue card segments  
- Evaluated week-over-week revenue movement  
- Analyzed profitability and utilization patterns  
- Monitored delinquency-related risk indicators
