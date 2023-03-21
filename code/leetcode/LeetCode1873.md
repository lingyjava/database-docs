# [LeetCode1873 计算特殊奖金](https://leetcode.cn/problems/calculate-special-bonus/)

```text
+-------------+---------+
| 列名        | 类型     |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
| salary      | int     |
+-------------+---------+
employee_id 是这个表的主键。
此表的每一行给出了雇员id ，名字和薪水。
```

要求：写出一个SQL 查询语句，计算每个雇员的奖金。
- 如果一个雇员的id是奇数并且他的名字不是以'M'开头， 那么他的奖金是他工资的100%，否则奖金为0。
- 返回的结果集请按照employee_id排序。

```text
输入：
Employees 表:
+-------------+---------+--------+
| employee_id | name    | salary |
+-------------+---------+--------+
| 2           | Meir    | 3000   |
| 3           | Michael | 3800   |
| 7           | Addilyn | 7400   |
| 8           | Juan    | 6100   |
| 9           | Kannon  | 7700   |
+-------------+---------+--------+
输出：
+-------------+-------+
| employee_id | bonus |
+-------------+-------+
| 2           | 0     |
| 3           | 0     |
| 7           | 7400  |
| 8           | 0     |
| 9           | 7700  |
+-------------+-------+
```

## MySQL

### UNION

```sql
-- MySQL
SELECT employee_id, salary AS bonus
FROM Employees
WHERE employee_id % 2 != 0 AND name NOT LIKE 'M%'
UNION
SELECT employee_id, salary * 0 AS bonus
FROM Employees
WHERE employee_id % 2 = 0 OR name LIKE 'M%'
ORDER BY employee_id ASC
;
```

### CASE
```sql
-- MySQL
SELECT employee_id,
       (
           CASE
               WHEN MOD(employee_id,2) != 0 AND LEFT(name,1) != 'M' THEN salary
               WHEN MOD(employee_id,2) = 0 OR LEFT(name,1) = 'M' THEN 0
               END
           ) bonus
FROM Employees
ORDER BY employee_id ASC
;
```

## IF
```sql
-- MySQL
SELECT employee_id, 
    IF(
        MOD(employee_id,2) != 0 AND LEFT(name,1) != 'M', salary, 0
    ) bonus
FROM Employees
ORDER BY employee_id ASC
;
```

### MySQL函数文档

- MOD(N,M)：该函数返回N除以M后的余数.
- LEFT(str,len)：返回最左边的n个字符的字符串str，或NULL如果任何参数是NULL。