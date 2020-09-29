## 多表操作

### 表与表之间的关系

#### 一对一

A表和B表：A表中一条记录对应B表中一条记录。如：身份证 - 护照

| **一对一的建表原则** | **说明**                                                     |
| -------------------- | ------------------------------------------------------------ |
| **外键唯一**         | 将从表的外键添加唯一约束，变成了一对一的关系。其实是一个特殊的一对多的关系。 |
| **外键是主键**       |                                                              |

**架构图**

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/11-MySQL.png)

**案例语句**

```mysql
-- 创建数据库
CREATE DATABASE test2;

# 使用数据库
USE test2;

# 一对一的关系

-- 主表
CREATE TABLE stu1(
	id INT PRIMARY KEY, -- 主键
	NAME VARCHAR(20)
);

-- 从表
CREATE TABLE info(
	id INT PRIMARY key, -- 主键
	address VARCHAR(20),
	use_name VARCHAR(10),
	weight double,
	-- 创建外键,外键又是主键
	FOREIGN KEY(id) REFERENCES stu1(id)
);
```

#### 一对多

| **表与表之间的关系** | 记录之间的对应关系                                           |
| -------------------- | ------------------------------------------------------------ |
| 一对多               | A表一方，B表多方：<br />A表中一条记录对应了B表中多条记录。如：部门 - 员工<br />B表中一条记录对应了A表中一条记录 |

实现一个"线路分类"中有多个"旅游线路"的一对多的关系。(category 分类, route 线路)

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/13-MySQL.png)

**架构图**

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/14-MySQL.png)

**案例语句**

```mysql
# 一对多,一个分类对应多条线路

/* 
  创建旅游线路分类表category
*/
CREATE TABLE category(
	cid INT PRIMARY KEY auto_increment, -- cid旅游线路分类主键，自动增长
	cname VARCHAR(100) NOT NULL UNIQUE -- cname旅游线路分类名称非空，唯一，字符串100
);

DROP TABLE category;
DROP TABLE route;

/*
 创建旅游线路表route
*/
CREATE TABLE route(
	rid INT PRIMARY KEY auto_increment, -- rid旅游线路主键，自动增长
	rname VARCHAR(100) NOT NULL UNIQUE, -- 旅游路线名称
	price DOUBLE, -- 路线价格
	rdate DATE, -- 日期时间
	cid INT, -- 添加外键约束
	CONSTRAINT fk_cid FOREIGN KEY(cid)REFERENCES category(cid)
);

-- 删除外键约束
alter table route drop foreign key fk_cid;
```

#### 多对多

| **表与表之间的关系** | 记录之间的对应关系                                           |
| -------------------- | ------------------------------------------------------------ |
| 多对多               | A表中一条记录对应了B表中多条记录<br />B表中一条记录对应了A表中多条记录 |

**多对多关系建表原则**：

```properties
创建一张中间表，中间表从表，其它2张表都是主表。中间表与其它表之间是2个1对多个关系。
```

**案例语句**

一个用户收藏多个线路，一个线路被多个用户收藏，建表体现线路与用户之间的关系。用户与线路之间是多对多的关系。

```mysql
## 多对多关系


/*
 创建旅游线路表route
*/
CREATE TABLE route(
	rid INT PRIMARY KEY auto_increment, -- rid旅游线路主键，自动增长
	rname VARCHAR(100) NOT NULL UNIQUE, -- 旅游路线名称
	price DOUBLE, -- 路线价格
	rdate DATE, -- 日期时间
	cid INT, -- 添加外键约束
	CONSTRAINT fk_cid FOREIGN KEY(cid)REFERENCES category(cid)
);

/*
	旅游线路表
*/
CREATE TABLE route(
	rid INT PRIMARY KEY auto_increment, -- 主键
	rname VARCHAR(100) NOT NULL UNIQUE, -- 旅游路线名称
	price DOUBLE, -- 路线价格
	rdate DATE, -- 日期时间
	cid INT, -- 添加外键约束
	CONSTRAINT fk_cid FOREIGN KEY(cid)REFERENCES category(cid)
);


/*
	用户表user
*/
CREATE TABLE user(
	uid INT PRIMARY KEY auto_increment, -- uid用户主键,自增长
	username VARCHAR(100) UNIQUE NOT NULL,	-- 用户名长度100，唯一，非空
	password varchar(30) NOT NULL,	-- 密码长度30,为空....
	NAME VARCHAR(100),	-- 姓名长度
	birthday date, -- 生日
	sex CHAR(1),	-- sex性别,定长字符串1
	telephone VARCHAR(11), -- 手机号,字符串11
	email VARCHAR(100) -- email邮箱,字符串长度100
);

/*
	收藏表favorite
*/
CREATE TABLE favorite(
	date datetime, -- 收藏时间
	rid INT, -- 路线id的外键
	CONSTRAINT fk_rid FOREIGN KEY(rid)REFERENCES route(rid), -- 关联了线路的主键
	uid INT, -- 用户id的外键
	CONSTRAINT fk_uid FOREIGN KEY(uid)REFERENCES user(uid)	-- 关联了用户的主键
);
```

**架构图**

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/15-MySQL.png)

## 多表查询

### 内连接

#### 隐式内联接

**语法**：

```properties
select 列名 from 左表,右表 where 从表.外键=主表.主键
```

#### 显示内联接

**语法**：

```properties
-- 显示内连接, on后面就是表连接的条件
select 列名 from 左表 inner join 右表 on 从表.外键=主表.主键
```

**案例语句**

```mysql
### 笛卡尔积和内连接

-- 创建部门表
create table dept(
	id int primary key auto_increment, -- 部门主键id,自增长
	name varchar(20) -- 部门name长度
);

-- 插入数据
insert into dept(name) values("开发部"),("市场部"),("财务部");

-- 创建员工表
create table emp(
	id int primary key auto_increment,
	name varchar(10), -- 员工姓名长度
	gender char(1), -- 性别
	salary double, -- 工资
	join_date date, -- 入职日期
	dept_id int, -- 外键
	constraint fk_dept_id foreign key(dept_id) references dept(id) -- 关联了部门的主键
);

-- 插入数据
insert into emp(name,gender,salary,join_date,dept_id) values('侯大利','男',7200,'2013-02-24',1);
insert into emp(name,gender,salary,join_date,dept_id) values('龚建明','男',3600,'2010-12-02',2);
insert into emp(name,gender,salary,join_date,dept_id) values('朱琳','男',9000,'2008-08-08',2);
insert into emp(name,gender,salary,join_date,dept_id) values('田甜','女',5000,'2015-10-07',3);
insert into emp(name,gender,salary,join_date,dept_id) values('杨红','女',4500,'2011-03-14',1);

### 需求：查询所有的员工和所有的部门
select * from emp, dept; -- (产生笛卡尔积)
## 消除笛卡尔积

-- 隐式内连接(消除笛卡尔积) 从表.外键 = 主表.主键
select * from emp, dept where emp.dept_id = dept.id;
-- 给表起别名
select * from emp E, dept D where E.dept_id = D.id;

-- 显示内连接,on后面就是表连接的条件
select * from emp E inner join dept D on E.dept_id = D.id;
```

**需求实现**

```mysql
## 1.只显示两列
select E.name 员工名, D.name 部门名 from emp E, dept D where E.dept_id = D.id and E.id = 1;

## 2.查询朱琳在哪个部门名字
select * from emp E, dept D where E.dept_id = D.id and E.id = 3;

## 3.查询田甜的信息，显示员工id，姓名，性别，工资和所在的部门名称
-- 3.1.确定查询哪些表
select * from emp E inner join dept D;
-- 3.2.确定表连接的条件
select * from emp E inner join dept D on E.dept_id = D.id;
-- 3.3. 如果有其它的查询条件，添加条件
select * from emp E inner join dept D on E.dept_id = D.id where E.`name` = "田甜";
-- 3.4.确定查询列
select E.id 编号, E.`name`姓名, E.gender 性别, E.salary 工资, D.`NAME` 部门名 
from emp E inner join dept D on E.dept_id = D.id where E.`name` = "田甜";
```

### 外连接

#### 左连接

**语法**

```properties
select 列名 from 左表 left join 右表 on 从表.外键=主表.主键
```

**案例语句**

```mysql
## 需求：在部门表中增加一个销售部,将部门表设置成左表,员工表设置成右表.

select * from dept;
-- 插入数据
insert into dept VALUES(null, "销售部");
-- 使用显示内连接查询
select * from dept D inner join emp E on D.id = E.dept_id;

-- 使用左外连接查询
select * from dept D left join emp E on D.id = E.dept_id;
```

#### 右连接

**语法**

```properties
select 列名 from 左表 right join 右表 on 从表.外键=主表.主键
```

**案例语句**

```mysql
### 显示全部员工信息

select * from emp;
-- 添加元素
insert into emp values (null, "王永强", "男", 666, "2013-02-24", null);

-- 内连接
select * from dept D inner join emp E on D.id = E.dept_id;
-- 右外连接
select * from dept D right join emp E on D.id= E.dept_id;
```

### 子查询

#### 单行单列

**语法**:

```properties
如果子查询是单行单列，父查询使用比较运算符：> < = 
```

**案例语句**

```mysql
## 子查询:单行单列的情况
select id from dept where name = "开发部";

-- 案例：查询工资最高的员工

-- 1.查询最高的工资
select max(salary) from emp;
-- 2. 根据最高工资到员工表查询到对应的员工信息
select * from emp where salary = (select max(salary) from emp);

-- 查询工资大于"田甜"的员工

-- 1.查询田甜的工资
select salary from emp where name = "田甜";
-- 2.查询大于这个工资的员工
select * from emp where salary > (select salary from emp where name = "田甜");
```

#### 多行单列

**语法**

```properties
多行单列认为是一个数组，父查询使用in /any /all
```

**案例查询**

```mysql
## 多行单列的情况

### 查询工资大于5000的员工，来自于哪些部门,得到部门的名字

-- 1. 先查询大于5000的员工所在的部门id
select dept_id from emp where salary > 5000;
-- 2. 再查询在这些部门id中部门的名字
select * from dept where id=(select dept_id from emp where salary > 5000);
select * from dept where id in (select dept_id from emp where salary > 5000);

### 列出工资高于在1号部门工作的所有员工，显示员工姓名和工资、部门名称。

-- 1. 查询1号部门所有员工的工资,得到多行单列
select salary from emp where dept_id = 1;
-- any关键字使用,表示任何一个
select * from emp where salary > any (select salary from emp where dept_id = 1);
-- all关键字使用,all表示所有
select * from emp where salary > all (select salary from emp where dept_id = 1);
```

#### 多行多列

**语法**

```properties
认为它是一张虚拟表，可以使用表连接再次进行多表查询
```

**案例语句**

```mysql
### 查询出2011年以后入职的员工信息,包括部门名称

-- 1. 在员工表中查询2011-1-1以后入职的员工
select * from emp where join_date > "2011-1-1";
-- 2. 查询所有的部门信息，与上面的虚拟表中的信息组合，找出所有部门id等于的dept_id
## -- 显示内连接
select * from dept D inner join (select * from emp where join_date > "2011-1-1") E on D.id = E.dept_id;
## -- 右连接
select * from dept D right join (select * from emp where join_date > "2011-1-1") E on D.id = E.dept_id;
```

## 多表查询案例

### 准备数据

```mysql
## 创建数据库
create database test3;

-- 使用数据库
use test3;

## 部门表
create table department(
	id INT PRIMARY KEY auto_increment, -- 部门表主键
	dep_name VARCHAR(10), -- 部门名
	dep_location VARCHAR(20) -- 部门所在城市
);

select * from department;
## 添加四个部门
insert into department(id, dep_name, dep_location) VALUES
(1, "市场部", "江州"),
(2, "销售部", "阳州"),
(3, "广告部", "秦阳"),
(4, "研发部", "广州");

## 职务表
create table job(
	id INT PRIMARY KEY auto_increment, -- 职务表主键
	jb_name VARCHAR(20), -- 职务名称
	jb_description VARCHAR(50) -- 职务描述
);

## 添加4个职务
insert into job(id, jb_name, jb_description) VALUES
(01, "董事长", "管理整个公司"),
(02, "经理", "管理部门员工"),
(03, "销售员工", "推销产品"),
(04, "文员", "使用办公软件");

### 员工表
create table emp(
	id INT PRIMARY KEY auto_increment, -- 员工表主键
	ep_name varchar(50), -- 员工姓名
	mgr INT, -- 上级领导
	joindate DATE, -- 入职日期
	salary DECIMAL(7,2), -- 工资
	bonus DECIMAL(7,2), -- 奖金
	job_id INT, -- 外键,引用职务表中的主键
	constraint fk_job_id foreign key(job_id) references job(id),
	dep_id INT, -- 外键,引用部门表中的主键
	constraint fk_dep_id foreign key(dep_id) references department(id)
);

### 向员工表中添加数据
INSERT INTO emp(id,ep_name,job_id,mgr,joindate,salary,bonus,dep_id) VALUES 
(1001,'丁晨光',4,1004,'2000-12-17','8000.00',NULL,2),
(1002,'黄大磊',3,1006,'2001-02-20','16000.00','3000.00',3),
(1003,'侯国龙',3,1006,'2001-02-22','12500.00','5000.00',3),
(1004,'朱林',2,1009,'2001-04-02','29750.00',NULL,2),
(1005,'侯大利',4,1006,'2001-09-28','12500.00','14000.00',3),
(1006,'田甜',2,1009,'2001-05-01','28500.00',NULL,3),
(1007,'杨红',2,1009,'2001-09-01','24500.00',NULL,1),
(1008,'葛朗台',4,1004,'2007-04-19','30000.00',NULL,2),
(1009,'宫建明',1,NULL,'2001-11-17','50000.00',NULL,1),
(1010,'丁莉',3,1006,'2001-09-08','15000.00','0.00',3),
(1011,'王永强',4,1004,'2007-05-23','11000.00',NULL,2),
(1012,'金传统',4,1006,'2001-12-03','9500.00',NULL,3),
(1013,'李武林',4,1004,'2001-12-03','30000.00',NULL,2),
(1014,'樊勇',4,1007,'2002-01-23','13000.00',NULL,1);

select * from emp;

### 工资等级表
create table salarygrade(
	grade int primary key, -- 级别
	low_salary int, -- 最低工资
	high_salary int -- 最高工资
);

### 添加5个工资等级
insert into salarygrade(grade, low_salary, high_salary) VALUES
(1,7000,12000),
(2,12010,14000),
(3,14010,20000),
(4,20010,30000),
(5,30010,99990);

-- 查询操作
select * from department;
select * from emp;
select * from job;
select * from salarygrade;
```

**架构图**

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/16-MySQL.png)

### 实现需求1

```mysql
### 查询所有员工姓名，工资，工资等级

-- 1.确定查询哪些表
select * from emp E inner join salarygrade S;
-- 2.确定连接的条件
select * from emp E inner join salarygrade S on E.salary BETWEEN S.low_salary and S.high_salary;
-- 3.确定查询哪些列
select E.ep_name 姓名, E.salary 工资, S.grade 工资等级 from emp E inner join salarygrade S on E.salary BETWEEN S.low_salary and S.high_salary;
```

### 实现需求2

```mysql
### 查询经理的信息。显示经理姓名，工资，职务名称，部门名称，工资等级
select E.ep_name 姓名, E.salary 工资, J.jb_name 职务,D.dep_name 部门名,S.grade 工资等级 from emp E inner join department D on E.dep_id = D.id 
inner join job J on J.id = E.job_id inner join salarygrade S on E.salary BETWEEN S.low_salary and S.high_salary where J.jb_name = "经理";
```

### 实现需求3

查询部门编号、部门名称、部门位置、部门人数.

```mysql
### 查询部门编号、部门名称、部门位置、部门人数
select D.*, count(E.dep_id) 部门人数 from department D left join emp E on D.id = E.dep_id group by D.id;
```

### 实现需求4

```mysql
### 列出所有员工的姓名及其直接上级的姓名。
select E.ep_name 员工名字, IFNULL(M.ep_name,'BOSS') 上级名字 from emp E left join emp M on E.mgr = M.id;
```

### 实现需求5

```mysql
### 查询工资高于公司平均工资的所有员工列:员工所有信息,部门名称,上级领导,工资等级。

-- 1. 查询公司的平均工资
select avg(salary) from emp;
-- 2. 确定查询哪些表:员工表,员工表,部门表,工资等级
select * from emp E left join emp M on E.mgr = M.id inner join department D on D.id = E.dep_id inner join salarygrade S on E.salary BETWEEN S.low_salary and S.high_salary;
-- 3.确定查询哪些列
select E.*, D.dep_name 部门, ifnull(M.ep_name, "自己") 上级名字, S.grade 工资等级 from emp E left join emp M on E.mgr = M.id inner join department D on D.id = E.dep_id inner join salarygrade S on E.salary BETWEEN S.low_salary 
and S.high_salary where E.salary > (select avg(salary) from emp);
```

