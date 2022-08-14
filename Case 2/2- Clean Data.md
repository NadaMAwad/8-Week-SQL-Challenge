## Data type check for customer_orders column
```sql
SELECT
  table_name,
  column_name,
  data_type
FROM information_schema.columns
WHERE table_name = 'customer_orders';
```
**Result:**
| table\_name      | column\_name | data\_type                  |
| ---------------- | ------------ | --------------------------- |
| customer\_orders | order\_id    | int                   |
| customer\_orders | customer\_id | int                   |
| customer\_orders | pizza\_id    | int                   |
| customer\_orders | exclusions   | varchar          |
| customer\_orders | extras       | varchar           |
| customer\_orders | order\_time  | time |

## Data type check for runner_orders column
```sql
SELECT
  table_name,
  column_name,
  data_type
FROM information_schema.columns
WHERE table_name = 'runner_orders';
```
**Result:**
| table\_name    | column\_name | data\_type        |
| -------------- | ------------ | ----------------- |
| runner\_orders | order\_id    | int          |
| runner\_orders | runner\_id   | int           |
| runner\_orders | pickup\_time | varchar |
| runner\_orders | distance     | varchar |
| runner\_orders | duration     | varchar |
| runner\_orders | cancellation | varchar |

## Tables check
```sql
SELECT * FROM customer_orders
SELECT * FROM runner_orders
```
**Result:**

| order\_id | customer\_id | pizza\_id | exlcusions | extras | order\_time              |
| --------- | ------------ | --------- | ---------- | ----- | ------------------------ |
| 1         | 101          | 1         |            |       | 2021-01-01T18:05:02.000Z |
| 2         | 101          | 1         |            |       | 2021-01-01T19:00:52.000Z |
| 3         | 102          | 1         |            |       | 2021-01-02T23:51:23.000Z |
| 3         | 102          | 2         |            |NULL   | 2021-01-02T23:51:23.000Z |
| 4         | 103          | 1         | 4          |       | 2021-01-04T13:23:46.000Z |
| 4         | 103          | 1         | 4          |       | 2021-01-04T13:23:46.000Z |
| 4         | 103          | 2         | 4          |       | 2021-01-04T13:23:46.000Z |
| 5         | 104          | 1         |null        | 1     | 2021-01-08T21:00:29.000Z |
| 6         | 101          | 2         |null        |null   | 2021-01-08T21:03:13.000Z |
| 7         | 105          | 2         |null        | 1     | 2021-01-08T21:20:29.000Z |
| 8         | 102          | 1         |null        |null   | 2021-01-09T23:54:33.000Z |
| 9         | 103          | 1         | 4          | 1, 5  | 2021-01-10T11:22:59.000Z |
| 10        | 104          | 1         |null        |null   | 2021-01-11T18:34:49.000Z |
| 10        | 104          | 1         | 2, 6       | 1, 4  | 2021-01-11T18:34:49.000Z |

| order\_id | runner\_id | pickup\_time             | distance | duration | cancellation            |
| --------- | ---------- | ------------------------ | -------- | -------- | ----------------------- |
| 1         | 1          | 2021-01-01T18:15:34.000Z | 20km     | 32 minutes|                         |
| 2         | 1          | 2021-01-01T19:10:54.000Z | 20km     | 27 minutes|                         |
| 3         | 1          | 2021-01-03T00:12:37.000Z | 13.4km   | 20 mins  |  NULL                       |
| 4         | 2          | 2021-01-04T13:53:03.000Z | 23.4     | 40       |  NULL                       |
| 5         | 3          | 2021-01-08T21:10:57.000Z | 10       | 15       |  NULL                       |
| 6         | 3          |                          |null      |null      | Restaurant Cancellation |
| 7         | 2          | 2021-01-08T21:30:45.000Z | 25km     | 25mins   |   null                      |
| 8         | 2          | 2021-01-10T00:15:02.000Z | 23.4 km  | 15 minute|       null                  |
| 9         | 2          |                          |null      |null      | Customer Cancellation   |
| 10        | 1          | 2021-01-11T18:50:20.000Z | 10       | 10minutes|        null                 |


## Start cleaning and Create Temp tables 

-- 1. customer_order (remove null):

```sql
SELECT order_id, customer_id, pizza_id, 
CASE
	WHEN exclusions IS null OR exclusions LIKE 'null' THEN ' '
	ELSE exclusions
	END AS exclusions,
CASE
	WHEN extras IS NULL or extras LIKE 'null' THEN ' '
	ELSE extras
	END AS extras,
	order_time
INTO #customer_orders
FROM customer_orders
```
**New Table Result:**
| order\_id | customer\_id | pizza\_id | exlcusions | extras | order\_time              |
| --------- | ------------ | --------- | ---------- | ----- | ------------------------ |
| 1         | 101          | 1         |            |       | 2021-01-01T18:05:02.000Z |
| 2         | 101          | 1         |            |       | 2021-01-01T19:00:52.000Z |
| 3         | 102          | 1         |            |       | 2021-01-02T23:51:23.000Z |
| 3         | 102          | 2         |            |       | 2021-01-02T23:51:23.000Z |
| 4         | 103          | 1         | 4          |       | 2021-01-04T13:23:46.000Z |
| 4         | 103          | 1         | 4          |       | 2021-01-04T13:23:46.000Z |
| 4         | 103          | 2         | 4          |       | 2021-01-04T13:23:46.000Z |
| 5         | 104          | 1         |            | 1     | 2021-01-08T21:00:29.000Z |
| 6         | 101          | 2         |            |       | 2021-01-08T21:03:13.000Z |
| 7         | 105          | 2         |            | 1     | 2021-01-08T21:20:29.000Z |
| 8         | 102          | 1         |            |       | 2021-01-09T23:54:33.000Z |
| 9         | 103          | 1         | 4          | 1, 5  | 2021-01-10T11:22:59.000Z |
| 10        | 104          | 1         |            |       | 2021-01-11T18:34:49.000Z |
| 10        | 104          | 1         | 2, 6       | 1, 4  | 2021-01-11T18:34:49.000Z |


-- 2. runner_order:
  - remove nulls.
  - trim strings in distance & duration.

``` sql
SELECT order_id, runner_id,  
CASE
	WHEN pickup_time LIKE 'null' THEN ' '
	ELSE pickup_time
	END AS pickup_time,
CASE
	WHEN distance LIKE 'null' THEN ' '
	WHEN distance LIKE '%km' THEN TRIM('km' from distance)
	ELSE distance
	END AS distance,
CASE
	WHEN duration LIKE 'null' THEN ' '
	WHEN duration LIKE '%mins' THEN TRIM('mins' from duration)
	WHEN duration LIKE '%minute' THEN TRIM('minute' from duration)
	WHEN duration LIKE '%minutes' THEN TRIM('minutes' from duration)
	ELSE duration
	END AS duration,
CASE
	WHEN cancellation IS NULL or cancellation LIKE 'null' THEN ' '
	ELSE cancellation
	END AS cancellation
INTO #runner_orders
FROM runner_orders
```
**New Table Result:**
| order\_id | runner\_id | pickup\_time             | distance | duration | cancellation            |
| --------- | ---------- | ------------------------ | -------- | -------- | ----------------------- |
| 1         | 1          | 2021-01-01T18:15:34.000Z | 20       | 32       |                         |
| 2         | 1          | 2021-01-01T19:10:54.000Z | 20       | 27       |                         |
| 3         | 1          | 2021-01-03T00:12:37.000Z | 13.4     | 20       |                         |
| 4         | 2          | 2021-01-04T13:53:03.000Z | 23.4     | 40       |                         |
| 5         | 3          | 2021-01-08T21:10:57.000Z | 10       | 15       |                         |
| 6         | 3          |                          |          |          | Restaurant Cancellation |
| 7         | 2          | 2021-01-08T21:30:45.000Z | 25       | 25       |                         |
| 8         | 2          | 2021-01-10T00:15:02.000Z | 23.4     | 15       |                         |
| 9         | 2          |                          |          |          | Customer Cancellation   |
| 10        | 1          | 2021-01-11T18:50:20.000Z | 10       | 10       |                         |
  - convert data types
```sql
ALTER TABLE #runner_orders
ALTER COLUMN pickup_time DATETIME

ALTER TABLE #runner_orders
ALTER COLUMN distance FLOAT

ALTER TABLE #runner_orders
ALTER COLUMN duration INT
```
