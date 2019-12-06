---
title: "Nacos安装"
date: 2019-12-06 14:24:11
categories:
- "组件安装"
---
Nacos致力于帮助您发现、配置和管理微服务，将使用Nacos作为微服务架构中的注册中心（替代：eurekba、consul等传统方案）以及配置中心（spring cloud config）来使用。

## Nacos下载
https://pan.baidu.com/s/185VXiqRw0yK73IvI1ICeXA

提取码：i5mn

## 单机版安装
### 安装
将压缩包上传至服务器并解压
### 启动
进行安装目录/bin，执行命令

```
sh startup.sh -m standalone
```
启动完成之后，访问：http://IP:8848/nacos/ ，默认用户名密码为：nacos
## 集群版安装
![image](https://note.youdao.com/yws/api/personal/file/CF5DC72EC81E43658CA05952E7F28502?method=download&shareKey=d5fb06b0cdee9734a60dee262bb92dc8)
### MySQL数据源配置
1. 第一步：初始化MySQL数据库，数据库初始化文件：nacos-mysql.sql，该文件可以在Nacos程序包下的conf目录下获得。
2. 第二步：修改conf/application.properties文件，增加支持MySQL数据源配置，添加（目前只支持mysql）数据源的url、用户名和密码。配置样例如下：

```
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://localhost:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=
```
### 集群配置
在Nacos的conf目录下有一个cluster.conf.example，可以直接把example扩展名去掉来使用，也可以单独创建一个cluster.conf文件，然后打开将后续要部署的Nacos实例地址配置在这里。

本文以在本地不同端点启动3个Nacos服务端为例，可以如下配置：

```
127.0.0.1:8841
127.0.0.1:8842
127.0.0.1:8843
```
### 启动实例
生产环境下直接执行命令即可 sh startup.sh

本地安装集群，则需要指定端口启动各个实例，修改startup.sh脚本中的Dserver.port即可
### Proxy配置
在Nacos的集群启动完毕之后，根据架构图所示，我们还需要提供一个统一的入口给我们用来维护以及给Spring Cloud应用访问。简单地说，就是我们需要为上面启动的的三个Nacos实例做一个可以为它们实现负载均衡的访问点。这个实现的方式非常多，这里就举个用Nginx来实现的简单例子吧。

- Nginx安装

传送门：http://note.youdao.com/noteshare?id=fd83221332c62e79f1bc5deb579aab64&sub=3DE8BD1782EF4741A34A9E87E2C9762F
- Nginx配置

![image](https://note.youdao.com/yws/api/personal/file/EC5EF30098344E7688B68C6F0EEC7BBA?method=download&shareKey=a1bf763dd7bf593602d0f88efbb88959)
