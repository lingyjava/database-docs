# [LeetCode 595.大的国家]  (https://leetcode.cn/problems/big-countries/)

```text
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | int     |
+-------------+---------+
name 是这张表的主键。
这张表的每一行提供：国家名称、所属大陆、面积、人口和 GDP 值。
```

如果一个国家满足下述两个条件之一，则认为该国是大国：
- 面积至少为 300 万平方公里（即，3000000 km2）
- 人口至少为 2500 万（即 25000000）

要求：编写一个SQL查询以报告 大国 的国家名称、人口和面积。 按任意顺序返回结果表。

```text
输入：
World 表：
+-------------+-----------+---------+------------+--------------+
| name        | continent | area    | population | gdp          |
+-------------+-----------+---------+------------+--------------+
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
+-------------+-----------+---------+------------+--------------+
输出：
+-------------+------------+---------+
| name        | population | area    |
+-------------+------------+---------+
| Afghanistan | 25500100   | 652230  |
| Algeria     | 37100000   | 2381741 |
+-------------+------------+---------+
```

## MySQL

### OR

```sql
SELECT name, population, area
FROM World
WHERE population >= 3000000 OR area >= 25000000
;
```

### UNION

```sql
SELECT name, population, area
FROM World
WHERE population >= 3000000
UNION
SELECT name, population, area
FROM World
WHERE area >= 25000000
;
```
