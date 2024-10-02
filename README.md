# Pizza_Sales_Insights
Pizza Sales Insights Project using SQL


Pizza Sales Analysis Project
Project Overview
Objective: This project analyzes pizza sales data from a pizza chain to derive valuable insights regarding orders, revenue, and customer preferences using SQL.

Dataset: The dataset consists of four tables:

Order_Details: Information on each pizza ordered, including quantity.
Orders: Details of each order, such as the date and time.
Pizza_Types: Information about different types of pizzas, including category and name.
Pizzas: Pricing and size details for each pizza.
Tools Used: SQL (for querying the dataset).

Problem-Solving Approach
The analysis is divided into three levels: Basic, Intermediate, and Advanced. Each level aims to answer specific business questions.

1. Basic Analysis
Q.1 Retrieve total number of orders placed:

SELECT COUNT(order_id) AS Total_Orders FROM orders$;

Result: This query provides the total number of orders, offering insight into customer demand.

Q.2 Calculate total revenue generated from pizza sales:

SELECT ROUND(SUM(order_details$.quantity * pizzas$.price), 2) AS Total_Revenue 
FROM order_details$ 
JOIN pizzas$ ON order_details$.pizza_id = pizzas$.pizza_id;

Result: The total revenue from all pizza sales is calculated, providing a key financial metric.

Q.3 Identify the highest-priced pizza:

SELECT TOP (1) name, price 
FROM pizza_types$ 
INNER JOIN pizzas$ ON pizzas$.pizza_type_id = pizza_types$.pizza_type_id 
ORDER BY price DESC;

Result: The most expensive pizza is identified, which could be useful for pricing strategy.

Q.4 Identify the most common pizza size ordered:

SELECT size, COUNT(order_details$.order_details_id) AS Order_Count 
FROM pizzas$ 
JOIN order_details$ ON pizzas$.pizza_id = order_details$.pizza_id 
GROUP BY size;

Result: Insights into customer preferences for pizza sizes.

Q.5 List the top 5 most ordered pizza types with quantities:

SELECT TOP (5) name, SUM(quantity) AS Quantity 
FROM pizza_types$ 
JOIN pizzas$ ON pizza_types$.pizza_type_id = pizzas$.pizza_type_id 
JOIN order_details$ ON order_details$.pizza_id = pizzas$.pizza_id 
GROUP BY name 
ORDER BY SUM(quantity) DESC;

Result: Identifies the most popular pizzas to focus on inventory and marketing efforts.

2. Intermediate Analysis
Q.1 Find the total quantity of each pizza category ordered:

SELECT category, SUM(quantity) AS Quantity 
FROM pizza_types$ 
JOIN pizzas$ ON pizza_types$.pizza_type_id = pizzas$.pizza_type_id 
JOIN order_details$ ON pizzas$.pizza_id = order_details$.pizza_id 
GROUP BY category 
ORDER BY SUM(quantity) DESC;

Result: Category-wise order insights to assess which types of pizzas are in demand.

Q.2 Distribution of orders by hour:

SELECT DATEPART(HOUR, time) AS Day_Hour, COUNT(order_id) AS No_of_Orders 
FROM orders$ 
GROUP BY DATEPART(HOUR, time) 
ORDER BY COUNT(order_id) DESC;

Result: Identifies peak hours for orders, aiding in staffing and inventory planning.

Q.3 Category-wise distribution of pizzas:

SELECT category, COUNT(name) AS Name 
FROM pizza_types$ 
GROUP BY category;

Result: Breakdown of pizza categories to guide menu planning.

Q.4 Average number of pizzas ordered per day:

SELECT ROUND(AVG(quantity), 0) AS Avg_Pizza_Order_Per_Day 
FROM (SELECT orders$.date, SUM(order_details$.quantity) AS quantity 
      FROM orders$ 
      JOIN order_details$ ON orders$.order_id = order_details$.order_id 
      GROUP BY orders$.date) AS Daily_Orders;

Result: Determines the average daily order volume for inventory management.

Q.5 Top 3 pizzas based on revenue:

SELECT TOP (3) pizza_types$.name, SUM(order_details$.quantity * price) 
FROM order_details$ 
JOIN pizzas$ ON order_details$.pizza_id = pizzas$.pizza_id 
JOIN pizza_types$ ON pizzas$.pizza_type_id = pizza_types$.pizza_type_id 
GROUP BY pizza_types$.name 
ORDER BY SUM(order_details$.quantity * price) DESC;

Result: Focus on high-revenue pizza types to drive sales strategies.

3. Advanced Analysis
Q.1 Percentage contribution of each pizza type to total revenue:

SELECT pizza_types$.category, ROUND(SUM(order_details$.quantity * pizzas$.price) / 
(SELECT ROUND(SUM(order_details$.quantity * pizzas$.price), 2) AS Total_Revenue 
FROM order_details$ 
JOIN pizzas$ ON order_details$.pizza_id = pizzas$.pizza_id) * 100, 2) AS Revenue
FROM order_details$ 
JOIN pizzas$ ON order_details$.pizza_id = pizzas$.pizza_id 
JOIN pizza_types$ ON pizzas$.pizza_type_id = pizza_types$.pizza_type_id 
GROUP BY pizza_types$.category 
ORDER BY SUM(order_details$.quantity * price) DESC;

Result: Percentage contribution helps in understanding which pizzas drive the majority of the revenue.

Q.2 Cumulative revenue over time:

SELECT date, SUM(revenue) OVER (ORDER BY date) 
FROM (SELECT orders$.date, SUM(order_details$.quantity * pizzas$.price) AS Revenue 
      FROM orders$ 
      JOIN order_details$ ON orders$.order_id = order_details$.order_id 
      JOIN pizzas$ ON order_details$.pizza_id = pizzas$.pizza_id 
      GROUP BY orders$.date) AS Revenue_Per_Day;

Result: Tracks revenue growth over time for better financial forecasting.

Q.3 Top 3 pizzas by revenue for each category:

SELECT name, revenue, rank 
FROM (SELECT category, name, revenue, RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS Rank 
      FROM (SELECT pizza_types$.category, pizza_types$.name, SUM(order_details$.quantity * pizzas$.price) AS Revenue 
            FROM pizza_types$ 
            JOIN pizzas$ ON pizza_types$.pizza_type_id = pizzas$.pizza_type_id 
            JOIN order_details$ ON pizzas$.pizza_id = order_details$.pizza_id 
            GROUP BY pizza_types$.category, pizza_types$.name) AS R) AS Rn 
WHERE rank <= 3;

Result: Reveals the top-performing pizzas in each category for focused sales efforts.
