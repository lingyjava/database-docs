# [LeetCode586.订单最多的客户](https://leetcode.cn/problems/customer-placing-the-largest-number-of-orders/)

```text
表:Orders
+-----------------+----------+
| Column Name     | Type     |
+-----------------+----------+
| order_number    | int      |
| customer_number | int      |
+-----------------+----------+
Order_number是该表的主键。
此表包含关于订单ID和客户ID的信息。

编写一个SQL查询，为下了 最多订单 的客户查找 customer_number 。
测试用例生成后， 恰好有一个客户 比任何其他客户下了更多的订单。

查询结果格式如下所示。
示例 :
输入: 
Orders 表:
+--------------+-----------------+
| order_number | customer_number |
+--------------+-----------------+
| 1            | 1               |
| 2            | 2               |
| 3            | 3               |
| 4            | 3               |
+--------------+-----------------+
输出: 
+-----------------+
| customer_number |
+-----------------+
| 3               |
+-----------------+
```

## MySQL

### GROUP BY + COUNT + LIMIT
```mysql
SELECT customer_number 
FROM orders 
GROUP BY customer_number
ORDER BY COUNT(order_number) DESC 
LIMIT 1
;
```

### HAVING(解决存在并列问题)
```mysql
SELECT customer_number
FROM orders
GROUP BY customer_number
HAVING COUNT(order_number) = (
    SELECT COUNT(order_number)
    FROM orders
    GROUP BY customer_number
    ORDER BY COUNT(order_number) DESC
    LIMIT 1
);
```