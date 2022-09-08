# LeetCode183. 从不订购的客户
[题目来源](https://leetcode.cn/problems/customers-who-never-order/)

<details><summary>SQL架构</summary>

```sql
Create table If Not Exists Customers (id int, name varchar(255));
Create table If Not Exists Orders (id int, customerId int);
Truncate table Customers;
insert into Customers (id, name) values ('1', 'Joe');
insert into Customers (id, name) values ('2', 'Henry');
insert into Customers (id, name) values ('3', 'Sam');
insert into Customers (id, name) values ('4', 'Max');
Truncate table Orders;
insert into Orders (id, customerId) values ('1', '3');
insert into Orders (id, customerId) values ('2', '1');
```

</details>

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

<details><summary>返回示例</summary>

```text
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

</details>

要求：编写一个 SQL 查询，找出所有从不订购任何东西的客户。

## AS + NOT IN + subQuery
```sql
# MySQL
SELECT customers.name AS Customers
FROM customers
WHERE id NOT IN (
    SELECT orders.customerId FROM orders
);
```

## LEFT JOIN
```sql
# MySQL
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