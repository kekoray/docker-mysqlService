# docer-mysqlService

### 版本说明

| 版本         | CPU架构       |
| :----------- | ------------- |
| mysql:latest | amd64 / arm64 |



### 目录结构

```shell
docker-mysqlService/
├── conf
│   └── my.cnf        # 配置文件
├── initdb            
│   └── init.sql      # 初始化数据库脚本
├── .env              # 传参文件
├── docker-compose.yml
└── README.md
```



### Compose传参说明


| 参数项            | 说明                                                | 传参方式                         |
| ----------------- | --------------------------------------------------- | -------------------------------- |
| \${passwd} | 登录密码                        | \.env文件形式传参        |



### 容器操作

**1.进入容器中的MySQL**

```shell
docker exec -it mysql_dev mysql -uroot -p${passwd}
```

**2.修改root的密码以及跨域访问**

```sql
alter user 'root'@'localhost' identified by 'newPasswd';
use mysql;
update user set host='%' where user='root';   	    -- 修改跨域访问权限
flush privileges;
```

**3.新建用户并设置跨域访问权限, 数据库访问权限**

```sql
create database test_base;
create user 'koray'@'%' identified by '123456';       -- 设置跨域访问权限
grant all privileges on test_base.* to 'koray'@'%';   -- 设置数据库访问权限
flush privileges;
```

