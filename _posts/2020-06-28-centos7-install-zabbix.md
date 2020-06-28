---
layout: mypost
title: centos 7 安装zabbix-server docker版 & zabbix-agent
categories: [zabbix]
---

#### 一、zabbix-server

##### docker配置文件

- docker-compose.yaml

```
version: '3'

services:
  zabbix-postgres:
    image: postgres:9.6
    container_name: zabbix-postgres
    env_file:
      - env/postgres.env
    volumes:
      - /home/data/zabbix-db/data:/var/lib/postgresql/data
    restart: always

  zabbix-server:
    image: zabbix/zabbix-server-pgsql:centos-4.0-latest
    container_name: zabbix-server
    depends_on:
      - zabbix-postgres
    env_file:
      - env/zabbix-server.env
    links:
      - zabbix-postgres:postgres
    ports:
      - "10051:10051"
    restart: always
  zabbix-web:
    image: zabbix/zabbix-web-nginx-pgsql:centos-4.4-latest
    container_name: zabbix-web
    depends_on:
      - zabbix-postgres
      - zabbix-server
    env_file:
      - env/zabbix-nginx.env
    links:
      - zabbix-postgres:postgres
      - zabbix-server:zabbix-server
    ports:
      - "10061:80"
    restart: always

networks:
  default:
    external:
      name: zabbix_default
```

- postgres.env

```
POSTGRES_USER=zabbix
POSTGRES_PASSWORD=zabbix
POSTGRES_DB=zabbix
```

- zabbix-server.env

```
DB_SERVER_HOST=zabbix-postgres
POSTGRES_USER=zabbix
POSTGRES_PASSWORD=zabbix
POSTGRES_DB=zabbix
```

- zabbix-nginx.env

```
DB_SERVER_HOST=zabbix-postgres
POSTGRES_USER=zabbix
POSTGRES_PASSWORD=zabbix
POSTGRES_DB=zabbix
PHP_TZ="Asia/Shanghai"
```

##### 新建数据存储目录并授权

```
mkdir /data/zabbix-db/data && chmod -R 777 /data/zabbix-db/data
```

##### 启动服务

```
docker-compose up -d
```

启动成功后，用户名 Admin 密码 zabbix


#### 一、zabbix-agent

##### 安装数据库

```
rpm -Uvh https://repo.zabbix.com/zabbix/4.4/rhel/7/x86_64/zabbix-release-4.4-1.el7.noarch.rpm
yum clean all
```

##### yum安装

```
yum isntall zabbix-agent -y
```


##### 设置开机自启动

```
systemctl enable zabbix-agentd && systemctl restart zabbix-agentd
```



