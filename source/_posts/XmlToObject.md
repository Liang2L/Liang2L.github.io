---
title: "Xml转Object（xstream及自定义解析）"
date: 2019-07-16 10:47:11
categories:
- "小功能"
---

xml格式转为object对象，分三种情况：

1、常规标签格式转化

2、标签属性格式转化

3、两种方式相结合的xml格式转化

备注：需要用到的jar包 xstream，xpp3

### 一、常规标签格式转化

主要使用xstream中 @XStreamAlias 标签，将xml标签和实体类属性进行一一对应后，使用util类转化方法进行转化即可。

xml格式如下：

```xml
<User>
  <userName>lanweihong</userName>
  <email>lwhhhp@gmail.com</email>
</User>
```

#### User.java

```java
@XStreamAlias("User")
public class User {

    @XStreamAlias("userName")
    private String userName;
    
    @XStreamAlias("email")
    private String email;

}
```

#### 转化方式：

```
User user = XmlUtils.xmlToObject(xmlStr);
```

### 二、标签属性格式转化

主要使用xstream中 @XStreamAsAttribute 和 @XStreamImplicit 标签，将xml标签的属性和实体类属性进行一一对应后进行转化即可。

xml格式如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<info id="200000000002" type="general_log_info" resultnum="40000">
	<log SeviceGroup="15" SeviceType="401" User-Agent="1"></log>
	<log SeviceGroup="16" SeviceType="402" User-Agent="2"></log>
</info>
```

#### Info.java

最外层标签使用 @XStreamAlias 对应实体类名

标签属性使用 @XStreamAsAttribute 对应实体类属性（字段名称必须一致）

内层集合标签使用 @XStreamImplicit(itemFieldName = "log") 对应实体类集合属性

```java
@XStreamAlias("info")
public class HttpInfoVo {

    @XStreamAsAttribute
    private String id;

    @XStreamAsAttribute
    private String type;

    @XStreamAsAttribute
    private String resultnum;

    @XStreamImplicit(itemFieldName = "log")
    private List<HttpLogVo> logs = new ArrayList<HttpLogVo>();
}
```

#### Log.java

```java
@XStreamAlias("log")
public class HttpLogVo {

    @XStreamAsAttribute
    private String SeviceGroup;

    @XStreamAsAttribute
    private String SeviceType;

    @XStreamAsAttribute
    private String userAgent;

}
```

#### 转化方法

因为实体类属性不能使用"-"这类特殊符号，所以讲xml读取后要将带有"-"的字段进行转化（如User-Agent转为userAgent,转化后的字段保持与实体类字段一致即可）

```java
XStream xstream = new XStream(new DomDriver());
xstream.autodetectAnnotations(true);
xstream.processAnnotations(Info.class);
xmlStr = xmlStr.replaceAll("User-Agent","userAgent");

Info info = (Info) xstream.fromXML(xmlStr);
```

### 三、两种xml格式相结合的xml转化

xml样例如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<info id="200000000002" type="general_log_info" resultnum="40000">
    <log SeviceGroup="15" SeviceType="401" User-Agent="1">
        <id>1</id>
        <name>test</name>
    </log>
    <log SeviceGroup="16" SeviceType="402" User-Agent="2"></log>
</info>
```

此时只需针对内层的标签添加 @XStreamAlias 对应实体类属性名即可

#### 更改后的Log.java

```java
@XStreamAlias("log")
public class HttpLogVo {

    @XStreamAsAttribute
    private String SeviceGroup;

    @XStreamAsAttribute
    private String SeviceType;

    @XStreamAsAttribute
    private String userAgent;
    
    @XStreamAlias("id")
    private String id;
    
    @XStreamAlias("name")
    private String name;

}
```

### 四、自定义xml解析（性能最好）

引入maven依赖

```xml
<dependency>
    <groupId>dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>1.6.1</version>
</dependency>
```

代码示例

```java
private HttpInfoVo getLogsByXml(String line) {
        InputStream in = new ByteArrayInputStream(line.getBytes());
        SAXReader reader = new SAXReader();
        Document doc = null;
        Element root = null;
        try {
            doc = reader.read(in);
            root = doc.getRootElement();
        } catch (DocumentException e) {
            logger.info("xml文件读取失败!",e);
        }

        HttpInfoVo info = new HttpInfoVo();
        List<HttpLogVo> logVos = new ArrayList<>();
        if("info".equals(root.getName())) {
            //获取info标签中的type属性值，用于区分http和gene话单
            String type = root.attributeValue("type");
            info.setType(type);
            List<Element> elements = root.elements();
            for (Element element : elements) {
                if ("log".equals(element.getName())) {
                    HttpLogVo log = new HttpLogVo();
                    log.setMsisdn(element.attributeValue("MSISDN"));
                    log.setSeviceGroup(element.attributeValue("SeviceGroup"));
                    log.setSeviceType(element.attributeValue("SeviceType"));
                    String imsi = element.attributeValue("IMSI");
                    String imei = element.attributeValue("IMEI");
                    log.setIMSIIMEI(imsi+"/"+imei);
                    log.setApn(element.attributeValue("APN"));
                    log.setNetType(element.attributeValue("NetType"));
                    log.setSgsnIp(element.attributeValue("SGSN_IP"));
                    log.setSgsnPort(element.attributeValue("SGSN_PORT"));
                    log.setGgsnIp(element.attributeValue("GGSN_IP"));
                    log.setGgsnPort(element.attributeValue("GGSN_PORT"));
                    log.setsIP(element.attributeValue("SIP"));
                    log.setSport(element.attributeValue("Sport"));
                    log.setdIP(element.attributeValue("DIP"));
                    log.setDport(element.attributeValue("Dport"));
                    log.setDate(element.attributeValue("Date"));
                    log.setUserAgent(element.attributeValue("User-Agent"));
                    log.settVendor(element.attributeValue("TVENDOR"));
                    log.settModel(element.attributeValue("TMODEL"));
                    log.settType(element.attributeValue("TTYPE"));
                    if ("http_log_info".equals(type)) {
                        log.setUrl(element.attributeValue("URL"));
                        log.setHost(element.attributeValue("Host"));
                        log.setxOnlineHost(element.attributeValue("X-online-Host"));
                        log.setContentType(element.attributeValue("Content-Type"));
                        log.setHttpCmd(element.attributeValue("HTTP-CMD"));
                        log.setContentLength(element.attributeValue("Content-Length"));
                    }
                    logVos.add(log);
                }
            }

            //log节点解析完毕后
            info.setLogs(logVos);
        }
        return info;
    }
```