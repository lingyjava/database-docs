# 1·SQL简介

- [1·SQL简介](#1sql简介)
  - [SQL是什么](#sql是什么)
  - [组成](#组成)
  - [注释方式](#注释方式)

## SQL是什么
SQL(Structured Query Language)，结构化查询语言。
用于管理关系数据库管理系统（RDBMS）。 SQL 的范围包括数据插入、查询、更新和删除，数据库模式创建和修改，以及数据访问控制。
标准 SQL 由 ANSI 标准委员会管理，从而称为 ANSI SQL。各个 DBMS 都有自己的实现，如 PL/SQL、Transact-SQL 等。
SQL 语句不区分大小写，但是数据库表名、列名和值是否区分依赖于具体的 DBMS 以及配置。

## 组成
SQL包含以下四个部分：
1. 数据定义语言（DDL）：drop,create,alter等；
2. 数据操作语言（DML）：insert，update，delete等；
3. 数据查询语言（DQL）：select;
4. 数据控制语言(DCL）：grant,revoke,rollback,commit等；

## 注释方式
SQL拥有以下三种注释方式：
```sql
# 注释
SELECT *
FROM mytable; -- 注释
/* 注释1
   注释2 */
```

