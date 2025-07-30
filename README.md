# 🍕 SQL Pizza Sales Analysis

This project involves analyzing pizza sales data using SQL. It answers key business questions such as sales performance, customer behavior, product popularity, and revenue distribution to derive actionable insights for data-driven decision-making.

---

## 📂 Dataset Structure

- `orders`: Contains order date and time.
- `orders_details`: Line-item details of each pizza order.
- `pizzas`: Includes pizza IDs, size, and price.
- `pizza_types`: Includes pizza name, category, and type.

---

## 🧠 SQL Business Questions & Insights

### 1. 🧾 Total Number of Orders
```sql
SELECT COUNT(order_id) FROM orders;
````

* Gives the count of all orders placed.

---

### 2. 💰 Total Revenue Generated

```sql
SELECT ROUND(SUM(orders_details.quantity * pizzas.price), 2) AS total_revenue
FROM orders_details 
JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id;
```

---

### 3. 🧀 Highest-Priced Pizza

```sql
SELECT pizza_types.name, pizzas.price
FROM pizza_types 
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
ORDER BY pizzas.price DESC 
LIMIT 1;
```

---

### 4. 🍕 Most Common Pizza Size Ordered

```sql
SELECT pizzas.size, COUNT(orders_details.order_details_id) AS order_count
FROM pizzas 
JOIN orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizzas.size 
ORDER BY order_count DESC;
```

---

### 5. 🔝 Top 5 Most Ordered Pizza Types

```sql
SELECT pizza_types.name, SUM(orders_details.quantity) AS quantity
FROM pizza_types 
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN orders_details ON pizzas.pizza_id = orders_details.pizza_id
GROUP BY pizza_types.name 
ORDER BY quantity DESC 
LIMIT 5;
```

---

### 6. 📊 Total Quantity by Pizza Category

```sql
SELECT pizza_types.category, SUM(orders_details.quantity) AS quantity
FROM pizza_types 
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category 
ORDER BY quantity DESC;
```

---

### 7. 🕐 Order Distribution by Hour

```sql
SELECT HOUR(orders.order_time) AS hour, COUNT(order_id) AS order_count
FROM orders 
GROUP BY hour 
ORDER BY order_count DESC;
```

---

### 8. 📦 Pizza Category Distribution

```sql
SELECT category, COUNT(name) 
FROM pizza_types 
GROUP BY category;
```

---

### 9. 📅 Average Pizzas Ordered Per Day

```sql
SELECT ROUND(AVG(quantity), 0) AS avg_pizzas_order_per_day
FROM (
    SELECT orders.order_date, SUM(orders_details.quantity) AS quantity
    FROM orders 
    JOIN orders_details ON orders.order_id = orders_details.order_id
    GROUP BY orders.order_date
) AS order_quantity;
```

---

### 10. 💸 Top 3 Pizza Types by Revenue

```sql
SELECT pizza_types.name, SUM(orders_details.quantity * pizzas.price) AS revenue
FROM pizza_types 
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.name 
ORDER BY revenue DESC 
LIMIT 3;
```

---

### 11. 📈 Revenue Percentage by Pizza Category

```sql
SELECT pizza_types.category,
ROUND(SUM(orders_details.quantity * pizzas.price) / 
    (SELECT SUM(orders_details.quantity * pizzas.price)
     FROM orders_details 
     JOIN pizzas ON pizzas.pizza_id = orders_details.pizza_id) * 100, 2) AS revenue
FROM pizza_types 
JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN orders_details ON orders_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.category 
ORDER BY revenue DESC;
```

---

### 12. 📆 Cumulative Revenue Over Time

```sql
SELECT order_date, SUM(revenue) OVER (ORDER BY order_date) AS cum_revenue
FROM (
    SELECT orders.order_date, SUM(orders_details.quantity * pizzas.price) AS revenue
    FROM orders_details 
    JOIN pizzas ON orders_details.pizza_id = pizzas.pizza_id
    JOIN orders ON orders.order_id = orders_details.order_id
    GROUP BY orders.order_date
) AS sales;
```

---

### 13. 🏅 Top 3 Pizza Types by Revenue in Each Category

```sql
SELECT name, revenue 
FROM (
    SELECT category, name, revenue,
           RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS RN
    FROM (
        SELECT pizza_types.category, pizza_types.name,
               SUM(orders_details.quantity * pizzas.price) AS revenue
        FROM pizza_types 
        JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
        JOIN orders_details ON orders_details.pizza_id = pizzas.pizza_id
        GROUP BY pizza_types.category, pizza_types.name
    ) AS A
) AS B
WHERE RN <= 3;
```

---

## ✅ Key Takeaways

* The analysis reveals peak order times, top-selling pizza types, and revenue trends.
* Cumulative and category-wise revenue insights support better inventory and marketing strategies.
* Helps stakeholders identify best-selling products and optimize size/category offerings.

---

## 🛠 Tools Used

* **SQL (MySQL)**
* **Data Cleaning & Joins**
* **Aggregate Functions, CTEs, Window Functions**

