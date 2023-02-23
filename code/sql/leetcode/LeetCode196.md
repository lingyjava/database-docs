# [LeetCode196.删除重复的Email](https://leetcode.cn/problems/delete-duplicate-emails/)

```text
表Person
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
id是该表的主键列。
该表的每一行包含一封电子邮件。电子邮件将不包含大写字母。
```

要求：编写一个 SQL 删除语句来 删除 所有重复的电子邮件，只保留一个id最小的唯一电子邮件。 以 任意顺序 返回结果表。

```text
输入:
Person 表:
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
输出:
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

## MySQL

### selfJoin

```sql
DELETE p1
FROM person p1, person p2
WHERE p1.email = p2.email AND p1.id > p2.id
;
```
