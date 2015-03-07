+++
date = "2015-03-07T11:20:23+08:00"
draft = false
title = "docker wordpress 使用教程"
tags = ["Docker","Wordpress"]
categories = ["Web","Docker"]
+++

### docker wordpress 简介

这里使用的是 Github 上 stars 数最多的 Dockfile [docker-wordpress-nginx][#dwn]，它将 nginx, mysql, php-apc, php-fpm, wordpress 等 LNMP 基础软件安装在同一个镜像中。

### 安装方法

最简单的安装方式是从 Docker registry 上 pull 这个镜像：

```bash
$ docker pull eugeneware/docker-wordpress-nginx
```

你也可以下载相应文件自己安装镜像：

```bash
$ git clone https://github.com/eugeneware/docker-wordpress-nginx.git
$ cd docker-wordpress-nginx
$ sudo docker build -t="docker-wordpress-nginx" .
```

### 使用方法

创建一个 wordpress 新实例并绑定 80 端口。`-p 80:80` 将 container 内部的 80 端口和外部宿主机的 80 端口映射起来。

```bash
$ sudo docker run -p 80:80 --name docker-wordpress-nginx -d docker-wordpress-nginx
```

开启你创建的 docker。

```bash
$ sudo docker start docker-wordpress-nginx
```

在开启 `docker-wordpress-nginx` 之后，检查端口是否正确对应。这也能显示 docker 容器和宿主机的端口映射情况。

```bash
$ sudo docker ps

0.0.0.0:80 -> 80/tcp docker-wordpress-nginx
```

你可以通过如下 URL 访问 wordpress：

```
http://127.0.0.1:80
```

### 绑定域名

假如你的虚拟机上只有一个 container，并想绑定相应域名和 IP，则你需要事先修改 `nginx-site.conf`。

将其中的 `server_name localhost;` 修改成 `server_name your_domain;`。 然后 build 这个 docker image。

### ssh 连接

使用 [nsenter][#nsenter]。

新版本 docker 可以使用命令 `docker exec -it bash`

[#dwn]:https://github.com/eugeneware/docker-wordpress-nginx
[#nsenter]:https://github.com/jpetazzo/nsenter