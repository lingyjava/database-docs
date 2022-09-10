# [LeetCode627.变更性别](https://leetcode.cn/problems/swap-salary/)

```text
Salary 表：
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| id          | int      |
| name        | varchar  |
| sex         | ENUM     |
| salary      | int      |
+-------------+----------+
id 是这个表的主键。
sex 这一列的值是 ENUM 类型，只能从 ('m', 'f') 中取。
本表包含公司雇员的信息。
```

要求：
编写一个 SQL 查询来交换所有的 'f' 和 'm' （即，将所有 'f' 变为 'm' ，反之亦然）
- 不产生中间临时表。
- 必须仅使用一条 update 语句，且 不能 使用 select 语句。

## MySQL

### IF
```sql
UPDATE salary
SET sex = IF (sex = 'm','f','m'); 
```

### CASE
```sql
UPDATE salary
SET sex =
        (
            CASE
                WHEN sex = 'm' THEN 'f'
                WHEN sex = 'f' THEN 'm'
                END
            )
;
```

### CASE ELSE 
```sql
UPDATE salary
SET sex = 
(
    CASE sex
    WHEN 'm' THEN 'f'
    ELSE 'm'
    END
)
;
```

### ASCII
```sql
UPDATE salary
SET sex = CHAR(ASCII('m') + ASCII('f') - ASCII(sex))
;
```