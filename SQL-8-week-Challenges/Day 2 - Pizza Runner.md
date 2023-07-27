```python

```

# Day 2 - Pizza Runner

## A. Pizza Metrics

#### 1. How many pizzas were ordered?


```sql
%%sql

SELECT COUNT(order_id) as no_pizza
FROM pizza_runner.customer_orders;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>no_pizza</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>14</td>
        </tr>
    </tbody>
</table>



14 pizzas were ordered overall

#### 2. How many unique customer orders were made?


```sql
%%sql

SELECT COUNT(DISTINCT order_id) as no_cust
FROM pizza_runner.customer_orders;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>no_cust</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>10</td>
        </tr>
    </tbody>
</table>



10 unique customer orders were made

#### 3. How many successful orders were delivered by each runner?


```sql
%%sql

SELECT runner_id, COUNT(order_id)
FROM pizza_runner.runner_orders
WHERE pickup_time is not null
GROUP BY runner_id
ORDER BY runner_id;
```

     * postgresql://postgres:***@localhost:5432/master
    3 rows affected.
    




<table>
    <thead>
        <tr>
            <th>runner_id</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>4</td>
        </tr>
        <tr>
            <td>2</td>
            <td>3</td>
        </tr>
        <tr>
            <td>3</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



#### 4. How many of each type of pizza was delivered?


```sql
%%sql

SELECT pizza_name, COUNT(order_id)
FROM pizza_runner.customer_orders
INNER JOIN pizza_runner.pizza_names USING(pizza_id)
WHERE order_id NOT IN
	(SELECT order_id
     FROM pizza_runner.runner_orders
     WHERE pickup_time is null)
GROUP BY pizza_name;
```

     * postgresql://postgres:***@localhost:5432/master
    2 rows affected.
    




<table>
    <thead>
        <tr>
            <th>pizza_name</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Meatlovers</td>
            <td>9</td>
        </tr>
        <tr>
            <td>Vegetarian</td>
            <td>3</td>
        </tr>
    </tbody>
</table>



#### 5. How many Vegetarian and Meatlovers were ordered by each customer?


```sql
%%sql

SELECT customer_id, pizza_name, COUNT(order_id)
FROM pizza_runner.customer_orders
INNER JOIN pizza_runner.pizza_names USING(pizza_id)
GROUP BY customer_id, pizza_name
ORDER BY customer_id, pizza_name;
```

     * postgresql://postgres:***@localhost:5432/master
    8 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>pizza_name</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>101</td>
            <td>Meatlovers</td>
            <td>2</td>
        </tr>
        <tr>
            <td>101</td>
            <td>Vegetarian</td>
            <td>1</td>
        </tr>
        <tr>
            <td>102</td>
            <td>Meatlovers</td>
            <td>2</td>
        </tr>
        <tr>
            <td>102</td>
            <td>Vegetarian</td>
            <td>1</td>
        </tr>
        <tr>
            <td>103</td>
            <td>Meatlovers</td>
            <td>3</td>
        </tr>
        <tr>
            <td>103</td>
            <td>Vegetarian</td>
            <td>1</td>
        </tr>
        <tr>
            <td>104</td>
            <td>Meatlovers</td>
            <td>3</td>
        </tr>
        <tr>
            <td>105</td>
            <td>Vegetarian</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



#### 6. What was the maximum number of pizzas delivered in a single order?


```sql
%%sql

SELECT order_id, COUNT(pizza_id) as max_pizzas
FROM pizza_runner.customer_orders
WHERE order_id NOT IN
	(SELECT order_id
     FROM pizza_runner.runner_orders
     WHERE pickup_time is null)
GROUP BY order_id
ORDER BY COUNT(pizza_id) DESC
LIMIT 1;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>order_id</th>
            <th>max_pizzas</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>4</td>
            <td>3</td>
        </tr>
    </tbody>
</table>



Order number 4 had 3 pizzas

#### 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?


```sql
%%sql

select customer_id,
	SUM(
		case when exclusions is not null or extras is not null then 1
		else 0
		end) as has_changed,
	SUM(
		case when exclusions is null and extras is null then 1
		else 0
		end) as no_change
from pizza_runner.customer_orders co
where order_id not in
	(SELECT order_id
     FROM pizza_runner.runner_orders
     WHERE pickup_time is null)
group by customer_id
order by customer_id;
```

     * postgresql://postgres:***@localhost:5432/master
    5 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>has_changed</th>
            <th>no_change</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>101</td>
            <td>0</td>
            <td>2</td>
        </tr>
        <tr>
            <td>102</td>
            <td>0</td>
            <td>3</td>
        </tr>
        <tr>
            <td>103</td>
            <td>3</td>
            <td>0</td>
        </tr>
        <tr>
            <td>104</td>
            <td>2</td>
            <td>1</td>
        </tr>
        <tr>
            <td>105</td>
            <td>1</td>
            <td>0</td>
        </tr>
    </tbody>
</table>



#### 8. How many pizzas were delivered that had both exclusions and extras?


```sql
%%sql

select count(*) as pizza_with_both
from pizza_runner.customer_orders co
where order_id not in
	(SELECT order_id
     FROM pizza_runner.runner_orders
     WHERE pickup_time is null)
and (exclusions is not null and extras is not null);
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>pizza_with_both</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
        </tr>
    </tbody>
</table>



1 pizza had both exclusions and extras

#### 9. What was the total volume of pizzas ordered for each hour of the day?


```sql
%%sql

select date_part('hour', order_time), count(*) as num_pizzas
from pizza_runner.customer_orders co
group by date_part('hour', order_time)
order by date_part('hour', order_time);
```

     * postgresql://postgres:***@localhost:5432/master
    6 rows affected.
    




<table>
    <thead>
        <tr>
            <th>date_part</th>
            <th>num_pizzas</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>11.0</td>
            <td>1</td>
        </tr>
        <tr>
            <td>13.0</td>
            <td>3</td>
        </tr>
        <tr>
            <td>18.0</td>
            <td>3</td>
        </tr>
        <tr>
            <td>19.0</td>
            <td>1</td>
        </tr>
        <tr>
            <td>21.0</td>
            <td>3</td>
        </tr>
        <tr>
            <td>23.0</td>
            <td>3</td>
        </tr>
    </tbody>
</table>



#### 10. What was the volume of orders for each day of the week?


```sql
%%sql

select extract(dow from order_time), count(*)
from pizza_runner.customer_orders co
group by extract;
```

     * postgresql://postgres:***@localhost:5432/master
    4 rows affected.
    




<table>
    <thead>
        <tr>
            <th>extract</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>3</td>
            <td>5</td>
        </tr>
        <tr>
            <td>4</td>
            <td>3</td>
        </tr>
        <tr>
            <td>6</td>
            <td>5</td>
        </tr>
        <tr>
            <td>5</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



## B. Runner and Customer Experience

#### 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)


```sql
%%sql

select date_part('week', registration_date), count(*) as num_signeup
from pizza_runner.runners r 
group by date_part('week', registration_date);
```

     * postgresql://postgres:***@localhost:5432/master
    3 rows affected.
    




<table>
    <thead>
        <tr>
            <th>date_part</th>
            <th>num_signeup</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>53.0</td>
            <td>2</td>
        </tr>
        <tr>
            <td>1.0</td>
            <td>1</td>
        </tr>
        <tr>
            <td>2.0</td>
            <td>1</td>
        </tr>
    </tbody>
</table>



#### 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?


```sql
%%sql

select avg(date_part('minute', (ro.pickup_time - co.order_time))) as time_difference
from pizza_runner.runner_orders ro
inner join
	(select order_id, MIN(order_time) as order_time
	from pizza_runner.customer_orders 
	group by order_id) co
on co.order_id = ro.order_id
where ro.pickup_time is not null;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>time_difference</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>15.625</td>
        </tr>
    </tbody>
</table>



15 minutes is an average time it takes a runner to pick up a pizza

#### 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?


```sql
%%sql

select
	num_pizzas,
	avg(date_part('minute', (ro.pickup_time - co.order_time))) as time_difference
from
	(select order_id, count(pizza_id) as num_pizzas, min(order_time) as order_time
	from pizza_runner.customer_orders co 
	group by order_id) co
inner join pizza_runner.runner_orders ro
on ro.order_id = co.order_id 
group by num_pizzas;
```

     * postgresql://postgres:***@localhost:5432/master
    3 rows affected.
    




<table>
    <thead>
        <tr>
            <th>num_pizzas</th>
            <th>time_difference</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>3</td>
            <td>29.0</td>
        </tr>
        <tr>
            <td>2</td>
            <td>18.0</td>
        </tr>
        <tr>
            <td>1</td>
            <td>12.0</td>
        </tr>
    </tbody>
</table>



#### 4. What was the average distance travelled for each customer?


```sql
%%sql

select customer_id, round(avg(distance), 0) as avg_distance
from pizza_runner.runner_orders ro
inner join pizza_runner.customer_orders co 
on ro.order_id = co.order_id
group by customer_id
order by customer_id;
```

     * postgresql://postgres:***@localhost:5432/master
    5 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>avg_distance</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>101</td>
            <td>20</td>
        </tr>
        <tr>
            <td>102</td>
            <td>17</td>
        </tr>
        <tr>
            <td>103</td>
            <td>23</td>
        </tr>
        <tr>
            <td>104</td>
            <td>10</td>
        </tr>
        <tr>
            <td>105</td>
            <td>25</td>
        </tr>
    </tbody>
</table>



#### 5. What was the difference between the longest and shortest delivery times for all orders?


```sql
%%sql

select max(duration) - min(duration) as delivery_difference
from pizza_runner.runner_orders ro;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>delivery_difference</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>30</td>
        </tr>
    </tbody>
</table>



30 minutes is the delivery difference

#### 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?


```sql
%%sql

select
	runner_id,
	order_id,
	round(avg(distance/(duration/60.00)),2) as avg_speed
from pizza_runner.runner_orders ro
where pickup_time is not null
group by runner_id, order_id
order by order_id;
```

     * postgresql://postgres:***@localhost:5432/master
    8 rows affected.
    




<table>
    <thead>
        <tr>
            <th>runner_id</th>
            <th>order_id</th>
            <th>avg_speed</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>1</td>
            <td>37.50</td>
        </tr>
        <tr>
            <td>1</td>
            <td>2</td>
            <td>44.44</td>
        </tr>
        <tr>
            <td>1</td>
            <td>3</td>
            <td>40.20</td>
        </tr>
        <tr>
            <td>2</td>
            <td>4</td>
            <td>35.10</td>
        </tr>
        <tr>
            <td>3</td>
            <td>5</td>
            <td>40.00</td>
        </tr>
        <tr>
            <td>2</td>
            <td>7</td>
            <td>60.00</td>
        </tr>
        <tr>
            <td>2</td>
            <td>8</td>
            <td>93.60</td>
        </tr>
        <tr>
            <td>1</td>
            <td>10</td>
            <td>60.00</td>
        </tr>
    </tbody>
</table>



#### 7. What is the successful delivery percentage for each runner?


```sql
%%sql

select
	runner_id,
	sum(case when distance is not null then 1 else 0 end) / count(*)::float as rate_sucess
from pizza_runner.runner_orders ro 
group by runner_id;
```

     * postgresql://postgres:***@localhost:5432/master
    3 rows affected.
    




<table>
    <thead>
        <tr>
            <th>runner_id</th>
            <th>rate_sucess</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2</td>
            <td>0.75</td>
        </tr>
        <tr>
            <td>1</td>
            <td>1.0</td>
        </tr>
        <tr>
            <td>3</td>
            <td>0.5</td>
        </tr>
    </tbody>
</table>



## C. Ingredient Optimisation

#### 1. What are the standard ingredients for each pizza?


```sql
%%sql

with ingridients_list as
	(select
	pizza_id,
	regexp_split_to_table(toppings, '[,\s]+')::integer as topping_id
	from pizza_runner.pizza_recipes pr )
select pizza_id, string_agg(topping_name, ',') as recipe
from ingridients_list
inner join pizza_runner.pizza_toppings pt 
on ingridients_list.topping_id = pt.topping_id
group by pizza_id
order by pizza_id;
```

     * postgresql://postgres:***@localhost:5432/master
    2 rows affected.
    




<table>
    <thead>
        <tr>
            <th>pizza_id</th>
            <th>recipe</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>Bacon,BBQ Sauce,Beef,Cheese,Chicken,Mushrooms,Pepperoni,Salami</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Cheese,Mushrooms,Onions,Peppers,Tomatoes,Tomato Sauce</td>
        </tr>
    </tbody>
</table>



#### 2. What was the most commonly added extra?


```sql
%%sql

with extras_list as
	(select 
	regexp_split_to_table(extras , '[,\s]+')::integer as topping_id
	from pizza_runner.customer_orders co)
select pt.topping_name, count(*)
from extras_list
inner join pizza_runner.pizza_toppings pt 
on extras_list.topping_id = pt.topping_id
group by pt.topping_name
order by count desc
limit 1;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>topping_name</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Bacon</td>
            <td>4</td>
        </tr>
    </tbody>
</table>



#### 3. What was the most common exclusion?


```sql
%%sql

with extras_list as
	(select 
	regexp_split_to_table(exclusions, '[,\s]+')::integer as topping_id
	from pizza_runner.customer_orders co)
select pt.topping_name, count(*)
from extras_list
inner join pizza_runner.pizza_toppings pt 
on extras_list.topping_id = pt.topping_id
group by pt.topping_name
order by count desc
limit 1;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>topping_name</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Cheese</td>
            <td>4</td>
        </tr>
    </tbody>
</table>



#### 4. Generate an order item for each record in the customers_orders table in the format of one of the following:
- Meat Lovers
- Meat Lovers - Exclude Beef
- Meat Lovers - Extra Bacon
- Meat Lovers - Exclude Cheese, Bacon - Extra Mushroom, Peppers


```python

```

#### 5. Generate an alphabetically ordered comma separated ingredient list for each pizza order from the customer_orders table and add a 2x in front of any relevant ingredients
-- For example: "Meat Lovers: 2xBacon, Beef, ... , Salami"


```python

```

#### 6. What is the total quantity of each ingredient used in all delivered pizzas sorted by most frequent first?


```python

```
