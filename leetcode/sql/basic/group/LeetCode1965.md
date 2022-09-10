# [LeetCode1965.丢失信息的雇员](https://leetcode.cn/problems/employees-with-missing-information/)

```text
表: Employees
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
+-------------+---------+
employee_id 是这个表的主键。
每一行表示雇员的id 和他的姓名。

表: Salaries
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| salary      | int     |
+-------------+---------+
employee_id is 这个表的主键。
每一行表示雇员的id 和他的薪水。
```

要求：写出一个查询语句，找到所有 丢失信息 的雇员id，返回这些雇员的id employee_id，从小到大排序。
当满足下面一个条件时，就被认为是雇员的信息丢失：
- 雇员的 姓名 丢失了，
- 雇员的 薪水信息 丢失了，

## MySQL

### UNION ALL

```sql
SELECT employee_id
FROM
    (
        SELECT employee_id
        FROM employees
        WHERE name IS NOT NULL
        UNION ALL
        SELECT employee_id
        FROM salaries
        WHERE salary IS NOT NULL
    ) AS t
GROUP BY employee_id
HAVING COUNT(employee_id) = 1
ORDER BY employee_id ASC
;
-- 1、查询各表中不为空的雇员ID。
-- 2、将雇员ID分组计算,若数量不为1则说明有表中确实了该雇员ID
```

### subQuery + UNION
```sql
SELECT employee_id
FROM salaries
WHERE employee_id NOT IN (SELECT employee_id FROM employees WHERE name IS NOT NULL)
UNION
SELECT employee_id
FROM employees
WHERE employee_id NOT IN (SELECT employee_id FROM salaries WHERE salary IS NOT NULL)
ORDER BY employee_id ASC
;
-- 1、查询salaries表中存在,但employees表中不存在的employee_id.
-- 2、查询employees表中存在,但salaries表中不存在的employee_id.
-- 3、连接两个查询数据。
-- 可扩展性低
```

### WITH + UNION ALL

```sql
-- MySQL
WITH tmp AS (
    SELECT employee_id
    FROM employees
    WHERE name IS NOT NULL
    UNION ALL
    SELECT employee_id
    FROM salaries
    WHERE salary IS NOT NULL
)
SELECT employee_id
FROM tmp
GROUP BY employee_id
HAVING COUNT(employee_id) = 1
ORDER BY employee_id
;
```

### LEFT JOIN + RIGHT JOIN

```sql
SELECT employee_id
FROM (
         SELECT e.employee_id employee_id FROM employees e LEFT JOIN salaries s ON e.employee_id = s.employee_id WHERE s.salary IS NULL
         UNION ALL
         SELECT s.employee_id employee_id FROM employees e RIGHT JOIN salaries s ON e.employee_id = s.employee_id WHERE e.name IS NULL
     ) AS tmp
ORDER BY employee_id
;
```

### NOT EXISTS + subQuery + UNION ALL

```sql
-- MySQL
SELECT e.employee_id employee_id
FROM employees e
WHERE NOT EXISTS (
        SELECT s.employee_id
        FROM salaries s
        WHERE e.employee_id = s.employee_id AND s.salary IS NOT NULL)
UNION ALL
SELECT s.employee_id employee_id
FROM salaries s
WHERE NOT EXISTS (
        SELECT employee_id
        FROM employees e
        WHERE e.employee_id = s.employee_id AND e.name IS NOT NULL)
ORDER BY employee_id ASC
;
```
