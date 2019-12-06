---
title: "使用自定义注解完成URL鉴权功能"
date: 2019-06-10 15:47:11
updated: 2019-06-10 15:47:11
categories:
- "小功能"
---

    背景：用户有对应菜单的权限，但是如果用户知道了其他菜单的URL，直接访问URL的话，就可以访问用户本来并不
    具有权限的菜单，所以为了避免这种越权访问现象，提出根据URL鉴权的功能。

# 新建自定义注解

```
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
public @interface MenuToUrl {

    //菜单id
    String menuId() default "";
    
    //访问URL
    String url() default "";

}
```

# 实现自定义注解信息收集
新建自定义类MyListenerProcessor，实现BeanPostProcessor接口，重写postProcessAfterInitialization方法。此方法会在所有bean加载完成后调成，可以在这里通过反射获取所有类中所有方法，在获取方法上的自定义注解信息，保存在一个全局变量 [StoreMenuUrlMap](https://note.youdao.com/ynoteshare1/index.html?id=5d63243bfac49452c815e41e5c3a2536&type=note) 中

```
@Component
public class MyListenerProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        Method[] methods = ReflectionUtils.getAllDeclaredMethods(AopUtils.getTargetClass(bean));
        if (methods != null) {
            for (Method method : methods) {
                MenuToUrl menuToUrl = method.getAnnotation(MenuToUrl.class);
                if(menuToUrl != null){
                    String[] menuIds = menuToUrl.menuId().split(",");
                    for(String menuId : menuIds){
                        StoreMenuUrlMap.getMenuUrlMap().put(menuId, menuToUrl.url());
                    }
                }
            }
        }
        return bean;
    }
}
```
***备注:*** 在通过ReflectionUtils.getAllDeclaredMethods()获取所有方法是，如果注解的类在被代理的包中时，需要AopUtils.getTargetClass()通过代理类获取实际类，才能获取到所有方法。

# 监听器将自定义注解信息入库
新建InitMenuAndUrlListener，在项目启动时，将全局变量StoreMenuUrlMap中的数据持久化到数据库指定表中。

```
public class InitMenuAndUrlListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        DbUtil.update("delete from tab_menu_url");
        Map<String,String> menuUrlMap = StoreMenuUrlMap.getMenuUrlMap();
        String insertSql = "insert into tab_menu_url (menu_id,url) values ";
        StringBuilder sb = new StringBuilder();
        for(Map.Entry<String,String> entry : menuUrlMap.entrySet()){
            sb.append(",('").append(entry.getKey()).append("','").append(entry.getValue()).append("')");
        }
        DbUtil.update(insertSql + sb.substring(1));
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {

    }
}
```

# 拦截器完成URL鉴权功能
通过拦截器将所有请求进行拦截，通过项目启动时生成的表完成URL鉴权功能。

​    
