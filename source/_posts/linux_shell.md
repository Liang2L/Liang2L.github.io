---
title: "Linux常用命令"
date: 2019-07-15 15:47:11
categories:
- "常用命令"
- "shell"
---
## 获取当前所在目录
currentdir=$(cd "$(dirname "$0")"; pwd)
## date和+中间要有空格，获取当前时间和日期
baktime=`date "+%Y-%m-%d-%H-%M-%S"`  
clear_dir=`date +"%Y-%m-%d" -d'-1 day'` 
remove_date=`date "+%Y-%m-%d"`
## 获取$app项目的进程号
ps -ef | grep $app| grep -v grep | awk '{ print $2 }'  
## 查找某目录下两天前的.log文件，并删除
find $ROOT_DIR/* -type f -name *.log -mtime +2 -exec rm -rf {} \;
