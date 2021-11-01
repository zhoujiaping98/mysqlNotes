## 							Mysql 学习笔记

### 一、DML语言

#### 1.1 数据操作语言

- 插入：insert
- 修改：update
- 删除：delete

### 二、插入语句

#### 1.1 方式一

- 经典的插入

#### 1.2 语法

- insert   into   表名(字段名,....) values(值,....);

#### 1.3 特点

- 要求值的类型和字段的类型要一直或兼容；

- 字段的个数和顺序不一定与原始表中的字段个数和顺序一致但必须保证值和字段意义对应；

- 假如表中有可以为null的字段，注意可以通过以下两种方式插入  null 值

  ①：字段和值都省略

  ②：字段写上，值使用null

- 字段和值的个数必须一致
- 字段可以省略，默认所有列

```mysql
SELECT * FROM BEAUTY;
#1.插入的值的类型要与列的烈性一致或兼容
insert into beauty(id,name,sex,borndate,phone,photoboyfriend_id) values(13,'唐艺昕','女','1990-4-23','189000000',null,2);

#2.不可以为null的列必须插入值。可以为null的列如何插入值？
    #方法一：
    INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
    VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);

    #方法二：
    INSERT INTO beauty(id,NAME,sex,phone)
    VALUES(15,'娜扎','女','1388888888');

#3.列的顺序是否可以调换
INSERT INTO beauty(NAME,sex,id,phone)
VALUES('蒋欣','女',16,'110');

#4.列数和值的个数必须一致

INSERT INTO beauty(NAME,sex,id,phone)
VALUES('关晓彤','女',17,'110');

#5.可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致

INSERT INTO beauty
VALUES(18,'张飞','男',NULL,'119',NULL,NULL);

```

- 方式 二语法：insert  into  表名 set 列名=值，列名=值，...
- INSERT INTO beautyset id = 19,name = '刘涛',phone='9999' ;
- 两种方式的区别

```mysql
1.方式一支持一次插入多行，语法如下：
insert into 表名【(字段名,..)】 values(值，..),(值，...),...;
2.方式一支持子查询，语法如下：
    insert into 表名 查询语句;
    
#1、方式一支持插入多行,方式二不支持

INSERT INTO beauty
VALUES(23,'唐艺昕1','女','1990-4-23','1898888888',NULL,2)
,(24,'唐艺昕2','女','1990-4-23','1898888888',NULL,2)
,(25,'唐艺昕3','女','1990-4-23','1898888888',NULL,2);

#2、方式一支持子查询，方式二不支持

INSERT INTO beauty(id,NAME,phone) SELECT 26,'宋茜','11809866';

INSERT INTO beauty(id,NAME,phone) 
SELECT id,boyname,'1234567' FROM boys WHERE id<3;       

```

### 三、修改语句

#### 3.1 修改表单的记录

- 语法：update  表名 set 列 = 新值，列 = 新值,.....where  筛选条件；

```mysql
#1.修改单表的记录
#案例1：修改beauty表中姓唐的女神的电话为13899888899
UPDATE beauty SET phone = '13899888899'
WHERE NAME LIKE '唐%';

#案例2：修改boys表中id好为2的名称为张飞，魅力值 10
UPDATE boys SET boyname='张飞',usercp=10
WHERE id=2;

```

#### 3.2 修改多表记录

```mysql
#2.修改多表的记录
#案例 1：修改张无忌的女朋友的手机号为114
UPDATE boys bo
INNER JOIN beauty b ON bo.`id`=b.`boyfriend_id`
SET b.`phone`='119',bo.`userCP`=1000
WHERE bo.`boyName`='张无忌';

#案例2：修改没有男朋友的女神的男朋友编号都为2号
UPDATE boys bo
RIGHT JOIN beauty b ON bo.`id`=b.`boyfriend_id`
SET b.`boyfriend_id`=2 WHERE bo.`id` IS NULL;

```

#### 3.3 删除语句

```mysql
1、单表的删除【★】
delete from 表名 where 筛选条件

2、多表的删除【补充】

sql92语法：
delete 表1的别名,表2的别名
from 表1 别名,表2 别名
where 连接条件
and 筛选条件;

sql99语法：
delete 表1的别名,表2的别名
from 表1 别名
inner|left|right join 表2 别名 on 连接条件
where 筛选条件;
```

```gas
#1.单表的删除
#案例：删除手机号以9结尾的女神信息
DELETE FROM beauty WHERE phone LIKE '%9';
SELECT * FROM beauty;

#2.多表的删除
#案例：删除张无忌的女朋友的信息
DELETE b
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName`='张无忌';

#案例：删除黄晓明的信息以及他女朋友的信息
DELETE b,bo
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id`=bo.`id`
WHERE bo.`boyName`='黄晓明';

```

#### 3.4 truncate

```mysql
语法：truncate table 表名;
#案例：将魅力值>100的男神信息删除
TRUNCATE TABLE boys ;
```

```mysql
1.delete 可以加where 条件，truncate不能加
2.truncate删除，效率高一点
3.假如要删除的表中有自增长列，
  如果用delete删除后，再插入数据，自增长列的值从断点开始，而truncate删除后，再插入数据，自增长列的值从1开始。
4.truncate删除没有返回值，delete删除有返回值
5.truncate删除不能回滚，delete删除可以回滚.
```

### 四、DDL语言

#### 4.1 库的管理

```mysql
create database 【if  not  exists 】库名 【character set 字符集名】
# 案例：创建 Books
create  database if not exists books;
```

#### 4.2 修改库

```mysql
alter database 库名 character set 字符集名;
# 案例： 更改库的字符集
alter database books character set gbk;
```

#### 4.3 删除库

```mysql
drop database 【if exists】 库名;
#案例：库的删除
DROP DATABASE IF EXISTS books;
```

### 五、表的管理

#### 5.1 表的创建

```mysql
/*
语法：
create table 表名(
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	列名 列的类型【(长度) 约束】,
	...
	列名 列的类型【(长度) 约束】
)
*/
#案例：创建表Book
CREATE TABLE book (
  id INT,
  #编号
  bName VARCHAR (20),
  #图书名
  price DOUBLE,
  #价格
  authorId INT,
  #作者编号
  publishDate DATETIME#出版日期
) ;

#查看表的   结构
desc book;

#案例：创建表author
CREATE TABLE IF NOT EXISTS author (
  id INT,
  au_n`author`ame VARCHAR (20),
  nation VARCHAR (10)
);
DESC author ;

```

 #### 5.2 表修改

```mysql

1.添加列
alter table 表名 add column 列名 类型 【first|after 字段名】;
2.修改列的类型或约束
alter table 表名 modify column 列名 新类型 【新约束】;
3.修改列名
alter table 表名 change column 旧列名 新列名 类型;
4 .删除列
alter table 表名 drop column 列名;
5.修改表名
alter table 表名 rename 【to】 新表名;

案例：
#①修改列名
ALTER TABLE book CHANGE COLUMN publishdate pubDate DATETIME ;

#②修改列的类型或约束
ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;

#③添加新列
ALTER TABLE author ADD COLUMN annual DOUBLE; 

#④删除列
ALTER TABLE book_author DROP COLUMN  annual;

#⑤修改表名
ALTER TABLE book_author RENAME TO author;

DESC book;
```

#### 5.3 表的删除

```mysql
drop table【if exists】 表名;

案例：
DROP TABLE IF EXISTS book_author;

SHOW TABLES;

#通用的写法：
DROP DATABASE IF EXISTS 旧库名;
CREATE DATABASE 新库名 ;

DROP TABLE IF EXISTS 旧表名;
CREATE TABLE  表名();
```

#### 5.4 复制表

```mysql
1、复制表的结构
create table 表名 like 旧表;
2、复制表的结构+数据
create table 表名 
select 查询列表 from 旧表【where 筛选】;

案例
INSERT INTO author VALUES
(1,'村上春树','日本'),
(2,'莫言','中国'),
(3,'冯唐','中国'),
(4,'金庸','中国');

SELECT * FROM Author;
SELECT * FROM copy2;

#1.仅仅复制表的结构
CREATE TABLE copy LIKE author;

#2.复制表的结构+数据
CREATE TABLE copy2 SELECT * FROM author;

#只复制部分数据
CREATE TABLE copy3 SELECT id,au_name
FROM author WHERE nation='中国';

#仅仅复制某些字段
CREATE TABLE copy4 SELECT id,au_name
FROM author WHERE 0;

```

#### 六、数据类型

#### 6.1 数值类型

```mysql
tinyint、smallint、mediumint、int/integer、bigint
1	       2		   3	       4		 8
```

- 特点：
-  如果不设置无符号还是有符号，默认是有符号，如果想设置无符号，需要添加unsigned关键字
-  如果插入的数值超出了整型的范围,会报out of range异常，并且插入临界值
-  如果不设置长度，会有默认的长度
  长度代表了显示的最大宽度，如果不够会用0在左边填充，但必须搭配zerofill使用！

```mysql
#1.如何设置无符号和有符号
DROP TABLE IF EXISTS tab_int ;

CREATE TABLE tab_int (t1 INT (7) ZEROFILL, t2 INT (7) ZEROFILL) ;

DESC tab_int ;

INSERT INTO tab_int VALUES (- 123456) ;

INSERT INTO tab_int VALUES (- 123456, - 123456) ;

INSERT INTO tab_int VALUES (2147483648, 4294967296) ;

INSERT INTO tab_int VALUES (123, 123) ;

SELECT * FROM tab_int ;
```

#### 6.2 浮点型

- 定点数：dec(M,D)；decimal(M,D)

- 浮点数：float(M,D) 4；double(M,D) 8

- 特点：
- ①M代表整数部位+小数部位的个数，D代表小数部位
  ②如果超出范围，则报out or range异常，并且插入临界值
  ③M和D都可以省略，但对于定点数，M默认为10，D默认为0
  ④如果精度要求较高，则优先考虑使用定点数

```mysql
#测试M和D
DROP TABLE tab_float ;

CREATE TABLE tab_float (f1 FLOAT, f2 DOUBLE, f3 DECIMAL) ;

SELECT * FROM tab_float ;

DESC tab_float ;

INSERT INTO tab_float VALUES(123.4523,123.4523,123.4523);
INSERT INTO tab_float VALUES(123.456,123.456,123.456);
INSERT INTO tab_float VALUES(123.4,123.4,123.4);
INSERT INTO tab_float VALUES(1523.4,1523.4,1523.4);

#原则：
#所选择的类型越简单越好，能保存数值的类型越小越好
```

#### 6.3 字符型

- 较短的文本：char、varchar

- 其他：
  1. binary和varbinary用于保存较短的二进制
  2. enum用于保存枚举
  3. set用于保存集合

- 较长的文本：text、blob(较大的二进制)

```mysql
 		写法			M的意思								特点		空间的耗费	效率
	char	char(M)		最大的字符数，可以省略，默认为1	 固定长度的字符	比较耗费	 高

   varchar	varchar(M)	最大的字符数，不可以省略			可变长度的字符	   比较节省		低
```

```mysql
CREATE TABLE tab_char(
	c1 ENUM('a','b','c')
);

INSERT INTO tab_char VALUES('a');
INSERT INTO tab_char VALUES('b');
INSERT INTO tab_char VALUES('c');
INSERT INTO tab_char VALUES('m');
INSERT INTO tab_char VALUES('A');

SELECT * FROM tab_set;

CREATE TABLE tab_set(
	s1 SET('a','b','c','d')
);
INSERT INTO tab_set VALUES('a');
INSERT INTO tab_set VALUES('A,B');
INSERT INTO tab_set VALUES('a,c,d');
```

#### 6.4 日期类型

- 分类
  1. date只保存日期；
  2. time只保存时间；
  3. year只保存年；
  4. datetime保存日期+时间
  5. timestamp保存日期+时间；

```mysql
			字节		范围			时区等的影响
datetime	 8		1000——9999	       不受
timestamp	 4	    1970-2038	        受
```

```mysql
CREATE TABLE tab_date(
	t1 DATETIME,
	t2 TIMESTAMP
);

INSERT INTO tab_date VALUES(NOW(),NOW());

SELECT * FROM tab_date;

SHOW VARIABLES LIKE 'time_zone';

SET time_zone='+9:00';
```





























