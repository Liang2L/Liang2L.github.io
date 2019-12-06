---
title: "【基础使用】分页查询"
date: 2019-12-06 14:37:11
categories:
- "学习笔记"
- "Mybatis（Plus）"
---

### 创建项目分页类

[QueryPageVo.java](https://note.youdao.com/ynoteshare1/index.html?id=9782e48a6f541cba5fe3d07515052061&type=note)

==前端查询条件要封装成一个名为vo的实体对象==
```
public class QueryPageVo<T,E> extends AbstractVo<E> implements IPage<T> {
    /**
     *  每页第一行 index,从0开始
     */
    @Getter
    @Setter
    private long index;
    /**
     * 每页记录数
     */
    @Getter
    @Setter
    private long pageSize;
    /**
     * 当前页结果集
     */
    private List<T> rows = new ArrayList<>();
    /**
     * 总记录数
     */
    @Getter
    @Setter
    private long total;
    /**
     * 总页数
     */
    @Getter
    @Setter
    private int totalPageCount;
}
```
### 后台查询条件、分页条件参数接收
![image](https://note.youdao.com/yws/api/personal/file/916BF0D8A6A5489DA75AEC168C5913F4?method=download&shareKey=c27046fc471c9945c37536d33d8df521)
### 单表分页查询
使用BaseMapper自带selectPage传入指定参数即可
![image](https://note.youdao.com/yws/api/personal/file/8B37C6E1EFEC4DC1AE5E985CEA4F8849?method=download&shareKey=22b8818b6cd55fc4b4cbb5a9fd384865)
### 多表关联分页查询
需要手写查询sql，封装分页查询结果
#### service层调用mapper手写分页方法
![image](https://note.youdao.com/yws/api/personal/file/FF72F0E754CD4C88BE2874A0241E5A69?method=download&shareKey=5425ef12d3ac44f85f1a800f9b39b097)
#### 对应的mapper.xml文件
==注意点：==
- 需要指定参数类型和返回值类型
- 查询条件直接从封装好的名为vo的实体类中调用即可

![image](https://note.youdao.com/yws/api/personal/file/28C4983768CC4805A3F67C9347C5AA3C?method=download&shareKey=6f364188c841b85ccd098cab68f3996a)
