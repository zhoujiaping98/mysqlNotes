#### 一、mysql mysqldump命令导出指定表的数据

##### 1.1 导出指定表的数据

```mysql
mysqldump -t database -u username -p password --tables table_name1 table_name2 >D:\db_script.sql
例如：
mysqldump -t gormDB -uroot -proot --tables auth_rule >D:\auth_rule.sql
```

##### 1.2 导出数据不导出表的结构

```mysql
mysqldump -t 【数据库名】-u username -p password > D:\xxx.sql
```

##### 1.3 导出指定表的结构

```mysql
mysqldump -d database -u username -p password --tables table_name1 table_name2 table_name3 >D:\db_script.sql
```

##### 1.4 导出表的数据及结构

```mysql
mysqldump database -u username -p password --tables table_name1 table_name2 table_name3 >D:\db_script.sql
```

##### 1.5 若 数据库中，某些表除外，其余表都需要导出

```mysql
mysqldump -h IP -u username -p password --default-character-set=utf8 --database database_name --ignore-table=database_name.table_name1  --ignore-table=database_name.table_name2 --ignore-table=database_name.table_name3 >D:\db_script.sql
```

#### 二、mysql 导入sql命令 导入.sql 文件及常用的命令

##### 2.1 连接数据库 mysql

```mysql
mysql -h 主机地址 -u 用户名 -p 用户密码
```

##### 2.2 进入到要导入 的数据库

```mysql
use 数据库名
```

##### 2.3 使用如下命令倒数.sql脚本

```mysql
mysql> source d:/path/../db.sql
```



























