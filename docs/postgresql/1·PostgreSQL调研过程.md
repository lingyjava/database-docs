# 1·PostgreSQL调研过程

- [1·PostgreSQL调研过程](#1postgresql调研过程)
  - [书签](#书签)
  - [数据类型](#数据类型)
  - [约束](#约束)
  - [索引](#索引)
  - [SQL](#sql)
  - [表解锁](#表解锁)
  - [版本差异](#版本差异)
  - [配置远程访问](#配置远程访问)

## 书签
- [PostgerSQL各版本官方文档](https://www.postgresql.org/docs/release/)
- [重新发现PG之美](https://developer.aliyun.com/article/793280)
- [PostgreSQL 数据库开发规范](https://developer.aliyun.com/article/60899#slide-8)
- [Docker 安装 postgreSQL 11.4](https://blog.csdn.net/qq_40180411/article/details/105952776)
- [Mybatis 转义字符（xml 转义写法）](https://my.oschina.net/mobinchao/blog/883510)
- [PostgreSQL的SQL语句参数上限 An I/O error occurred while sending to the backend](https://blog.csdn.net/qq_33732195/article/details/103898754)
- [PostgreSQL 数据库多列复合索引的字段顺序选择原理](https://developer.aliyun.com/article/582852)
- [PostgreSQL 类似于Mysql的replace into的功能](https://blog.csdn.net/axiang_/article/details/116158433)
- [sql 中 limit 与 limit，offset连用的区别](https://blog.csdn.net/AinUser/article/details/72803175)
- [SQL开窗函数（窗口函数）详解](https://blog.csdn.net/qq_31183727/article/details/107023293)
- [sql over函数_SQL高级知识——OVER开窗函数的用法](https://blog.csdn.net/weixin_39740272/article/details/111090489)
- [PostgreSQL中批量Upsert的用法](https://www.jianshu.com/p/832eba230bc3)
- [PostgreSQL upsert功能(insert on conflict do)的用法](https://developer.aliyun.com/article/74419)
- [释放空间](https://www.runoob.com/sqlite/sqlite-vacuum.html)

## 数据类型
- [PostgreSQL 数据类型](https://www.runoob.com/postgresql/postgresql-data-type.html)
- [pgsql:添加注释与查询注释](https://blog.csdn.net/xiaoxuechi/article/details/79297009)

```sql
--修改列的数据类型
ALTER TABLE table_name ALTER COLUMN column_name TYPE type_name USING column_name::type_name;
```

## 约束
```sql
ALTER TABLE public.hk_ccassholdrecdet ADD CONSTRAINT pk_hk_ccassholdrecdet PRIMARY KEY ("id"); 
ALTER TABLE hk_ccassholdrecdet DROP CONSTRAINT pk_hk_ccassholdrecdet;
```

## 索引
- [Combining Multiple Indexes](https://www.postgresql.org/docs/11/indexes-bitmap-scans.html)

```sql
--查看SQL语句查询计划
explain (analyze,verbose,buffers,costs,timing) 

--抛弃所有会话状态
DISCARD ALL;

-------踩坑---------
--临时空间不足，使用命令修改，单位kb
ALTER ROLE ALL SET temp_file_limit = 1000000000;

--查询所有表大小
SELECT relname,pg_size_pretty ( pg_relation_size ( relid ) ) AS SIZE FROM pg_stat_user_tables;
--查询所有表大小（包括索引）
SELECT relname,pg_size_pretty ( pg_total_relation_size ( relid ) ) AS SIZE FROM pg_stat_user_tables;

-------布隆索引，支持类型：int(4)，text，更适合多条件查询和等值查询---------
--下载扩展插件
CREATE extension bloom;
--创建布隆索引
CREATE INDEX idx_hkccass_code_parti ON hk_ccassholdrecdet USING bloom ("innercode","partinumber");
--查看索引大小
SELECT pg_size_pretty ( pg_relation_size ( 'idx_hkccass_code_parti' ) ) AS SIZE;

-------btree索引，通用---------
CREATE INDEX idx_hkccass_date_code ON hk_ccassholdrecdet USING btree("holdingdate","innercode");

-------brin索引，块级索引---------
CREATE INDEX idx_hkccass_date ON hk_ccassholdrecdet USING brin("holdingdate");


-------唯一索引-----------
CREATE UNIQUE INDEX uk_hkccass_code_parti_date ON hk_ccassholdrecdet USING btree("innercode","partinumber","holdingdate");

-------聚簇索引需要定期手动一次性的聚簇，顺序-------
CREATE INDEX idx_hkccass_cluster_date ON hk_ccassholdrecdet;
CLUSTER hk_ccassholdrecdet USING idx_hkccass_cluster_date;
```

## SQL
```sql
--MySQL的replace into，在插入数据时如果数据已存在，则不插入数据且不报错。
--PgSQL中没有replace into，使用的如下命令，根据三列的唯一索引判断是否有相同数据。
INSERT INTO hk_ccassholdrecdet VALUES
(562269963791,1000225,TO_DATE('2017-10-24', 'yyyy-mm-dd'),'C00093',635649784.00,0.20890000,TO_DATE('2017-11-23', 'yyyy-mm-dd'),TO_DATE('2017-11-23', 'yyyy-mm-dd'),564790319721),
(562269963791,1000225,TO_DATE('2017-10-24', 'yyyy-mm-dd'),'C00093',635649784.00,0.20890000,TO_DATE('2017-11-23', 'yyyy-mm-dd'),TO_DATE('2017-11-23', 'yyyy-mm-dd'),564790319721)
ON conflict("innercode","holdingdate","partinumber")
DO NOTHING;
```

## 表解锁
```sql
--查询正在运行的进程
SELECT * FROM pg_stat_activity 
--查询是否锁表了
select oid from pg_class where relname='可能锁表了的表'
select pid from pg_locks where relation='上面查出的oid'
--如果查询到了结果，表示该表被锁
--杀掉pid进程
select pg_terminate_backend('pid')
```

## 版本差异
- [PostgreSQL 13: Btree索引优化(引入Deduplication技术)](https://postgres.fun/20200702083900.html)

```sql
--表，3亿数据，28GB

--pg12和pg13的发行说明中提到对btree的空间和性能进行了优化，对重复的索引只存储一次

--三列组合索引，每条索引唯一不重复
--local postgresql13 索引大小11GB
--local postgresql11 索引大小11GB
CREATE INDEX idx_hkccass_date_parti_code ON hk_ccassholdrecdet1 USING btree("InnerCode","PartiNumber","HoldingDate")
SELECT pg_size_pretty ( pg_relation_size ( 'idx_hkccass_date_parti_code' ) ) AS SIZE;--11GB

--两列组合索引，索引存在大量重复
--local postgresql13 索引大小2105MB
--local postgresql11 索引大小9033MB
CREATE INDEX idx_hkccassholdrecdet_code_parti ON hk_ccassholdrecdet1 USING btree("InnerCode","PartiNumber");
SELECT pg_size_pretty ( pg_relation_size ( 'idx_hkccassholdrecdet_code_parti' ) ) AS SIZE;--2105MB

--单列组合索引，索引存在大量重复
--local postgresql13 索引大小1987MB
--local postgresql11 索引大小6433MB
CREATE INDEX idx_hkccass_code ON hk_ccassholdrecdet1 USING btree("InnerCode");
SELECT pg_size_pretty ( pg_relation_size ( 'idx_hkccass_code' ) ) AS SIZE;
```

## 配置远程访问
- [PostgreSQL 连接失败问题](https://blog.csdn.net/qq_50119033/article/details/120922628)

1.查看正在运行的容器
docker ps 
2.通过id进入docker容器
docker exec -it ad336fe4c0f8 bash
3.Docker中PostgreSQL数据文件和配置文件位置
/var/lib/postgresql/data
