# Questions and Solution


## Part B: Transaction Analysis


### 1. How many unique transactions were there?

```SQL
SELECT 
    COUNT(DISTINCT txn_id) AS unique_transactions 
FROM 
    balanced_tree.sales;
```

| unique_transactions |
|---------------------|
|         2500        |



### 2. What is the average unique products purchased in each transaction?

```SQL
WITH cte AS (
    SELECT 
        txn_id,
        COUNT(prod_id) AS number_of_products 
    FROM 
        balanced_tree.sales 
    GROUP BY 
        1
)
SELECT 
    ROUND(AVG(number_of_products)) AS avg_number_of_products_per_transaction 
FROM 
    cte;
```

| avg_number_of_products_per_transaction |
|----------------------------------------|
| 6                                      |



### 3. What are the 25th, 50th and 75th percentile values for the revenue per transaction?

```SQL
WITH cte AS (
    SELECT 
        txn_id,
        ROUND(SUM(qty * price), 2) AS revenue 
    FROM 
        balanced_tree.sales 
    GROUP BY 
        txn_id
)
SELECT 
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY revenue) AS percentile_25,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY revenue) AS percentile_50,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY revenue) AS percentile_75 
FROM 
    cte;
```

| percentile_25 | percentile_50 | percentile_75 |
|---------------|---------------|---------------|
|     375.75    |     509.5     |     647       |



### 4. What is the average discount value per transaction?

```SQL
WITH cte AS (
    SELECT 
        txn_id,
        ROUND(SUM((price * qty) * (discount::NUMERIC / 100)), 2) AS discounts_per_txn 
    FROM 
        balanced_tree.sales 
    GROUP BY 
        txn_id
)
SELECT 
    ROUND(AVG(discounts_per_txn), 2) AS discount_avg 
FROM 
    cte;
```

| discount_avg |
|--------------|
|     62.49    |



### 5. What is the percentage split of all transactions for members vs non-members?

```SQL
SELECT 
    member,
    ROUND(100 * COUNT(member)::numeric / (SELECT COUNT(member) FROM balanced_tree.sales), 2) AS member_percentage  
FROM 
    balanced_tree.sales 
GROUP BY 
    member;
```

| member | member_percentage |
|--------|-------------------|
| false  |      39.97        |
| true   |      60.03        |



### 6. What is the average revenue for member transactions and non-member transactions?

```SQL
WITH cte AS (
    SELECT 
        member,
        txn_id,
        SUM(price * qty) AS revenue 
    FROM 
        balanced_tree.sales 
    GROUP BY 
        member, 
        txn_id
)
SELECT 
    member,
    ROUND(AVG(revenue), 2) AS avg_revenue 
FROM 
    cte 
GROUP BY 
    member;
```

| member | avg_revenue |
|--------|-------------|
| false  |   515.04    |
| true   |   516.27    |
