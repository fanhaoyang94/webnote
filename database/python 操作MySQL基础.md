## 基于 py 链接 MySQL 并执行操作

```
# python本身无法连接MySQL需要依赖pymysql
pip install pymsql
```

py 文件内容

```
import pymysql
# 默认查询返回的是元组类型，导入此崔对象后将其传入游标对象查询结果即返回为字典列表
# from pymsql.cursors import DictCursor

# 链接数据库,如果添加了 database='database_name' 之后后续代码即可不用每次执行 cursor.execute('use database_name')
conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', charset='utf8', database='database_name')
cursor = conn.cursor(cursor=DictCursor) # 创建游标 ，参数是定义查询返回为字典列表
# 1.查看数据库
# cursor.execute('show databases;')
# result = cursor.fetchall()
# print(result) # 返回一个元组

# 2.创建数据库
# cursor.execute('create database database_name;');
# conn.commit()

# 3.删除数据库
# cursor.execute('drop database database_name;');
# conn.commit()

# 4.进入数据库&查看有哪些表
# cursor.execute('use database_name')
# cursor.execute('show tables')
# result = cursor.fetchall()
# print(result)

# 5.创建表
# cursor.execute('use database_name')
# sql = """
# create table table_name(
#     id int,
#     name carchar(16),
#     age int
# )default charset=utf8;
# """
# cursor.excute(sql)
# conn.commit

# 6.删除表
# cursor.execute('use database_name')
# cursor.execute('drop table table_name')
# conn.commit

# 7.数据行操作
# 插入数据
# sql = "insert into table_name(name,mobile,email,salary,ctime) values('张三','15111111111','123@qq.com',1000,'2024-01-16');"
# cursor.excute(sql)
# conn.commit

# 数据占位
# sql = "insert into table_name(name,mobile,email) values(%s,%s,%s);"
# cursor.excute(sql,[name,mobile,email]) # 第二个参数是对values中数据的格式化映射，即对应的变量名
# conn.commit

# 删除数据
# sql = "delete from table_name where id=5;"
# cursor.excute(sql)
# conn.commit

# 修改
sql = "update table_name set salary=2000,mobil='13731111111' where id=5 and name='李四';"
cursor.excute(sql)
conn.commit

# 查询 默认返回的是元组类型
# sql = "selece * from tb;"
# cursor.excute(sql)

# result = cursor.fetchall() # 查出所有
# print(result) # 元组

# result = cursor.fetchone() # 查出一个
# print(result)


cursor.close()
conn.close()
```

### 批量导入

account.txt

```
张三,123,15111111111
李四,123,15111111111
王五,123,15111111111
```

```
import pymysql
from pymsql.cursors import DictCursor

with open('account.txt',mode='r',encoding='utf-8') as f:
    for line in f:
        line = line.strip()
        if nor line:
            continue
        data_list = line.split(",") #[用户名,密码,手机号]

        # 链接
        conn = pymysql.connect(host='127.0.0.1', port=3306, user='root',passwd='root123' charset='utf8', database='database_name')
        cursor = conn.cursor(cursor=DictCursor)# 创建游标

        # 执行SQL
        sql = "insert into table_name(name,password,mobile) values(%s,%s,%s);"
        cursor.excute(sql,data_list)
        conn.commit
        # 关闭
        cursor.close()
        conn.close()
```

### 导出

```
import pymysql
from pymsql.cursors import DictCursor
# 链接
conn = pymysql.connect(host='127.0.0.1', port=3306, user='root',passwd='root123' charset='utf8', database='database_name')
cursor = conn.cursor(cursor=DictCursor)
# 执行SQL
sql = "select id,username,password,mobile from tb"

cursor.excute(sql)
data_list = cursor.fetchall()

# 关闭
cursor.close()
conn.close()

for item in data_list:
    line = "{},{},{}\n".format(item['username'],item["password"],item["mobile"])
    with open('account.txt',mode='a',encoding='utf-8') as f:
        f.write(line)
```

### 注意

一般情况下，开发项目，项目的表结构应该是提前创建好的。

- 先用工具创建相应的书库以及表结构。
- 运行程序，对数据表中的数据进行增删改查
