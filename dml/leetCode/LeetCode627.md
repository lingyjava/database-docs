# LeetCode627.[变更性别](https://leetcode.cn/problems/swap-salary/)

<details><summary>SQL架构</summary>

```sql
Create~~ table If Not Exists Salary (id int, name varchar(100), sex char(1), salary int)  
Truncate table Salary  
insert into Salary (id, name, sex, salary) values ('1', 'A', 'm', '2500')  
insert into Salary (id, name, sex, salary) values ('2', 'B', 'f', '1500')  
insert into Salary (id, name, sex, salary) values ('3', 'C', 'm', '5500')  
insert into Salary (id, name, sex, salary) values ('4', 'D', 'f', '500')  
```

</details>

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

## IF
```sql
# MySQL
UPDATE salary
SET sex = IF (sex = 'm','f','m'); 
```

## CASE WHEN THEN END
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

## CASE WHEN THEN ELSE END 
```sql
UPDATE salary
SET sex = 
(
    CASE sex
    WHEN 'm' THEN 'f'
    WHEN 'f' THEN 'm'
    ELSE ''
    END
)
;
```

## ASCII
```sql
UPDATE salary
SET sex = CHAR(ASCII('m') + ASCII('f') - ASCII(sex))
;
```