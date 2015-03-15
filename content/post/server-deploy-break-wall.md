+++
date = "2015-03-15T10:58:33+08:00"
draft = false
title = "国内部署服务器环境被墙解决方案"
tags = ["Linux","Deploy","GFW"]
categories = ["Web","Linux"]

+++

### 服务器部署问题

在国内服务器部署时，通常会遇到源被墙等问题，通常只是 GFW 的无差别 DNS 污染造成的，和域名屏蔽 IP 封锁还是不一样的，所以通常可以通过修改本机或服务器的 DNS 地址来解决。

```bash
11:04:40.741350 host.go:130: tuf: failed to download v1/images/ceb6da07e96d7c6e732af955c9b6f64618b4c3a3cf7c7b2a7cdad07e8af227b3/layer: read tcp 54.230.159.50:443: connection reset by peer
===> 11:04:40.778 ERROR: failed to run "/usr/local/bin/flynn-host download --repository https://dl.flynn.io/tuf --tuf-db /etc/flynn/tuf.db --config-dir /etc/flynn --bin-dir /usr/local/bin", exit status 1
```

应该是网络问题，每次中断处不一样。

### 百度公共 DNS

1. 以下设置对所用的Linux系统如 Redhat/Ubuntu/Debian/CentOS 等都有效，但您必须是管理员 root 或者具有管理员权限
```bash
$ vim /etc/resolv.conf
```
2. 在其中加入:
```
nameserver 180.76.76.76
nameserver 114.114.114.114
```
3. 保存退出，使用 nslookup 或者 dig 验证是否可以通过 `180.76.76.76` 正常解析 `www.baidu.com`

### 更改镜像源

大部分开源软件镜像在淘宝上都有相应的国内镜像源，可以修改程序镜像源的地址来实现部署。

比如 rails 开发可以设置 `http://ruby.taobao.org`

### 反向代理至国外服务器

### 使用 proxychains 执行命令