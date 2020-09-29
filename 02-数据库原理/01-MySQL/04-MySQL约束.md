## 约束种类

| **约束名** | **约束关键字**             |
| ---------- | -------------------------- |
| **主键**   | primary key                |
| **非空**   | not null                   |
| **唯一**   | unique                     |
| **外键**   | foreign key ... references |
| **默认**   | default                    |

### 表约束的作用

 约束通常是在创建表结构的时候创建的，表中约束防止不符合要求的数据添加到表中，保证表中数据的正确性。如果表中已经有违反约束的数据，会导致约束创建失败。

### 主键约束

#### 主键的作用和特点

```properties
唯一标识表中每一条记录，通过主键来查询到每一条记录。

主键的特点:
1.非空:不能为NULL
2.唯一:不能重复
```

#### 字段作为表的主键

```properties
通常是能够唯一代表这条记录的字段，尽量不要使用与业务相关的主键。如：身份证，学生证,最好这个主键是没有意义。
```

#### 复合主键

```properties
一张表中如果有多列做为主键，称为复合主键。需要通过多列才能唯一标识表中一行记录。只要这几列加到一起不重复就可以了。
```

#### 创建主键

```mysql
1.在创建表的时候给字段添加主键
create table 表名(
	字段名字 数据类型 primary key
)

2.在已有表中添加主键,修改表结构
alter table 表名 add primary key(列名)
```

**案例语句**

```mysql
-- 使用数据库
USE test1;

-- 创建学生表stu4,包括字段(id, name, age)将id作为主键
CREATE TABLE stu4(
	id INT PRIMARY KEY,
	NAME VARCHAR(20),
	age INT
);

-- 插入主键值

INSERT INTO stu4 VALUES(1, "张三", 18);

INSERT INTO stu4 VALUES(2, "小刘", 21);
INSERT INTO stu4 VALUES(3, "李刚", 51);
INSERT INTO stu4 VALUES(4, "科比", 41);

-- 删除stu4表的主键
ALTER TABLE stu4 DROP PRIMARY KEY;

-- 表存在的情况下,添加主键
ALTER TABLE stu4 ADD PRIMARY KEY(id);

SELECT * FROM stu4;

-- stu4的结构
DESC stu4;
```

#### 主键自增长

```properties
由系统自动增长主键，不需要人为添加主键的值避免重复,auto_increment只能用在主键上，不能用在其它的字段上.
```

**语法**：

```mysql
create table 表名(
	字段名字 数字类型 primary key auto_increment
)
```

**案例语句**

```mysql
-- 创建学生表st5, 包含字段(id, name, age)将id做为主键并自动增长
CREATE TABLE stu5(
	id INT PRIMARY KEY auto_increment,
	NAME VARCHAR(20),
	age INT
);

-- 插入数据
INSERT INTO stu5(`NAME`, age) VALUES("张三", 10);
INSERT INTO stu5(`NAME`, age) VALUES("李四", 23);

-- 将主键的起始值设置为100
ALTER TABLE stu5 auto_increment = 100;

-- 查询stu5
SELECT * FROM stu5;
-- stu5结构
DESC stu5;

-- 删除自增长的值(数据表)
DELETE FROM stu5;
-- 删除表中所有记录
TRUNCATE stu5;
```

### 唯一约束

指定某一列不能出现相同的值.

**语法**：

```mysql
create table 表名(
	字段名 字段类型 unique
)
```

**案列语句**

```mysql
-- 创建学生表st6，包含字段(id,name)，NAME这一列设置唯一约束,不能出现同名的学生
CREATE TABLE stu6(
	id int,
	NAME VARCHAR(20) UNIQUE
);
--  表结构
DESC stu6;

-- 添加数据
INSERT INTO stu6 VALUES(1, "张三");
-- 再次添加
# INSERT INTO stu6 VALUES(2, "张三"); -- 插入失败, Duplicate entry '张三' for key 'name'

-- 删除数据
DELETE from stu6 WHERE id = 1;

-- 查询表数据
SELECT * FROM stu6;
```

### 非空约束

 设置某列数据不能为空，必须要输入。

**语法格式**:

```properties
create table 表名(
	字段名, 字段类型 not null
)
```

**案列语句**

```mysql
# 非空约束
-- 创建表学生表st7, 包含字段(id,name,gender)其中name不能为NULL
CREATE TABLE stu7(
	id INT,
	NAME VARCHAR(20) NOT NULL,
	age INT

);

-- 查看表结构
DESC stu7;

-- 添加一条记录其中姓名不赋值
# INSERT INTO stu7 VALUES(1, NULL, 20);	-- 1048 - Column 'NAME' cannot be null
# INSERT INTO stu7(id, age) VALUES(1, 30);	-- 1364 - Field 'NAME' doesn't have a default value

-- 添加数据
INSERT INTO stu7 VALUES(1, "李四", 20);
-- 查询表中数据
SELECT * FROM stu7;
```

### 默认值约束

如果一个字段没有设置它的值，将使用默认值。

**语法格式**:

```properties
create table 表名(
	字段名 字段类型 default 默认值
)
```

**案列语句**

```mysql
# 默认值约束
-- 创建一个学生表,包含字段(id, name, address),地址默认值是广州
CREATE TABLE stu5(
	id INT,
	NAME VARCHAR(20),
	address VARCHAR(30) DEFAULT "广州"
);

-- 查看表结构
DESC stu5;

-- 添加一条记录,使用默认地址
INSERT INTO stu5(id, `NAME`) VALUES(1, "刘菲");
INSERT INTO stu5 VALUES (2, "侯大利", DEFAULT);

-- 添加一条记录,不使用默认地址
INSERT INTO stu5 VALUES(3, "杨帆", "深圳");
-- stu5表信息
SELECT * FROM stu5;
-- 删除id=3的记录
DELETE FROM stu5 WHERE id = 3;
```

### 外键约束

#### 创建外键

新表创建时候添加外键

```mysql
create table 表名 (
   外键字段名 字段类型,
   constraint 约束名 foreign key(外键字段名)references 主表(主键)
)
```

已有表添加外键

```mysql
alter table 表名 add constraint 约束名 foreign key(外键字段名) references 主表(主键)
```

#### 删除外键

```mysql
alter table 从表 drop foreign key 约束名;
```

**案例语句**

```mysql
USE db3;

-- 数据库所有表
SHOW TABLES;

-- 部门表
CREATE TABLE department(
	id INT PRIMARY KEY auto_increment, -- 部门表主键
	dep_name VARCHAR(10), -- 部门名
	dep_location VARCHAR(20) -- 部门所在城市
);

-- 添加部门表的记录
insert into department values(null,'刑警部', '江州'),(null, '法医部', '阳州');

-- 员工表
CREATE TABLE employee(
	id INT PRIMARY KEY auto_increment,	-- 主键自增长
	NAME VARCHAR(20), -- 姓名
	age INT,
	dept_id INT -- 外键,引用部门表中的主键
	-- CONSTRAINT fk_ep_id FOREIGN KEY(dept_id)REFERENCES department(id)
);

-- 删除外键
alter table employee drop foreign key fk_ep_id;

-- 添加从表存在的情况下添加外键
alter TABLE employee ADD constraint fk_ep_id FOREIGN KEY(dept_id) REFERENCES department(id);

# 向员工表中添加元素
INSERT INTO employee (NAME, age, dept_id) VALUES ('侯大利', 27, 1);
INSERT INTO employee (NAME, age, dept_id) VALUES ('朱琳', 50, 1);
INSERT INTO employee (NAME, age, dept_id) VALUES ('张小天', 30, 1);
INSERT INTO employee (NAME, age, dept_id) VALUES ('田甜', 26, 2);
INSERT INTO employee (NAME, age, dept_id) VALUES ('老李', 42, 2);
INSERT INTO employee (NAME, age, dept_id) VALUES ('樊勇', 35, 2);

-- 查询部门表信息
SELECT * FROM department;

-- 查询员工表信息
SELECT * FROM employee;

-- 删除表结构
DROP table department;
DROP table employee;
```

#### 级联操作

**语法**：

```properties
写在外键约束的后面，在创建外键约束的时候创建级联操作。
```

| 级联操作 | 语法              |
| -------- | ----------------- |
| 级联更新 | on update cascade |
| 级联删除 | on delete cascade |

**案例语句**

```mysql
-- 删除外键约束
ALTER TABLE employee DROP FOREIGN KEY fk_ep_id;
-- 添加外键约束,级联更新和级联删除
ALTER TABLE employee ADD CONSTRAINT fk_ep_id FOREIGN KEY(dep_id) REFERENCES department(id) ON UPDATE ON DELETE CASCADE;

SELECT * from employee;

-- 删除员工表id=6的值
DELETE FROM employee WHERE id = 6;
-- 把部门表中id等于2的部门改成id等于3
UPDATE department set id=3 WHERE id=2

-- 删除部门号是3的部门
DELETE FROM department WHERE id = 3;

-- 查询部门表信息
SELECT * FROM department;
```

