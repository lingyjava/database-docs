# [LeetCode1729.求关注者的数量](https://leetcode.cn/problems/find-followers-count/)

```text
表：Followers
+-------------+------+
| Column Name | Type |
+-------------+------+
| user_id     | int  |
| follower_id | int  |
+-------------+------+
(user_id, follower_id) 是这个表的主键。
该表包含一个关注关系中关注者和用户的编号，其中关注者关注用户。

写出 SQL 语句，对于每一个用户，返回该用户的关注者数量。
按user_id的顺序返回结果表。

查询结果的格式如下示例所示。
示例 ：
输入：
Followers 表：
+---------+-------------+
| user_id | follower_id |
+---------+-------------+
| 0       | 1           |
| 1       | 0           |
| 2       | 0           |
| 2       | 1           |
+---------+-------------+
输出：
+---------+----------------+
| user_id | followers_count|
+---------+----------------+
| 0       | 1              |
| 1       | 1              |
| 2       | 2              |
+---------+----------------+
```

## MySQL

### GROUP BY + COUNT
```mysql
SELECT user_id , COUNT(follower_id) AS followers_count
FROM followers 
GROUP BY user_id
ORDER BY user_id ASC;
```