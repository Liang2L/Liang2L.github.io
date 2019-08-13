---
title: "集成Springcontext上下文环境"
date: 2019-07-16 14:47:11
categories:
- "小功能"
---

##### 具体工具类见 上传资源->util工具类->[SpringContextUtil](http://note.youdao.com/noteshare?id=ac5849ba55a357a2d12e091c95d5609f&sub=B0325273427A4E72B8AABEB6C5E4386B)

一、将工具类放入项目中

二、在applicationContext.xml中添加SpringContextUtil.java配置即可

```xml
<bean class="com.seentech.ucenter.sysmanage.utils.SpringContextUtil" lazy-init="false" />
```

三、使用方法，在需要加载bean的地方调用即可

```java
AccessLogQueryService accessLogQueryService = SpringContextUtil.getBean("accessLogQueryService");
```

