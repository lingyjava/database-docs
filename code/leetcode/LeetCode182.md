# [LeetCode182.查找重复的电子邮箱](https://leetcode.cn/problems/duplicate-emails/solution/)

```text
编写一个 SQL 查询，查找Person 表中所有重复的电子邮箱。

示例：
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+

根据以上输入，你的查询应返回以下结果：
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```

## MySQL

### GROUP BY + HAVING + COUNT

```mysql
SELECT email AS Email
FROM person
GROUP BY email
HAVING COUNT(email) > 1;
```

### 内连接(自连接)

```mysql
SELECT DISTINCT p1.email AS Email
FROM person p1 , person p2
WHERE p1.email = p2.email AND p1.id != p2.id;
```