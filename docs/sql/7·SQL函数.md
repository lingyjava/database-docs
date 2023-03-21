# 7·SQL函数

- [7·SQL函数](#7sql函数)
  - [函数的数据库差异](#函数的数据库差异)
  - [nvl函数](#nvl函数)
  - [字符串拼接](#字符串拼接)
  - [字符函数](#字符函数)
  - [数学函数](#数学函数)
  - [日期函数](#日期函数)
  - [分析函数](#分析函数)

## 函数的数据库差异
各个 DBMS 的函数都是不相同的，因此不可移植，函数仅供参考。

## nvl函数
如果一个数字跟一个空白(null)相加,那么结果为空白(null)
`nvl(column,value)`:当column不为空的时候返回column的value，否则返回value
## 字符串拼接
在查询过程中可以通过`||`拼接多个字段,做为一个字段
```sql
-- 将查询到的多列拼接成一列
SELECT '姓名' || name || '，年龄' || age || '，家住' || address 用户信息 
FROM user;
```
## 字符函数
一般使用程序完成，不在数据库内完成

- `initCap(str)`将字符串首字母转换为大写，其他字母转换为小写
- `lower(str)`将字符串转换为小写
- `upper(str)`将字符串转换为大写
- `concat(str1,str2)`将字符串1和字符串2拼接起来
- `substr(str,开始位置,数量)`截取字符串，包括开始位置，下标从1开始
- `lpad(str,位数，指定字符)`从左侧补齐字符串
- `rpad(str,位数，指定字符)`从右侧补齐字符串
- `instr(str,字符)`查询字符在字符串中的第一个出现位置
- `replace(str,oldstr,newstr)`新字符串替换旧字符串
- `length(str)`返回字符串长度
- `lengthb(str)`返回字节长度
```sql
-- 1、查询所有员工姓名，首字母大写
select initCap(ename)
from emp;

-- 2、 查询所有学员姓名的长度
select stuname,length(stuname) 
from studentinfo;

-- 3、查询身份证中第9，10位为‘89’的学员信息
select stuname,stucard from studentinfo
where substr(stucard,9,2) = '89';

-- 4、查询所有班主任的邮箱的用户名
select teachername,substr(teacheremail,1,length(teacheremail)-1-(length(teacheremail)-instr(teacheremail,'@'))) 
from teacherinfo;

-- 5、查询所有班主任的邮箱的所属网站
select teachername,substr(teacheremail,instr(teacheremail,'@')+1,instr(teacheremail,'.')-instr(teacheremail,'@')-1) from teacherinfo;
```
## 数学函数

- `ceil(num)`向上取整
- `floor(num)`向下取整
- `round(num,保留小位数)`四舍五入
- `trunc(num,保留小数位)`截断
```sql
-- 1、查询平均工资，并四舍五入
select round(avg(sal)) from emp;

-- 2、求小于-58.9的最大整数
select floor(-58.9) from dual;

-- 3、求大于78.8的最小整数
select ceil(78.8) from dual;

-- 4、求64除以7的余数
select mod(64,7) from dual;

-- 5、查询所有学员的平均年龄（要求保留两位小数）
select trunc(avg(stuage),2) from studentinfo;
```
## 日期函数

- `add_months(日期，要增加月份)`返回增加月份后的日期
- `next_day(指定的日期,星期几)`返回指定日期的下一个的星期几
- `trunc(指定的日期)`截断时分秒，返回年月日
- `to_date(日期的字符串，转换格式)`把字符串类型日期转换成date
- `to_char(指定的日期,字母格式)`返回指定格式的时间信息

时间格式：
`yyyy`年
`mm`月
`dd`日
`hh`24时
`mi`分
`ss`秒
`ff`毫秒
`day`星期
```sql
-- 1、查询‘2007-3-5’后入学的学员信息
select stuname from studentinfo
where stujointime > to_date('2007-3-5','yyyy-mm-dd');

-- 2、查询入职超过30年的员工
-- 两个日期加减以日为单位
select ename,hiredate from emp
where (sysdate-hiredate)/365 > 30;

-- 3、查询所有学员从入学到今天，一共度过了多少天
select stuname,(sysdate-stujointime) 
from studentinfo;

-- 4、查询每月2号入学的学员信息
select stuname from studentinfo 
where to_char(stujointime,'dd') = 2;

-- 5、查询所有学员的毕业日期，假定按每个学员入学时间1年半之后将毕业
select stuname,stujointime,add_months(stujointime,18) 
from studentinfo;

-- 6、查询星期四入学的学员姓名，性别，年龄，班级编号
select stuname,stusex,stuage,sclassid from studentinfo 
where to_char(stujointime,'day') = '星期四';

-- 7、查询‘2007-3-10’之前入学的学员信息
select stuname,stujointime from studentinfo
where stujointime < to_date('2007-3-10','yyyy/mm/dd');

-- 8、查询2007年入学的学员信息
select stuname,stujointime from studentinfo
where to_char(stujointime,'yyyy') = '2007';

-- 9、查询当前时间，显示格式为2021年8月17日14:20:21
select to_char(sysdate,'yyyy"年"mm"月"dd"日"hh24":"mi":"ss') 
from dual;

-- 10、查询所有学员入学时间，要求显示格式为‘2007年03月02日
select stuname,to_char(stujointime,'yyyy"年"mm"月"dd"日"') 入学时间 
from studentinfo;
```

## 分析函数
可以在组内对数据进行排序，返回一个数字。
- partition by在分析函数中用来替代`group by`.
- row_number返回连续的排位，不论值相等。
- rank具有相等值的排位相同，序数然后跳跃。
- dense_rank具有相等值的排位相同，序号是连续的。

```sql
-- 1、通过分析函数获得每个部门的最高工资
select e.*,
row_number()over(partition by deptno orderby sal) rn,
rank()over(partition by deptno order by sal) r,
dense_rank()over(partition by deptno order by sal) dr
from emp e;

-- 2、查询学员成绩，按成绩排序，并计算出名次

-- 2.1、要求不论成绩是否相同，名次是连续的序号
select estuid,examresult,examsubject,
	row_number()over(partition by examsubject order by examresult) 
from studentexam;

-- 2.2、要求成绩相等的排位相同，名次随后跳跃
select estuid,examresult,examsubject,
	rank()over(partition by examsubject order by examresult) 
from studentexam;

-- 2.3、要求成绩相等的排位相同，名次是连续的
select estuid,examresult,examsubject,
	dense_rank()over(partition by examsubject order by examresult) 
from studentexam;
```