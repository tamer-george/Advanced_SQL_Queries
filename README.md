# Advanced_SQL_Quires

### Question 1 - Maximum Transaction each day 
SQL query to report the IDs of the transactions with the maximum amount on their respective day. If in one day there are multiple 
such transaction, return all of them. 

____

### Question 2 - Orders with maximum quantity 
SQL query to find the order_id of all imbalanced orders. 
An imbalanced order is one whose maximum quantity is strictly greater than the average quantity of every order (including it self).


____

### Question 3 - The most frequently ordered products for each customer. 
SQL query to find the most frequently ordered products for each customer. 

Table I   : Customers - table that contains information about the customer 
table II  : Orders - contains information about the orders made by customer_id 
table III : Products - This table contains information about the products 


___

### Qustion 4 - Leetflex Banned Accounts 
SQL query to find the account_id of the accounts that should be banned from the leetflex. 
An account should be banned if it was logged in at the same moment from two different IP addresses. 


___

### Question 5 - Countries you can safely invest in 
SQL Query to find the countries where the company can invest. 
The company intends to invest in the countries where the average call duration of the calls in this country is strictly greater than 
the global average call duration. 


Table I   : Person  - Each row of this table contains the name of the person and their phone number
table II  : Country - The table contains the country name and its code 
table III : Calls   - There is no primary key for this table , it may contain duplicates. It contain the caller_id, callee_id and the duration of the call 
                      in minutes. Note: caller_id != callee_id

____                      







