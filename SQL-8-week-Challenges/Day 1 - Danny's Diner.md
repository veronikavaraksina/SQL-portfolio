# Case Study Questions

#### 1. What is the total amount each customer spent at the restaurant?


```sql
%%sql

SELECT customer_id, SUM(price) as total_sales
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu USING(product_id)
GROUP BY customer_id
ORDER BY customer_id ASC;
```

     * postgresql://postgres:***@localhost:5432/master
    3 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>total_sales</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>A</td>
            <td>76</td>
        </tr>
        <tr>
            <td>B</td>
            <td>74</td>
        </tr>
        <tr>
            <td>C</td>
            <td>36</td>
        </tr>
    </tbody>
</table>



#### 2. How many days has each customer visited the restaurant?


```sql
%%sql

SELECT customer_id, COUNT(DISTINCT order_date) as visits
FROM dannys_diner.sales
GROUP BY customer_id
ORDER BY customer_id ASC;
```

     * postgresql://postgres:***@localhost:5432/master
    3 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>visits</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>A</td>
            <td>4</td>
        </tr>
        <tr>
            <td>B</td>
            <td>6</td>
        </tr>
        <tr>
            <td>C</td>
            <td>2</td>
        </tr>
    </tbody>
</table>



#### 3. What was the first item from the menu purchased by each customer?


```sql
%%sql

SELECT DISTINCT B.customer_id, product_name
FROM dannys_diner.sales H
INNER JOIN
	(SELECT customer_id, MIN(order_date) as first_purchase
     FROM dannys_diner.sales
     GROUP BY customer_id) B
ON H.customer_id = B.customer_id AND H.order_date = B.first_purchase
INNER JOIN dannys_diner.menu USING(product_id)
ORDER BY customer_id ASC;
```

     * postgresql://postgres:***@localhost:5432/master
    4 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>product_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>A</td>
            <td>curry</td>
        </tr>
        <tr>
            <td>A</td>
            <td>sushi</td>
        </tr>
        <tr>
            <td>B</td>
            <td>curry</td>
        </tr>
        <tr>
            <td>C</td>
            <td>ramen</td>
        </tr>
    </tbody>
</table>



#### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?


```sql
%%sql

SELECT product_name, COUNT(order_date) as number_of_orders
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu USING(product_id)
GROUP BY product_name
LIMIT 1;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>product_name</th>
            <th>number_of_orders</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ramen</td>
            <td>8</td>
        </tr>
    </tbody>
</table>



Ramen is most popular and it was purchased 8 times

#### 5. Which item was the most popular for each customer?


```sql
%%sql

WITH pop_items AS
	(SELECT
     customer_id,
     product_id,
     COUNT(order_date) AS no_ord,
     RANK() OVER(PARTITION BY customer_id ORDER BY COUNT(order_date) DESC) AS rank_food
     FROM dannys_diner.sales
     GROUP BY customer_id, product_id)
SELECT customer_id,
    product_name
FROM pop_items
INNER JOIN dannys_diner.menu USING(product_id)
WHERE rank_food = 1
ORDER BY customer_id;
```

     * postgresql://postgres:***@localhost:5432/master
    5 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>product_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>A</td>
            <td>ramen</td>
        </tr>
        <tr>
            <td>B</td>
            <td>sushi</td>
        </tr>
        <tr>
            <td>B</td>
            <td>curry</td>
        </tr>
        <tr>
            <td>B</td>
            <td>ramen</td>
        </tr>
        <tr>
            <td>C</td>
            <td>ramen</td>
        </tr>
    </tbody>
</table>



#### 6. Which item was purchased first by the customer after they became a member?


```sql
%%sql

WITH joined AS (
  SELECT
  	m.customer_id,
  	s.product_id,
  	ROW_NUMBER() OVER (PARTITION BY m.customer_id ORDER BY s.order_date) AS row_num
  FROM dannys_diner.members m
  INNER JOIN dannys_diner.sales s
  ON m.customer_id = s.customer_id
  AND s.order_date > m.join_date
  )
  SELECT customer_id, product_name
  FROM joined
  INNER JOIN dannys_diner.menu USING(product_id)
  WHERE row_num = 1
  ORDER BY customer_id;
```

     * postgresql://postgres:***@localhost:5432/master
    2 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>product_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>A</td>
            <td>ramen</td>
        </tr>
        <tr>
            <td>B</td>
            <td>sushi</td>
        </tr>
    </tbody>
</table>



#### 7. Which item was purchased just before the customer became a member?


```sql
%%sql

WITH joined AS (
  SELECT
  	m.customer_id,
  	s.product_id,
  	ROW_NUMBER() OVER (PARTITION BY m.customer_id ORDER BY s.order_date DESC) AS row_num
  FROM dannys_diner.members m
  INNER JOIN dannys_diner.sales s
  ON m.customer_id = s.customer_id
  AND s.order_date < m.join_date
  )
  SELECT customer_id, product_name
  FROM joined
  INNER JOIN dannys_diner.menu USING(product_id)
  WHERE row_num = 1
  ORDER BY customer_id;
```

     * postgresql://postgres:***@localhost:5432/master
    2 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>product_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>A</td>
            <td>sushi</td>
        </tr>
        <tr>
            <td>B</td>
            <td>sushi</td>
        </tr>
    </tbody>
</table>



#### 8. What is the total items and amount spent for each member before they became a member?


```sql
%%sql

WITH joined AS (
  SELECT
  	m.customer_id,
  	s.product_id
  FROM dannys_diner.members m
  INNER JOIN dannys_diner.sales s
  ON m.customer_id = s.customer_id
  AND s.order_date < m.join_date
  )
  SELECT customer_id, SUM(price)
  FROM joined
  INNER JOIN dannys_diner.menu USING(product_id)
  GROUP BY customer_id
  ORDER BY customer_id;
```

     * postgresql://postgres:***@localhost:5432/master
    2 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>sum</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>A</td>
            <td>25</td>
        </tr>
        <tr>
            <td>B</td>
            <td>40</td>
        </tr>
    </tbody>
</table>



#### 9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?


```sql
%%sql

WITH order_points AS (SELECT customer_id,
	product_name,
    price,
    price * CASE WHEN product_name = 'sushi' THEN 20
    ELSE 10 END AS points
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu USING(product_id))
SELECT customer_id, SUM(points)
FROM order_points
GROUP BY customer_id
ORDER BY customer_id;
```

     * postgresql://postgres:***@localhost:5432/master
    3 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>sum</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>A</td>
            <td>860</td>
        </tr>
        <tr>
            <td>B</td>
            <td>940</td>
        </tr>
        <tr>
            <td>C</td>
            <td>360</td>
        </tr>
    </tbody>
</table>



#### 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?


```sql
%%sql

WITH dates_cte AS (
  SELECT 
    customer_id, 
      join_date, 
      join_date + 6 AS valid_date, 
      DATE_TRUNC(
        'month', '2021-01-31'::DATE)
        + interval '1 month' 
        - interval '1 day' AS last_date
  FROM dannys_diner.members
)

SELECT 
  sales.customer_id, 
  SUM(CASE
    WHEN menu.product_name = 'sushi' THEN 2 * 10 * menu.price
    WHEN sales.order_date BETWEEN dates.join_date AND dates.valid_date THEN 2 * 10 * menu.price
    ELSE 10 * menu.price END) AS points
FROM dannys_diner.sales
INNER JOIN dates_cte AS dates
  ON sales.customer_id = dates.customer_id
  AND dates.join_date <= sales.order_date
  AND sales.order_date <= dates.last_date
INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
GROUP BY sales.customer_id;
```

     * postgresql://postgres:***@localhost:5432/master
    2 rows affected.
    




<table>
    <thead>
        <tr>
            <th>customer_id</th>
            <th>points</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>B</td>
            <td>320</td>
        </tr>
        <tr>
            <td>A</td>
            <td>1020</td>
        </tr>
    </tbody>
</table>


