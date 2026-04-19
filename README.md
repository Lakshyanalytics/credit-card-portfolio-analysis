# Credit Card Portfolio Analysis

## Project Objective
Analyze credit card portfolio performance using customer and transaction data to support profitability, portfolio monitoring, and business insights.

## Import Data to SQL Database

### SQL Queries

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
 FOREIGN KEY (customer_id) REFERENCES cust_detail(customer_id)
);
```

```sql
COPY cust_detail FROM 'customer.csv' DELIMITER ',' CSV HEADER;

COPY cc_detail FROM 'credit_card.csv' DELIMITER ',' CSV HEADER;
```

---

## DAX Queries

```DAX
AgeGroup =
SWITCH(
TRUE(),
customer_master[Customer_Age] < 30,"20-30",
customer_master[Customer_Age] < 40,"30-40",
customer_master[Customer_Age] < 50,"40-50",
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

## Dashboard Images
See dashboards/ folder for:
- CC Transaction Analytics
- CC Customer Insights
