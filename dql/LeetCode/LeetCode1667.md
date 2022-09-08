# LeetCode1667.[修复表中的名字](https://leetcode.cn/problems/fix-names-in-a-table/)

SQL架构：
<details><summary>SQL</summary>

```text
Create table If Not Exists Users (user_id int, name varchar(40))
Truncate table Users
insert into Users (user_id, name) values ('1', 'aLice')
insert into Users (user_id, name) values ('2', 'bOB')
```

</details>

```text
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| name           | varchar |
+----------------+---------+
user_id 是该表的主键。
该表包含用户的 ID 和名字。名字仅由小写和大写字符组成。
```

要求：编写一个 SQL 查询来修复名字，使得只有第一个字符是大写的，其余都是小写的。 返回按 user_id 排序的结果表。

<details><summary>返回示例：</summary>

```text
输入：
Users table:
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | aLice |
| 2       | bOB   |
+---------+-------+
输出：
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | Alice |
| 2       | Bob   |
+---------+-------+
```

</details>

## CONCAT + SUBSTR + UPPER + LOWER + LENGTH
```sql
SELECT user_id, CONCAT(UPPER(SUBSTR(name,1,1)), LOWER(SUBSTR(name,2,LENGTH(name) - 1))) AS name
FROM users
ORDER BY user_id ASC
;
```

## CONCAT + SUBSTR + UPPER + LOWER + LENGTH + LEFT
```sql
SELECT user_id, CONCAT(UPPER(LEFT(name,1)), LOWER(SUBSTR(name,2))) AS name
FROM users
ORDER BY user_id ASC
;
```

## 字符串函数
- CONCAT 用来拼接字符串
- LEFT 从左边截取字符
- RIGHT 从右边截取字符
- UPPER 变为大写
- LOWER 变为小写
- LENGTH 获取字符串长度