# Pizza-Sales-Data-Analysis-SQL-

create database pizzahut;
use pizzahut;

alter table oders
rename to orders ;

-- #SIMPLE LEVEL QUESTIONS

-- retive total no of order placed
select count(order_id) as total_orders from orders;


-- calculate total revenue generated from pizza sales
select sum(price) as total_revenue from pizzas;

select sum(oders_details.quantity * pizzas.price) as total_sales
from oders_details
join pizzas
on oders_details.pizza_id = pizzas.pizza_id;
-- if we want value to 2 decimals select round(sum(oders_details.quantity * pizzas.price),2) as total_sales

-- identify highest priced pizza  
select pizza_types.name  , pizzas.price 
from pizza_types 
join pizzas
on pizza_types.pizza_type_id  = pizzas.pizza_type_id
order by pizzas.price desc
 limit 1;
 
--  most common pizza size orded

select pizzas.size,count(oders_details.order_details_id) as oc
from pizzas
join oders_details 
on pizzas.pizza_id = oders_details.pizza_id
group by pizzas.size
order by oc desc 
limit 1;

-- list top 5 most pizza type oders
-- alomg with quantities
-- join 3 tables

select pizza_types.name,sum(oders_details.quantity) as quantity
from pizza_types
join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join oders_details
on oders_details.pizza_id = pizzas.pizza_id
group by pizza_types.name
order by quantity desc
limit 5;


-- #INTERMIDIATE LEVEL QUESTIONS

-- join the necessary tables to find the total quantity of each pizza category order

select pizza_types.category,sum(oders_details.quantity) as quantity
from pizza_types
join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id 
join oders_details
on pizzas.pizza_id = oders_details.pizza_id
group by pizza_types.category 
order by quantity desc;


-- Determine the distribution of orders by hour of the day.
select hour(order_time),count(order_id) as order_count from orders
group by hour(order_time);

-- Join relevant tables to find the category-wise distribution of pizzas.
select category ,count(name) from pizza_types
group by category;

-- Group the orders by date and calculate the average number of pizzas ordered per day. 
select round(avg(avg_quatity),0) from  
(select orders.order_date , sum(oders_details.quantity) as avg_quatity
from orders
join oders_details
on orders.order_id = oders_details.order_id
group by orders.order_date)as order_quatity;

-- select avg(alias) from  subquery from
-- (select orders.order_date , sum(oders_details.quantity) as alias
-- from orders)

-- Determine the top 3 most ordered pizza types based on revenue.

select pizza_types.name ,
sum(oders_details.quantity * pizzas.price) as revenu
from pizza_types
join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join oders_details
on oders_details.pizza_id = pizzas.pizza_id
group by pizza_types.name order by revenu desc
limit 3;


-- #ADVANCE QUESTION 

-- Calculate the percentage contribution of each pizza type to total revenue.

select pizza_types.category , sum(oders_details.quantity * pizzas.price)
 /
( select sum(oders_details.quantity * pizzas.price) as sales 
from oders_details 
join pizzas
on pizzas.pizza_id = oders_details.pizza_id) *100 as revenue

from pizza_types
join pizzas
on pizza_types.pizza_type_id = pizzas.pizza_type_id
join oders_details
on pizzas.pizza_id = oders_details.pizza_id
group by pizza_types.category
order by revenue desc ;


-- Analyze the cumulative revenue generated over time.

select order_date, 
sum( revenue) over(order by order_date) as cum_rev
from 
(select orders.order_date,
sum(pizzas.price * oders_details.quantity) as revenue
from oders_details
join pizzas
 on  oders_details.pizza_id = pizzas.pizza_id
 join orders 
 on orders.order_id = oders_details.order_id 
 group by orders.order_date) as sales;
 
 
 
 -- Determine the top 3 most ordered pizza types based on revenue for each pizza category.
 select pizza_types.category, pizza_types.name ,
 sum(pizzas.price * oders_details.quantity) as revenue 
 from pizza_types
 join pizzas
 on pizza_types.pizza_type_id = pizzas.pizza_type_id
 join oders_details
 on oders_details.pizza_id = pizzas.pizza_id
 group by pizza_types.category,pizza_types.name;
 
 
