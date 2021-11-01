## 						Mysql 学习笔记

### 一、DDL语言

#### 1.1 常见的约束

- 含义：一种限制，用于限制表中的数据，为了保证保证表中的数据的准确和可靠性。
- 分类：

```mysql
六大约束
	NOT NULL:非空，用于保证该字段的值不能为空
	比如：姓名，学号等
	DEFAULT：默认，用于保证盖在段有默认值
	比如：性别
    PRIMARY KEY:主键，用于保证该字段的值具有唯一性，并且非空
    比如：学号，员工编号等
    UNIQUE：唯一，用于办证该字段的值具有唯一性，可以为空
    比如：座位号
    CHECK：检查约束【mysql中不支持】
    比如：年龄，性别
    FOREIGN KEY:外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值，在表添加外键约束，用于引用主表中某列的值
    比如：学生表娥专业编号，员工表的部门编号，员工标的工种编号
    
```

- 添加约束的实际：1. 创建表时；2. 修改表时
- 约束的添加分类：
  1. 列级约束：六大约束语法上都支持，但外键约束没有效果
  2. 表级约束：除了非空，默认，其他的都支持

```mysql
CREATE TABLE 表名 {
	字段名  字段类型 列级约束,
	字段名  字段类型,
	标记约束
```

#### 1.2 创建表时 添加约束

```mysql
#1.先创建一个库
CREATE DATABASES students;
```

##### 1.2.1.添加列级约束

- 语法：直接在字段名和类型后面追加 约束类型即可。只支持：默认、非空、主键、唯一
- 案例：

```mysql
USE students;

drop table stuinfo;

create table stuinfo (
	id int primary key, # 主键
    stuName varchar(20) not null unique,#非空
    gender char(1) check(gender='男' or gender='女'),#检查
	seat int unique,#唯一
    age int default 18,#默认约束
    majord int references major(id)#外键
);

create table  major (
	id int  primary key,
    majorName varchar(20)
);
#查看stuinfo中的所有索引，包括主键，外键，唯一
SHOW INDEX FROM stuinfo;

```

##### 1.2.2 添加标记约束

- 语法：在各个字段的最下面【constraint约束名】约束类型(字段名)

```mysql
table if exists stuinfo;
create table stuinfo(
	id int,
    stuname varchar(20),
    gender char(1),
    seat int,
    age int,
    majorid int,
	
    CONSTRAINT PK PRIMARY KEY(id),#主键
    CONSTRAINT uq UNIQUE(seat),#唯一键
    CONSTRAINT ck CHECK(gender='男' OR gender='女'),#检查
    CONSTRAINT fk_stuinfo_major foreign key(majorif) references major(id)#外键
);
SHOW INDEX FROM stuinfo;



```

- 通用的写法：

```mysql
CREATE TABLE IF NOT EXISTS stuinfo(
	id int primary key,
    stuname varchar(20),
    sex char(1),
    age int default 18,
    seat int unique,
    majorid int,
    constraint fk_stuinfo_major foreign key(majorid) references majorid(id)
);
create table 表名(
	字段名  字段类型  not null;#非空
    字段名  字段类型  primary key;#主键
    字段名  字段类型  unique,#默认
    字段名  字段类型  default 值,#默认
    constraint 约束名 foreign key(字段名) references 主表(被引用)

);
```

- 主键和唯一的区别

```mysql
		保证唯一性     是否允许为空    一个表中可以有多少个   是否允许组合
主键		√				×			至多有1个           √，但不推荐
唯一		√				√			可以有多个          √，但不推荐
```

#### 1.3 修改表时添加约束

- 语法

```mysql
#1.添加列级约束
alter table 表名 modify column 字段名 字段类型 新约束;

#2.添加表级约束
alter table 表名 add 【constraint 约束名】 约束类型(字段名) 【外键的引用】;
```

```mysql
drop table if exists stuinfo;
create table stuinfo(
	id int,
    stuname varchar(20),
    gender char(1),
    seat int,
    age int,
    majorid int
);
desc stuinfo;
#1.添加非空约束
alter table stuinfo modify column stuname varchar(20) not null;

#2.添加默认约束
alter table stuinfo modify column age int default 18;

#3.添加主键
alter table stuinfo modify column id int primary key;
alter table stuinfo add primary key(id);

#4.添加唯一索引
alter table stuinfo modify column seat int unique;

alter table stuinfo add uunique (seat);
#5.添加外键
alter table stuinfo add constraint fk_stuinfo_major foreign key(majorid) references major(id);

```

#### 1.4 修改表时删除约束

```mysql
#1.删除非空约束
alter table stuinfo modify column stuname varchar(20) null;
#2.删除默认约束
alter table stuinfo modify column age int;
#3.删除主键
alter table stuinfo drop primary key;
#4.删除唯一索引
alter table stuinfo drop index seat;
#5.删除主键
alter table stuinfo drop foreign key fk_stuinfo_major;

show index from stuinfo

```

#### 1.5 修改表时添加或删除约束的具体语法总结：

```mysql
#1、非空
#添加非空
alter table 表名 modify column 字段名 字段类型 not null;

#删除非空
alter table 表名 modify column 字段名 字段类型 ;

#2、默认
#添加默认
alter table 表名 modify column 字段名 字段类型 default 值;

#删除默认
alter table 表名 modify column 字段名 字段类型 ;

#3、主键
#添加主键
alter table 表名 add【 constraint 约束名】 primary key(字段名);

#删除主键
alter table 表名 drop primary key;

#4、唯一
#添加唯一索引
alter table 表名 add【 constraint 约束名】 unique(字段名);
#删除唯一
alter table 表名 drop index 索引名;

#5、外键
#添加外键
alter table 表名 add【 constraint 约束名】 foreign key(字段名) references 主表（被引用列）;

#删除外键
alter table 表名 drop foreign key 约束名;
```

### 二、自增长列（表示列）

#### 2.1 含义：

- 可以不用手动的插入值，系统提供默认的序列值

```mysql
1.不用手动插入值，可以自动提供序列值，默认从1开始，步长为1
  auto_increment_increment
  如果要更改起始值：手动插入值
  如果要更改步长：更改系统变量
  set auto_increment_increment=值;
2.一个表至多有一个自增长列
3.自增长列只能支持数值型
4.自增长列必须为一个key
```

- 案例

```mysql
drop table if exists tab_identify;
create table tab_identify(
	id int,
    name float unique auto_increment,
    seat int
)truncate table tab_identify;

insert into tab_identify(id,name) values(null,'john');

insert into tab_identify(name) values('lucy');

select * from tab_indentify;

show variables like '%auto_increment%';

set auto_increatement_increment=3;
```

#### 2.2 语法总结

```mysql
#一、创建表示设置自增长列
create table 表(
	字段名  字段类型  约束 auto_increment
);

#二、修改表时设置自增长列
alter table 表 modify column 字段名 字段烈性 约束 auto_increment;

#三、删除自增长列
alter table 表 modify column 字段名 字段类型 约束;

```

###  三、TCL语言

#### 3.1 事务控制语言

事务：一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行。

```mysql
案例：转账

张三丰  1000
郭襄	1000

update 表 set 张三丰的余额=500 where name='张三丰'
意外
update 表 set 郭襄的余额=1500 where name='郭襄'
```

#### 3.2 事务的特性(ACID)

- 原子性：一个事务不可以在分割，要么全部执行要么全都不执行
- 一致性：一个事务执行会使数据从一个一直状态切换到另外一个一致状态
- 隔离性：一个事务的执行不受其他事务的干扰
- 持久性：一个事务一旦提价，则会永久的改变数据库的数据

#### 3.3 事务的使用步骤：

- 隐式事务：没有明显的开启和结束，本身就是一条事务可以自动提交，比如insert，update，delete
- 显式事务：事务具有明显的开启和结束的标记；前提：必须先设置自动提交功能为禁用

```mysql
开启事务的语句;
update 表 set 张三丰的余额=500 where name='张三丰'

update 表 set 郭襄的余额=1500 where name='郭襄' 
结束事务的语句;
```

#### 3.4 具体步骤

```mysql
步骤1：开启事务
set autocommit = 0;
start transaction ;可选的

步骤2：编写事务中的sql语句(select insert update delete.....)
语句1;
语句2;
...
步骤3：事务结束
commit;事务提交
rollback;事务回滚
```

#### 3.5 事务操作案例：

```mysql
#查看是否开启事务
show variables like 'autocommit';
show engines;

#1.演示事务的使用步骤
drop table if exists account;
create table account(
	id int primary key auto_increment,
    username varchar(20),
    balance DOUBLE
)
insert into account(username,balance) values('张无忌',1000),('赵敏',1000);

#开启事务
set autocommit = 0;
start transaction;

#编写一组事务的语句
update account set balance = 1000 where username='张无忌';
update account set balance = 1000 where username='赵敏';

#提交事务
commit;

#事务回滚
rollback

select * from account;
```

#### 3.6 并发事务

- 事务的并发问题是如何发生的？

  多个事务同时操作 同一个数据库的相同数据时

- 并发问题都有哪些？

  1、 对于同时运行的多个事务，当这些事务方式数据库中相同的数据时，如果没有采取必要的隔离机制，就会导致各种并发问题：

  脏读：一个事务读取了其他事务还没有提交的数据，读到的是其他事务 “ 更新” 的数据；

  不可重复读：一个事务多次读取，结果不一样；

  幻读：一个事务读取了其他事务还没有提交的数据，只读取到的是 其他事务"插入"的数据。

- 数据库事务的隔离性：数据库系统必须具有隔离并发运行各个事务的能力，使它们不会互相影响，避免各种并发问题。
- 一个事务与其他事务隔离的程度称为蛤蜊级别 数据库规定了多种事务隔离级别，不同隔离界别对应不同的干扰程度，隔离级别约到，数据一致性就越好，但并发性越弱。

- 如何解决并发问题？

  通过设置蛤蜊级别来解决并发问题

- 隔离级别

```mysql
							脏读			不可重复读		  幻读
read uncommitted:读未提交     ×                ×              ×        
read committed：读已提交      √                ×              ×
repeatable read：可重复读     √                √              ×
serializable：串行化          √                √              √

```

- Mysql支持4种事务隔离级别。Mysql默认的事务隔离级别为：REPEATABLE READ

```mysql
#查看隔离级别
select @@tx_isolation;
#设置隔离级别
set session | global transaction isolation level隔离级别;

#2.演示事务对于delete 和 truncate 的处理的区别
set autocommit = 0;
start transaction;

delete from account;
rollback;

```

#### 3.7 savepoint 节点名;设置保存点

```mysql
#3.演示savepoint 的使用
SET autocommit=0;

start transaction;
delete from account where id=25;
savepoint a;#设置保存点
delete from account where id=28;
rollback to a;#回滚到保存点

```

### 四、视图

#### 4.1 什么是视图

- 虚拟表和普通表一样使用。

- 应用场景：

  多个地方用到同样的查询结果

  该查询结果使用的sql语句比较复杂

````mysql
select stuname，majorname from stuinfo s inner join major m ON s.majorid = m.majorid where s.stuname like '张%';

create view  v1 AS select stuname,majorname from stuinfo s inner join major m on s.majorid = m.id;

select * from v1 where stuname like '张%';


````

#### 4.2 视图的创建

- 语法：create view 视图名 as 查询语句;
- 案例：

```mysql
USE myemployees;

#1.查询姓名中包含a字符的员工名，部门和工种信息
create view myview AS select last_name,department_name,job_title from employees e join departments d on e.department_id = d.department_id join jobs j on j.hob_id = e.job_id;

#2.使用
select * from myview where last_name LIKE '%a%';

#3.查询各部门的平均工资级别
#创建视图查看每个部门的平均工资

create view myview2 as select avg(salary) ag,department_id from employees group by department_id;

select myview2.ag,g.grade_level from myview2 join job_grades g on myv2.ag between g.lowest_sal and g.highest_sal;


#4.查询平均工资最低的部门信息
select * from myview2 order by ag limit 1;

#4.查询平均工资最低的部门名和工资
create view v3 as select * from myview2 order by ag limit 1;

select d.*,m.ag from v3 m join departments d on m.department_id = d.department_id;

```

#### 4.3 视图的好处

- 重用sql语句
- 简化复杂的sql操作，不必知道他的查询细节
- 保护数据，提供安全性

#### 4.4 视图的修改

```mysql
方式一：
create or replace view 视图名 as 查询语句;
方式二：
alter view 视图名  as 查询语句;
```

```mysql
/*
create or replace view 视图名 as 查询语句;
*/
select * from myview3 

create or replace view myview select avg(salary),job_id from employees group by job_id;


#方式二：
/*
alter view 视图名 as 查询语句;
*/

alter view myview3 as select * from employees;

```

#### 4.5 视图的删除

- 用户可以一次删除一个或者多个视图，前提是必须有该视图的drop权限。
- 语法：drop  view  视图1,视图2,......

- 案例

```mysql
/*
语法 ：drop view 视图名,视图名, ....;
*/
DROP　view emp_v1,emp_v2,emp_v3;
```

#### 4.6 视图的查看

```mysql
desc  视图名;
show create view 视图名;
```

```mysql
desc myview3;
show create view myview;
```

#### 4.7 视图的更新

- 插入：insert
- 修改：update
- 删除：delete
- 查看：select

```mysql
create or replace view myv1 as select last_name ,email,salary*12*(1+IFFULL(commission_PCT,0)) "annual salary" from employees;

create or replace view myv1 as select last_name,email from employees;

select * from myv1;
select * from employees;

insert into myv1 values('张飞','zf@qq.com');

#2.修改
update myv1 set last_name = '张无忌' where last_name='张飞';
#3.删除
delete from myview3 where last_name = '张无忌';

```

注意：视图一般用于查询的，而不是更新的，所以具备以下特点的司徒都不允许更新

- 包含以下关键字的sql语句：分组函数，distinct，group by，having，union或者 union all 
- 常量视图
- Select中包含子查询
- join
- from一个不能更新的视图
- where子句的子查询引用了from子句中的表

```mysql
#具备以下特点的视图不允许更新
#包含以下关键字的sql语句：分组函数，distinct，group by,having ,union 或者 union all

create or replace view myview as select MAX(salary) m,department_id from employees group by department_id;

#查询视图
select * from myview;

#更新
update myview set m=9000 where department_id=10;

#常量视图
create or replace view myview as select 'john' name;

select * from myview;

#更新
update myview set name = 'lucy';

#Select 中包含子查询
create or replace view myview as select department_id,(select MAX(salary) from employees)最高工资 from departments;

#更新
select * from myview update myview set 最高工资=10000;

#join
create or replace view myview4 as select last_name,department_name from employees e join department d on e.department_id = d.depaetment_id;

#更新
select * from myview4;

update myview4 set last_name = '张飞' where last_name='whalen';

insert into myview4 values('陈真','xxx');

#from一个不能更新的视图
create or replace view myview5 as select * from myview4;

#更新
select * from myview5 update myview5 set 最高工资=1000 where department_id=60;

#where 子句的子查询引用了from子句中的表
create or replace view myview6 as select last_name,email,salary from employees where employee_id in (select manager_id from employees where employees where manager_id is not null);

#更新
select * from myview6;

```

#### 4.8 视图和表的对比

```mysql
		关键字		    是否占用物理空间			   使用
视图	   view		   占用较小，只保存sql逻辑	    一般用于查询
表	    table	     保存实际的数据			    增删改查
```



























