# LeetCode 584.寻找用户推荐人
[题目来源](https://leetcode.cn/problems/find-customer-referee/)

SQL架构：
```sql
Create table If Not Exists Customer (id int, name varchar(25), referee_id int)
Truncate table Customer
insert into Customer (id, name, referee_id) values ('1', 'Will', 'None')
insert into Customer (id, name, referee_id) values ('2', 'Jane', 'None')
insert into Customer (id, name, referee_id) values ('3', 'Alex', '2')
insert into Customer (id, name, referee_id) values ('4', 'Bill', 'None')
insert into Customer (id, name, referee_id) values ('5', 'Zack', '1')
insert into Customer (id, name, referee_id) values ('6', 'Mark', '2')
```
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

输出示例：
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

## OR + IS NULL

MySQL：
```sql
SELECT name
FROM customer
WHERE referee_id <> 2 OR referee_id IS NULL;
;
```