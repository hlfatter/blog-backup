---
title: 如何使用 Docker-Compose 部署 WordPress
author: Deepspace
top: true
img: https://github.com/IDeepspace/ImageHosting/raw/master/bg/0.jpg 
categories: Docker
date: 2018-05-12
urlname: docker-wordpress
tags:
  - Docker-Compose
---

<!-- ## 如何使用 Docker-Compose 部署 WordPress -->

> 准备：腾讯云服务器：centos 7.5

部署工具：

- `Docker`
- `Docker Compose`
- `WordPress` 和 `MySql5.7` (运行在 Docker 容器中)

<!-- more -->

### 一、安装 Docker

#### 1、卸载旧版本（重装的新系统可跳过）

```shell
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

#### 2、安装 Docker 所需要的包

```shell
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

#### 3、配置到稳定的 Docker CE 安装库

```shell
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

#### 4、安装 Docker CE

```shell
$ sudo yum install docker-ce
```

#### 5、启动 Docker service

```shell
$ sudo systemctl start docker
```

#### 6、验证安装

```shell
// 可以通过查看版本的形式确认安装是否成功：
$ docker --version
// Docker version 18.06.1-ce, build e68fc7a
// 也可以通过直接运行hello-world容器来确认安装是否成功：
$ docker run hello-world
```

### 二、安装 Docker Compose

#### 1、下载安装文件

```shell
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

#### 2、给已下载的安装文件添加执行权限

```shell
$ sudo chmod +x /usr/local/bin/docker-compose
```

#### 3、验证安装

```shell
$ docker-compose --version
// docker-compose version 1.22.0, build f46880fe
```

### 三、部署 WordPress 和 MySql 容器

创建一个文件夹（`WordPress-Blog`），并在该目录下创建名为 `docker-compose.yml` 的文件：

```shell
$ sudo mkdir WordPress-Blog && cd WordPress-Blog
$ sudo touch docker-compose.yml
```

编辑 `docker-compose.yml` ，将如下内容保存在里面：

```yml
version: '3'
services:
   db:
     image: mysql:5.7
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: your-mysql-root-password
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress
   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     volumes:
        - wp_site:/var/www/html
     ports:
       - "80:80"
       - "443:443"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    db_data:
    wp_site:
```

使用 `docker-compose` 命令启动容器：

```shell
$ docker-compose up -d
```

### 四、完成

浏览器访问 IP 地址，安装 WordPress！
