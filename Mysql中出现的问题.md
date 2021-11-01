## Mysql 笔记 ERROR 

### 一、The used command is not allowed with this MySQL version

#### 1.1 无法使用LOAD DATA 语法上传数据

本来想将txt/log文件中的数据传入数据库，但是出现报错：

![image](assets/20190406131558939.png)

错误的原因是没有开启local_infile模块。

#### 1.2 解决方法

首先看一下local_infile模块是否打开：

```msyql
show global variables like 'local_infile';
```

显示如下：

![image](assets/20190406131719389.png)

然后可以发现这个模块已经启用了：

![image](assets/20190406131859826.png)



然后还需要重启一下Mysql，首先要退出Mysql ，然后重新登录Mysql：

```mysql
mysql --local-infile=1 -uroot -p123456
```

这个时候在上传数据可以上传了：

```mysql
LOAD DATA LOCAL INFILE '文件位置' INTO TABLE pet;
```

```mysql
load data local infile 'D:/Temp/sql1.log' into table `tb_user_2` fields terminated by ',' lines terminated by '\n' ;
```

![image-20210321190809029](assets/image-20210321190809029.png)







































