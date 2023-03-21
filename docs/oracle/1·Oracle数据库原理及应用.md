# 1·Oracle数据库原理及应用

- [1·Oracle数据库原理及应用](#1oracle数据库原理及应用)
  - [Oracle数据库是什么](#oracle数据库是什么)
  - [存储结构](#存储结构)
    - [物理存储结构](#物理存储结构)
    - [逻辑存储结构](#逻辑存储结构)
  - [块，区，段](#块区段)
  - [数据库实例](#数据库实例)
  - [数据文件](#数据文件)
  - [缓冲块的类型](#缓冲块的类型)
  - [模式对象](#模式对象)
  - [数据库](#数据库)
  - [表空间](#表空间)
  - [数据表](#数据表)
  - [表约束](#表约束)
  - [注释](#注释)
  - [修改表](#修改表)
  - [索引](#索引)
  - [分区表](#分区表)
    - [范围分区](#范围分区)
    - [列表分区](#列表分区)
    - [散列分区](#散列分区)
    - [复合分区](#复合分区)
    - [分区索引](#分区索引)
  - [视图](#视图)
  - [序列](#序列)
  - [同义词](#同义词)
  - [数据库链接](#数据库链接)
  - [安全管理](#安全管理)
  - [用户](#用户)
  - [权限](#权限)
  - [角色管理](#角色管理)

## Oracle数据库是什么
Oracle数据库是甲骨文公司开发的高安全性，高效率的付费的关系型数据库。
Oracle数据库端口号：1521

事务：原子性，一致性，隔离性，持久性
隔离级别：未提交读，提交读，可重复读，可串行化
数据库设计流程：需求分析--概念结构设计--逻辑结构设计--物理设计--数据库实施--数据库运行和维护

## 存储结构
在Oracle数据库的存储结构包括物理存储结构和逻辑存储结构。
![图1-Oracle数据库存储结构](/docs/images/图1-Oracle数据库存储结构.png)

### 物理存储结构
![图2-Oracle数据库物理结构](/docs/images/图2-Oracle数据库物理结构.png)

- 数据文件：用于存储数据库中的所有数据的文件。
   - 一个表空间可以包含几个数据文件
   - 一个数据文件只能从属于一个表空间
- 控制文件：用于记录和描述数据库的物理存储结构信息。
- 重做日志文件：用于记录外部程序（用户）对数据库的改变操作。
- 归档文件：用于保存已经写满的重做日志文件。
- 初始化参数文件：用于设置数据库启动时的参数初始值。
- 跟踪文件：用于记录用户进程、数据库后台进程等的运行情况。
- 口令文件：用于保存具有SYSDBA，SYSOPER权限的用户名和SYS用户口令。
- 警告文件：用于记录数据库的重要活动以及发生的错误。
- 备份文件：用于存放数据库备份所产生的文件。

### 逻辑存储结构
逻辑存储结构是从逻辑的角度来分析数据库的构成的，是数据库创建后利用逻辑概念来描述Oracle数据库内部数据的组织和管理形式。

- 数据块：BLOCK
- 区：EXTENT
- 段：SEGMENT
- 表空间：ABLESPACE

## 块，区，段
块最小的 I/O单元，在数据库创建时设置，不可更改 ，分为p标准块和非标准块 
数据库块结构：
p块头部包括标题、表目录、行目录三部分。

- 标题包含块的一般属性信息，如块的物理地址、块所属段的类型等。
- 表目录包含数据块中保存的表的信息。
- 行目录包含数据块中的行地址等信息。

---

存储区包括行数据区和空闲区两部分。

- 行数据区是已经使用的空间，保存数据库的对象数据。
- 空闲区是尚未使用的存储空间，用于存放新的行或用来更新已存在的行。区

区是由一系列连续的数据块构成的逻辑存储单元，是存储空间分配与回收的最小单位。
当创建一个数据库对象时，Oracle为对象分配若干个区，以构成一个段来为对象提供初始的存储空间。当段中已分配的区都写满后，Oracle会为段分配一个新区，以容纳更多的数据。

---

段是由一个或多个扩展区组成的逻辑存储单元。
数据库模式对象在逻辑上是以段来占据表空间的大小，段代表特定数据类型的数据存储结构。
段的类型：

- 数据段：用来存储表或簇的数据。
   - 表数据段
   - 索引表数据段
   - 分区表数据段
   - 簇数据段
- 索引段：用来存放索引信息，主要包括存储ROWID和索引键。
- 临时段：当用户进行排序查询时，如果在指定的内存无法完成排序，Oracle将自动从用户默认的临时表空间中指派临时段进行排序。会话结束，数据将从临时段中自动删除。
- 回滚段：回滚段用于保存数据库的回滚信息，包含当前未提交事务所修改的数据的原始版本。

## 数据库实例
数据库管理系统是处于用户与物理数据库之间的Oracle数据库主要由放在磁盘中的物理数据库和对物理数据库进行管理的数据库管理系统。一个中间层软件，又称为实例，由一系列内存结构和后台进程组成。

内存结构：系统全局区（SGA）和程序全局区（PGA）

实例的组成：
![图3-Oracle数据库实例组成](/docs/images/图3-Oracle数据库实例组成.png)

SGA是由Oracle分配的共享内存结构，包含一个数据库实例共享的数据和控制信息。当多个用户同时连接同一个实例时，SGA数据可供多个用户共享，SGA又称为共享全局区（Shared Global Area）。用户对数据库的各种操作主要在SGA中进行。该内存区随数据库实例的创建而分配，随实例的终止而释放。SGA 包含的内存结构有：数据高速缓冲区、共享池、重做日志缓冲区、大型池、Java流池、其他结构。
PGA是在用户进程连接数据库、创建一个会话时，由Oracle为用户分配的内存区域，保存当前用户私有的数据和控制信息，因此该区又称为私有全局区（Private Global Area）。每个服务器进程只能访问自己的PGA，所有服务器进程的PGA总和即为实例的PGA的大小。

数据高速缓存区工作过程
![图4-Oracle数据库高速缓冲区工作流程](/docs/images/图4-Oracle数据库高速缓冲区工作流程.png)

## 数据文件
用于保存数据库中所有数据的文件。
数据文件与表空间的关系 :
1. 一个表空间可以包含几个数据文件
2. 一个数据文件只能从属于一个表空间
```sql
--1.创建数据文件
alter tablespace tablespace_name
add datafile/tempfile  --数据文件/临时数据文件
'd:\...\.dbf' size 10m;

--2.修改数据文件大小
alter database 
datafile 'd:\...'
resize ...
--设置数据文件为自动增长方式
autoextend on next []m maxsize []m | unimited

--3.改变数据文件的名称或位置
--改变同一表空间中的数据文件的名称或位置
alter tablespace tablespace_name rename datafile
'd:\...','d:\...'to
'd:\...'.'d:\...';
--改变多个表空间中的数据文件的名称或位置
alter database_name rename file
'd:\...','d:\...'to
'd:\...','d:\...';

--4.删除数据文件
alter tablespace tablespace_name 
drop datafile 'd:\...' 
alter tablespace temp_name 
drop 'd:\...'

--5.取消数据文件lc233_2.dbf的自动扩展
alter database
datafile 'd:\lc233_2.dbf'
autoextend off;

--6.查询数据库当前所有的数据文件的详细信息
select * from dba_data_files;

--7.查询数据库当前所有控制文件信息
select * from v$controlfile;

--8.向数据库中添加一个重做日志文件组(组号为4),包含一个成员文件undo04a.log,大小为4mb
alter database add logfile group 4 ('d:\undo04a.log')size 4m;

--9.向数据库中的重做日志组4中添加一个成员文件,名称为undo04b.log
alter database add logfile member 'd:\undo04b.log' to group 4;
```

## 缓冲块的类型
- 脏缓存块（Dirty Buffers）
脏缓存块中保存的是已经被修改过的数据。当一条SQL语句对某个缓存块中的数据进行修改后，这个缓存块就被标记为脏缓存块。它们最终将由DBWR进程写入数据文件，以永久性地保存修改结果。

- 空闲缓存块（Free Buffers）
空闲缓存块中不包含任何数据，它们等待后台进程或服务器进程向其中写入数据。当Oracle从数据文件中读取数据时，将会寻找空闲缓存块，以便将数据写入其中

- 命中缓存块（Pinned Buffers）
正被使用，或者被显式地声明为保留的缓存块。这些缓存块始终保留在数据高速缓冲区中，不会被换出内存。

## 模式对象
模式是指一系列逻辑数据结构或对象的集合。
模式与用户相对应，一个模式只能被一个数据库用户所拥有，并且模式的名称与这个用户的名称相同。
- 如果用户以NORMAL身份登录，则进入同名模式；
- 若以SYSDBA身份登录，则进入SYS模式；
- 如果以SYSOPER身份登录，则进入PUBLIC模式。

## 数据库
1. 创建数据库 。
在Oracle中创建一个用户，每个用户的表空间作为一个库，不需要再创建数据库。
```sql
create database 库名
[on primary]
(name='',filename='',
size= ,maxsize= ,filegrowth= ),
......
```

2. 连接数据库
```sql
--1.连接命令
--若是以特权用户连接则必须带[as sysdba/sysoper]
connect username/password@orcl

--2.断开连接
discribe
```

## 表空间
Oracle数据库在逻辑上可以划分为一系列的逻辑空间，每一个逻辑空间就可以称为一个表空间。

- 一个数据库由有一个或多个表空间构成，表空间大小决定了数据库的大小。
-  一个表空间对应一个或多个数据文件，数据文件大小决定了表空间的大小。
- 一个数据文件只能从属于一个表空间。 
```sql
--1.创建表空间
create tablespace tablespace_name 
datafile 'd:\...\.dbf' size []m;
autoextend on next []m maxsize []m;  --数据文件自动扩展
extent management local --指定表空间的管理方式local或dictionary
uniform size []m;--设定区的分配方式autoallocate或uniform
segment space management manual--设定段的管理方式manual或auto

--2.修改表空间
alter tablespace tablespace_name 
add datafile/tempfile
'd:\...' size[]m;
--resize []m;  改变数据文件的大小

--3. 设置默认表空间
alter database default tablespace tablespace_name;
alter database default temporary tablespace tablespace_name;

--4.表空间重命名
alter tablespace tablespace_name
rename to tablespace_name;

--5.表空间的备份
alter tablespace tablespace_name 
begin backup  --设置为备份模式
end backup  --结束备份模式

--6.删除表空间
drop tablespace tablespace_name--只删除表空间
including contents  --表空间非空应带子句,删除其文件
and datafiles  --删除操作系统下的数据文件
cascade constraints;  --删除参照完整性约束
```

## 数据表
在创建表之前最好把表设计完整。
表中都会存在一个id作为表的主键，唯一不重复。
数据表的每一列都要指定一个数据类型。

Oracle中列包含6个数据类型。

| char(长度) | 固定长度字符串类型 |
| --- | --- |
| varchar2(长度) | 可变字符串类型 |
| long | 长文本，最大2GB |
| number(位数,精度) | 数字类型 |
| date | 日期类型（精准到秒） |
| time | 时间类型（精准到毫秒） |

- sysdate：获得系统当前时间。
```sql
create table 表名
(列名 数据类型 完整性约束,
 ......
);

--复制表(表结构和数据)
create table table_name1 as select * from scott.emp;
--复制表(只复制表结构)
create table table_name1 as select * from scott.emp where 1=2;
```

## 表约束
在表中定义的用于维护数据库完整性的一些规则。通过对表中列定义约束，可以防止在执行DML操作时，将不符合要求的数据插入到表中。
在Oracle数据库中对列的约束包括主键约束、惟一性约束、检查约束、外键约束和空/非空约束等5种，定义方法有表级约束和列级约束2种。
在Oracle中创建表时一般不给完整性约束，这样的完整性约束没有名，无法删除。一般在创建后手动添加。
```sql
primary key --主键约束
unique		--唯一性约束
check		--检查约束
foreign key references  --外键约束
null/not null			--空值约束

--1.添加约束
alter table table_name
	add constraint p_pk primary key(id);
	add constraint p_uk unique(sname);
	add constraint p_ck check(sage between 20 and 30);
	add constraint p_fk foreign key(sno) references student(sno) on delete cascade;
	modify resume not null;  
	--添加空值约束必须使用modify代替add子句。

--2.删除约束
alter table table_name
	drop unique(sname); --删除指定内容的约束
	drop constraint p_ck;  --删除指定名称的约束
	drop constraint p_uk keep index  --保留唯一性索引
	drop constraint p_pk cascade  --删除引用该约束的其他约束
```

## 注释
在Oracle中不能在创建表时在每一列后comment的方式给列添加注释。
应该在创建后手动添加注释。
```sql
--给表添加注释
commit on table t_blog is '博客表';
--给表字段添加注释
commit on column t_blog.id is '博客的唯一ID';
```

## 修改表
```sql
--1.添加列
alter table table_name
add(列名 数据结构 [完整性约束],...);

--2.修改列名
alter table table_name
rename 列名 oldname to newname

--3.删除列
alter table table_name
drop column 列名 [cascade constraints]

--4.表重命名
alter table old_name rename to newname
rename old_name to new_name

--5.删除表
drop table table_name
[cascade constraints][purge]

--当对表中的数据进行修改时需要进行确认
commit;--提交
rollback;--回滚
```

## 索引
索引是为了加速对表中元组的检索而创建的一种分散存储结构。Oracle自动使用、维护索引。

- 使用索引加速行的检索，但减慢更新的速度。
- 快速定位数据，减少磁盘 I/O。

索引使用原则：

1.  导入数据后再创建索引。 
2.  在适当的表和字段上创建索引。 
3.  合理设置复合索引中的字段顺序。 
4.  限制表中索引的数目。 
5.  为索引设置合适的PCTFREE参数。 
6.  选择存储索引的表空间。 
```sql
--1.创建索引
create [unique][bitmap] index index_name
on table_name (列名[asd|desc],...)
[expression]
[reverse]
[parameter_list]
--unique表示建立惟一性索引；
--bitmap表示建立位图索引；
--asc/desc指定索引值的排列顺序，asc升序排序，desc降序排序，缺省值为asc；
--reverse表示建立反键索引；
--parameter_list用于指定索引的存放位置、存储空间分配和数据块参数设置。

--2.合并索引
alter index table_name coalesce

--3.重建索引
alter index table_name rebuild

--4.索引重命名
alter index old_name to new_name

--5.删除索引
drop index table_name
```

## 分区表
分区原则：

1.  表的大小超过2GB 
2.  要对一个表进行并行DML操作，必须分区 
3.  为了平衡硬盘的I/O操作，将一个表分散存储在不同的表空间中，必须对它进行分区 
4.  如果需要将表一部分设置为只读，另一部分为可更新的，必须对表进行分区 

### 范围分区
范围分区是按照分区列值的范围来对表进行分区的。
```sql
create table table_name(
列名 数据类型 完整性约束,
    ...
)
partition by range(列名)
(partition 分区名 values less than()
 tablespace tablespace_name,
 partition 分区名 values less than()
 tablespace tablespace_name,
 ...
);
```
通过PARTITION BY RANGE子句说明根据范围进行分区，其后括号中列出分区列，可以进行多列分区。每个分区以PARTITION关键字开头，其后是分区名。VALUES LESS THAN子句用于设置分区中分区列值的范围。可以对每个分区的存储进行设置，也可以对所有分区采用默认的存储设置。

### 列表分区
如果分区列的值并不能划分范围（非数值类型或日期类型），同时分区列的取值范围只是一个包含少数值的集合，则可以对表进行列表分区（LIST），如按地区、性别等分区。与范围分区不同，列表分区不支持多列分区，只能根据一个单独的列来进行分区。创建列表分区时需要指定分区列和分区描述。
```sql
create table table_name(
列名 数据类型 完整性约束,
    ...
)
partition by list(列名)
(partition 分区名 values('[]')
 tablespace tablespace_name,
 partition 分区名 values('[]')
 tablespace tablespace_name,
 ...
);
```
通过PARTITION BY LIST子句说明根据列表进行分区，其后括号中列出分区列。每个分区以PARTITION关键字开头，其后是分区名。VALUES子句用于设置分区所对应的分区列取值。

### 散列分区
在进行范围分区或列表分区时，由于无法对各个分区中可能具有的记录数量进行预测，可能导致数据在各个分区中分布不均衡，某个分区中数据很多，而某个分区中数据很少。此时可以采用散列分区（HASH）方法，在指定数量的分区中均等地分配数据。为了创建散列分区，需要指定分区列、分区数量或单独的分区描述。
```sql
create table table_name(
列名 数据类型 完整性约束,
    ...
)
--1.
partition by hash(列名)
(partition 分区名 tablespace tablespace_name,
 partition 分区名 tablespace tablespace_name,
 ...
 );
--2.
partition by hash(列名)
partition 2 store in(tablespace_name1,tablespace_name2);
```
通过PARTITION BY HASH指定分区方法，其后的括号指定分区列。使用PARTITION子句指定每个分区名称和其存储空间。或者使用PARTITIONS子句指定分区数量，用STORE IN子句指定分区存储空间。

### 复合分区
复合分区包括：范围-列表复合分区，范围-散列复合分区。

范围-列表复合分区先对表进行范围分区，然后再对每个分区进行列表分区，即在一个范围分区中创建多个列表子分区。
```sql
create table table_name(
列名 数据类型 完整性约束,
    ...
)
partition by range(列名)
subpartition by list(列名)
(
partition 分区名 values less than([])
(subpartition 分区名 values('[]') 
 tablespace tablespace_name ,
 ...),
partition 分区名 values less than([])
(subpartition 分区名 values('[]') 
 tablespace tablespace_name ,
 ...),
 ...
 );
```
范围-散列复合分区先对表进行范围分区，然后再对每个分区进行散列分区，即在一个范围分区中创建多个散列子分区。
```sql
create table table_name(
列名 数据类型 完整性约束,
    ...
)
partition by range(列名)
subpartition by hash(列名)
(
partition 分区名 values less than([])
(subpartition 分区名 tablespace tablespace_name,
 subpartition 分区名 tablespace tablespace_name,
 ...),
partition 分区名 values less than([])
(subpartition 分区名 tablespace tablespace_name,
 subpartition 分区名 tablespace tablespace_name,
 ...),
 ...
 );
```

### 分区索引
本地分区索引是指为分区表中的各个分区单独建立索引分区，各个索引分区之间是相互独立的。
```sql
--在分区表的sname列上创建本地分区索引
on index_name(sname) local;

--为分区表的sage列建立基于范围的全局分区索引
create idnex table_name
on table_name(sage)
global partition by range(sage)
(partition 分区名 values less than([])
 tablespace tablespace_name,
 partition 分区名 values less than([])
 tablespace tablespace_name,
 ...
)

--为分区表创建全局非分区索引。
create index table_name
on table_name(sname)
```
全局分区索引与表分区方法类似，索引分区方法也包括范围分区、列表分区、散列分区和复合分区4种。在指明分区方法时使用GLOBAL关键字标识全局分区索引。
全局非分区索引 为分区表创建全局非分区索引与为标准表创建索引一样。

## 视图
视图是从一个或多个表或视图中提取出来的数据的一种表现形式。在数据库中只有视图的定义，而没有实际对应“表”的存在，因此视图是一个“虚”表。当对视图进行操作时，系统根据视图定义临时生成数据。
```sql
--1.创建视图
create view view_name
as
select *
from ...

--2.修改视图
create or replace view view_name
as
select *
from ...

--3.重命名试图
sp_rename oldname,newname;

4.--删除视图
drop view view_name;
```
通过视图的使用可以提高数据安全性、隐藏数据的复杂性、简化查询语句、分离应用程序与基础表、保存复杂查询等。

## 序列
序列用于产生惟一序号的数据库对象，用于为多个数据库用户依次生成不重复的连续整数。
```sql
--1.语法
create sequence sequence_name
[increment by n]
[start with n]
[maxvalue n | nomaxvalue]
[minvalue n | nominvalue]
[cycle | nocycle]
[cache n | nocache];

--2.修改序列
alter sequence sequence_name
...

--3.删除序列
drop sequence sequence_name
```
| increment by | 设置相邻两个元素之间的差值，即步长，默认值为1 |
| --- | --- |
| start with | 设置序列初始值，默认值为1 |
| maxvalue  | 设置序列最大值 |
| nomaxvalue | 默认情况下，递增序列的最大值为1027，递减序列的最大值为-1 |
| minvalue | 设置序列最小值 |
| nominvalue | 默认情况下，递增序列的最小值为1， 递减序列的最小值为-1026 |
| cycle &#124; nocycle | 指定当序列达到其最大值或最小值后，是否循环生成值，NOCYCLE 是默认选项 |
| cache &#124; nocache | 设置是否在缓存中预先分配一定数量的数据值，以提高获取序列值的速度，默认为缓存20个值 |

## 同义词
同义词是数据库中表、索引、视图或其他模式对象的一个别名。利用同义词，一方面为数据库对象提供一定的安全性保证，例如可以隐藏对象的实际名称和所有者信息，或隐藏分布式数据库中远程对象的位置信息；另一个方面是简化对象访问。此外，当数据库对象改变时，只需要修改同义词而不需要修改应用程序。
```sql
--1.创建同义词
create [public] synonym synonym_name
for table_name

--2.删除同义词
drop [public] synonym synonym_name
```
同义词分为私有同义词和公有同义词两种。私有同义词只能被创建它的用户所拥有，该用户可以控制其他用户是否有权使用该同义词；公有同义词被用户组PUBLIC拥有，数据库所有用户都可以使用公有同义词。

## 数据库链接
数据库链接是在分布式数据库应用环境中的一个数据库与另一个数据库之间的通信途径，将远程数据库映射到本地。所有能够访问本地数据库链接的应用程序即可访问远程数据库中的模式对象。

当用户正在访问一个本地数据库同时，需要访问其他非本地数据库的数据时，就需要使用数据库链接。数据库链接分为私有和公有两种类型。
```sql
create [public] database link link_name
[connect to [current user] | [user inentified by password] ]
using connect_string--指定服务名
```
| connect to | 设置与远程数据库建立连接的方式 |
| --- | --- |
| current user | 指明用当前数据库用户连接远程数据库 |
| user inentified by password | 设置连接远程数据库的用户名和口令 |

## 安全管理
数据库的安全性主要包括两个方面的含义：

1.  一方面是防止非法用户对数据库的访问，未授权的用户不能登录数据库； 
2.  另一方面是每个数据库用户都有不同的操作权限，只能进行自己权限范围内的操作。 

Oracle数据安全控制机制 ：用户管理、权限管理、角色管理 、表空间设置和配额用户资源限制、数据库审计。

Oracle数据库的安全可以分为两类：
1.  系统安全性是指在系统级控制数据库的存取和使用的机制，包括有效的用户名与口令的组合、用户是否被授权可连接数据库、用户创建数据库对象时可以使用的磁盘空间大小、用户的资源限制、是否启动了数据库审计功能，以及用户可进行哪些系统操作等。 
2.  数据安全性是指在对象级控制数据库的存取和使用机制，包括用户可存取的模式对象和在该对象上允许进行的操作等。 

## 用户
SYS：是数据库中具有最高权限的数据库管理员，可以启动、修改和关闭数据库，拥有数据字典；
SYSTEM：是一个辅助的数据库管理员，不能启动和关闭数据库，但可以进行其他一些管理工作，如创建用户、删除用户等。
SCOTT：是一个用于测试网络连接的用户，其口令为TIGER。
PUBLIC：实质上是一个用户组，数据库中任何一个用户都属于该组成员。要为数据库中每个用户都授予某个权限，只需把权限授予PUBLIC就可以了。
```sql
--1.创建用户
create user user_name identified
[by password | externally | globally as 'external_name']
[default tablespace tablespace_name]
[temporary tablespace temp_tablespace_name]
[quota n k|m|unlimited on tablespace_name]
[profile profile_name]
[password expire]
[account lock|unlock];

--2.修改用户
alter user user_name
...

--3.删除用户
drop user user_name [cascade];
```
| user_name | 设置用户名，在数据库中必须是唯一的 |
| --- | --- |
| identified | 指明用户身份认证方式 |
| by password | 用于设置用户的数据库身份认证，password为口令 |
| externally | 用于设置用户的外部身份认证。 |
| globally as 'external_name' | 设置用户的全局身份认证，external_name为Oracle的安全管理服务器相关信息。 |
| default tablespace | 设置用户的默认表空间 |
| temporary tablespace | 设置用户的临时表空间。 |
| quota | 指定用户在特定表空间上的配额，即用户在该表空间中可以分配的最大空间。 |
| profile | 为用户指定概要文件，默认值为default，采用系统默认的概要文件。 |
| password expire | 设置用户口令的初始状态为过期，用户在首次登录数据库时必须修改口令。 |
| account lock&#124;unlock | 设置用户初始状态为锁定&#124;不锁定，默认为不锁定。 |

## 权限
权限就是执行特定类型SQL命令或访问其他用户的对象的权利。用户在数据库中可以执行什么样的操作，以及可以对哪些对象进行操作，完全取决于该用户所拥有的权限。
系统权限：系统权限是指在数据库级别执行某种操作的权限，或针对某一类对象执行某种操作的权限。例如，CREATE SESSION权限、CREATE ANY TABLE权限。
对象权限：对象权限是指对某个特定的数据库对象执行某种操作的权限。例如，对特定表的插入、删除、修改、查询的权限。
```sql
--1.系统权限
grant 权限,...
--create session 数据库连接权限
--create database link 创建数据库链接
--create any index  创建索引
--create role 创建角色
--create sequence  创建修改删除序列
--create synonym  创建修改删除同义词
--create table  创建修改删除表
--create tablespace  创建表空间
--create trigger 创建修改删除触发器
--create view  创建修改删除视图
--create user  创建用户
to 用户,...;
[with admin option]  --允许用户再授权

--2.对象权限
grant 权限
--select on emp
--select any table
--insert any table
--update any table
--delete any table
to 用户

--3.权限回收
revoke <权限>...
from <用户>
```
grant语句是将指定操作对象的指定操作权限授予指定的用户。如果指定了with grant option子句，则获得某种权限的用户还可以把这种权限在授予别的用户，但在收回权限时，将级联收回。
用户被授予的权限可由DBA或其他授权者用revoke语句收回。

## 角色管理
所谓角色就是一系列相关权限的集合。

![](https://img2020.cnblogs.com/blog/2103651/202104/2103651-20210421220531015-1961358036.png#id=uMBnB&originHeight=648&originWidth=1382&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

oracle为兼容以前版本，提供三种标准角色（role）:connect/resource和dba.
```sql
--1.创建角色
create role role_name
[not identified]
[identified by password]

--2.修改角色
alter role role_name
...

--3.删除角色
drop role role_name
```
| role_name | 指定自定义角色名称，该名称不能与任何用户名或其他角色相同 |
| --- | --- |
| not identified | 指定该角色由数据库授权，使该角色生效时不需要口令 |
| identified by password | 设置角色生效时的认证口令 |

