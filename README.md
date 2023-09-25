# Advanced_SQL_Quires

### Question 1 - Maximum Transaction each day 
SQL query to report the IDs of the transactions with the maximum amount on their respective day. If in one day there are multiple 
such transaction, return all of them. 

~~~
use que1 ;

select * from transactions ;

transaction_id       day               amount
8	            2021-04-03 15:57:28	57
9	            2021-04-28 08:47:25	21
1	            2021-04-29 13:28:30	58
5	            2021-04-28 16:39:59	40
6	            2021-04-29 23:39:28	58


with max_transaction as (
select day,transaction_id, amount, rank() over(partition by date(day) order by amount desc ) as ranks
      from transactions
)

# outside query

select transaction_id from max_transaction
where ranks = 1 
order by transaction_id;

# return maximum transaction each day 
transaction_id  amount
1	           58
5	           40
6	           58
8	           57

 ~~~

____

### Question 2 - Orders with maximum quantity 
SQL query to find the order_id of all imbalanced orders. 
An imbalanced order is one whose maximum quantity is strictly greater than the average quantity of every order (including it self).
The AVG quantity of an order is calculated as (total quantity of all products in the order / number of different products in the order) 
The MAX quantity of an order is the highest quantity of any single product in the order . 
~~~
use que2 ; 
select * from ordersdetails ;

order_id	product_id	quantity
1	            1	12
1	            2	10
1	            3	15
2	            1	8
2	            4	4
2	            5	6
3	            3	5
3	            4	18
4	            5	2
4	            6	8
5	            7	9
5	            8	9
3	            9	20
2	            9	4


with quantity_above_avg as (
select order_id, product_id, quantity, avg(quantity) over(partition by order_id) as avg_quantity, 
max(quantity) over(partition by order_id) as max_quantity from ordersdetails 
)

select distinct order_id from quantity_above_avg 
where max_quantity > (select avg (avg_quantity) from quantity_above_avg  ) ;

order_id
1
3

~~~
____

### Question 3 - The most frequently ordered products for each customer. 
SQL query to find the most frequently ordered products for each customer. 

- Table I   : Customers - table that contains information about the customer 
- Table II  : Orders - contains information about the orders made by customer_id 
- Table III : Products - This table contains information about the products 

~~~
use que3;

select * from orders ; 
select * from customers; 
select * from products ; 

create view join_tables as (
select orders.customer_id, orders.product_id, products.product_name,
count(products.product_name) as nr_products_sold
from orders 
join customers  using (customer_id)
join products using (product_id)
group by  orders.customer_id, orders.product_id, products.product_name
);

select * from join_tables ; 

with frequent_table as (
select customer_id, product_id, product_name, 
rank() over(partition by customer_id
order by nr_products_sold desc) as rank_p 
 from join_tables
)

select customer_id, product_id, product_name 
from frequent_Table
where rank_p = 1 
order by customer_id, product_id;

product_id      	product_name	price
1	            keyboard	      120
2	            mouse            	80
3	            screen	      600
4	            hard disk	      450

~~~

___

### Qustion 4 - Leetflex Banned Accounts 
SQL query to find the account_id of the accounts that should be banned from the leetflex. 
An account should be banned if it was logged in at the same moment from two different IP addresses. 

~~~
use que4 ; 
select * from loginfo;

account_id      	ip_address	      login	                  logout
1	                  1	2021-02-01 09:00:00	2021-02-01 09:30:00
1	                  2	2021-02-01 08:00:00	2021-02-01 11:30:00
2	                  6	2021-02-01 20:30:00	2021-02-01 22:00:00
2	                  7	2021-02-02 20:30:00	2021-02-02 22:00:00
3	                  9	2021-02-01 16:00:00	2021-02-01 16:59:59
3	                  13	2021-02-01 17:00:00	2021-02-01 17:59:59
4	                  10	2021-02-01 16:00:00	2021-02-01 17:00:00
4	                  11	2021-02-01 17:00:00	2021-02-01 17:59:59

with 
t1 as (select account_id, ip_address, login from loginfo ),
t2 as (select account_id as id, ip_address, login, logout from loginfo)

select distinct account_id from t1 join t2
where (t1.login between t2.login and t2.logout) and (t1.account_id = t2.id) and (t1.ip_address != t2.ip_address) ;

account_id
1
4

~~~
___

### Question 5 - Countries you can safely invest in 
SQL Query to find the countries where the company can invest. 
The company intends to invest in the countries where the average call duration of the calls in this country is strictly greater than 
the global average call duration. 


- Table I   : Person  - Each row of this table contains the name of the person and their phone number
- Table II  : Country - The table contains the country name and its code 
- Table III : Calls   - There is no primary key for this table , it may contain duplicates. It contain the caller_id, callee_id and the duration of the call 
                      in minutes. Note: caller_id != callee_id

                      
~~~
use que5 ; 
select * from calls; 
select * from person ; 
select * from country ; 


create view  
join_t as
select p.name, p.phone_number, c2.duration
from person as p 
join calls c on c.caller_id = p.id
join calls c2 on c2.callee_id = p.id;

select * from join_t;

name      	phone_number        duration
Rachel	972-0011100	            33
Rachel	972-0011100	            4
Maroua	212-6523651	            59
Elvis	      051-7654321	            102
Elvis	      051-7654321            	330
Jonathan	051-1234567            	5
Jonathan	051-1234567            	5
Rachel	972-0011100            	13
Moncef	212-1234567	            3
Moncef	212-1234567	            3
Moncef	212-1234567	            3
Meir	      972-1234567	            1
Meir	      972-1234567            	1
Meir	      972-1234567            	7
Meir      	972-1234567	            7

with country_table as (
select name, phone_number, duration, 
 case when left(phone_number, 3) = 051 then "Peru"
      when left(phone_number, 3) = 972 then "Israel"
      when left(phone_number, 3) = 212 then "Morocco"
      when left(phone_number, 3) = 972 then "Germany"
      else "Ethiopia" End country_dist
      from join_t )

select country_dist 
from  country_table
group by country_dist 
having avg(duration) > (select avg(duration) from country_table);


country_dist
Peru
~~~






