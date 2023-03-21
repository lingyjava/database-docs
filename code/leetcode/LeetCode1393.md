# [Leetcode1393.股票的资本损益](https://leetcode.cn/problems/capital-gainloss/solution/)

```text
Stocks表：
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| stock_name    | varchar |
| operation     | enum    |
| operation_day | int     |
| price         | int     |
+---------------+---------+
(stock_name, day) 是这张表的主键
operation 列使用的是一种枚举类型，包括：('Sell','Buy')
此表的每一行代表了名为 stock_name 的某支股票在 operation_day 这一天的操作价格。
保证股票的每次'Sell'操作前，都有相应的'Buy'操作。

编写一个SQL查询来报告每支股票的资本损益。
股票的资本损益是一次或多次买卖股票后的全部收益或损失。
以任意顺序返回结果即可。

SQL查询结果的格式如下例所示：
Stocks 表:
+---------------+-----------+---------------+--------+
| stock_name    | operation | operation_day | price  |
+---------------+-----------+---------------+--------+
| Leetcode      | Buy       | 1             | 1000   |
| Corona Masks  | Buy       | 2             | 10     |
| Leetcode      | Sell      | 5             | 9000   |
| Handbags      | Buy       | 17            | 30000  |
| Corona Masks  | Sell      | 3             | 1010   |
| Corona Masks  | Buy       | 4             | 1000   |
| Corona Masks  | Sell      | 5             | 500    |
| Corona Masks  | Buy       | 6             | 1000   |
| Handbags      | Sell      | 29            | 7000   |
| Corona Masks  | Sell      | 10            | 10000  |
+---------------+-----------+---------------+--------+

Result 表:
+---------------+-------------------+
| stock_name    | capital_gain_loss |
+---------------+-------------------+
| Corona Masks  | 9500              |
| Leetcode      | 8000              |
| Handbags      | -23000            |
+---------------+-------------------+
```

## MySQL

### SUM() + subQuery + GROUP BY

```mysql
# 总卖出 - 总买入
SELECT b.stock_name , (s.sumSell - b.sumBuy) AS capital_gain_loss
FROM (
    SELECT stock_name , SUM(price) AS sumBuy
    FROM stocks
    WHERE operation = 'Buy'
    GROUP BY stock_name
) AS b INNER JOIN (
    SELECT stock_name , SUM(price) AS sumSell
    FROM stocks 
    WHERE operation = 'Sell'
    GROUP BY stock_name
) AS s 
ON s.stock_name = b.stock_name ;
```

### SUM() + IF() + GROUP BY

```mysql
SELECT stock_name , SUM(IF(operation = 'Buy', -price , price) ) AS capital_gain_loss
FROM stocks
GROUP BY stock_name;
```