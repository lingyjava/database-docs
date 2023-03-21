# DDL语法结构

- [DDL语法结构](#ddl语法结构)
  - [创建数据库](#创建数据库)
  - [创建数据表](#创建数据表)
  - [修改数据表](#修改数据表)
    - [增加列](#增加列)
    - [修改列和属性](#修改列和属性)
    - [删除列](#删除列)
  - [删除数据表](#删除数据表)

## 创建数据库
```sql
CREATE DATABASE test;
USE test;
```

## 创建数据表
```sql
CREATE TABLE mytable (
  id INT NOT NULL AUTO_INCREMENT,
  col1 INT NOT NULL DEFAULT 1,
  col2 VARCHAR(45) NULL,
  col3 DATE NULL,
  PRIMARY KEY (`id`));
```

## 修改数据表

### 增加列
```sql
ALTER TABLE mytable
ADD col CHAR(20);
```

### 修改列和属性
```sql
---ALTER TABLE 表名 CHANGE 原字段名 新字段名 字段类型 约束条件
ALTER TABLE mytable 
CHANGE col col1 CHAR(32) NOT NULL DEFAULT '123';
```

### 删除列
```sql
ALTER TABLE mytable
DROP COLUMN col;
```

## 删除数据表
```sql
DROP TABLE mytable;
```
