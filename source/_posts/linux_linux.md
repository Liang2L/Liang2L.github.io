---
title: "Linux常用命令"
date: 2019-07-15 15:47:11
categories:
- "常用命令"
- "Linux"
---
## 防火墙状态
systemctl status firewalld.service

## 通过进程号，找到软件安装目录
ls -l /proc/xxxx/cwd   （XXXXX代表进程号）

## 端口号占用
netstat -nltp | grep port

## 每个文件夹大小(* 可以替换成任意目录)
du -hls *

## 执行rpm包
rpm -ivh  ucenter.rpm --force
