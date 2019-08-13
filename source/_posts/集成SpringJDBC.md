---
title: "集成SpringJDBC"
date: 2019-07-16 17:47:11
categories:
- "小功能"
---

### 1、spring.xml配置文件中配置连接池，id为datasource(必须，以c3p0为例)，其余属性在jdbc.config配置文件中进行配置，在引入spring.xml文件中即可

![image](https://note.youdao.com/yws/api/personal/file/9F2884EF365D41C1ABD11BA15887673E?method=download&shareKey=4e5d4081fa35cdf105834a6c0f291e1b)

### 2、同样在spring.xml中进行jdbc配置

DataSource，数据源（即上一步所配置的连接池)

MaxRows，使用jdbc一次查询出的最大数量 ![image](https://note.youdao.com/yws/api/personal/file/1810612AC1AB4B74BC2A429802125953?method=download&shareKey=057c88fd0530de2b62cef2c6375df6d8)

### 3、将获取jdbc连接封装进工具类中，同样在Spring.xml中进行配置

在Spring.xml中进行配置 ![image](https://note.youdao.com/yws/api/personal/file/494F4C9DBAE9420BBE0BEA1CEDCEBCED?method=download&shareKey=e0f066f57270746238372cd9a1a694d1)

将jdbc连接封装进SpringUtil中，提供getJdbcTemplete方法调用jdbc 对属性提供set/get方法 ![image](https://note.youdao.com/yws/api/personal/file/4A046DF8D9374B889FCEE7BA7D1200E1?method=download&shareKey=dd1a565573c30f32cc30ba6485b9c976)

### 4、即可使用DbUtil进行jdbc查询

[DbUtils.java](https://note.youdao.com/ynoteshare1/index.html?id=ff91c117336737a62e5d6411dc310679&type=note)