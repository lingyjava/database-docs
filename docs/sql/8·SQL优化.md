# 8·SQL优化

- [8·SQL优化](#8sql优化)
  - [建立索引](#建立索引)
  - [避免null值判断](#避免null值判断)
  - [避免使用 不等于](#避免使用-不等于)
  - [避免使用 or](#避免使用-or)
  - [慎用 in](#慎用-in)
  - [慎用 @参数](#慎用-参数)
  - [避免使用表达式](#避免使用表达式)
  - [避免函数操作](#避免函数操作)
  - [避免update全部字段](#避免update全部字段)
  - [数据大先分页后join](#数据大先分页后join)
  - [合理规划索引](#合理规划索引)
  - [合理设计字段类型](#合理设计字段类型)
  - [避免使用select \*](#避免使用select-)
  - [避免返回大量无用数据](#避免返回大量无用数据)
  - [大字段存储方式](#大字段存储方式)

## 建立索引
对查询进行优化，要尽量避免全表扫描，首先应考虑在 where及order by涉及的列上建立索引。

## 避免null值判断
应尽量避免在where子句中对字段进行null值判断，否则将导致引擎放弃使用索引而进行全表扫描。
如:`select id from t where num is null`

 1. 最好不要给数据库留nulll，尽可能的使用not null填充数据库。（备注，描述，评论之类的可以设置为NULL, 其他的最好不要使用NULL）
 2. 不要以为null不需要空间，如：char(100)类型，在字段建立时，空间就固定了，不管是否插入值(NULL也包含在内)，都是占用100 个字符的空间的，如果是varchar这样的变长字段，null不占用空间。
 3. 可以在num上设置默认值0，确保表中num列没有null值，然后这样查询`select id from t where num = 0`

## 避免使用 不等于
应尽量避免在where子句中使用!=或<>操作符，否则引擎将放弃使用索引而进行全表扫描。

## 避免使用 or
应尽量避免在where子句中使用or来连接条件，如果一个字段有索引，一个字段没有索引，将导致引擎放弃使用索引而进行全表扫描。
如:`select id from t where num=10 or Name='admin'`
可以这样查询:
```sql
select id from t where num = 10
union all
select id from t where Name =‘admin'
```

## 慎用 in
in和not in也要慎用，否则会导致全表扫描。如`select id from t where num in(1,2,3)`
1. 对于连续的数值，能用between 就不要用in，如`select id from t where num between 1 and 3`
2. 很多时候用exists代替in是一 个好的选择。
```sql
select num from a where num in(select num from b)
--可以用下面的语句代替
select num from a where exists(select 1 from b where num=a.num);
```

## 慎用 @参数
如果在where子句中使用参数，也会导致全表扫描，因为SQL只有在运行时才会解折局部变量，但优化程序不能将访问计划的选择推迟到运行时，它必须在编译时进行选择，然而，如果在编译时建立访问计划，变量的值还是未知的，因此无法作为索引选择的输入项。如下面语句将进行全表扫描:
`select id from t where num = @num`

可以改为强制查询使用索引:
`select id from t with(index(索引名)) where num = @num`

## 避免使用表达式
应尽量避免在where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。
不要在where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引。

如:`select id from t where num/2 = 100`
应改为:`select id from t where num = 100 * 2`

## 避免函数操作
应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描，除非建立函数索引。

如：name以abc开头的id：`select id from t where substring (name, 1,3) =’abc'`
生成的id：`select id from t where datediff (day, createdate,' 2005-11-30' ) = 0`

应改为:
`select id from t where name like’ abc%'`
`select id from t where createdate >=’ 2005-11-30' and createdate <’ 2005-12-1’`

## 避免update全部字段
Update语句，如果只更改1、2个字段，不要Update全部字段，否则频繁调用会引起明显的性能消耗，同时带来大量日志。

## 数据大先分页后join
对于多张大数据量(这里几百条就算大了)的表JOIN,要先分页再JOIN,否则逻辑读会很高，性能很差。

## 合理规划索引
索引并不是越多越好，索引虽然可以提高相应的select的效率，但同时也降低了Insert及update的效率，因为insert 成update时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。一个表的索引数最好不要超过6个， 若太多则应考虑一些不常使用到的列上建的索引是否有必要。

## 合理设计字段类型
1.  尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。这是因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。
2.  尽可能的使用varchar/nvarchar 代替char/nchar ，因为首先变长字段存储空间小，可以节省存储空间，其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些。

## 避免使用select *
任何地方都不要使用select * from t，用具体的字段列表代替“*”，不要返回用不到的任何字段。

## 避免返回大量无用数据
尽量避免向客户端返回大量数据，若数据量过大，应该考虑相应需求是否合理。

## 大字段存储方式
1.  表中有大字段X(例如: text 类型)，且字段X不会经常更新，以读为为主，请问您是选择拆成子表，还是继续放一起?写出您这样选择的理由

拆带来的问题：连接消耗+存储拆分空间；
不拆可能带来的问题：查询性能。
如果能容忍拆分带来的空间问题，拆的话最好和经常要查询的表的主键在物理结构上放置在一起(分区)顺序IO，减少连接消耗，最后这是一个文本列再加上一个全文索引来尽量抵消连接消耗。
如果能容忍不拆分带来的查询性能损失的话：上面的方案在某个极致条件下肯定会出现问题，那么不拆就是最好的选择。
