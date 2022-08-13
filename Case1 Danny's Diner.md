# [Case 1](https://8weeksqlchallenge.com/case-study-1/) 

## Create Data Set

````sql
CREATE SCHEMA dannys_diner;

CREATE TABLE sales (
  "customer_id" VARCHAR(1),
  "order_date" DATE,
  "product_id" INTEGER
);

INSERT INTO sales
  ("customer_id", "order_date", "product_id")
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
 

CREATE TABLE menu (
  "product_id" INTEGER,
  "product_name" VARCHAR(5),
  "price" INTEGER
);

INSERT INTO menu
  ("product_id", "product_name", "price")
VALUES
  ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');
  

CREATE TABLE members (
  "customer_id" VARCHAR(1),
  "join_date" DATE
);

INSERT INTO members
  ("customer_id", "join_date")
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
```` 

### 1. What is the total amount each customer spent at the restaurant?

````sql
SELECT 
customer_id, SUM(price) "Total_sales"
FROM sales s, menu m
WHERE s.product_id = m.product_id
GROUP BY customer_id
````
#### Answer:
| Customer_id | Total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |


### 2. How many days has each customer visited the restaurant?
````sql
SELECT 
customer_id, COUNT(DISTINCT order_date) "Times_visited"
FROM sales s
GROUP BY customer_id
````
#### Answer:
| Customer_id | Times_visited |
| ----------- | ----------- |
| A           | 4          |
| B           | 6          |
| C           | 2          |

### 3. What was the first item from the menu purchased by each customer?
````sql
WITH Ranking AS(
  SELECT 
  customer_id, order_date,product_name,
  DENSE_RANK() OVER (PARTITION BY s.customer_id Order by s.order_date) as rank
  FROM sales s, menu m
  WHERE s.product_id = m.product_id)
  
  SELECT 
   customer_id, product_name
   FROM ranking 
   WHERE rank = 1
 ````
 #### Answer:
 | Customer_id | product_name | 
| ----------- | ----------- |
| A           | curry        | 
| A           | sushi        | 
| B           | curry        | 
| C           | ramen        |
| C           | ramen        |

### 4. What is the most purchased item on the menu and how many times was it purchased by all customers?

````sql
SELECT TOP(1)
product_name,
COUNT(S.product_id) "Purchased"
FROM sales s, menu m
WHERE s.product_id = m.product_id
GROUP BY product_name
ORDER BY COUNT(S.product_id) DESC
````
#### Answer:
| Product_name  | Purchased | 
| ----------- | ----------- |
| ramen       | 8|

### 5. Which item was the most popular for each customer?
```` sql
WITH items AS(
SELECT customer_id, product_name ,
COUNT(s.product_id)  "ordered",
dense_rank() over (partition by customer_id  ORDER BY count(s.product_id) DESC)  "rank"
FROM sales s, menu m
WHERE s.product_id = m.product_id
GROUP BY customer_id, product_name)

SELECT 
customer_id, product_name, ordered
FROM items 
WHERE rank = 1
````
#### Answer:
| Customer_id | Product_name | ordered |
| ----------- | ---------- |------------  |
| A           | ramen        |  3   |
| B           | sushi        |  2   |
| B           | curry        |  2   |
| B           | ramen        |  2   |
| C           | ramen        |  3   |

### 6. Which item was purchased first by the customer after they became a member?
```` sql
WITH memb_orders AS(
SELECT
s.customer_id, order_date, join_date, product_id,
row_number() over (partition by s.customer_id ORDER BY order_date) as rank
FROM sales s, members m
WHERE
m.customer_id = s.customer_id
AND order_date >= join_date
)

SELECT 
customer_id, product_name, order_date, join_date
FROM  memb_orders mo, menu m
WHERE m.product_id = mo.product_id
AND rank = 1
````
#### Answer:
| customer_id | product_name | order_date | join_date  |
|-------------|--------------|------------|------------|
| A           | curry        | 2021-01-07 | 2021-01-07 |
| B           | sushi        | 2021-01-11 | 2021-01-09 |

### 7. Which item was purchased just before the customer became a member?

```` sql 
WITH memb_orders AS(
SELECT
s.customer_id, order_date, join_date, product_id,
row_number() over (partition by s.customer_id ORDER BY order_date desc) as rank
FROM sales s, members m
WHERE
m.customer_id = s.customer_id
AND order_date < join_date
)

SELECT 
customer_id, product_name, order_date, join_date
FROM  memb_orders mo, menu m
WHERE m.product_id = mo.product_id
AND rank = 1
````
#### Answer:

| customer_id | product_name | order_date | join_date  |
|-------------|--------------|------------|------------|
| A           | sushi        | 2021-01-01 | 2021-01-07 |
| B           | sushi        | 2021-01-04 | 2021-01-09 |

## 8. What is the total items and amount spent for each member before they became a member?
```sql
SELECT
s.customer_id,
COUNT(s.product_id)  items,
SUM(men.price)  spent
FROM sales s, members m, menu men
WHERE m.customer_id = s.customer_id
AND  men.product_id = s.product_id
AND order_date < join_date
GROUP BY s.customer_id
```
| customer_id | items | spent |
|-------------|-------|-------|
| A           | 2     | 25    |
| B           | 3     | 40    |

## 9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
```sql
SELECT customer_id,
SUM(CASE
WHEN s.product_id = 1 THEN price*20
ELSE price*10 
END) as total_points
FROM sales s, menu m
WHERE m.product_id = s.product_id
GROUP BY customer_id
```
| customer_id | total_points |
|-------------|--------------|
| A           | 860          |
| B           | 940          |
| C           | 360          |

## 10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi
- how many points do customer A and B have at the end of January?
``` sql
WITH Period AS(
SELECT *, 
DATEADD(DAY, 6, join_date) AS valid_date, 
EOMONTH('2021-01-1') AS last_date
FROM members
)
SELECT
s.customer_id,
SUM(CASE
WHEN s.product_id = 1 THEN price*20
WHEN s.order_date between p.join_date and p.valid_date THEN price*20
ELSE price*10 
END) as total_points
FROM
Period p,sales s,menu m
WHERE p.customer_id = s.customer_id
AND m.product_id = s.product_id
AND	s.order_date <= p.last_date
GROUP BY s.customer_id
```
#### Answer:
| Customer_id | Points | 
| ----------- | ---------- |
| A           | 1370 |
| B           | 820 |
