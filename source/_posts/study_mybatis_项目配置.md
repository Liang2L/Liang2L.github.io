---
title: "【基础使用】项目配置"
date: 2019-12-06 14:33:11
categories:
- "学习笔记"
- "Mybatis（Plus）"
---

### 基于Springboot的MybatisPlus配置

```
mybatis-plus:
  # 扫描 mapper.xml
  mapper-locations: classpath*:/mapper/*Mapper.xml
  #typeAliasesPackage: com.act.service.entity
  global-config:
    #banner: false
    db-config:
      id-type: INPUT #默认值0
      logic-delete-value: 1 #默认值1
      logic-not-delete-value: 0
  configuration:
    map-underscore-to-camel-case: true
    cache-enabled: false
#pagehelper分页插件配置
pagehelper:
  helperDialect: mysql #设置sql语言
  reasonable: true
  supportMethodsArguments: true
  params: count=countSql
```
