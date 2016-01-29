---
layout: post
title: PostgreSQL安装配置记录
category: PostgreSQL
date: 2016/1/29 13:35
---

因为支持JSON等功能，转投PostgreSQL，目前在CentOS、OS X等上安装成功，记录一下配置过程。

### 环境
CentOS 7.2.1511 (Core)  
Linux 3.10.0-327.el7  
PostgreSQL版本：v9.5.0 [下载](https://ftp.postgresql.org/pub/source/v9.5.0/postgresql-9.5.0.tar.gz)

<!--more-->

## 编译安装

```bash
yum install readline-devel zlib-devel libxml2-devel libxslt-devel
./configure --prefix=/usr/local/pgsql --with-libxml --with-libxslt
make
make install
```

## 非必需步骤，安装扩展

```bash
cd 源代码目录/contrib
make
make install
```
安装之后登录到数据库，可安装扩展。比如加强[pgAdmin](http://pgadmin.org/)工具的`CREATE EXTENSION adminpack`。

## 安装完成后的操作

```bash
# 建立专门用户运行
adduser postgres
# 数据目录
mkdir /usr/local/pgsql/data
chown -R postgres /usr/local/pgsql

# 切换
su - postgres
# 初始化
/usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
# 启动
pg_ctl start -l logfile
```

## 配置文件

1. 修改`/etc/profile`，添加`export PGDATA=/usr/local/pgsql/data`作为默认数据目录。
2. 修改`/usr/local/pgsql/data/postgresql.conf`，增加`listen_addresses='*'`，修改监听IP。
3. 修改`/usr/local/pgsql/data/pg_hba.conf`，增加`host all all 0.0.0.0/0 md5`，允许用户远程登录。




