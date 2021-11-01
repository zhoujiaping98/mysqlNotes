## 					Mysql 学习笔记

### 一、变量

#### 1.1 变量的介绍

- 系统变量：
  1. 全局变量
  2. 回话变量

- 自定义变量：
  1. 用户变量
  2. 局部变量

#### 1.2 系统变量的介绍和语法

- 说明：变量有系统定义，不是用户定义 ，属于服务器层面
- 注意：全局变量需要添加global关键字，会话变量需要添加session关键字，如果不写，默认会话级别
- 使用步骤：
  1. 查看所有系统变量

```mysql
show  global | 【session】 variables;
```

​		  2. 查看满足条件的部分系统变量

```mysql
show global | 【session】 variables like '%char%';
```

   		  3. 查看指定的系统变量的值 

```mysql
select @@global | 【session】 系统变量名;
```

       4. 为某个系统变量赋值

```mysql
#方式一
set global | 【session】 系统变量名=值;
#方式二
set @@global |【session】系统变量名=值;
```

#### 1.3 全局变量的演示

```mysql
#全局变量
/*
作用域：针对于所有会话(连接)有效，但不能跨重启
*/
#查看所有全局变量
show global variables;
#查看满足条件的部分系统变量
show global variables like '%char%';
#查看指定的系统变量的值
select @@global.autocommit;
#为某个系统变量赋值
set @@global.autocommit=0;
set global autocommit=0;

```

#### 1.4 会话变量的演示

```mysql
#会话变量
/*
作用域：针对当前会话(连接)有效
*/
#查看所有会话变量
show session variables;

#查看满足条件的部分会话变量
show session variables like '%char%';

#查看指定的回话变量的值
select @@autocommit;
select @@session.tx_isolation;

#为某个回话变量赋值
set @@session.tx_isolation='read-uncommited';
set session tx_isolation='read-commited';

```



#### 1.5 自定义变量---用户变量

```mysql
#自定义变量
/*
说明：变量有用户自定义，而不是系统提供的
使用步骤：
1.声明
2.赋值
3.使用(查看，比较，运算等)
**/
#1》用户变量
/*
作用域：针对于当前会话（连接）有效，作用域同于会话变量
*/
#赋值操作 :=或:=
#声明并初始化
set @变量名 = 值;
set @变量名 := 值;
select @变量名 := 值;

#赋值(更新变量的值)
#方式一：
	set @变量名=值;
	set @变量名:=值;
	SELECT @变量名:=值;
#方式二：
	SELECT 字段 INTO @变量名
	FROM 表;
#③使用（查看变量的值）
SELECT @变量名;

```

#### 1.6 自定义变量—局部变量

```mysql
#2》局部变量
/*
作用域：仅仅在定义它的begin end块中有效
应用在 begin end中的第一句话
*/

#①声明
DECLARE 变量名 类型;
DECLARE 变量名 类型 【DEFAULT 值】;


#②赋值（更新变量的值）

#方式一：
	SET 局部变量名=值;
	SET 局部变量名:=值;
	SELECT 局部变量名:=值;
#方式二：
	SELECT 字段 INTO 具备变量名
	FROM 表;
#③使用（查看变量的值）
SELECT 局部变量名;


#案例：声明两个变量，求和并打印
#用户变量
SET @m=1;
SET @n=1;
SET @sum=@m+@n;
SELECT @sum;

#局部变量
DECLARE m INT DEFAULT 1;
DECLARE n INT DEFAULT 1;
DECLARE SUM INT;
SET SUM=m+n;
SELECT SUM;

```

#### 1.7 用户变量和局部变量的对比

```mysql
		  作用域			定义位置		语法
用户变量	当前会话		会话的任何地方		加@符号，不用指定类型
局部变量	定义它的BEGIN END中 	BEGIN END的第一句话	一般不用加@,需要指定类型
```



### 二、存储过程和函数

#### 2.1 说明

- 都类似于java中的方法，将一组完成特定功能的逻辑语句包装起来，对外暴露名字

- 好处：
  1. 提高代码的重用性
  2. 简化操作
  3. 减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

#### 2.2 存储过程

- 创建语法

```mysql
create procedure 存储过程名(参数名)
begin
	存储过程体(一组合法的SQL语句)
END
注意：
1.  参数列表包含三部分
	参数模式   参数名   参数类型
举例：
in stuname varchar(20)

参数模式：
in：该参数可以作为输入，也就是该参数需要调用方传入值
out：该参数可以作为输出，也就是该参数可以作为返回值
inout：该参数既可以作为输入又可以作为输出，也就是该参数既需要传入值，又可以返回值

2、如果存储过程体仅仅只有一句话，begin end可以省略
存储过程体中的每条sql语句的结尾要求必须加分号。
存储过程的结尾可以使用 delimiter 重新设置
语法：
delimiter 结束标记
案例：
delimiter $

```

#### 2.3 调用语法

```mysql
CALL 存储过程名(实参列表)

举例：
调用in模式的参数：call sp1('值');
调用out模式的参数：set @name;call sp1(@name);select @name;
调用inout模式的参数：set @name=值;call sp1(@name);select @name;
```

#### 2.4 案例

```mysql
#1.表中
#案例：插入到admin 表中五条记录
select * from admin;

delimiter $
create procedure myp1()
begin
	insert into admin(username,password) values('john1','0000'),('john2','0000'),('john3','0000'),('john4','0000'),('john5','0000');

end $
#调用
call myp1()$

#2.创建带in模式参宿的存储过程
#案例1：创建存储过程实现，根据女神名，查询对应的男神信息

create procedure myp2(in beautyName varchar(20))
begin
	select bo.* from boys bo right join beauty b on bo.id = b.boyfriend_id where b.name=beautyName;
	
end $

#调用
call myp2('柳岩')$

#案例2：创建存储过程实现，用户是否登录成功

create procedure myp3(in username varchar(20),in password varchar(20))
begin
	declare result varbinary(20) default '';#声明并初始化
	
	select count(*) into result #赋值
	from admin where admin.username = username and admin.password = password;
	select result;#使用
end $

call myp3('张飞','8888')$


create procedure myp4(in username varchar(20),in password varchar(20))
begin
	declare result int default 0;#声明并初始化
	
	select count(*) into result #赋值
	from admin where admin.username and admin.password = password;
	
	select if(result > 0,'成功','失败');#使用
end $
call myp4('张飞','8888')$

#3.创建out 模式参数的存储过程
#案例1：根据输入的女神名，返回对应的男神名

create procedure myp6(in beautyName varchar(20),out boyName varchar(20))
begin 
	select bo.boyname into boyname from boys bo right join beauty b on b.boyfriend_id=bo.id where b.name = beatuyName;
	
end$

#案例2：根据输入的女神名，返回对应的男神名和魅力值
create procedure myp7(in beautyName varchar(20),out boyName varchar(20),out usercp int)
begin 
	select boys.boyname,boys.usercp into boyname,usercp from boys
	right join beauty b on b.boyfriend_id = boys.id
	where b.name=beautyName;
end $	
#调用
call myp7('小昭',@name,@cp)$
select @name,@cp$

#创建带 inout 模式参数的存储过程
#案例3：传入a和b两个值，最终a和b都翻倍并返回

create procedure myp8(inout a int,inout b int)
begin
	set a=a*2;
	set b=b*2;
end $

#调用
set @m=10$
set @n=20$
call myp8(@m,@n)$
select @m,@n$

```

#### 2.5 查看语法

```mysql
show create procedure 存储过程名;
案例
desc myp2;x
show create procedure myp2;

```

#### 2.6 删除语法

```mysql
drop procedure  存储过程名;

#案例：
drop procedure p1;
drop procedure p2,p3;#x

```

#### 2.7 函数

- 含义：一组预先编译好的sql语句的集合，理解成批处理语句

- 区别：

  存储过程：可以有0个返回，也可以有多个返回，适合批量插入，批量更新；

  函数：有且仅有1个返回，适合做处理数据后返回一个结果。

##### 2.7.1 创建语法

```mysql
create function 函数名(参数列表) return 返回类型
begin
	函数体
end
/*
注意：
1.参数列表 包含两部分：
 参数名 参数类型
2.函数体：肯定会有return语句，如果没有会报错
如果return语句没有放在函数体的最后也不报错，但不建议

return 值;
3.函数体重仅有一句话，则可以省略begin end
4.使用delimiter 语句设置结束标记
*/
delimiter $

```

##### 2.7.2 调用语法

- select    函数名 (参数列表);

```mysql
use employees $
#1.无参有返回
#案例：返回公司的员工个数
create function myf1() returns int
begin
	declare c int default 0;
	select count(*) into c from employees;
	return c;
end $

select myf1()$

#2.有参有返回
#案例1：根据员工名，返回它的工资
create function myf2(empName varchar(20)) return double
begin 
	set @sal = 0;#定义用户变量
	select salary into @sal #赋值
	from employees where last_name = empName;
	return @sal;
end $
select myf2('kochhor') $

#案例2：根据部门名，返回该部门的平均工资
create function myf3(deptName varchar(20)) returns double 
begin
	declare sal double;
	select avg(salary) into sal from employees e join departments d on e.department_id = d.department_id where d.department_name = deptName;
	return sal;
end $	
select myf3('IT')$

```

##### 2.7.3 查看函数

```mysql
show create function 函数名;

show create function myf3$;
```

##### 2.7.4 删除函数

```mysql
drop function 函数名;
drop function myf3 $
```

### 三、流程控制

- 顺序结构：程序从上往下依次执行
- 分支结构：程序按条件进行选择执行，从两条或多条路径中选择一条执行
- 循环结构：程序满足一定条件下，重复执行一组语句

#### 3.1 分支结构

1.if函数

- 语法：if(条件,值1，值2)；
- 功能：实现双分支；
- 应用：可以作为表达式放在任何位置

2.case 结构

```mysql
情况1：类似于switch，一般用于实现等值判断。
语法：
case 变量或表达式
when 值1 then 语句1;
when 值2 then 语句2;
...
else 语句n;
end 

情况2：类似于多重if语句，一般用于实现区间判断。
语法：
case 
when 条件1 then 语句1;
when 条件2 then 语句2;
...
else 语句n;
end 

```

- 特点

  ①可以作为表达式，嵌套在其他语句中使用。
  ②可以放在任何地方，BEGIN END 中或BEGIN END 的外面可以作为独立的语句去使用，只能放在BEGIN END中如果wHEN中的值满足或条件成立，则执行对应的THzN后面的语句，并且结束CASE如果都不满足，则执行E1SE中的语句或值。
  ③ELSE可以省略，如果ELSE省略了，并且所有WHEN条件都不满足，则返回NULL。

- 位置

  可以放在任何位置，
  如果放在begin end 外面，作为表达式结合着其他语句使用
  如果放在begin end 里面，一般作为独立的语句使用
  案例

```mysql
#案例 
#创建存储过程，根据传入的成绩，来显示等级，比如传入的成绩：90-100, 显示A，80-90，显示B，60-80，显示c，否则，显示D

CREATE PROCEDURE test_case (IN score INT) 
BEGIN 
	CASE 
	WHEN score>=90 AND score<=100 THEN SELECT 'A'; 
	WHEN score>=80 THEN SELECT 'B';
	WHEN score>=60 THEN SELECT 'C'; 
	ELSE SELECT 'D';
	END CASE; 
END $
CALL test_case(95)$

```

3.if结构

```mysql
if 条件1 then 语句1;
elseif 条件2 then 语句2;
....
else 语句n;
end if;
```

- 功能：类似于多重if；只能应用在begin end 中

```mysql
#案例1：创建函数，实现传入成绩，如果成绩>90,返回A，如果成绩>80,返回B，如果成绩>60,返回C，否则返回D

CREATE FUNCTION test_case(score FLOAT) RETURNS CHAR
BEGIN 
	DECLARE ch CHAR DEFAULT 'A';
	
	CASE 
	WHEN score>90 THEN SET ch='A';
	WHEN score>80 THEN SET ch='B';
	WHEN score>60 THEN SET ch='C';
	ELSE SET ch='D';
	END CASE;
	
	RETURN ch;
END $

SELECT test_case(56)$

```

#### 3.2 循环结构

- 位置：只能放在begin end中
- 特点：都能实现循环结构
- 语法

```mysql
1、while
语法：
【名称:】while 循环条件 do
		循环体
end while 【名称】;
2、loop
语法：
【名称：】loop
		循环体
end loop 【名称】;

3、repeat
语法：
【名称:】repeat
		循环体
until 结束条件 
end repeat 【名称】;

```

- 对比

```mysql
①这三种循环都可以省略名称，但如果循环中添加了循环控制语句（leave或iterate）则必须添加名称
②
loop 一般用于实现简单的死循环
while 先判断后执行
repeat 先执行后判断，无条件至少执行一次
```

- 案例

```mysql
#1.没有添加循环控制语句
#案例：批量插入，根据次数插入到admin表中多条记录
USE girls$
DROP PROCEDURE pro_while1$
CREATE PROCEDURE pro_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	WHILE i<=insertCount DO
		INSERT INTO admin(username,`password`) VALUES(CONCAT('Rose',i),'666');
		SET i=i+1;
	END WHILE;
	
END $

CALL pro_while1(158)$

select * from admin $
/*
int i=1;
while(i<=insertcount){
	//插入
	i++;

}
*/

#2.添加leave语句
#案例：批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 1;
	a:WHILE i<=insertCount DO
		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
		IF i>=20 THEN LEAVE a;
		END IF;
		SET i=i+1;
	END WHILE a;
END $

CALL test_while1(100)$

select * from admin $

#3.添加iterate语句
#案例：批量插入，根据次数插入到admin表中多条记录，只插入偶数次
TRUNCATE TABLE admin$
DROP PROCEDURE test_while1$
CREATE PROCEDURE test_while1(IN insertCount INT)
BEGIN
	DECLARE i INT DEFAULT 0;
	a:WHILE i<=insertCount DO
		SET i=i+1;
		IF MOD(i,2)!=0 THEN ITERATE a;
		END IF;
		
		INSERT INTO admin(username,`password`) VALUES(CONCAT('xiaohua',i),'0000');
		
	END WHILE a;
END $

CALL test_while1(100)$

/*
int i=0;
while(i<=insertCount){
	i++;
	if(i%2==0){
		continue;
	}
	插入
}
*/

select * from admin $

```

#### 3.3 循环控制语句

- leave：类似于break，用于跳出所在的循环
- iterate：类似于continue，用于结束背刺循环，继续下一次





























