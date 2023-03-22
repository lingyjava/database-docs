# DDL语法结构

- [DDL语法结构](#ddl语法结构)
  - [创建数据库](#创建数据库)
  - [创建数据表](#创建数据表)
  - [修改数据表](#修改数据表)
    - [增加列](#增加列)
    - [修改列和属性](#修改列和属性)
    - [删除列](#删除列)
  - [删除数据表](#删除数据表)
  - [主键自动生成](#主键自动生成)
    - [使用序列自增。](#使用序列自增)
    - [auto\_increment key](#auto_increment-key)
    - [sys\_guid](#sys_guid)
  - [时间自动生成](#时间自动生成)
    - [建表方式](#建表方式)
    - [修改方式](#修改方式)

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

## 主键自动生成
数据库中每个表都要有一个主键，主键不应是表中有意义的属性列。
主键唯一不重复且不能为空。

主键生成策略：
1. 自动增长：数字
2. 随机生成：32位字符串

### 使用序列自增。
```sql
/*先创建一个序列，在插入数据时使用序列名.nextval获取序列的值作为主键。*/
create sequence seq_student_id
minvalue 1--最小值
maxvalue 99999--最大值
increment by 1--步长

insert into student(id,name,age) values(seq_student_id.nextval,'lee',20)
```

使用触发器自动化给主键id自增。
```sql
--为主键创建一个序列
create sequence seq_student_id
minvalue 10
maxvalue 99999
increment by 1;

/*使用触发器添加主键自动增长*/
create trigger tri_student_id
/*在对表bank_info进行插入操作前*/
before insert on student
for each row
begin
	select seq_student_id.nextval into :new.id from dual;
end;
```

### auto_increment key
Mysql数据库在创建数据表时为主键加上auto_increment key
```sql
create table user(
	uid int auto_increment key primary key 
);
```

### sys_guid
sys_guid()方法：系统根据当前时间和机器码，生成全球唯一的一个32位序列号。
```sql
insert into student(id,name,age) values(sys_guid(),'lee',20)
```

## 时间自动生成
数据表中常见的创建时间和修改时间，可以通过自动生成的方式实现。

### 建表方式
```sql
CREATE TABLE `course` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

### 修改方式
```sql
-- 修改create_time 设置默认时间 CURRENT_TIMESTAMP 
ALTER TABLE `course`
MODIFY COLUMN  `create_time` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间' ;
-- 添加update_time 设置 默认时间 CURRENT_TIMESTAMP   设置更新时间为 ON UPDATE CURRENT_TIMESTAMP 
ALTER TABLE `course`
ADD COLUMN `update_time` datetime NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间' ;
```

