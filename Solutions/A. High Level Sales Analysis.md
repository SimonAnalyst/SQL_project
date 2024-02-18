# Questions and Solution


## Part A: High Level Sales Analysis


### 1. What was the total quantity sold for all products?

```SQL
SELECT 
    SUM(qty) AS total_product_quantity
FROM 
    balanced_tree.sales;
```

| total_product_quantity |
|------------------------|
| 45216                  |



### 2. What was the total quantity sold for each product?

```SQL
SELECT 
    p.product_name,
    SUM(s.qty) AS total_product_quantity
FROM 
    balanced_tree.sales s
JOIN 
    balanced_tree.product_details p ON s.prod_id = p.product_id
GROUP BY 
    p.product_name;
```

|product_name                  | total_product_quantity |
|-------------------------------|------------------------|
| White Tee Shirt - Mens        | 3800                   |
| Navy Solid Socks - Mens       | 3792                   |
| Grey Fashion Jacket - Womens  | 3876                   |
| Navy Oversized Jeans - Womens | 3856                   |
| Pink Fluro Polkadot Socks - Mens | 3770                |
| Khaki Suit Jacket - Womens    | 3752                   |
| Black Straight Jeans - Womens | 3786                   |
| White Striped Socks - Mens    | 3655                   |
| Blue Polo Shirt - Mens        | 3819                   |
| Indigo Rain Jacket - Womens   | 3757                   |
| Cream Relaxed Jeans - Womens  | 3707                   |
| Teal Button Up Shirt - Mens   | 3646                   |



### 3. What is the total generated revenue for all products before discounts?

```SQL
SELECT 
    SUM(price * qty) AS gross_revenue
FROM 
    balanced_tree.sales;
```

| gross_revenue |
|---------------|
| 1289453       |


  
### 4. What is the total generated revenue for each product before discounts?

```SQL
SELECT 
    p.product_name,
    SUM(s.qty*s.price) AS gross_revenue
FROM 
    balanced_tree.sales s
JOIN 
    balanced_tree.product_details p ON s.prod_id = p.product_id
GROUP BY 
    p.product_name;
 ```

| product_name                  | gross_revenue |
|-------------------------------|---------------|
| White Tee Shirt - Mens        | 152000        |
| Navy Solid Socks - Mens       | 136512        |
| Grey Fashion Jacket - Womens  | 209304        |
| Navy Oversized Jeans - Womens | 50128         |
| Pink Fluro Polkadot Socks - Mens | 109330     |
| Khaki Suit Jacket - Womens    | 86296         |
| Black Straight Jeans - Womens | 121152        |
| White Striped Socks - Mens    | 62135         |
| Blue Polo Shirt - Mens        | 217683        |
| Indigo Rain Jacket - Womens   | 71383         |
| Cream Relaxed Jeans - Womens  | 37070         |
| Teal Button Up Shirt - Mens   | 36460         |



### 5. What was the total discount amount for all products?

```SQL
SELECT 
    ROUND(SUM((price * qty) * (discount::NUMERIC / 100)), 2) AS total_discounts
FROM 
    balanced_tree.sales;
  ```

| total_discounts |
|-----------------|
| 156229.14       |



### 6. What is the total discount for each product?

```SQL
SELECT 
    p.product_name,
    ROUND(SUM((s.price * s.qty) * (s.discount::NUMERIC / 100)), 2) AS total_discounts
FROM 
    balanced_tree.sales s
JOIN 
    balanced_tree.product_details p ON s.prod_id = p.product_id
GROUP BY 
    p.product_name;
```

| product_name                  | total_discounts |
|------------------------------|----------------|
| White Tee Shirt - Mens        | 18377.60        |
| Navy Solid Socks - Mens       | 16650.36        |
| Grey Fashion Jacket - Womens  | 25391.88        |
| Navy Oversized Jeans - Womens | 6135.61         |
| Pink Fluro Polkadot Socks - Mens | 12952.27     |
| Khaki Suit Jacket - Womens    | 10243.05        |
| Black Straight Jeans - Womens | 14744.96        |
| White Striped Socks - Mens    | 7410.81         |
| Blue Polo Shirt - Mens        | 26819.07        |
| Indigo Rain Jacket - Womens   | 8642.53         |
| Cream Relaxed Jeans - Womens  | 4463.40         |
| Teal Button Up Shirt - Mens   | 4397.60         |

