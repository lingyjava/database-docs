# [排名靠前的旅行者](https://leetcode.cn/problems/top-travellers/)

```text
表：Users
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
+---------------+---------+
id 是该表单主键。
name 是用户名字。

表：Rides
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| user_id       | int     |
| distance      | int     |
+---------------+---------+
id 是该表单主键。
user_id 是本次行程的用户的 id, 而该用户此次行程距离为 distance 。

写一段 SQL ,报告每个用户的旅行距离。
返回的结果表单，以travelled_distance降序排列 ，如果有两个或者更多的用户旅行了相同的距离,那么再以name升序排列 。

查询结果格式如下例所示。
Users 表：
+------+-----------+
| id   | name      |
+------+-----------+
| 1    | Alice     |
| 2    | Bob       |
| 3    | Alex      |
| 4    | Donald    |
| 7    | Lee       |
| 13   | Jonathan  |
| 19   | Elvis     |
+------+-----------+

Rides 表：
+------+----------+----------+
| id   | user_id  | distance |
+------+----------+----------+
| 1    | 1        | 120      |
| 2    | 2        | 317      |
| 3    | 3        | 222      |
| 4    | 7        | 100      |
| 5    | 13       | 312      |
| 6    | 19       | 50       |
| 7    | 7        | 120      |
| 8    | 19       | 400      |
| 9    | 7        | 230      |
+------+----------+----------+

Result 表：
+----------+--------------------+
| name     | travelled_distance |
+----------+--------------------+
| Elvis    | 450                |
| Lee      | 450                |
| Bob      | 317                |
| Jonathan | 312                |
| Alex     | 222                |
| Alice    | 120                |
| Donald   | 0                  |
+----------+--------------------+
```

## MySQL

### GROUP BY + SUM() + IFNULL() + LEFT JOIN + ORDER BY 

```mysql
SELECT u.name , SUM(IFNULL(r.distance, 0)) AS travelled_distance
FROM users u LEFT JOIN rides r ON u.id = r.user_id
GROUP BY r.user_id
ORDER BY travelled_distance DESC , name ASC;
```