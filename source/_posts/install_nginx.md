---
title: "Nginx安装"
date: 2019-12-06 14:17:11
categories:
- "组件安装"
---
## 安装依赖包

```
//一键安装上面四个依赖
yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel
```
## 下载Nginx安装包
任意目录下载安装包（/usr/local/目录下除外）。如果在/usr/local/目录下，make install时会报错（复制配置文件已存在）

```
wget  http://nginx.org/download/nginx-1.13.11.tar.gz
```
## 创建Nginx安装目录
我习惯于创建在/usr/local/目录下

```
mkdir -p /usr/local/nginx
```
## 编译Nginx
在安装包目录下解压安装包，执行./configure命令，prefix指定创建的安装目录

```
./configure --prefix=/usr/local/nginx
make && make install
```
## Nginx配置文件
配置文件中可更改Nginx监听端口(默认80，一般和Apache服务端口冲突)

## Nginx命令
在安装目录（/usr/local/nginx/sbin）下
```
./nginx             启动
./nginx -s stop     停止
./nginx -s reload   重启
```
