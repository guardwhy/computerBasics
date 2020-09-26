## DQL:数据库查询语言

### 1-简单查询(无条件查询)

#### 1.1-查询所有的数据

```properties
select * from 表名;
```

#### 1.2-查询指定的多个列

```properties
select 列名1，列名2 from 表名;
```

**案列语句**

```mysql
-- 使用数据库
USE test1;

-- 查询数据表
SELECT * FROM student;

-- 查询student表中的id和name列
SELECT id, name FROM student;

-- 没有指定条件，查询的是所有的行。条件是对行进行过滤
SELECT name, birthday FROM student;
```

#### 1.2-指定列的别名

##### 1.2.1-AS关键字

**案列语句**

```mysql
-- 使用别名,定义别名关键字是:AS
SELECT id AS 编号, name AS 姓名 FROM student;
-- AS关键字可以省略
SELECT name 姓名, birthday 生日 FROM student;
```

#### 1.3-清除重复值

语法：

```properties
select distinct 1个或多个字段名 from 表名
```

**案列语句**

```mysql
-- 添加一个字段address VARCHAR(50)
ALTER TABLE student ADD address VARCHAR(50);

UPDATE student SET address = "广州";

-- 查询学生来至于哪些地方,并且去掉重复行
SELECT address FROM student;
SELECT DISTINCT address FROM student;

-- 查询学生的姓名和地址,去掉重复行.必须几个列都相同,才会去除
SELECT DISTINCT name, address FROM student;
```

#### 1.4-查询结果参与运算

注意: 参与运算的必须是数值类型

##### 1.4.1-某列数据和固定值运算

```mysql
select 列+数值 from 表名
```

##### 1.4.2-某列数据和其他列数据参与运算

```properties
select 列表1+列表2 from 表名
```

**案列语句**

```mysql
-- 使用数据库
USE test1;

-- 查询数据表
SELECT * FROM student;

-- 修改student表结构,添加数学和英语成绩列
ALTER TABLE student ADD math int, ADD English int;

-- 将student中的math字段名改成Math，类型为INT
ALTER TABLE student CHANGE math Math INT;

-- 查询name Math字段
select name, Math FROM student;

-- 更新数据
UPDATE student set math = "90", English = "96" WHERE id = 1;
UPDATE student set math = "99", English = "95" WHERE id = 2;
UPDATE student set math = "91", English = "88" WHERE id = 3;
UPDATE student set math = "90", English = "96" WHERE id = 4;
UPDATE student set math = "95", English = "100" WHERE id = 5;
UPDATE student set math = "98", English = "98" WHERE id = 6;

-- 只影响查询结果
SELECT name, math-10 from student;

-- 查询所有列与math + english的和并使用别名”总成绩”
select *,(math+english) 总成绩 from student;
```

### 2-条件查询

#### 2.1-运算符查询

**简单语法**：

```properties
select 列表 from 表名 where 条件
```

##### 2.1.1-比较运算符

```properties
>、<、<=、>=、=、<> 
<> 在SQL中表示不等于，mysql中也可以使用!=
= 等于
```

##### 2.1.2-逻辑运算符

| **and** **或 &&**  | 全真为真 |
| ------------------ | -------- |
| **or** **或 \|\|** | 见真为真 |
| **not** **或 !**   | 取反     |

**案列语句**

```mysql
-- 使用数据库
USE test1;

-- 创建数据表
CREATE TABLE stu3(
	id INT, -- 编号
	NAME VARCHAR(20), -- 姓名
	age INT, -- 年龄
	sex VARCHAR(5), -- 性别
	address VARCHAR(100), -- 地址
	Math INT, -- 数学
	English INT -- 英语
);

-- 插入数据
INSERT INTO stu3(id,NAME,age,sex,address,Math,English) VALUES (1,'刘云',18,'男','广州',66,78),(2,'刘涛',30,'女','深圳',98,87),
																															(3,'马小天',35,'男','北京',56,77),(4,'柳云',20,'女','湖南',76,65),
																															(5,'张晓天',20,'女','北京',86,NULL),(6,'侯大利',27,'男','重庆',99,99),
																															(7,'田甜',42,'女','重庆',99,99),(8,'朱琳',50,'男','南京',56,65);
																															
-- 查询数据表
SELECT * FROM stu3;

-- 查询math分数大于80分的学生
select * from stu3 where Math > 80;

-- 查询english分数小于或等于78分的学生
SELECT * FROM stu3 WHERE Math <= 77;

-- 查询age等于20岁的学生
SELECT * FROM stu3 WHERE age = 20;

-- 查询age不等于20岁的学生
SELECT *FROM stu3 WHERE age <>20;
SELECT * FROM stu3 where age!= 20;

-- 查询age大于35且性别为男的学生(两个条件同时满足)
select * FROM stu3 where age>35 && sex="男";
select * FROM stu3 where age>35 AND sex="男";

-- 查询age大于35或性别为男的学生(两个条件其中一个满足)
SELECT * FROM stu3 where age > 35 or sex = "男";

-- 查询id是1或者3或者5的学生
SELECT * FROM stu3 WHERE id = 1 OR id = 3 OR id = 5;
```

#### 2.2-关键字查询

##### 2.2.1-in关键字 

**语法**: **in**里面的每个数据都会作为一次匹配条件,只要满足条件的就会显示。

```properties
select 列名 from 表名 where 列名 in (值1，值2，值3);
```

##### 2.2.2-范围查询

**语法**：表示从值1到值2范围，包头又包尾

```properties
select 列名 from 表名 where 列名 between 小值 and 大值
```

##### 2.2.3-like关键字

**语法**：用于字符串的模糊查询

```properties
select 列名 from 表名 where 列名 like '字符串'
```

##### 2.2.4-MySQL通配符

**语法：**如果没有通配符，相当于<font color=red>**等于号**</font>。char, varchar

| **通配符** | **说明**     |
| ---------- | ------------ |
| **%**      | 匹配多个字符 |
| **_**      | 匹配一个字符 |

##### 2.2.5-查询为空的列

**语法**：判断列是否为空

```properties
IS NULL 不写成=NULL
```

**案列语句**

```mysql
# 模糊查询

-- 查询姓马的学生
SELECT * from stu3 where `NAME` LIKE '刘%';
-- 查询姓名中包含'天'的姓名
SELECT * FROM stu3 WHERE name LIKE "%天%";
-- 查询姓刘,且姓名中有2个字的学生
SELECT * FROM stu3 where `NAME` LIKE "刘_";

# 查询为空的列

-- 查询英文成绩为NULL的学生
SELECT * FROM stu3 WHERE English IS	NULL;
-- 查询英语成绩不为NULL的学生
SELECT * FROM stu3 WHERE English IS NOT	NULL;
-- 查询姓名和英语成绩，如果英语为null，则显示为0分
SELECT `NAME`,English FROM stu3;
-- IFNULL(列名,默认值): 如果这一列有值,则显示它的值,如果为NULL,则显示后面默认值
SELECT name, English(English,0) 英语 FROM stu3;
```

#### 2.3-查询结果排序

**语法：**对指定的列进行排序，默认是asc升序。

```properties
select 列名 from 表名 order by 列名 asc/desc		升序:asc 	降序:desc
```

**案列语句**：

```mysql
# 排序
-- 查询所有数据,使用年龄降序排序
SELECT * FROM stu3 ORDER BY age DESC;
-- 查询所有数据大于20岁的学生,在年龄降序排序的基础上，如果年龄相同再以数学成绩升序排序
SELECT * FROM stu3 ORDER BY age , Math ASC
```

#### 2.4-聚合函数

| **SQL中的聚合函数** | **作用**                     |
| ------------------- | ---------------------------- |
| count(列名)         | 统计个数(行)                 |
| sum(列名)           | 求和，对数值类型的列求和     |
| avg(列名)           | 求平均，对数值类型的列求平均 |
| max(列名)           | 求这一列中最大值             |
| min(列名)           | 求这一列中最小值             |

**案例语句**：

```mysql
# 聚合函数
-- 查询学生总数
SELECT COUNT(id) FROM stu3;	-- 8
SELECT COUNT(English) FROM stu3;	-- 7
SELECT count(*) FROM stu3; -- 8

-- 查询年龄大于40的总数
SELECT COUNT(id) FROM stu3 WHERE age > 40;	-- 2

-- 查询数学成绩总分
SELECT SUM(Math) 数学总分 FROM stu3;	-- 636

-- 查询数学成绩的平均分
SELECT AVG(Math) 数学平均分 FROM stu3;	-- 79.5000

-- 查询数学成绩最高分
SELECT MAX(Math) 数学最高分 FROM stu3;	-- 99

-- 查询数学成绩最低分
SELECT MIN(Math) 数学最低分 FROM stu3;	-- 56
```

#### 2.5-分组查询

**语法**：对表中所有的行进行分组查询，默认是返回每一组的第一行记录，可以使用having对分组后的结果再进行过滤。

```properties
select 列名 from 表名 group by 列名 having 条件
```

**注意:**MySQL5.7以上版本使用group by关键字出现报错。

```properties
解决方案：
找到数据库配置文件 my.cnf，在里面的[mysqld] 中添加以下语句:
sql_mode = "STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION"
```

**having与where的区别:**

| 关键字             | 功能                                                         |
| ------------------ | ------------------------------------------------------------ |
| **where** **子句** | 1. 先过滤，再进行分组<br />2. where后面不能使用聚合函数      |
| **having子句**     | 1. 先分组，再进行过滤<br />2. having后面可以使用聚合函数，having用在group by后面 |

##### 2.5.1-直接分组

将分组字段结果中相同内容作为一组，如按性别将学生分成2组。

```mysql
-- 按性别分组,查询每组的第一条记录
select * from stu3 group by sex ;
```

##### 2.5.2-和聚合函数一起使用

```mysql
-- 按性别分组,求男女数学平均分
SELECT sex, AVG(Math) FROM stu3 GROUP BY sex;
-- 按性别分组,求男女数学总和
SELECT sex, SUM(Math) FROM stu3 GROUP BY sex;

-- 查询年龄大于25岁的人,按性别分组,统计每组的人数
SELECT sex, COUNT(*) FROM stu3 WHERE age > 25 GROUP BY sex;
```

##### 2.5.3-对分组后的结果过滤

```mysql
-- 查询年龄大于25岁的人，按性别分组，统计每组的人数，并只显示性别人数大于2的数据
SELECT sex, COUNT(*) FROM stu3 WHERE age > 25 GROUP BY sex HAVING COUNT(*) > 2;
```

### 3-分页操作

#### 3.1-limit的作用

**语法**：限制从服务器返回的数据行数，通常用于分页操作。

**案例语句**：

```mysql
SELECT * from stu3;

-- 插入数据
INSERT INTO stu3(id,NAME,age,sex,address,Math,English) VALUES 
(9,'候国龙',50,'男','江州',87,78),
(10,'黄晓军',18,'男','广州',100,66),
(11,'杜强',22,'男','江州',58,78),
(12,'石秋菊',50,'男','扬州',77,88),
(13,'朱倩',22,'女','北京',66,66),
(14,'张晓欣',23,'女','江州',88,88);

# limit的作用
-- 返回0到3这三条记录 (起始行,返回行数)
SELECT * from stu3 LIMIT 0,3;

-- 查询学生表中数据，从第3条开始显示，显示6条。
SELECT * FROM stu3 LIMIT 2,6;

-- 每页显示5条
SELECT * FROM stu3 LIMIT 0,5;
SELECT * FROM stu3 LIMIT 5,5;
SELECT * FROM stu3 LIMIT 10,5;
```

#### 3.2-增删改查关键字

| **操作类型**             | **增** | **删** | **改** | **查** |
| ------------------------ | ------ | ------ | ------ | ------ |
| **DDL对表和库的操作**    | create | drop   | alter  | show   |
| **DML和DQL对记录的操作** | insert | delete | update | select |