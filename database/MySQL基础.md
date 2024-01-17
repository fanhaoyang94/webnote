## MySQL

### 1.下载安装

- 官网下载安装包[下载地址](https://downloads.mysql.com/archives/community/)

  1.1 解压安装

mysql-5.7.31-winx64.zip 是免安装版本，解压即可

- 解压至任意目录
- 解压的目录不要有中文路径

  1.2 进入安装目录创建 `my.ini` 配置文件

```
[mysqld]

# port
port=3306

# set basedir to your installation path 安装目录位置
basedir=D:\\application\\mysql-5.7.31-winx64

# set daatadir to the location of your data directory 设置你数据存储的位置
datadir=D:\\application\\mysql-5.7.31-winx64\\data
# skip-grant-tables=1
```

1.3 初始化数据库（MySQL 提供了管理功能、需要提供用户名密码进入、以及创建内部数据库，所以需要初始化）

- 使用管理员权限打开 cmd 终端
- 执行初始化命令
- 安装完成后 MySQL 安装目录自动生成 data 文件夹

```
>>> MySQL安装目录\bin\mysqld.exe --initialize-insecure

# "C:\Program Files\mysql-5.7.31-winx64\bin\mysqld.exe" --initialize-insecure
# 如果命令中存在控制 终端默认会隔断处理，所以当执行的目录存在控制时使用引号讲起引用代表是一个整体
```

### 2. 启动 MySQL

2.1 临时启动

```
>>> MySQL安装目录\bin\mysqld.exe
```

2.2 window 服务
（1）制作服务

```
# myService为定义的服务名称
>>> MySQL安装目录\bin\mysqld.exe --install myService

# 制作好服务即可使用终端启动/停止服务
>>> net start mysql57
>>> net stop mysql57
```

(2)桌面启动

- 打开任务管理器
- 服务-->打开服务
- 点一下某一个服务按下 M 键盘即可找到对应的 `mysql57` 然后选择启动或者关闭

### 3. 连接并发送指令（使用 MySQL 自带的工具）

```
>>> MySQL安装目录\bin\mysql.exe -h 127.0.0.1 -P 3306 -u root -p
```

(1)设置密码

```
set password = pasword('root123') //首次登录，设置密码
```

(2)重新打开终端

```
>>> MySQL安装目录\bin\mysql.exe -h 127.0.0.1 -P 3306 -u root -p
#提示书输入密码： root123

>>> show databases； // 查看已有的数据库
>>> exit; // 退出连接
```

可以将 `MySQL安装目录\bin\` 设置为全局变量然后即可使用 `mysql.exe` 或者 `mysql` (终端默认可以省略 `.exe` )执行更简洁；

### 4.忘记密码

```
-正常模式
    >>>mysql -u root -p
    >>>输入密码

-无授权模式
    >>>mysql -u root -p
    >>>
```

(1)改成无授权模式

- MySQL 服务停止

```
net stop mysql57
```

- 修改配置文件

```
# 添加上一句
skip-grant-tables=1
```

- 重新启动 MySQL 服务

```
net start mysql57
```

- 连接 MySQL 自带的工具（终端）

```
>>> mysql -u root -p
```

(2)修改密码

```
use mysql;

update user set authentication_string = password('root123'),passowrd_last_changed=now() where user='root';
```

(3)改回授权模式

- 停止 MySQL 服务
- 修改配置文件删除 `skip-grant-tables=1`
- 重新启动并连接，使用密码连接

### 5.MySQL 指令

5.1 查看数据库

```
# 查看已有数据库
show databases;

# 创建数据库(数据库后面的参数是指定utf-8的编码格式)
create database database_name DEFAULT CHARSET utf8 COLLATE utf8_general_ci;

# 删除数据库
drop database database_name;

# 进入数据库（进入数据库后不用退出可以直接使用该指令进入其他换数据库）
use database_name;

# 查看数据库下所有数据表
show tables;
```

5.2 数据表

```
# 进入数据库&查看表
use database_name;
show tables;

# 创建表(在终端中末尾加上分好后换行标识执行当前语句，没加分好则标识换行继续输入语句)
# create table 表名(
#   列名 类型,
#   列名 类型,
#   列名 类型
#)

create table table_name(id int, name varchar(16), age int) default charset=utf8;
#create table table_name(
#    id int,
#    name varchar(16),
#    age int
#) default charset=utf8;

# 删除表
drop table table_name;

# 查看定义表相关信息
desc table_name;
# mysql> desc table2;
# +-------+-------------+------+-----+---------+----------------+
# | Field | Type        | Null | Key | Default | Extra          |
# +-------+-------------+------+-----+---------+----------------+
# | id    | int(11)     | NO   | PRI | NULL    | auto_increment |
# | name  | varchar(16) | NO   |     | NULL    |                |
# | age   | int(11)     | NO   |     | 3       |                |
# +-------+-------------+------+-----+---------+----------------+
# 3 rows in set (0.00 sec)

```

5.2.1 外键约束

表 B 中某一列的值，必须是表 A 中已存在的 ID 值,如果不存在则报错

```
# create table 表名(
#   列名 类型,
#   列名 类型,
#   constrain 约束名(随意定义一半fk_开头) foreign key (被约束列名字) references 约束表名(约束列名)
#)

#create table table_name(
#    id int,
#    name varchar(16),
#    age int,
#    depart_id int,
#    constrain fk_user_depart foreign key (depart_id) references depart(id)
#) default charset=utf8;

```

5.3 常用创建表时对列的约束

1. 约束

- 列是否允许为空

```
create table table_name(
    id int,
    name varchar(16) not null, # 不许为空
    age int null #允许为空
) default charset=utf8;
```

- 默认值

```
create table table_name(
    id int,
    name varchar(16) not null, # 不许为空
    age int not null default 3 # 不许为空且默认为3
) default charset=utf8;
```

- 主键
  主键，表述数据库 ID。
  主键特点：值不能重复、不能为空、查询速度非常快。
  一般将主键设置成自增

```
create table table_name(
    id int not null auto_increment primary key, # 不许为空 值自增 并将此列设置为主键
    name varchar(16) not null, # 不许为空
    age int not null default 3 # 不许为空且默认为3
) default charset=utf8;
```

2. 类型

- tinyint/tinyint unsigned

```
tinyint             有符号，取值范围：-128 ~ 127（有正负）【默认】

tinyint unsigned    无符号，取值范围：0 ~ 255（只有正）
```

```
create table table_name(
    id int not null auto_increment primary key, # 不许为空 值自增 并将此列设置为主键
    name varchar(16) not null, # 不许为空
    age tinyint unsigned,

) default charset=utf8;
```

- int/int unsigned

```
int             表示有符号，取值范围：-2147483648 ~ 2147483648（有正负）【默认】

int unsigned    表示无符号，取值范围：0 ~ 4294967295（只有正）
```

- bigint

```
bigint             表示有符号，取值范围：-9223372036854775808 ~ 9223372036854775808（有正负）【默认】

bigint unsigned    表示无符号，取值范围：0 ~ 18446744073709552000（只有正）
```

- char(m)

定长字符串，用固定的长度保存字符串，最多可容纳 255 个字符。
应用场景：如果你确定数据长度固定，eg: 手机号

```
create table table_name(
    id int not null auto_increment primary key, # 不许为空 值自增 并将此列设置为主键
    name char(5), # 固定使用5个字符，即便不足5个也会占据5个，超长则异常
    age tinyint unsigned,

) default charset=utf8;
```

- varchar(m)

变长字符串，动态的存储数据。最大 65535 字节/3 = 最大的 m

- text

text 数据类型用户保存变长大字符串，可以最多到 65535(2\*\*16 - 1)个字符，不需要设定长度。

应用场景：一般情况下，长文本会用 text 类型。eg：文章、新闻等

```
# 拓展更长字符串类型 ，一般不会用，超大的会存为文件然后数据库中存储对应的路径
mediumtext
A TEXT column with a maximum length of 16,777,215(2**24-1) characters.
longtext
A TEXT column with a maximum length of 4,294,967,295 or 4GB (2**32-1) characters.

```

- datetime

YYYY-MM-DD HH:MM:SS（1000-01-01 00:00:00/9999-12-31 23:59:59）

- date

YYYY-MM-DD（1000-01-01/9999-12-31）

- float

单精度

- double

双精度

- decimal(m,d)

准确的小数值,m 是数字总个数（负号不算），d 是小数点后个数。m 最大值为 65，d 最大值为 30。

5.4 数据行操作

```
create table tb(
    id bigint unsigned not null auto_increment primary key,
    name varchar(16),
    mobile char(11),
    email varchar(128),
    salary decimal(10,2),
    ctime datetime
)default charset=utf8;

# 单行数据插入
#insert into 表名(列名,列名,...) values(值,值,...)
insert into table_name(name,mobile,email,salary,ctime) values('张三','15111111111','123@qq.com',1000,'2024-01-16');

# 多行添加
insert into table_name(name,mobile,email,salary,ctime) values('张三','15111111111','123@qq.com',1000,'2024-01-16'),('李四','15111111111','123@qq.com',1000,'2024-01-16'),('王五','15111111111','123@qq.com',1000,'2024-01-16')

# 删除
# delete from 表名;
# delete from 表名 where 条件;
delete from table_name; # 删除所有的
delete from table_name where id=4;
delete from table_name where id>4;
delete from table_name where id<4;
delete from table_name where id!=4;
delete from table_name where id in (1,2,3);
delete from table_name where id=4 and name='张三';

# 修改数据
# update 表名 set 列名=值;
# update 表名 set 列名=值 where 条件;
# update 表名 set 列名=值, 列名=值;
# update 表名 set 列名=值, 列名=值 where 条件;
update tb set mobile=15132222222;
update tb set email='234@qq.com' where name='张三';
update tb set email='239@qq.com', mobile='13711111111' where name='张三';

# 查询数据
select * from 表名;
select 列名,列名 from 表名;
select 列名,列名 from 表名 where 条件;

select * from tb;
select id,name,email from tb;
select id,name,email from tb where id > 10;
```

### navicat

- mac https://www.macdo.cn/17030.html
