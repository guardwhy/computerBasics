## MySQL安装

### 1.1-Windows 上安装 MySQL

#### 1、进入官方下载地址

下载安装MySQL5.7版本:：https://dev.mysql.com/downloads/mysql/

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/1-mysql.png)

#### 2.选择直接下载

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/2-mysql.png)

#### 3.解压安装

3.1、将下载完的 zip 包解压到相应的目录,这里我将解压后的文件夹放在E:\Develop\MySQL-5.7.30下,解压文件如图所示

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/3-mysql.png)



3.2、在该文件夹下创建 my.ini 配置文件，编辑 my.ini 配置以下基本信息：

```ini
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
# 设置3306端口
port = 3306

# 设置mysql的安装目录
basedir=E:\\Develop\\MySQL-5.7.30

# 设置 mysql数据库的数据的存放目录（MySQL8.0+ 不需要以下配置，系统自己生成即可，否则有可能报错）
datadir=E:\\Develop\\WorkPlace\\SqlData

# 允许最大连接数
max_connections=20

# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

3.3、以管理员身份打开 cmd 命令行工具，进入目录：

```mysql
E:
cd E:E:\Develop\MySQL-5.7.30
初始化数据库: mysqld --initialize --console
```

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/4-MYSQL.png)

3.4、运行`mysqld install`，若提示 “Service successfully installed.” 表示安装成功。

#### 4.修改密码

4.1、执行`net start mysql`启动MySQL

4.2、运行`mysql -u root -p`后根据提示输入初始密码，回车后即可登录进MySQL。

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/5-MySQL.png)

4.3、修改密码命令的格式为

```sql
修改格式:set password for userName@localhost = password('newPassword');
密码设置为:set password for userName@localhost = password('root');
```

输入`exit`后回车，再运行`mysql -u root -p`后就可以用新密码登录了。

#### 5.配置环境变量

5.1、右键此电脑 >> 高级系统设置 >> 环境变量 ，新建系统变量 MYSQL_HOME，变量值是安装MySQL的根目录：

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/6-MySQL.png)

5.2、编辑系统变量 Path >> 新建 >> 将 %MYSQL_HOME%\bin 添加到尾行 >> 确定。

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/7-MySQL.png)

5.3、配置好后，不用进入MySQL安装目录，以管理员身份打开命令行后，就可以直接登录MySQL了

![](https://guardwhy.oss-cn-beijing.aliyuncs.com/img/MySQL/8-MySQL.png)



### 2.1-Linux上安装MySQL

#### 1-linux安装docker

Linux环境中的Centos7.x以上版本,安装docker虚拟化容器.

```properties
（1）yum 包更新到最新：yum update

（2）安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的
> yum install -y yum-utils device-mapper-persistent-data lvm2

（3）设置yum源为阿里云
> yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

（4）安装docker
> yum install docker-ce -y

（5）安装后查看docker版本
> docker -v
```

#### 2-设置mirror镜像源

```properties
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://0wrdwnn6.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 3-Docker的启动与停止

```properties
# 启动docker：
systemctl start docker

# 停止docker：
systemctl stop docker
  
# 重启docker：
systemctl restart docker

# 查看docker状态：
systemctl status docker

# 开机启动：  
systemctl enable docker
systemctl unenable docker

# 查看docker概要信息
docker info

# 查看docker帮助文档
docker --help
```

#### 4-Docker镜像相关的命令

4.1、查看镜像

```css
docker images
```

4.2、搜素镜像

docker search 镜像名称

```properties
NAME：仓库名称
DESCRIPTION：镜像描述
STARS：用户评价，反应一个镜像的受欢迎程度
OFFICIAL：是否官方
AUTOMATED：自动构建，表示该镜像由Docker Hub自动构建流程创建的
```

4.3 拉取镜像

拉取镜像就是从中央仓库中下载镜像到本地
docker pull 镜像名称

```properties
docker pull centos:7
```

4.4、删除镜像
按镜像ID删除镜像

```properties
docker rmi 镜像ID
docker rmi 镜像名称
docker rmi 镜像名称:tag
docker rmi `docker images -q` 删除所有镜像（谨慎操作）
```

#### 5-容器创建与启动容器

5-1:查看容器

```properties
查看最后一次运行的容器:docker ps -l

查看运行容器:docker ps

查看所有容器:docker ps -a

进入容器其中字符串为容器ID:docker exec -it d27bd3008ad9 /bin/bash

停用全部运行中的容器:docker stop $(docker ps -q)

删除全部容器：docker rm $(docker ps -aq)
```

5-2:创建容器

```properties
交互式方式创建容器(==创建以后就进入到容器内部了==)
docker run -it --name 容器名称 镜像名称:标签 /bin/bash
退出当前容器:exit

守护式方式创建容器：（默认自动启动你的容器）:
docker run -di --name 容器名称 镜像名称:标签
登录守护式容器方式：
docker exec -it 容器名称 (或者容器ID)  /bin/bash
```

#### 6-启动与停止容器

```properties
#停止容器：
docker stop 容器名称（或者容器ID） 
docker stop 容器名称（或者容器ID） ，容器名称（或者容器ID）


#重启容器：
docker restart 容器名称（或者容器ID） 
docker restart 容器名称（或者容器ID） ，容器名称（或者容器ID）


# 启动容器：
docker start 容器名称（或者容器ID）   
docker start 容器名称（或者容器ID）容器名称（或者容器ID）

# 删除容器
docker rm 容器名称（或者容器ID） 
docker rm 容器名称（或者容器ID） 容器名称（或者容器ID）

# 查询启动日志
docker log -f 容器id(容器名字)
```

#### 7-使用Docker安装mysql容器

7.1-拉取mysql镜像

```properties
docker pull mysql
docker pull  mysql:5.7
```

7.2-创建容器(安装MySQL)

```properties
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

7.3、进入容器，修改root用户允许远程访问。

```properties
进入容器：docker exec -it 7681b85e73a1 /bin/sh 
修改远程权限：alter user 'root'@'%' identified with mysql_native_password by 'root';
```

7.4、 打开3306、22端口允许远程连接

```properties
开端口命令：firewall-cmd --zone=public --add-port=3306/tcp --permanent
开端口命令：firewall-cmd --zone=public --add-port=22/tcp --permanent
重启防火墙：systemctl restart firewalld.service
```

7.5、创建挂载目录

```properties
[root@guardwhy docker]# mkdir -p mysql/data #挂载数据文件
[root@guardwhy docker]# mkdir -p mysql/logs #挂载日志文件
[root@guardwhy docker]# mkdir -p mysql/conf #挂载配置文件
```

7.6、拷贝配置文件并且修改配置文件

```properties
1.docker cp mysql:/etc/mysql/mysql.conf.d/mysqld.cnf /usr/local/docker/mysql/conf
2. 编辑mysqld.conf
vi /my/mysql/conf/mysqld.conf
3-设置字符集
character-set-server=utf8
```

7.7-删除容器，重新整合安装.

```sql
docker run \
--name mysql \
-p 3306:3306 \
-v /usr/local/docker/mysql/conf:/etc/mysql/mysql.conf.d/ \
-v /usr/local/docker/mysql/data:/var/lib/mysql \
-v /usr/local/docker/mysql/logs:/logs \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7 \
--character-set-server=utf8mb4 \
--collation-server=utf8mb4_unicode_ci
```




