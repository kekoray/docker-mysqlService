# docer-mysqlService




### 1.目录结构

```shell
docker-clickhouseService/
├── docker-compose.yml 
├── conf
│   ├── config.xml    # 配置文件
│   └── users.xml     # 用户配置文件
├── data              # 数据目录
├── initdb            # 初始化数据库脚本
└── logs              # 日志目录
```



### 2.compose文件





#### init.sql

> docker-entrypoint-initdb里的初始化脚本

```sql
vim ./initdb/init.sql

# -------------------------
create database blogdb;
create table blogdb.user (id int, name varchar(255));
insert into blogdb.user values (1,"kk");
insert into blogdb.user values (1,"ll");
```



#### my.cnf

> 数据库配置文件

```shell
vim ./my.cnf

# -------------------------
[mysqld]
user=mysql
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
character-set-client-handshake=FALSE
init_connect='SET NAMES utf8mb4'
secure_file_priv=''
#指定日志的保存位置
log-error=/home/mysql/mysql_error.log 

[client]
default-character-set=utf8mb4

[mysql]
default-character-set=utf8mb4
```



#### docker-compose.yml

> ${passwd}参数在portainer中部署时指定环境参数；

```yml
version: '3.1'
services:
  mysql:
    image: mysql:latest
    restart: always
    container_name: mysql
    ports:
      - 6606:3306
    volumes:
      - ./initdb:/docker-entrypoint-initdb.d  # 绑定初始化数据库脚本
      - ./datadir:/var/lib/mysql              # 指定数据库数据存储位置
      - ./my.cnf:/etc/my.cnf                  # 指定MySQL配置文件
    environment:
      - MYSQL_ROOT_PASSWORD=${passwd}
```



### 容器操作

**1.进入容器中的MySQL**

```shell
docker exec -it mysql_dev mysql -uroot -p${passwd}
```

**2.修改root的密码以及跨域访问**

```sql
alter user 'root'@'localhost' identified by 'newPasswd';
use mysql;
update user set host='%' where user='root';   	 -- 修改跨域访问权限
flush privileges;
```

**3.新建用户并设置跨域访问权限, 数据库访问权限**

```sql
create database test_base;
create user 'koray'@'%' identified by '123456';          -- 设置跨域访问权限
grant all privileges on test_base.* to 'koray'@'%';         -- 设置数据库访问权限
flush privileges;
```

