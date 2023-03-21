# [LeetCode 584.寻找用户推荐人](https://leetcode.cn/problems/find-customer-referee/)

```text
+------+------+-----------+
| id   | name | referee_id|
+------+------+-----------+
|    1 | Will |      NULL |
|    2 | Jane |      NULL |
|    3 | Alex |         2 |
|    4 | Bill |      NULL |
|    5 | Zack |         1 |
|    6 | Mark |         2 |
+------+------+-----------+
表 customer ，里面保存了所有客户信息和他们的推荐人。
```

要求：写一个查询语句，返回一个客户列表，列表中客户的推荐人的编号都 不是 2。

```text
+------+
| name |
+------+
| Will |
| Jane |
| Bill |
| Zack |
+------+
```

## MySQL

### OR + IS NULL

```sql
-- MySQL
SELECT name
FROM customer
WHERE referee_id <> 2 OR referee_id IS NULL;
;
```