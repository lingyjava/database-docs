# [LeetCode608.树节点](https://leetcode.cn/problems/tree-node/)

<details><summary>SQL架构</summary>

```sql
Create table If Not Exists Tree (id int, p_id int);
Truncate table Tree;
insert into Tree (id, p_id) values ('1', 'None');
insert into Tree (id, p_id) values ('2', '1');
insert into Tree (id, p_id) values ('3', '1');
insert into Tree (id, p_id) values ('4', '2');
insert into Tree (id, p_id) values ('5', '2');
```

</details>

## 要求

```text
给定一个表tree，id 是树节点的编号，p_id是它父节点的id 。

+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
树中每个节点属于以下三种类型之一：

叶子：如果这个节点没有任何孩子节点。
根：如果这个节点是整棵树的根，即没有父节点。
内部节点：如果这个节点既不是叶子节点也不是根节点。

写一个查询语句，输出所有节点的编号和节点的类型，并将结果按照节点编号排序。上面样例的结果为：


+----+------+
| id | Type |
+----+------+
| 1  | Root |
| 2  | Inner|
| 3  | Leaf |
| 4  | Leaf |
| 5  | Leaf |
+----+------+
```

## UNION
```sql
-- MySQL
-- 1、Root: 没有父节点
-- 2、叶子节点是没有孩子节点的节点，且它有父亲节点。
-- 3、内部节点是有孩子节点和父节点的节点。
-- 4、UNION三种结果
-- 5、使用WITH抽出重复的子查询
WITH tmp AS (
    SELECT DISTINCT p_id FROM tree WHERE p_id IS NOT NULL
)
SELECT id , 'Root' AS type
FROM tree
WHERE p_id IS NULL
UNION
SELECT id , 'Leaf' AS type
FROM tree
WHERE p_id IS NOT NULL
  AND id NOT IN (SELECT p_id FROM tmp)
UNION
SELECT id , 'Inner' AS type
FROM tree
WHERE p_id IS NOT NULL
  AND id IN (SELECT p_id FROM tmp)
ORDER BY id ASC
;
```

## CASE WHEN
```sql
SELECT id , (
    CASE
    WHEN p_id IS NULL THEN 'Root'
    WHEN id IN (SELECT DISTINCT p_id FROM tree) THEN 'Inner'
    ELSE 'Leaf'
    END
) type
FROM tree
;
```

## IF
```sql
SELECT id , 
(
    IF (ISNULL(p_id) , 'Root' , 
        IF (id IN (SELECT DISTINCT p_id FROM tree WHERE p_id IS NOT NULL) , 'Inner' , 'Leaf')
    )
) type
FROM tree
ORDER BY id ASC
;
```