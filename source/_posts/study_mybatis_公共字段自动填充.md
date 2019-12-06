---
title: "【高级功能】公共字段自动填充"
date: 2019-12-06 14:43:11
categories:
- "学习笔记"
- "Mybatis（Plus）"
---

    对于一些基础字段，比如创建时间、修改时间、创建人、修改人等基础字段，可以使用mybatis plus的公共字段自动填充功能进行自动入库、更新，减少手写代码及代码耦合。

### 提取公共字段
将需要自动填充的功能字段提取出来，形成父类，所有有这些字段的实体继承父类即可
### @TableField注解
fill 值为INSERT时代表insert方法生效，INSERT_UPDATE代表insert和update方法都生效
```
  @TableField(fill = FieldFill.INSERT)
  
 @TableField(fill = FieldFill.INSERT_UPDATE)
```
### 自定义公共字段填充处理器
实现MetaObjectHandler接口，重新update和insert方法

```
@Log4j2
@Component
public class MetaHandler implements MetaObjectHandler {
    /**
     * 更新数据执行
     * @param metaObject
     */
    @Override
    public void updateFill(MetaObject metaObject) {
        String userId = BaseContextHandler.getUserID();
        this.setFieldValByName("updatedTime", LocalDateTime.now(), metaObject);
        this.setFieldValByName("updatedBy", userId!=null?userId:"auto", metaObject);
    }



    /**
     * 新增数据执行
     * @param metaObject
     */
    @Override
    public void insertFill(MetaObject metaObject) {
        String userId = BaseContextHandler.getUserID();
        this.setFieldValByName("createdTime", LocalDateTime.now(), metaObject);
        this.setFieldValByName("createdBy", userId!=null?userId:"auto", metaObject);
        this.setFieldValByName("updatedTime", LocalDateTime.now(), metaObject);
        this.setFieldValByName("updatedBy", userId!=null?userId:"auto", metaObject);
    }
}
```
