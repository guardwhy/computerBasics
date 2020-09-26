## MySQL基础

### 1-SQL定义：

SQL：Structured Query Language 结构化的查询语言，所有关系型数据库通用的语言。用来对数据库和数据进行管理的命令。

### 2-SQL语句分类

1. **Data Definition Language(建库建表)**

   数据定义语言，简称DDL，用于对库和表的管理

2. **Data Manipulation Language(记录增删改)**

   数据操纵语言，简称DML，用于对记录的增删改

3. **Data Query Language(查询)**

   数据查询语言，简称DQL，用于对记录查询操作

4. **Data Control Language(用户的管理)**

   数据控制语言，简称为DCL，用于对用户的管理和权限的设置

### 3-DDL:数据库定义语言

#### 3.1-创建数据库基本语法

```mysql
-- 创建数据库
CREATE DATABASE db1;

-- 判断是否存在，如果不存在则创建数据库db2
create database if not exists db2;

-- 创建数据库并指定字符集为gbk
create database if not exists db3 character set gbk;
```

#### 3.2-查看数据库

```mysql
-- 查看数据库
SHOW DATABASES;
-- 查看某个数据库的信息
SHOW CREATE DATABASE db3;
```

#### 3.3-修改数据库

```mysql
-- 修改数据库
ALTER DATABASE db3 CHARACTER SET utf8;
-- 查看某个数据库的信息
SHOW CREATE DATABASE db3;
```

#### 3.4-删除数据库

```mysql
-- 创建db2数据库
CREATE DATABASE if not exists db2;
-- 删除db2数据库
DROP DATABASE db2;
-- 查看数据库
SHOW DATABASES;
```

#### 3.5-使用当前数据库

```mysql
-- 查看正在使用的数据库
SELECT	DATABASE();
-- 使用db3数据库
USE	db3;
```

#### 3.6-创建表结构

**基本语法**

```mysql
-- 字段名就是列名，字段类型：数据类型，约束。多个字段之间使用逗号来分隔
create table 表名 (
   字段名1 字段类型1 约束1,
   字段名2 字段类型2 约束2
)
```

**MySQL数据类型**

| 数据类型 | 关键字         |
| -------- | -------------- |
| 整型     | int或integer   |
| 浮点型   | double或float  |
| 字符串型 | varchar或char  |
| 日期类型 | date或datetime |

图示:

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/9-MySQL.png)

##### 3.6.1-创建表结构

```mysql
-- 创建数据库test1
create database if not exists test1;

-- 使用数据库test1
use test1;

-- 创建student表包含id整数,name变长字符串长20,sex性别定长型1,birthday字段日期类型
create table student(
	-- 字段名,字段类型
	id int,
	name VARCHAR(20),
	sex char(1),
	birthday date
);
```

##### 3.6.2-查看表结构

```mysql
-- 查看数据库的所有表
SHOW TABLES;

-- 查看某个表结构
DESC student;

-- 查看创建表的SQL语句 
SHOW CREATE TABLE student;

-- 创建相同的表结构
CREATE TABLE stu1 LIKE student;

-- 查看stu1表结构
DESC stu1;
```

##### 3.6.3-删除表结构

```mysql
-- 使用test1数据库
use test1
-- 显示数据库中所有表
show tables;

-- 创建数据表
create table stu2(
	-- 字段名,字段类型
	id int,
	name VARCHAR(20),
	sex char(1)
);

-- 直接删除表
DROP TABLE stu1;

-- 判断表是否存在并且删除stu2
DROP TABLE	if EXISTS stu2;
```

##### 3.6.4-修改表结构

**添加表列ADD**

```properties
alter table 表名 add 字段名 数据类型
```

**修改列类型MODIFY**

```properties
alter table 表名 modify 字段名 新的数据类型
```

**修改列名和类型 CHANGE**

```properties
alter table 表名 change 旧列名 新列名 数据类型;
```

**删除列 DROP**

```properties
alter table 表名 drop 列名;
```

**修改表名**

注：MySQL中没有直接修改库名的语句

```properties
rename table 旧表名 to 新的表名;
```

**案列语句**

```mysql
-- 查看student的结构
DESC student;

-- 为学生表添加一个新的字段remark,类型为char(20)
ALTER TABLE	student ADD	remark CHAR(20);

-- 将student表中的remark字段的改成varchar(100)
ALTER TABLE student MODIFY remark VARCHAR(100);

-- 将student表中的remark字段名改成intro，类型varchar(30)
ALTER TABLE	student CHANGE remark intro VARCHAR(30);

-- 删除student表中的字段intro
alter TABLE student DROP intro;

-- 将学生表student改成student2
RENAME TABLE student to student2;

-- 查看所有test01的数据表
SHOW TABLES;
```

### 4-DML:数据库操纵语言

#### 4.1-插入记录

##### 4.1.1-插入全部字段

```properties
insert into 表名 values(值1， 值2.....)
```

##### 4.1.2-插入部分字段

```properties
insert into 表名(列名1， 列名2) values(值1，值2);
```

注：没有添加数据的字段值为NULL

##### 4.1.3-插入多条记录

```properties
insert into 表名 values(值1，值2)，(值1，值2)，(值1，值2)
```

**案列语句**

```mysql
-- 查询表中的记录
SELECT * from student;

-- 向表中插入所有字段
INSERT INTO student VALUES(1, 'curry', '男','1988-3-14');
-- now() 函数表示现在的时间：同时包含日期和时间
INSERT INTO student VALUES(2, 'James', '男', now());

-- 插入部分数据，往学生表中添加id, name数据
INSERT INTO student(id, name) VALUES(3, 'Rondo');
delete from student where id=4;

-- 一条insert语句插入多条记录
INSERT INTO	student VALUES (4, "guardwhy", "男","1993-6-19"), (5, "邓肯", "男","1976-4-25"),(6, "kobe", "男","1978-8-23");
```

#### 4.2-更新表记录

##### 4.2.1-不带条件修改数据

修改所有行的记录

```properties
update 表名 set 字段名 = 值
```

##### 4.2.2-带条件修改数据

修改符合条件的数据

```properties
update 表名 set 字段名 = 值 where 条件
```

**案列语句**

```mysql
-- 查询表中的记录
SELECT * from stu2;

-- 为学生表添加一个新的字段birthday,类型为date
ALTER TABLE	stu2 ADD birthday date;

-- 插入数据
INSERT INTO	stu2 VALUES (1, "小刘", "男","1993-6-30"), (2, "李红", "女","1976-4-11"),(3, "小李", "男","1998-8-13");

-- 不带条件修改数据，将所有的性别改成女
UPDATE stu2 set sex = '女';

-- 带条件修改数据，将id号为2的学生性别改成男
UPDATE stu2 set sex= "男" where id = 2;

-- 一次修改多个列，把id为3的学生，修改性别为男，生日：1996-03-18
UPDATE stu2 set sex = "男", birthday = "1996-03-18" WHERE id = 3;
```

#### 4.3-删除表记录

##### 4.3.1-不带条件删除数据  

```properties
-- 删除表中所有的记录.
delete from 表名;
```

##### 4.3.2-带条件删除数据

```properties
-- 删除符合条件的记录
delete from 表名 where 条件
```

##### 4.3.3-删除表中所有记录

```mysql
-- 与前两条语法的区别:相当于先删除表结构:drop table 表名; 然后在创建一张相同结构的表:create table 表名;
truncate 表名;
```

**案列语句**

```mysql
use test1;

-- 查看表结构
SHOW TABLES;

-- 查看stu2表结构
select * from stu2;

-- 带条件删除数据，删除id为3的记录
DELETE from stu2 WHERE id = 3;

-- 不要带条件删除数据,删除表中的所有数据
DELETE from stu2;

-- 查看stu2表结构
SELECT * FROM stu2;

-- 插入多条记录
INSERT INTO	stu2 VALUES (1, "guardwhy", "男","1993-6-19"), (2, "邓肯", "男","1976-4-25"),(3, "kobe", "男","1978-8-23");

-- 删除表结构再创建表
truncate stu2;
```

