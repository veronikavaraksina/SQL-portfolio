# Day 3 SQL challenge 

## Part B - Data Analysis Questions

#### 1. How many customers has Foodie-Fi ever had?


```sql
%%sql
select count(distinct customer_id)
from foodie_fi.subscriptions s;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1000</td>
        </tr>
    </tbody>
</table>



There are 1000 unique customer

#### 2. What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value


```sql
%%sql

select
	date_part('month', start_date) as month_of_start,
	count(plan_id)
from foodie_fi.subscriptions s
where plan_id = 0
group by date_part('month', start_date)
order by month_of_start;
```

     * postgresql://postgres:***@localhost:5432/master
    12 rows affected.
    




<table>
    <thead>
        <tr>
            <th>month_of_start</th>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1.0</td>
            <td>88</td>
        </tr>
        <tr>
            <td>2.0</td>
            <td>68</td>
        </tr>
        <tr>
            <td>3.0</td>
            <td>94</td>
        </tr>
        <tr>
            <td>4.0</td>
            <td>81</td>
        </tr>
        <tr>
            <td>5.0</td>
            <td>88</td>
        </tr>
        <tr>
            <td>6.0</td>
            <td>79</td>
        </tr>
        <tr>
            <td>7.0</td>
            <td>89</td>
        </tr>
        <tr>
            <td>8.0</td>
            <td>88</td>
        </tr>
        <tr>
            <td>9.0</td>
            <td>87</td>
        </tr>
        <tr>
            <td>10.0</td>
            <td>79</td>
        </tr>
        <tr>
            <td>11.0</td>
            <td>75</td>
        </tr>
        <tr>
            <td>12.0</td>
            <td>84</td>
        </tr>
    </tbody>
</table>



#### 3. What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name?


```sql
%%sql

select
	s.plan_id,
	plan_name,
	count(customer_id) as count_of_events
from foodie_fi.subscriptions s 
left join foodie_fi."plans" p 
on s.plan_id = p.plan_id 
where start_date >= '2021-01-01'
group by s.plan_id, plan_name
order by plan_id ;
```

     * postgresql://postgres:***@localhost:5432/master
    4 rows affected.
    




<table>
    <thead>
        <tr>
            <th>plan_id</th>
            <th>plan_name</th>
            <th>count_of_events</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>basic monthly</td>
            <td>8</td>
        </tr>
        <tr>
            <td>2</td>
            <td>pro monthly</td>
            <td>60</td>
        </tr>
        <tr>
            <td>3</td>
            <td>pro annual</td>
            <td>63</td>
        </tr>
        <tr>
            <td>4</td>
            <td>churn</td>
            <td>71</td>
        </tr>
    </tbody>
</table>



#### 4. What is the customer count and percentage of customers who have churned rounded to 1 decimal place?


```sql
%%sql

select
	count(distinct customer_id) as churned_cust,
	round(100.0 * count(customer_id) /
	(select count(distinct customer_id) from foodie_fi.subscriptions), 1) as churn_percentage
from foodie_fi.subscriptions s2 
where plan_id = 4;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>churned_cust</th>
            <th>churn_percentage</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>307</td>
            <td>30.7</td>
        </tr>
    </tbody>
</table>



307 customer have churned which is 30.7% of all customers

#### 5. How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?


```sql
%%sql

with churned_customers as (select
	customer_id,
	plan_id,
	start_date,
	lead(start_date) over (partition by customer_id order by customer_id) as churn_date
from foodie_fi.subscriptions s
where (plan_id = 0 or plan_id = 4) and customer_id in
(select customer_id
from foodie_fi.subscriptions s
where (plan_id = 0 or plan_id = 4)
group by customer_id 
having sum(case when plan_id = 0 then 0 else 1 end) >0))
select
	count(customer_id) as churned_customers,
	count(customer_id) / (select count(distinct customer_id)::decimal from foodie_fi.subscriptions s2) * 100 as percentage_customers
from churned_customers
where churn_date is not null and (churn_date - start_date) = 7;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>churned_customers</th>
            <th>percentage_customers</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>92</td>
            <td>9.20000000000000000000</td>
        </tr>
    </tbody>
</table>



92 customers or 9.2%

#### 6. What is the number and percentage of customer plans after their initial free trial?


```sql
%%sql

with next_cust_plan as (select
	customer_id,
	plan_id,
	start_date,
	lead(plan_id) over(partition by customer_id order by start_date) as next_plan
from foodie_fi.subscriptions s
order by customer_id, start_date)
select
	next_plan,
	count(next_plan) as number_of_plans,
	count(next_plan)::decimal / (select count(distinct customer_id) from foodie_fi.subscriptions) *100 as percentage 
from next_cust_plan
where plan_id = 0
group by next_plan
order by next_plan;
```

     * postgresql://postgres:***@localhost:5432/master
    4 rows affected.
    




<table>
    <thead>
        <tr>
            <th>next_plan</th>
            <th>number_of_plans</th>
            <th>percentage</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>546</td>
            <td>54.60000000000000000000</td>
        </tr>
        <tr>
            <td>2</td>
            <td>325</td>
            <td>32.50000000000000000000</td>
        </tr>
        <tr>
            <td>3</td>
            <td>37</td>
            <td>3.70000000000000000000</td>
        </tr>
        <tr>
            <td>4</td>
            <td>92</td>
            <td>9.20000000000000000000</td>
        </tr>
    </tbody>
</table>



#### 7. What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?


```sql
%%sql

with lastest_plan as (select
	customer_id,
	plan_id,
	start_date,
	row_number() over(partition by customer_id order by start_date desc) as latest_plan
from foodie_fi.subscriptions s
where start_date <= '2020-12-31')
select
	plan_name,
	count(customer_id),
	100*count(customer_id)::decimal / (select count(distinct customer_id) from foodie_fi.subscriptions) as percentage
from lastest_plan
inner join foodie_fi."plans" p using(plan_id)
where latest_plan = 1
group by plan_name;
```

     * postgresql://postgres:***@localhost:5432/master
    5 rows affected.
    




<table>
    <thead>
        <tr>
            <th>plan_name</th>
            <th>count</th>
            <th>percentage</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>basic monthly</td>
            <td>224</td>
            <td>22.4000000000000000</td>
        </tr>
        <tr>
            <td>churn</td>
            <td>236</td>
            <td>23.6000000000000000</td>
        </tr>
        <tr>
            <td>pro annual</td>
            <td>195</td>
            <td>19.5000000000000000</td>
        </tr>
        <tr>
            <td>pro monthly</td>
            <td>326</td>
            <td>32.6000000000000000</td>
        </tr>
        <tr>
            <td>trial</td>
            <td>19</td>
            <td>1.9000000000000000</td>
        </tr>
    </tbody>
</table>



#### 8. How many customers have upgraded to an annual plan in 2020?


```sql
%%sql

select count(*)
from foodie_fi.subscriptions s
where date_part('year', start_date) = 2020 and plan_id = 3;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>195</td>
        </tr>
    </tbody>
</table>



195 customer have upgraded their plan in 2020 to annual

#### 9. How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?


```sql
%%sql

with annual_plan as (select
	customer_id,
	plan_id,
	start_date,
	min(start_date) over (partition by customer_id) as joined_day
from foodie_fi.subscriptions s
where customer_id in (select customer_id from foodie_fi.subscriptions where plan_id = 3))
select
	ceil(avg(start_date - joined_day)) as average_to_join
from annual_plan
where plan_id = 3;
```

     * postgresql://postgres:***@localhost:5432/master
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>average_to_join</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>105</td>
        </tr>
    </tbody>
</table>



105 days on average take our customers to join the annual plan

#### 10. Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)


```sql
%%sql

with bins as 
(select
	width_bucket(time_till_upgrade, 0, 365, 12) as avg_days_till_upgrade
from
(select customer_id, start_date - joined_day as time_till_upgrade from
(select customer_id, plan_id, start_date, min(start_date) over (partition by customer_id) as joined_day
from foodie_fi.subscriptions where customer_id in (select customer_id from foodie_fi.subscriptions where plan_id = 3)) sub
where plan_id = 3) subs)
select
	((avg_days_till_upgrade - 1) * 30 || ' - ' || avg_days_till_upgrade *30 || ' days') as bucker,
	count(*) as number_of_customer
from bins
group by avg_days_till_upgrade
order by avg_days_till_upgrade;

-- How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
with downgrade_table as (select
	customer_id,
	plan_id,
	start_date,
	coalesce(lead(plan_id) over(partition by customer_id order by start_date),0) as next_plan
from foodie_fi.subscriptions s
where date_part('year', start_date) = 2020 and plan_id in(1,2))
select count(*)
from downgrade_table
where plan_id = 2 and next_plan = 1;
```

     * postgresql://postgres:***@localhost:5432/master
    12 rows affected.
    1 rows affected.
    




<table>
    <thead>
        <tr>
            <th>count</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
        </tr>
    </tbody>
</table>



There are no customer who made a downgrade
