# [LeetCode175.组合两个表](https://leetcode.cn/problems/combine-two-tables/)

```text
表: Person

+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
personId 是该表的主键列。
该表包含一些人的 ID 和他们的姓和名的信息。
 

表: Address

+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
addressId 是该表的主键列。
该表的每一行都包含一个 ID = PersonId 的人的城市和州的信息。
 

编写一个SQL查询来报告 Person 表中每个人的姓、名、城市和州。如果 personId 的地址不在 Address 表中，则报告为空  null 。

以 任意顺序 返回结果表。

查询结果格式如下所示。

输入: 
Person表:
+----------+----------+-----------+
| personId | lastName | firstName |
+----------+----------+-----------+
| 1        | Wang     | Allen     |
| 2        | Alice    | Bob       |
+----------+----------+-----------+
Address表:
+-----------+----------+---------------+------------+
| addressId | personId | city          | state      |
+-----------+----------+---------------+------------+
| 1         | 2        | New York City | New York   |
| 2         | 3        | Leetcode      | California |
+-----------+----------+---------------+------------+
输出: 
+-----------+----------+---------------+----------+
| firstName | lastName | city          | state    |
+-----------+----------+---------------+----------+
| Allen     | Wang     | Null          | Null     |
| Bob       | Alice    | New York City | New York |
+-----------+----------+---------------+----------+

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/combine-two-tables
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
```

## MySQL

### LEFT JOIN

```sql
SELECT p.firstName , p.lastName , a.city , a.state
FROM person p LEFT JOIN address a ON p.personId = a.personId
ORDER BY p.personId ASC
;
```

### IFNULL

```sql
SELECT firstName , lastName ,
    IFNULL((SELECT city FROM address a WHERE a.personId = p.personId) , NULL) AS city , 
    IFNULL((SELECT state FROM address a WHERE a.personId = p.personId) , NULL) AS state
FROM person p
ORDER BY personId ASC
;
```

### 内外连接解释

inner join：2表值都存在
outer join：附表中值可能存在null的情况。

总结：
A inner join B：取交集

A left join B：取A全部，B没有对应的值，则为null

A right join B：取B全部，A没有对应的值，则为null

A full outer join B：取并集，彼此没有对应的值为null

上述4种的对应条件，在on后填写。