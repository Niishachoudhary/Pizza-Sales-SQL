Pizza Sales Analysis
This project involves a comprehensive analysis of pizza sales data using MySQL Workbench for data manipulation.
The analysis covers various aspects, including identifying the best and worst selling pizzas, determining total revenue generated, calculating average revenue, and
examining sales trends on various time scales.

Dataset Overview
This dataset contain detailed information about pizza orders, including specifics about the pizza variants, quantities, pricing, dates, times, and categorization details.
...PIZZA_TYPE
pizza_type_id: An identifier linking to a specific name of the pizza.
name: Specifies the name of the specific pizza variant ordered.
category: Indicates the category of the pizza, such as vegetarian, non-vegetarian, etc.
ingredients: Provides a list or description of the ingredients used in the pizza.

...PIZZAS
pizza_id: A unique identifier assigned to each distinct pizza variant available for ordering.
pizza_type_id: An identifier linking to a specific name of the pizza.
size: Represents the size of the pizza (e.g., small, medium, large).
price: The cost of a single unit of the specific pizza variant.

...ORDER_DETAILS
order_id: A unique identifier for each order made, which links to multiple pizzas.
order_detailed_id: Primary key unique to every order placed.
pizza_id: A unique identifier assigned to each distinct pizza variant available for ordering.
quantity: The number of units of a specific pizza variant ordered within an order.

...ORDERS
order_date: The date when the order was placed.
order_time: The time when the order was placed.
order_id: A unique identifier for each order made, which links to multiple pizzas.

SQL Queries Used 
Basic Questions:
**Retrieve the total number of orders placed.**
SELECT 
    COUNT(od.order_id) AS total_orders
FROM
    orders AS od;
    
**Calculate the total revenue generated from pizza sales.**
SELECT 
    ROUND(SUM(p.price * od.quantity), 3) AS total_revenue
FROM
    pizzas p
        JOIN
    order_details od ON p.pizza_id = od.pizza_id;
    
**Identify the highest-priced pizza.**
SELECT 
    pt.name, p.price
FROM
    pizzas p
        JOIN
    pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
ORDER BY p.price DESC
LIMIT 1;

**Identify the most common pizza size ordered.**
SELECT 
    p.size, COUNT(od.order_details_id) AS odr
FROM
    pizzas p
        JOIN
    order_details od ON p.pizza_id = od.pizza_id
GROUP BY p.size
ORDER BY odr DESC
LIMIT 1;

**List the top 5 most ordered pizza types along with their quantities.**
SELECT 
    pt.name, SUM(od.quantity) AS quantity
FROM
    order_details od
        JOIN
    pizzas p ON od.pizza_id = p.pizza_id
        JOIN
    pizza_types pt ON pt.pizza_type_id = p.pizza_type_id
GROUP BY pt.name
ORDER BY quantity DESC
LIMIT 5;

Intermediate Questions:
**Join the necessary tables to find the total quantity of each pizza category ordered.**
SELECT 
    pt.category, sum(od.quantity) AS quantity
FROM
    pizza_types pt
        JOIN
    pizzas p ON pt.pizza_type_id = p.pizza_type_id
        JOIN
    order_details od ON od.pizza_id = p.pizza_id
GROUP BY pt.category;

**Determine the distribution of orders by hour of the day.**
SELECT 
    HOUR(o.order_time), COUNT(o.order_id) AS Count_orders
FROM
    orders o
GROUP BY HOUR(o.order_time)
ORDER BY Count_orders DESC; 

**Join relevant tables to find the category-wise distribution of pizzas.**
SELECT 
    category, COUNT(pizza_type_id) AS count_of_pizza_types
FROM
    pizza_types
GROUP BY category;

**Group the orders by date and calculate the average number of pizzas ordered per day.**
SELECT 
    ROUND(AVG(quantity), 2)
FROM
    (SELECT 
        o.order_date, SUM(od.quantity) AS quantity
    FROM
        orders o
    JOIN order_details od ON o.order_id = od.order_id
    GROUP BY o.order_date) AS order_quatity; 
 
 **Determine the top 3 most ordered pizza types based on revenue.**
SELECT 
    pt.name, SUM(p.price * od.quantity) AS revenue
FROM
    pizzas p
        JOIN
    order_details od ON p.pizza_id = od.pizza_id
        JOIN
    pizza_types pt ON pt.pizza_type_id = p.pizza_type_id
GROUP BY pt.name
ORDER BY revenue DESC
LIMIT 3; 

Advanced:
**Calculate the percentage contribution of each pizza type to total revenue.**

SELECT pt.category, sum(od.quantity * p.price) as revenue_total, ((sum(od.quantity * p.price)/(select sum(od.quantity * p.price)
from pizzas p join order_details od
on p.pizza_id = od.pizza_id))* 100) as revenue_percentage
FROM pizzas p join order_details od
on p.pizza_id = od.pizza_id
join pizza_types pt 
on pt.pizza_type_id = p.pizza_type_id 
group by pt.category;

**Analyze the cumulative revenue generated over time.**
 select order_date, sum(revenue) 
over(order by order_date) as cum_revenue -- windows function
from(select o.order_date, round(sum(od.quantity * p.price),
1)
as revenue 
from order_details  od join 
 orders o on
 od.order_id = o.order_id
 join pizzas p on 
 p.pizza_id = od.pizza_id
 group by o.order_date) as per_day_sales
 ;

**Determine the top 3 most ordered pizza types based on revenue for each pizza category.**
select name, revenue, category
from 
(select category, name, revenue, rank()
over(partition by category order by revenue desc) as rnk
from
(select category, name, sum(od.quantity * p.price) as revenue
from order_details od join  pizzas p on 
od.pizza_id = p.pizza_id join pizza_types pt on
pt.pizza_type_id = p.pizza_type_id 
group by category, name) as all_detail) as all_detail_rank
where rnk <= 3;

CONCLUSION
The Pizza Sales Analysis project using SQL provided valuable insights into customer preferences, sales trends, and revenue drivers.
By leveraging SQL queries, I identified top-selling pizzas, peak order times, and high-revenue categories, which can help businesses 
optimize inventory, pricing strategies, and marketing campaigns. This project demonstrates the power of SQL in transforming raw 
transactional data into actionable business intelligence.

