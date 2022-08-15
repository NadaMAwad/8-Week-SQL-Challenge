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

```



```sql
```
