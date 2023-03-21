# 4·DML语法结构

- [4·DML语法结构](#4dml语法结构)
  - [插入](#插入)
    - [普通插入](#普通插入)
    - [插入检索出来的数据](#插入检索出来的数据)
    - [将一个表的内容插入到一个新表](#将一个表的内容插入到一个新表)
  - [更新](#更新)
  - [删除](#删除)

## 插入

### 普通插入
```sql
INSERT INTO mytable(col1, col2)
VALUES(val1, val2);
```

### 插入检索出来的数据
```sql
INSERT INTO mytable1(col1, col2)
SELECT col1, col2
FROM mytable2;
```

### 将一个表的内容插入到一个新表
```sql
CREATE TABLE newtable AS
SELECT * FROM mytable;
```

## 更新
-[[Mysql语法]--update inner join使用（批量更新）](https://blog.csdn.net/high2011/article/details/83006786)

```sql
UPDATE mytable
SET col = val
WHERE id = 1;
```

## 删除
```sql
DELETE FROM mytable
WHERE id = 1;

-- TRUNCATE TABLE 可以清空表，也就是删除所有行。
TRUNCATE TABLE mytable;
```