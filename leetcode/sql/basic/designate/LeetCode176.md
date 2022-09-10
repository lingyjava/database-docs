# [LeetCode176.第二高的薪水](https://leetcode.cn/problems/second-highest-salary/)

```text
Employee 表：
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
id 是这个表的主键。
表的每一行包含员工的工资信息。

编写一个 SQL 查询，获取并返回 Employee表中第二高的薪水 。如果不存在第二高的薪水，查询应该返回 null 。

查询结果如下例所示。

输入：
Employee 表：
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
输出：
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

## MySQL

### IFNULL + LIMIT
```sql
-- IFNULL 不必须
SELECT IFNULL(
               (
                   SELECT DISTINCT salary
                   FROM Employee
                   ORDER BY salary DESC
                   LIMIT 1 OFFSET 1
               ), NULL) AS SecondHighestSalary
FROM DUAL -- MySQL可不必须
;
```