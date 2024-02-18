# Questions and solutions

## Part C: Product Analysis


### 1. What are the top 3 products by total revenue before discount?

```SQL
SELECT
    p.product_name,
    SUM(s.qty * s.price) AS total_revenue
FROM
    balanced_tree.sales s
LEFT JOIN
    balanced_tree.product_details p ON s.prod_id = p.product_id
GROUP BY
    p.product_name
ORDER BY
    total_revenue DESC
LIMIT 3;

```
| product_name                 | total_revenue |
|------------------------------|---------------|
| Blue Polo Shirt - Mens      |    217683     |
| Grey Fashion Jacket - Womens|    209304     |
| White Tee Shirt - Mens      |    152000     |



### 2. What is the total quantity, revenue and discount for each segment?

```SQL
SELECT
    p.segment_id,
    SUM(s.qty) AS total_quantity,
    SUM(s.qty * s.price) AS total_revenue_before_discounts,
    ROUND(SUM(s.qty * s.price * s.discount::numeric/100), 2) AS total_discount
FROM
    balanced_tree.sales s
LEFT JOIN
    balanced_tree.product_details p ON s.prod_id = p.product_id
GROUP BY
    p.segment_id;
```

| segment_id | total_quantity | total_revenue_before_discounts | total_discount |
|------------|----------------|-------------------------------|----------------|
| 3          | 11349          | 208350                        | 25343.97       |
| 5          | 11265          | 406143                        | 49594.27       |
| 4          | 11385          | 366983                        | 44277.46       |
| 6          | 11217          | 307977                        | 37013.44       |



### 3. What is the top selling product for each segment?

```SQL
WITH cte AS (
    SELECT 
        p.segment_id,
        p.product_name,
        RANK() OVER(PARTITION BY p.segment_id ORDER BY SUM(s.qty) DESC) AS segment_rank
    FROM 
        balanced_tree.sales s
    LEFT JOIN 
        balanced_tree.product_details p ON s.prod_id = p.product_id
    GROUP BY 
        p.segment_id, p.product_name
)
SELECT 
    segment_id,
    product_name
FROM 
    cte
WHERE 
    segment_rank = 1;
```

| segment_id | product_name                    |
|------------|--------------------------------|
| 3          | Navy Oversized Jeans - Womens |
| 4          | Grey Fashion Jacket - Womens  |
| 5          | Blue Polo Shirt - Mens        |
| 6          | Navy Solid Socks - Mens       |



### 4. What is the total quantity, revenue and discount for each category?

```SQL
SELECT
    p.category_id,
    SUM(s.qty) AS total_quantity,
    SUM(s.qty * s.price) AS total_revenue_before_discounts,
    ROUND(SUM(s.qty * s.price * s.discount::numeric/100), 2) AS total_discount
FROM
    balanced_tree.sales s
LEFT JOIN
    balanced_tree.product_details p ON s.prod_id = p.product_id
GROUP BY
    p.category_id
ORDER BY
    p.category_id;
```

| category_id | total_quantity | total_revenue_before_discounts | total_discount |
|-------------|----------------|--------------------------------|----------------|
| 1           | 22734          | 575333                         | 69621.43       |
| 2           | 22482          | 714120                         | 86607.71       |



### 5. What is the top selling product for each category?

```SQL
WITH cte AS (
    SELECT 
        p.category_id,
        p.product_name,
        RANK() OVER(PARTITION BY p.category_id ORDER BY SUM(s.qty) DESC) AS category_rank
    FROM 
        balanced_tree.sales s
    LEFT JOIN 
        balanced_tree.product_details p ON s.prod_id = p.product_id
    GROUP BY 
        p.category_id, p.product_name
)
SELECT 
    category_id,
    product_name
FROM 
    cte
WHERE 
    category_rank = 1;
```

| category_id | product_name                  |
|-------------|-------------------------------|
| 1           | Grey Fashion Jacket - Womens |
| 2           | Blue Polo Shirt - Mens       |



### 6. What is the percentage split of revenue by product for each segment?

```SQL
WITH cte AS (
    SELECT 
        p.segment_name,
        p.product_name,
        SUM(s.qty * s.price) AS total_revenue_before_discounts
    FROM 
        balanced_tree.sales s
    LEFT JOIN 
        balanced_tree.product_details p ON s.prod_id = p.product_id
    GROUP BY 
        p.segment_name, p.product_name
)
SELECT
    segment_name,
    product_name,
    ROUND(total_revenue_before_discounts / SUM(total_revenue_before_discounts) OVER (PARTITION BY segment_name) * 100, 2) AS revenue_percentage
FROM 
    cte;
```

| segment_name | product_name                      | revenue_percentage |
|--------------|----------------------------------|---------------------|
| Jacket       | Indigo Rain Jacket - Womens      | 19.45               |
| Jacket       | Khaki Suit Jacket - Womens       | 23.51               |
| Jacket       | Grey Fashion Jacket - Womens     | 57.03               |
| Jeans        | Navy Oversized Jeans - Womens    | 24.06               |
| Jeans        | Black Straight Jeans - Womens    | 58.15               |
| Jeans        | Cream Relaxed Jeans - Womens     | 17.79               |
| Shirt        | White Tee Shirt - Mens           | 37.43               |
| Shirt        | Blue Polo Shirt - Mens           | 53.60               |
| Shirt        | Teal Button Up Shirt - Mens      | 8.98                |
| Socks        | Navy Solid Socks - Mens         | 44.33               |
| Socks        | White Striped Socks - Mens      | 20.18               |
| Socks        | Pink Fluro Polkadot Socks - Mens | 35.50               |



### 7. What is the percentage split of revenue by segment for each category?

```SQL
WITH cte AS (
    SELECT 
        p.segment_name,
        p.category_id,
        SUM(s.qty * s.price) AS total_revenue_before_discounts
    FROM 
        balanced_tree.sales s
    LEFT JOIN 
        balanced_tree.product_details p ON s.prod_id = p.product_id
    GROUP BY 
        p.segment_name, p.category_id
)
SELECT
    segment_name,
    category_id,
    ROUND(total_revenue_before_discounts / SUM(total_revenue_before_discounts) OVER (PARTITION BY category_id) * 100, 2) AS revenue_percentage
FROM 
    cte;
```

| segment_name | category_id | revenue_percentage |
|--------------|-------------|--------------------|
| Jeans        | 1           | 36.21              |
| Jacket       | 1           | 63.79              |
| Socks        | 2           | 43.13              |
| Shirt        | 2           | 56.87              |



### 8. What is the percentage split of total revenue by category?

```SQL
SELECT
       p.category_name,
       ROUND(100*SUM(s.qty * s.price)::numeric / (SELECT SUM(qty * price) FROM balanced_tree.sales), 2) AS revenue_percentage
FROM 
       balanced_tree.sales s
LEFT JOIN 
       balanced_tree.product_details p ON s.prod_id = p.product_id
GROUP BY 
       p.category_name;
```

| category_name | revenue_percentage |
|---------------|--------------------|
| Mens          | 55.38              |
| Womens        | 44.62              |



### 9. What is the total transaction “penetration” for each product?

```SQL
SELECT 
       p.product_name,
       ROUND(100*COUNT(s.txn_id)::numeric / (SELECT COUNT(DISTINCT txn_id) 
                                       FROM balanced_tree.sales), 2) AS txn_penetration
FROM 
       balanced_tree.sales s
LEFT JOIN 
       balanced_tree.product_details p ON s.prod_id = p.product_id
GROUP BY 
       p.product_name;
```

| product_name                     | txn_penetration |
|----------------------------------|----------------|
| White Tee Shirt - Mens           | 50.72          |
| Navy Solid Socks - Mens          | 51.24          |
| Grey Fashion Jacket - Womens     | 51.00          |
| Navy Oversized Jeans - Womens    | 50.96          |
| Pink Fluro Polkadot Socks - Mens | 50.32          |
| Khaki Suit Jacket - Womens       | 49.88          |
| Black Straight Jeans - Womens    | 49.84          |
| White Striped Socks - Mens       | 49.72          |
| Blue Polo Shirt - Mens           | 50.72          |
| Indigo Rain Jacket - Womens      | 50.00          |
| Cream Relaxed Jeans - Womens     | 49.72          |
| Teal Button Up Shirt - Mens      | 49.68          |

