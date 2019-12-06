---
title: "common-pool2手写连接池"
date: 2019-07-16 14:47:11
categories:
- "小功能"
---

背景：使用common-pool2框架手写连接池（例如es连接池），提升创建es连接的性能

以es连接池为例，代码见 上传资源->util工具类->es连接池 文件夹

### 一、maven引入common-pool2依赖

```xml
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
    <version>2.4.2</version>
</dependency>
```

### 二、创建一个池类，这个池通过依赖的方式引入commons-pool2中的GenericObjectPool。在这个类中，我们定义了如何从池中借对象和返回对象

[Pool.java](http://note.youdao.com/noteshare?id=3a13329e90132cec5e567d79b4239b57&sub=37240E265FCE4AAB82334E73F645CD62)

```java
public T getResource(){
    try {
        return internalPool.borrowObject();
    } catch (Exception e) {
        log.info("Could not get a resource from the pool", e);
    }
    return null;
}


public void returnResource(final T resource){
    if (resource != null) {
        returnResourceObject(resource);
    }
}
```

### 三、创建ES的连接池，继承池类

[ElasticSearchPool.java](http://note.youdao.com/noteshare?id=b52f397efc91f6937a65535baa848170&sub=18BBED4FF515416586C3F3760D5D6D0A)

```java
private String clusterName;
private HostAndPort hostAndPort;

public ElasticSearchPool(ElasticSearchPoolConfig config,HostAndPort hostAndPort){
    super(config, new ElasticSearchClientFactory(config.getClusterName(), hostAndPort));
    this.clusterName = config.getClusterName();
    this.hostAndPort = hostAndPort;
}
```

### 四、定义了一个ES的连接池配置类用于配置连接池的属性，在apache提供的commons-pool2中已经提供了一个池基本属性配置的类GenericObjectPoolConfig，我们可以直接继承此类

[ElasticSearchPoolConfig.java](http://note.youdao.com/noteshare?id=cca045df50fc7cd2b309f2464ae6ac29&sub=2C9DED9784764232A06165BE9CB78C5F)

```java
/**
*有很多配置项，这里只选择两种进行展示
*/
//超时时间
private long connectTimeMillis;
//集群名称
private String clusterName;
```

### 五、ES连接属性配置类

[HostAndPort.java](http://note.youdao.com/noteshare?id=aaa085be6d1fe26242b60301d508a929&sub=3AB553A3EF1248FF8FDDB4B8289DB5CC)

```java
//ip
private String host ;
//端口
private int port ;
//连接协议
private String schema;

public HostAndPort(String host, int port, String schema) {
    this.host = host;
    this.port = port;
    this.schema = schema;
}

//使用如下
HostAndPort hostAndPort = new HostAndPort("172.31.133.21",19200,"http");
```

### 六、最后，我们还需要做的是给这个池提供一个工厂类，用于创建池中的对象和回收对象，我们只要实现PooledObjectFactory接口并实现接口中的makeObject()和destroyObject()方法即可

[ElasticSearchClientFactory.java](http://note.youdao.com/noteshare?id=ffc3bb2e2a5c67d2b6797dce24f33e6f&sub=DC0A36C649B143639AACE650E382B0E6)

```java
private HostAndPort hostAndPort;

private String clusterName;

public ElasticSearchClientFactory(String clusterName, HostAndPort hostAndPort){
    this.clusterName = clusterName;
    this.hostAndPort = hostAndPort;
}

@Override
public PooledObject<RestClient> makeObject() throws Exception {
    RestClient client = RestClient.builder(new HttpHost(hostAndPort.getHost(),hostAndPort.getPort(),hostAndPort.getSchema())).build();
    return new DefaultPooledObject<>(client);
}

@Override
public void destroyObject(PooledObject<RestClient> pooledObject) throws Exception {
    RestClient client = pooledObject.getObject();
    if(client!=null){
        try {
            client.close();
        }catch (Exception e){
            //ignore
        }
    }
}
```

### 七、验证

```java
public class ElasticSearchPoolTest {
 
    public static void main(String[] args) throws Exception {
        HostAndPort hostAndPort = new HostAndPort("172.31.133.21",19200,"http");
        ElasticSearchPoolConfig config = new ElasticSearchPoolConfig();
        //超时时间
        config.setConnectTimeMillis(8000);
        //最大连接
        config.setMaxTotal(10);
        //集群名称
        config.setClusterName("idc_es");
        ElasticSearchPool pool = new ElasticSearchPool(config,hostAndPort);
        
        for(int i=0;i<100;i++){
            RestClient client = (RestClient)pool.getResource();
            System.out.println(client.toString());
            pool.returnResource(client);
        }
    }
 
}
```