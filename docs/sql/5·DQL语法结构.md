# 5·DQL语法结构

- [5·DQL语法结构](#5dql语法结构)
  - [语法模板结构](#语法模板结构)
  - [SELECT](#select)
  - [FROM](#from)
  - [WHERE](#where)
    - [WHERE子句可用的操作符](#where子句可用的操作符)
  - [DISTINCT](#distinct)
  - [LIMIT OFFSET](#limit-offset)
  - [排序](#排序)
  - [通配符](#通配符)
  - [别名](#别名)
  - [分组](#分组)
    - [汇总函数](#汇总函数)
  - [HAVING](#having)
  - [子查询](#子查询)
  - [EXISTS](#exists)
  - [连接](#连接)
    - [内连接](#内连接)
    - [自连接](#自连接)
    - [自然连接](#自然连接)
    - [外连接](#外连接)
  - [UNION](#union)
  - [伪列](#伪列)

## 语法模板结构
查询语句模板结构如下，关键字同时存在时必须顺序使用。
```sql
SELECT [ALL|DISTINCT ] *|<目标列表达式>[别名],...
FROM table_name
WHERE ...
GROUP BY column_1
HAVING ...
ORDER BY column_2
LIMIT 10 OFFSET 0;
```

## SELECT
用于选择需要查询的列，一个DQL语句中必须存在的部分。
```sql
-- 查询所有列
SELECT ALL;
SELECT *; 

-- 查询名为id，name的列
SELECT id,name;
```

## FROM
用于选择查询的表，一个DQL语句中必须存在的部分。
```sql
-- 查询user表
FROM user;

-- 查询user和admin表
FROM user, admin;
```

## WHERE
用于设置筛选数据的条件，只查询出满足条件的数据，属性DQL语句中可选的部分。
```sql
-- 查询出user_name列为lingyuan的数据
WHERE user_name = "lingyuan"
-- 查询出user_name列为lingyuan 且 age小于等于18的数据
WHERE user_name = "lingyuan" AND age <= 18
-- 查询出user_name为lingyuan 或 user_name为zenghong的数据
WHERE user_name = "lingyuan" OR user_name = "zenghong"
```

### WHERE子句可用的操作符
| 操作符      |     说明     |
| ----------- | :----------: |
| =           |     等于     |
| <           |     小于     |
| >           |     大于     |
| <> !=       |    不等于    |
| <= !>       |   小于等于   |
| >= !<       |   大于等于   |
| BETWEEN     | 在两个值之间 |
| IS NULL     |  为 NULL 值  |
| IS NOT NULL | 不为 NULL 值 |

需要注意：
- NULL 与 0、空字符串都不同，需要使用关键字判断列是否为null.
- AND 和 OR 用于连接多个过滤条件。优先处理 AND，当一个过滤表达式涉及到多个 AND 和 OR 时，可以使用 () 来决定优先级，使得优先级关系更清晰。
- IN 操作符用于匹配一组值，其后也可以接一个 SELECT 子句，从而匹配子查询得到的一组值。
- NOT 操作符用于否定一个条件。

## DISTINCT
筛选查询的数据，使相同值只会出现一次。它作用于所有列，也就是说所有列的值都相同才算相同。
```sql
SELECT DISTINCT col1, col2
FROM mytable;
```

## LIMIT OFFSET
限制返回的行数和偏移量。
LIMIT可以有两个参数，第一个参数为起始行，从 0 开始；第二个参数为返回的总行数。
```sql
-- 返回前10行
LIMIT 10;
LIMIT 0, 10;

-- 返回第3 ~ 7行
LIMIT 2, 5;
LIMIT 5 OFFSET 2;
```

## 排序
使用`order by`关键字指定查询出的数据按列以某种方式排序。
ASC : 升序(默认)，DESC : 降序。
可以按多个列进行排序，并且为每个列指定不同的排序方式。

```sql
ORDER BY col1 DESC, col2 ASC;
```

## 通配符
通配符用在`where`语句中，只能用于文本字段。
不要滥用通配符，通配符位于开头处匹配会非常慢。

- `%` 匹配 >=0 个任意字符；
- `_` 匹配 ==1 个任意字符；
- `[ ]` 可以匹配集合内的字符，例如 [ab] 将匹配字符 a 或者 b。
- 用脱字符 ^ 可以对其进行否定，也就是不匹配集合内的字符。
- 使用 Like 来进行通配符匹配。

## 别名
为查询出的字段重命名，或者字段需要用作其他计算时，通常需要使用 AS 来取别名，否则输出的时候字段名为计算表达式。
```sql
SELECT col1 * col2 AS alias
FROM mytable;
```

## 分组
`group by`关键字可以指定查询的数据按某些列进行分组。
分组就是把具有相同的数据值的行放在同一组中，对同一分组数据使用汇总函数进行处理，例如求分组数据的平均值等。
`ORDER BY` 也可以按汇总字段来进行排序。

```sql
SELECT col, COUNT(*) AS num
FROM mytable
GROUP BY col
ORDER BY num;
```

分组规定：
- GROUP BY 子句出现在 WHERE 子句之后，ORDER BY 子句之前。
- 除了汇总字段外，SELECT 语句中的每一字段都必须在 GROUP BY 子句中给出（MySQL例外）。
- NULL 的行会单独分为一组。
- 大多数 SQL 实现不支持 GROUP BY 列具有可变长度的数据类型。

### 汇总函数
| 函数 | 作用 |
| ---- | --- |
| AVG()     | 计算平均数    |
| MIN()     | 计算最小值    |
| MAX()     | 计算最大值    |
| SUM()     | 计算求和    |
| COUNT()     | 计算结果行数，如果根据求行数的字段为null那么就不计算行数    | 

## HAVING
WHERE 过滤行，HAVING 过滤分组，行过滤应当先于分组过滤。
分组过滤条件必须使用HAVING关键字。
WHERE关键字的查询条件不能使用函数，要使用函数必须使用HAVING关键字。
```sql
SELECT col, COUNT(*) AS num
FROM mytable
WHERE col > 2
GROUP BY col
HAVING num >= 2;
```

## 子查询
在一个查询中嵌套另一个查询，嵌套的查询语句就是子查询，子查询的结果可以被本次查询语句使用，可以将子查询的结果作为 WHRER 语句的过滤条件。
- 单行子查询:子句只会查询出一个结果。
- 多行子查询:子句会查询出多个结果。

```sql
-- 1、查询工资比JONES高的所有员工。  
select ename 
from emp
where sal > 
	(select sal 
   from emp
   where ename = 'JONES');
/*关联查询方法*/
select e2.ename,e2.sal 
from emp e1,emp e2 
where e1.ename = 'JONES' 
  and e2.sal>e1.sal;

-- 2、和工资大于2000的人在同一个部门的人
select ename,deptno 
from emp
where deptno in
	(select distinct deptno from emp
     where sal>2000);
/*关联查询方法*/
select distinct e2.ename,e2.deptno,e2.sal 
from emp e1,emp e2 
where e1.sal>2000 
  and e2.deptno = e1.deptno;
```

## EXISTS
判断是否存在。如果子句有返回结果,那么返回true.否则返回false.
`NOT EXISTS` 和 `EXISTS` 相反。

```sql
-- 1、查询有员工的部门信息。
SELECT d.* FROM dept d
WHERE EXISTS
	(SELECT empno FROM emp e
     WHERE d.deptno = e.deptno);
```

## 连接
连接用于连接多个表，使用 JOIN 关键字，并且条件语句使用 ON .
连接可以替换子查询，并且比子查询的效率一般会更快。
可以用 AS 给列名、计算字段和表名取别名，给表名取别名是为了简化 SQL 语句以及连接相同表。

### 内连接
内连接又称等值连接，使用 INNER JOIN 关键字。
可以不明确使用 INNER JOIN，而使用普通查询并在 WHERE 中将两个表中要连接的列用等值方法连接起来。
在没有条件语句的情况下返回笛卡尔积。

```sql
-- 显式inner join
SELECT A.value, B.value
FROM tablea AS A INNER JOIN tableb AS B
ON A.key = B.key;

-- where等值连接
SELECT A.value, B.value
FROM tablea AS A, tableb AS B
WHERE A.key = B.key;
```

### 自连接
自连接可以看成内连接的一种，只是连接的表是自身而已。

一张员工表，包含员工姓名和员工所属部门，要找出与 Jim 处在同一部门的所有员工姓名。
```sql
-- 子查询版本
SELECT name
FROM employee
WHERE department = (
      SELECT department
      FROM employee
      WHERE name = "Jim");

-- 自连接版本
SELECT e1.name
FROM employee AS e1 INNER JOIN employee AS e2
ON e1.department = e2.department
      AND e2.name = "Jim";
```

### 自然连接
自然连接是把同名列通过等值测试连接起来的，同名列可以有多个。
内连接和自然连接的区别: 内连接提供连接的列，而自然连接自动连接所有同名列。

```sql
SELECT A.value, B.value
FROM tablea AS A NATURAL JOIN tableb AS B;
```

### 外连接
外连接保留了没有关联的那些行。分为左外连接，右外连接以及全外连接，左外连接就是保留左表没有关联的行。

检索所有顾客的订单信息，包括还没有订单信息的顾客。
```sql
SELECT Customers.cust_id, Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
```

## UNION
使用 UNION 来组合两个查询，如果第一个查询返回 M 行，第二个查询返回 N 行，那么组合查询的结果一般为 M+N 行。
每个查询必须包含相同的列、表达式和聚集函数。默认会去除相同行，如果需要保留相同行，使用 UNION ALL。
只能包含一个 ORDER BY 子句，并且必须位于语句的最后。

```sql
SELECT col
FROM mytable
WHERE col = 1
UNION
SELECT col
FROM mytable
WHERE col =2;
```

## 伪列
在插入数据时数据库自动给改行数据生成的唯一的rowid.
- rowid在插入数据时数据库自动给改行数据生成的唯一`rowid`，查数据最快。
- rownum查询返回结果集中行的序号，可以用来限制查询返回的行数。
- rownum在排序之前就已经存在，排序会打乱rownum的排序。
伪列只能直接查找等于1和<，若要查找大于1和一个区间，需要把rownum查出来变成一个列再使用。

```sql
-- 1、查询年龄最小的三位学员
select * from 
	(select * from studentinfo
     order by stuage)
where rownum <= 3;

-- 2、查询 Java 考试成绩第二名的学员信息
select * from
	(select exam.*,rownum r from(
		select * from studentexam exam 
        where examsubject = 'Java' 
        order by examresult desc))
where r = 2;
```
