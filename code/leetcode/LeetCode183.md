# [LeetCode183. 从不订购的客户](https://leetcode.cn/problems/customers-who-never-order/)

```text
Customers 表：
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
Orders 表：
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

要求：编写一个 SQL 查询，找出所有从不订购任何东西的客户。

```text
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

## MySQL

## AS + NOT IN + subQuery

```sql
SELECT customers.name AS Customers
FROM customers
WHERE id NOT IN (
    SELECT orders.customerId FROM orders
)
;
```

## LEFT JOIN
```sql
SELECT customers.name AS Customers
FROM customers LEFT JOIN orders ON customers.id = orders.CustomerId
WHERE orders.customerId IS NULL
;
```

```text
思路
1、两表左连接，以customers为主查询出主表中所有数据，右表不存在则字段为空，这时数据为
+--------------+-----------------+-----------+-------------------+
| customers.Id | customers.Name  | Orders.Id | Orders.CustomerId |
+--------------+-----------------+-----------+-------------------+
|      1       |   Joe           |     2     |          1        |
|      2       |   Henry         |    NULL   |         NULL      |
|      3       |   Sam           |     1     |          3        |
|      4       |   Max           |    NULL   |         NULL      |
+--------------+-----------------+-----------+-------------------+

2、排除ordersId未连接上的数据。
```