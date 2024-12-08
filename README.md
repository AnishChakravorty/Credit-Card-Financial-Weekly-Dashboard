# Credit-Card-Financial-Weekly-Dashboard

![image](https://github.com/user-attachments/assets/d937fce6-4444-47a8-8e14-1b476831c041)
![image](https://github.com/user-attachments/assets/9eb61914-3748-4e5c-aed7-84b76789adf8)
![image](https://github.com/user-attachments/assets/d8d00f8b-c5f8-4d51-96d4-f4c886e8bf7e)


## Table of Contents
1. [Project Objective](#project-objective)
2. [Tools Used](#tools-used)
3. [SQL Queries](#sql-queries)
4. [Data Processing & DAX](#data-processing--dax)
5. [Insights](#insights)


## Project Objective
To develop a comprehensive credit card weekly dashboard that provides real-time insights into key performance metrics and trends, enabling stakeholders to monitor and analyze credit card operations effectively.


### Import Data to SQL Database
1. Prepare the CSV files
2. Create tables in SQL
3. Import CSV files into SQL

## Tools Used 

- PostgreSQL
- PowerBI
- Power Query

## SQL Queries

```sql
0. Create a database 
CREATE DATABASE ccdb;

1. Create cc_detail table
CREATE TABLE cc_detail (
    Client_Num INT,
    Card_Category VARCHAR(20),
    Annual_Fees INT,
    Activation_30_Days INT,
    Customer_Acq_Cost INT,
    Week_Start_Date VARCHAR(15),
    Week_Num VARCHAR(20),
    Qtr VARCHAR(10),
    current_year INT,
    Credit_Limit DECIMAL(10,2),
    Total_Revolving_Bal INT,
    Total_Trans_Amt INT,
    Total_Trans_Ct INT,
    Avg_Utilization_Ratio DECIMAL(10,3),
    Use_Chip VARCHAR(10),
    Exp_Type VARCHAR(50),
    Interest_Earned DECIMAL(10,3),
    Delinquent_Acc VARCHAR(5)
);

2. Create cc_detail table
CREATE TABLE cust_detail (
    Client_Num INT,
    Customer_Age INT,
    Gender VARCHAR(5),
    Dependent_Count INT,
    Education_Level VARCHAR(50),
    Marital_Status VARCHAR(20),
    State_cd VARCHAR(50),
    Zipcode VARCHAR(20),
    Car_Owner VARCHAR(5),
    House_Owner VARCHAR(5),
    Personal_Loan VARCHAR(5),
    Contact VARCHAR(50),
    Customer_Job VARCHAR(50),
    Income INT,
    Cust_Satisfaction_Score INT
);

3. Copy csv data into SQL Database
COPY cc_detail
FROM 'E:/Power BI/Credit Card Dashboard/credit_card.csv' 
DELIMITER ',' 
CSV HEADER;

COPY cust_detail
FROM 'E:/Power BI/Credit Card Dashboard/customer.csv' 
DELIMITER ',' 
CSV HEADER;

COPY cc_detail
FROM 'E:/Power BI/Credit Card Dashboard/cc_add.csv' 
DELIMITER ',' 
CSV HEADER;

COPY cust_detail
FROM 'E:/Power BI/Credit Card Dashboard/cust_add.csv' 
DELIMITER ',' 
CSV HEADER;


```

## Data Processing & DAX

### DAX Queries

```dax
AgeGroup = SWITCH(
    TRUE(),
    'public cust_detail'[customer_age] < 30, "20-30",
    'public cust_detail'[customer_age] >= 30 && 'public cust_detail'[customer_age] < 40, "30-40",
    'public cust_detail'[customer_age] >= 40 && 'public cust_detail'[customer_age] < 50, "40-50",
    'public cust_detail'[customer_age] >= 50 && 'public cust_detail'[customer_age] < 60, "50-60",
    'public cust_detail'[customer_age] >= 60, "60+",
    "unknown"
)

IncomeGroup = SWITCH(
    TRUE(),
    'public cust_detail'[income] < 35000, "Low",
    'public cust_detail'[income] >= 35000 && 'public cust_detail'[income] <70000, "Med",
    'public cust_detail'[income] >= 70000, "High",
    "unknown"
)

week_num2 = WEEKNUM('public cc_detail'[week_start_date])

Revenue = 'public cc_detail'[annual_fees] + 'public cc_detail'[total_trans_amt] + 'public cc_detail'[interest_earned]

Current_week_Reveneue = CALCULATE(
    SUM('public cc_detail'[Revenue]),
    FILTER(
        ALL('public cc_detail'),
        'public cc_detail'[week_num2] = MAX('public cc_detail'[week_num2])
    )
)

Previous_week_Reveneue = CALCULATE(
    SUM('public cc_detail'[Revenue]),
    FILTER(
        ALL('public cc_detail'),
        'public cc_detail'[week_num2] = MAX('public cc_detail'[week_num2])-1
    )
)
```

## Insights 


1. Revenue Distribution:

- Total revenue: Approximately 57M
- Quarterly revenue is relatively stable, with Q4 slightly higher at 14.5M
- Blue cards generate the most revenue (47M), followed by Silver (6M), Gold (3M), and Platinum (1M)


2. Transaction Insights:

- Total transaction count: 667K
- Total transaction amount: 45.5M
- Most transactions are done via Swipe (36M), followed by Chip (17M), and Online (4M)


3. Customer Demographics:

- Highest revenue comes from Businessmen (18M), followed by White-collar workers (10M)
- Graduates contribute the most to revenue (23M), followed by High School graduates (11M)
- Age group 40-50 and 50-60 contribute significantly to revenue
- Married customers generate more revenue than single customers


4. Expenditure Patterns:

- Bills account for the highest revenue (14M), followed by Entertainment (10M) and Fuel (10M)
- Travel has the lowest revenue contribution (6M)


5. Customer Satisfaction:

- Overall Customer Satisfaction Score is 3.19 out of 5
- Self-employed customers have the highest satisfaction score (8.47)


6. Delinquency:

- 93.94% of accounts are not delinquent
- Self-employed customers have the highest delinquency rate (1.66%)


7. Card Activation:

- 57.46% of cards were activated within 30 days
- Bills category has the highest activation rate (16.76%)


8. Other Insights:

- Average credit limit: 8.64K
- Total customer income: 588M
- Texas, New York, and California are among the top 5 states by revenue
