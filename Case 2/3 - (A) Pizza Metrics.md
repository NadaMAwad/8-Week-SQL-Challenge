## 1. How many pizzas were ordered?
```sql
SELECT 
COUNT(order_id) Pizzas_ordered
FROM #customer_orders
```
**Result:**
| pizza\_orders |
| ------------- |
| 14            |

## 2. How many unique customer orders were made?
```sql 
SELECT 
COUNT(DISTINCT order_id)  unique_orders
FROM #customer_orders
```
**Result:**
| order\_count  |
| ------------- |
| 10            |

## 3. How many successful orders were delivered by each runner?
```sql
SELECT
  runner_id,
  COUNT(order_id) AS successful_orders
FROM #runner_orders
WHERE cancellation NOT IN ('Restaurant Cancellation', 'Customer Cancellation')
GROUP BY runner_id
ORDER BY successful_orders DESC
```
**Result:**
| runner\_id | successful\_orders |
| ---------- | ------------------ |
| 1          | 4                  |
| 2          | 3                  |
| 3          | 1                  |

## 4. How many of each type of pizza was delivered?
```sql
SELECT
   pizza_id, COUNT(pizza_id)  delivered_pizza
FROM #customer_orders  c , #runner_orders AS r
WHERE c.order_id = r.order_id
AND cancellation NOT IN ('Restaurant Cancellation', 'Customer Cancellation')
GROUP BY pizza_id
```
 **Result:**
|  pizza\_ID  | delivered\_pizza         |
| ----------- | ------------------------ |
| 1           | 9                        |
| 2           | 3                        |

## 5. How many Vegetarian and Meatlovers were ordered by each customer?

```sql
SELECT
  customer_id,
  SUM(CASE WHEN pizza_id = 1 THEN 1 ELSE 0 END) AS meat_lovers,
  SUM(CASE WHEN pizza_id = 2 THEN 1 ELSE 0 END) AS vegetarian
FROM #customer_orders
GROUP BY customer_id
```
  **Result:**
  | customer\_id | meatlovers | vegetarian |
| ------------ | ---------- | ---------- |
| 101          | 2          | 1          |
| 102          | 2          | 1          |
| 103          | 3          | 1          |
| 104          | 3          | 0          |
| 105          | 0          | 1          |

## 6. What was the maximum number of pizzas delivered in a single order?
```sql
WITH PIZZA_ AS
(
SELECT 
   c.order_id,
   COUNT(c.pizza_id)  pizzas_per_order
FROM #customer_orders  c, #runner_orders  r
WHERE c.order_id = r.order_id
AND cancellation NOT IN ('Restaurant Cancellation', 'Customer Cancellation')
GROUP BY c.order_id
)

SELECT 
    MAX(pizzas_per_order)  max\_count
FROM PIZZA_
```
  **Result:**
| max\_count |
| ---------- |
| 3          |

## 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```sql
```

## 8. How many pizzas were delivered that had both exclusions and extras?
```sql
```

## 9. What was the total volume of pizzas ordered for each hour of the day?
```sql
```

## 10. What was the volume of orders for each day of the week?
```sql
```
