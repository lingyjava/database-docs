# 9·PLSQL

- [9·PLSQL](#9plsql)
  - [PL/SQL是什么](#plsql是什么)
  - [PL/SQL结构](#plsql结构)
  - [变量](#变量)
  - [选择结构](#选择结构)
  - [循环结构](#循环结构)
  - [异常](#异常)
  - [游标](#游标)
    - [显式游标](#显式游标)
      - [静态游标](#静态游标)
      - [动态游标](#动态游标)
    - [隐式游标](#隐式游标)

## PL/SQL是什么
PL/SQL是对SQL语句的扩展。增加了编程语言的特点，把数据操作和查询语句组织通过逻辑判断、循环等操作实现复杂的功能的程序语言。
在PL/SQL中不能直接写select，如果要写select查询必须要先通过变量接收，但可以直接写insert，update，delete。
使用PL/SQL用编程的方式可以实现查询一些复杂业务逻辑。

## PL/SQL结构
```sql
declare
/* 声明部分:在此声明PL/SQL用到的变量,类型及游标，以及局部的存储过程和函数 */
begin
/* 执行部分:过程及 SQL 语句,即程序的主要部分 */
exception
/* 执行异常部分:错误处理 */
end;
```

## 变量
```sql
/*
定义一个变量:变量名 数据结构(number)
				 %type 与表中某个属性列的数据类型相一致，存储一个数据
				 %rowtype 表的行类型变量，数据类型和表的数据结构相一致,存储一行数据
:= 赋值运算符
|| 拼接符
可以用 SELECT 语句对记录变量进行赋值，, 只要保证记录字段与查询结果列表中的字段相配即可。
select into empno from emp 

set serveroutput on 打开输出
dbms_output.put_line(); 输出一行
*/
```

1.  根据一个员工的工资加奖金。  
```sql
declare
	v_sal emp.sal%type;
begin
	select sal into v_sal from emp where empno = 7876;
	if v_sal>3000 then
		update emp set comm = nvl(comm,0)+500
        where empno = 7876;
    elsif v_sal>2000 then
		update emp set comm = nvl(comm,0)+300
        where empno = 7876;
    elsif v_sal>1000 then
		update emp set comm = nvl(comm,0)+100
        where empno = 7876;
  end if;
commit;
end;
```

2.  查询编号7876员工的信息。  
```sql
declare
	row_emp emp%rowtype;
	v_name emp.ename%type;
begin
	select * into row_emp from emp where empno = '7876';
	select dname into v_name from dept where deptno = row_emp.deptno;
  dbms_output.put_line('工号'||row_emp.empno||'姓名'||row_emp.ename||'工资'||row_emp.sal||'部门名'||v_name);
end;
```

## 选择结构
```sql
/*
if 条件 then
	代码块;
	continue;跳出本次循环
	exit;退出循环
	return;退出程序
	goto 锚; 跳到描点继续执行
elsif 条件 then
	代码块
else
	代码块
end if;
*/
```

## 循环结构
用于遍历游标(集合)。
```sql
/*
loop:直接开始循环，当满足指定条件后结束循环
while for:当满足指定条件后开始循环
*/
```

1.  loop循环输出1，2，3，5，6  
```sql
declare
	v_i number(2):=0;
begin
	loop
		exit when v_i>=6;--退出条件
		v_i:=v_i+1;
		if v_i = 4 then
			continue;--跳出本次循环
			/*exit; 退出循环*/
			/*return; 退出程序*/
		end if;
		dbms_output.put_line(v_i);
	end loop;
end;
```

2.  while循环输出1，2，3，4，5  
```sql
declare
	v_i number(2):=1;
begin
	while v_i<=5 loop
		dbms_output.put_line(v_i);
		v_i:=v_i+1;
	end loop;
end;
```

3.  for循环输出1，2，3，4，5  
```sql
declare
begin
	for v_i in 1..5 loop
		dbms_output.put_line(v_i);
	end loop;
end;
```

4.  打印九九乘法表。  
```sql
declare
begin
	for i in 1..9 loop
		for j in 1..9 loop
			if (j<=i) then
				dbms_output.put(j||'*'||i||'='||i*j||'  ');
			end if;
		end loop;
	dbms_output.put_line(chr(10));/*chr(10):换行*/
	end loop;
end;
```

## 异常
程序在执行时报错，导致无法继续运行。通过异常处理，让程序继续运行。
[Oracle预定义的21种异常](https://www.cnblogs.com/lonelywolfmoutain/p/4234325.html)
```sql
declare
     v_i  emp.sal%type;
begin
     dbms_output.put_line('程序开始执行');
     select sal into v_i from emp where empno=7788; 
     dbms_output.put_line('程序执行完成');
     /*异常处理模块,写在最后*/
     exception
            when TOO_MANY_ROWS then   
                dbms_output.put_line('返回多行结果');
            when others then
            	dbms_output.put_line('r');
end;
```

自定义异常名称
```sql
declare
     /*自定义异常类型的名称*/
     NO_ClASS_ID exception;
     /*把自定义的异常类型名称跟异常编号进行绑定*/
     pragma exception_init(NO_ClASS_ID,-02291);
begin
     update studentinfo set  sclassid=99 where stuid=11;
     exception
            when NO_ClASS_ID then   
                    dbms_output.put_line('没有该班级编号');
end;
```

## 游标
游标(cursor)：相当于Java中的集合。
处理多行记录使用游标。

不能重复打开一个游标，关闭游标后可以重新打开游标提取数据。

定义游标->打开游标->提取游标数据->关闭游标
```sql
/*定义游标*/
cursor cursor_name sys_refcursor;--系统数据类型
type cur_emp_type is ref cursor return emp%rowtype;--自定义游标类型
/*打开游标*/
open cursor_name is select...
/*提取游标数据*/
loop
	exit when cursor_name%notfound;
	fetch cursor_name into v_i;
	dbms_output.put_line(v_i);
/*关闭游标*/
close cursor;
```

### 显式游标
显式游标是定义的游标，分为静态和动态游标。

#### 静态游标
静态游标在定义时里面的数据就固定了。
```sql
--loop循环遍历游标
declare
  row_dept dept%rowtype;
  cursor cur_dept is select * from dept;
begin
  open cur_dept;
  loop
    exit when cur_dept%notfound;
    fetch cur_dept into row_dept;
    dbms_output.put_line('部门编号：'||row_dept.deptno||',部门名：'||row_dept.dname);
  end loop;
  close cur_dept;
end;
```

```sql
--for循环遍历游标
declare
  row_dept dept%rowtype;
  cursor cur_dept is select * from dept;
begin
  for row_dept in cur_dept loop
  	dbms_output.put_line('部门编号：'||row_dept.deptno||',部门名：'||row_dept.dname);
  end loop;
end;
```
#### 动态游标
在程序运行时不能确定需要的数据，需要动态的获取。
```sql
declare
cursor cur_dept is select * from dept;
row_dept cur_dept%rowtype;
/*定义动态游标的数据类型：ref cursor*/
/*return emp%rowtype指定该类型游标只能存放emp的数据*/
type cur_emp_type is ref cursor return emp%rowtype;
/*定义游标变量，变量类型是cur_emp_type*/
cur_emp cur_emp_type;
/*cur_emp sys_refcursor 系统动态游标类型*/
row_emp cur_emp%rowtype;

begin
 open cur_dept;
 	loop
 		fetch cur_dept into row_dept;/*fetch循环将游标d数据赋给变量*/
 		exit when cur_dept%notfound;/*游标没有数据了就返回true*/
 		dbms_output.put_line('部门名称'||row_dept.dname);
 		open cur_emp for select * from emp where deptno = row_deptno;
 		loop
 			fetch cur_emp into row_emp;
 			exit when cur_emp%notfound;
 			dbms_output.put_line('员工姓名'||ename)
 		end loop;
 		close cur_emp;
 	end loop;
 close cur_dept;
```
### 隐式游标
当进行insert，update，delete操作时，系统会默认生成隐式游标。名字默认为sql
```sql
declare
begin
	update emp set sal = 666 where empno = 7788;
	/*isopen:游标是否打开，当insert，update，delete语句执行完后隐式游标自动关闭*/
	if sql%isopen then 
		dbms_output.put_line('sql游标打开');
	else 
		dbms_output.put_line('sql游标未打开');
	end if;
	/*
	found:用于隐式游标判断sql语句是否执行成功
		  用于显式游标判断是否还有数据
	not found:与found相反
	*/
	if sql%found then
		dbms_output.put_line('sql游标有数据');
	else
		dbms_output.put_line('sql游标没有数据');
	end if;
	/*rowcount:返回影响的行数*/
	dbms_output.put_line(sql%rowcount);
end;
```
