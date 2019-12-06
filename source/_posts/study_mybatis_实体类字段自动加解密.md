---
title: "【高级功能】实体类字段自动加解密"
date: 2019-12-06 14:44:11
categories:
- "学习笔记"
- "Mybatis（Plus）"
---

    针对项目安全性要求，对某些敏感字段数据需要进行加密存储，通过mybatis plus和自定义注解方式来完成对敏感字段的自动加解密

### 自定义注解

在需要加解密的实体类型注解@==EncryptionClass==，在需要加解密的字段上注解@==EncryptionField==
```
/**
 * 对实体类打标
 */
@Documented
@Inherited
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface EncryptionClass {
}

/**
 * 对实体类的字段打标
 */
@Documented
@Inherited
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface EncryptionField {
    SecretMode mode() default SecretMode.AES;
}
```
### 自定义mybatis拦截器
#### 入库或更新时拦截器
[ParamInterceptor.java](https://note.youdao.com/ynoteshare1/index.html?id=3594dfeedcc613081ac4c6cb630bd8f2&type=note)
![image](https://note.youdao.com/yws/api/personal/file/845F3C9C486F4CDE890DCFDA50E39E57?method=download&shareKey=0c86a90f4470e60c3bc5dab53296be70)

==注意：==

1、@Intercepts注解。method为update代表插入和更新时执行
```
@Intercepts({
        @Signature(type = Executor.class, method = "update", args = {MappedStatement.class, Object.class})
})
```
2、@ConditionalOnProperty注解。根据yml文件中==entity.encrypt==与==havingValue==值进行比较，一致则拦截器起效，不一致则拦截器不起效

```
@ConditionalOnProperty(prefix = "entity", value = "encrypt", havingValue = "true")
```
#### 查询时拦截器
[ResultInterceptor.java](https://note.youdao.com/ynoteshare1/index.html?id=028ac60cd7d431a6b02fd1b62c6f360d&type=note)

==注意点同入库拦截器==
### 加解密工具类
[SensitiveUtils.java](https://note.youdao.com/ynoteshare1/index.html?id=8bc1aa14a2bf334f272b2e472b78f261&type=note)
