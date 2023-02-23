# [LeetCode197.上升的温度](https://leetcode.cn/problems/rising-temperature/)

```text
表：Weather

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
id 是这个表的主键
该表包含特定日期的温度信息

编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 id 。
返回结果 不要求顺序 。

查询结果格式如下例。
示例 ：
输入：
Weather 表：
+----+------------+-------------+
| id | recordDate | Temperature |
+----+------------+-------------+
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |
+----+------------+-------------+
输出：
+----+
| id |
+----+
| 2  |
| 4  |
+----+
解释：
2015-01-02 的温度比前一天高（10 -> 25）
2015-01-04 的温度比前一天高（20 -> 30）
```

## MySQL

### dateDiff

```sql
SELECT w1.id
FROM weather w1 , weather w2
WHERE w1.temperature > w2.temperature AND dateDiff(w1.recordDate , w2.recordDate) = 1; 
```
- dateDiff(日期1, 日期2)：得到的结果是日期1与日期2相差的天数。  
如果日期1比日期2大，结果为正；如果日期1比日期2小，结果为负。

- timestampDiff(时间类型, 日期1, 日期2): 这个函数和上面diffdate的正、负号规则刚好相反。
日期1大于日期2，结果为负，日期1小于日期2，结果为正。  
在“时间类型”的参数位置，通过添加“day”, “hour”, “second”等关键词，来规定计算天数差、小时数差、还是分钟数差。

### cross join(交叉连接：将两表中所有的数据两两结合,数据条数为 x * y )

```sql
SELECT w1.id
FROM weather w1 CROSS JOIN weather w2 ON dateDiff(w1.recordDate , w2.recordDate) = 1
WHERE w1.temperature > w2.temperature ;
```