# Credit Card Portfolio Analysis

## Project Objective
Analyze credit card portfolio performance using customer and transaction data to support profitability, risk monitoring, and business insights.

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
CREATE TABLE customer_master (
 customer_id SERIAL PRIMARY KEY,
 customer_age INT,
 income FLOAT
);

CREATE TABLE card_transactions (
 transaction_id SERIAL PRIMARY KEY,
 customer_id INT,
 annual_fees FLOAT,
 total_trans_amt FLOAT,
 interest_earned FLOAT,
 week_start_date DATE,
 FOREIGN KEY (customer_id) REFERENCES customer_master(customer_id)
);
```

---

### 3. Import CSV Files into SQL

```sql
COPY customer_master
FROM 'customer_master.csv'
DELIMITER ',' CSV HEADER;

COPY card_transactions
FROM 'card_transactions.csv'
DELIMITER ',' CSV HEADER;
```

---

## 4. Analytical SQL Queries

```sql
SELECT card_category,
SUM(total_trans_amt) AS revenue
FROM card_transactions
GROUP BY card_category;
```

```sql
SELECT c.customer_job,
AVG(t.interest_earned) AS avg_interest
FROM customer_master c
JOIN card_transactions t
ON c.customer_id = t.customer_id
GROUP BY c.customer_job;
```

---

## 5. DAX Queries

```DAX
AgeGroup = SWITCH(
TRUE(),
customer_master[customer_age] < 30,"20-30",
customer_master[customer_age] >=30 && customer_master[customer_age] <40,"30-40",
customer_master[customer_age] >=40 && customer_master[customer_age] <50,"40-50",
customer_master[customer_age] >=50 && customer_master[customer_age] <60,"50-60",
customer_master[customer_age] >=60,"60+",
"unknown"
)
```

```DAX
IncomeGroup = SWITCH(
TRUE(),
customer_master[income] <35000,"Low",
customer_master[income] >=35000 && customer_master[income] <70000,"Med",
customer_master[income] >=70000,"High",
"unknown"
)
```

```DAX
Revenue =
card_transactions[annual_fees]
+ card_transactions[total_trans_amt]
+ card_transactions[interest_earned]
```

```DAX
week_num2 =
WEEKNUM(card_transactions[week_start_date])
```

```DAX
Current_week_Revenue =
CALCULATE(
SUM(card_transactions[Revenue]),
FILTER(
ALL(card_transactions),
card_transactions[week_num2]=MAX(card_transactions[week_num2])
))
```

```DAX
Previous_week_Revenue =
CALCULATE(
SUM(card_transactions[Revenue]),
FILTER(
ALL(card_transactions),
card_transactions[week_num2]=MAX(card_transactions[week_num2])-1
))
```

## Project Insights

### Week-over-Week (WoW) Trends
- Revenue increased by approximately 29%
- Total transaction amount increased by ~35%, while transaction count increased by ~3%
- Customer count increased by nearly 13%

### Year-to-Date (YTD) Highlights
- Portfolio revenue exceeded 57M
- Interest earned was approximately 8M
- Total transaction amount reached 46M
- Male customers contributed slightly higher revenue than female customers
- Blue and Silver cards accounted for the vast majority of transaction activity (~94%)
- Texas, New York, and California were the leading contributing states (~69%)
- Activation rate remained above 57%
- Delinquency rate remained near 6%
