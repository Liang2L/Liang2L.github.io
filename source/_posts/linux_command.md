---
title: "Linux常用命令"
date: 2019-07-15 15:47:11
categories:
- "Linux"
- "常用命令"
---

# Linux

## software

### kafka

#### kafka启动

bin/kafka-server-start.sh config/server.properties

#### kafka生产者启动

bin/kafka-console-producer.sh --broker-list localhost:20882 --topic test

#### kafka消费者启动

bin/kafka-console-consumer.sh --zookeeper localhost:48890 --topic test --from-beginning

#### kafka创建topic

bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 3 --topic test

#### kafka topic列表

bin/kafka-topics.sh --list --zookeeper localhost:48889

### ES

#### ES启动

 ./bin/elasticsearch -d 

#### ES_HEAD 启动

nohup grunt server 2>&1 &

#### 更新

{"query": {"bool": {"must": [{"term": {"urlid": "12776257936719959271"}}]}},"script":{"source":"ctx._source['ip_type']='0';"}}

#### 日期聚合

{
	"size": 0,
	"aggs": {
		"count": {
			"date_histogram": {
				"field": "cur_time",
				"interval": "day",
				"format": "yyyy-MM-dd"
			}
		}
	}
}

## vim

### 查找字符串

/string    enter   N 下一个 n上一个

### 替换字符串

:s/vivian/sky/ 替换当前行第一个 vivian 为 sky

:s/vivian/sky/g 替换当前行所有 vivian 为 sky

:n,$s/vivian/sky/ 替换第 n 行开始到最后一行中每一行的第一个 vivian 为 sky

:n,$s/vivian/sky/g 替换第 n 行开始到最后一行中每一行所有 vivian 为 sky

n 为数字，若 n 为 .，表示从当前行开始到最后一行

:%s/vivian/sky/(等同于 :g/vivian/s//sky/) 替换每一行的第一个 vivian 为 sky

:%s/vivian/sky/g(等同于 :g/vivian/s//sky/g) 替换每一行中所有 vivian 为 sky

### 删除vim中的^M

:%s/^M$//g

### 字符串计数

:%s/yes//gn

## system

### 进程号，找软件目录

ls -l /proc/xxxx/cwd   （XXXXX代表进程号）

### 防火墙状态

systemctl status firewalld.service

stop 关闭，start开启

### 端口号占用

netstat -nltp | grep 

### 执行rpm包

rpm -ivh  ucenter.rpm --force

### 每个文件夹大小

du -hls *

"*"可以替换成任意目录，即为查看该目录下所有文件夹占用大小

### 后台运行

nohup command > myout.file 2>&1 &

# mysql

## 导入

1、首先建空数据库
mysql>create database dbname ;
2、导入数据库
方法一：
（1）选择数据库
mysql>use dbname ;
（2）设置数据库编码
mysql>set names utf8;
（3）导入数据（注意sql文件的路径）
mysql>source /home/xxxx/dbname .sql;
方法二：
mysql -u用户名 -p密码 数据库名 < 数据库名.sql

## 导出

1、导出数据和表结构：
mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql
mysqldump -uroot -p dbname > dbname .sql
敲回车后会提示输入密码
2、只导出表结构
mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql
mysqldump -uroot -p -d dbname > dbname .sql

## 加本地连接权限

grant all privileges on *. * to rooact@'172.31.131.31' identified by 'Yhsj_idc@2019';

flush privileges;

# shell

## 文件

### 修改目录属主

 chown -R rzxsystemuser:rzxsystemuser tomcat

### 删除某目录下两天前的.log文件

find $ROOT_DIR/* -type f -name *.log -mtime +2 -exec rm -rf {} \;

### 建立软连接

ln -s /usr/local/tomcat-9.0.17/ tomcat

当前目录下，建立一个tomcat目录，软连接指向/usr/local/tomcat-9.0.17/

### 文件内容替换

#### 整行替换

sed -i "/database.password=/c\database.password=${DB_PASSWD}"  /usr/local/tomcat-9.0.17/webapps/ucenter_illegal/WEB-INF/classes/globalMessages_zh_CN.properties

将database.password= 这一行替换为 database.password=${DB_PASSWD}

#### 指定内容替换

sed -i "s/127.0.0.1:48889/${DB_IP}:48889/g" /usr/local/tomcat-9.0.17/webapps/ucenter_illegal/WEB-INF/classes/config/spring/applicationContext-dubbo.xml

将文件内容中 127.0.0.1:48889 替换为 ${DB_IP}:48889

## shell脚本

### 当前所在目录

currentdir=$(cd "$(dirname "$0")"; pwd) 

### 当前时间和日期

baktime=`date "+%Y-%m-%d-%H-%M-%S"`  

clear_dir=`date +"%Y-%m-%d" -d'-1 day'` 

remove_date=`date "+%Y-%m-%d"`

### 获取$app进程号

ps waux| grep $app| grep -v grep | awk '{ print $2 }' 

### 获取本机IP

current_ip=`/sbin/ifconfig -a|grep inet|grep -v 127.0.0.1|grep -v inet6|awk '{print $2}'|tr -d "addr:"`

### 从配置文件中获取相关信息

DB_IP=`cat ${SERVER_PATH}/conf/baseClass.conf | grep "^db_ip=" | cut -d '=' -f2 | head -1`
DB_PASSWD=`cat ${SERVER_PATH}/conf/baseClass.conf  | grep "^db_passwd=" | cut -d '=' -f2 | head -1`

### 文件格式转换

将从window上传的文件转为unix文件格式

dos2unix "/usr/local/tomcat/webapps/act-idc-web/WEB-INF/classes/config.properties"  > /dev/null 2>&1

### 定时清理指定目录下文件

查找并清理不需要的文件__查找系统时间为30天以前的全部文件

find $log_path -mtime +30 -name "*" -exec rm -rf {} \;
 
