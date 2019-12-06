---
title: "https接口调用去掉证书验证"
date: 2019-12-06 14:50:51
categories:
- "日常踩坑"
---

### https接口调用示例

#### 1、POST格式调用
通过POST方式调用https接口

```
TrustManager[] tm = {new HttpsManager()};
SSLContext sslContext = SSLContext.getInstance("TLS");
sslContext.init(null, tm, new SecureRandom());
SSLSocketFactory ssf = sslContext.getSocketFactory();
HttpsURLConnection httpsURLConnection = (HttpsURLConnection) url.openConnection();
httpsURLConnection.setRequestMethod("POST");
httpsURLConnection.setUseCaches(false);
httpsURLConnection.setInstanceFollowRedirects(true);
httpsURLConnection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
httpsURLConnection.setHostnameVerifier(new HttpsManager().new TrustAnyHostnameVerifier());
httpsURLConnection.setSSLSocketFactory(ssf);
httpsURLConnection.setDoOutput(true);
httpsURLConnection.connect();
DataOutputStream out = new DataOutputStream(httpsURLConnection.getOutputStream());
out.write(data.getBytes("UTF-8"));
out.flush();
out.close();
BufferedReader responseReader = new BufferedReader(new InputStreamReader(httpsURLConnection.getInputStream(), "UTF-8"));
String lines;
while ((lines = responseReader.readLine()) != null) {
    result.append(lines);
}
responseReader.close();
// 断开连接
httpsURLConnection.disconnect();
```

#### 2、GET格式调用
通过GET方式调用https接口
相比较于POST方式调用https接口，GET方式要注意一下几点
- httpsURLConnection.setRequestMethod("GET")
- 去掉httpsURLConnection.setDoOutput(true)
- 去掉new DataOutputStream(httpsURLConnection.getOutputStream())。否则即使将RequestMethod设置为GET后，通过new DataOutputStream()也==会将RequestMethod重置为POST==
```
TrustManager[] tm = {new HttpsManager()};
SSLContext sslContext = SSLContext.getInstance("TLS");
sslContext.init(null, tm, new SecureRandom());
SSLSocketFactory ssf = sslContext.getSocketFactory();
HttpsURLConnection httpsURLConnection = (HttpsURLConnection) url.openConnection();
//将POST改为GET
httpsURLConnection.setRequestMethod("GET");
httpsURLConnection.setUseCaches(false);
httpsURLConnection.setInstanceFollowRedirects(true);
httpsURLConnection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
httpsURLConnection.setHostnameVerifier(new HttpsManager().new TrustAnyHostnameVerifier());
httpsURLConnection.setSSLSocketFactory(ssf);
//httpsURLConnection.setDoOutput(true);
httpsURLConnection.connect();
/** 
DataOutputStream out = new DataOutputStream(httpsURLConnection.getOutputStream());
out.write(data.getBytes("UTF-8"));
out.flush();
out.close();
**/
BufferedReader responseReader = new BufferedReader(new InputStreamReader(httpsURLConnection.getInputStream(), "UTF-8"));
String lines;
while ((lines = responseReader.readLine()) != null) {
    result.append(lines);
}
responseReader.close();
// 断开连接
httpsURLConnection.disconnect();
```
#### 不校验https证书的调用方式
某些情况下服务端只能通过https进行访问，但服务端并没有正式合法证书，这时可以通过绕过证书校验的方式访问到服务端，修改点如下(==以GET方式为例==)：
1. 重写TrustManager

```
TrustManager[] trustAllCerts = new TrustManager[] {new X509TrustManager() {
        @Override
        public java.security.cert.X509Certificate[] getAcceptedIssuers() {
            return new java.security.cert.X509Certificate[]{};
        }
        @Override
        public void checkClientTrusted(X509Certificate[] certs, String authType) {
        }
        @Override
        public void checkServerTrusted(X509Certificate[] certs, String authType) {
        }
    }   
};
```
2. 初始化sslContext

```
sslContext.init(null, trustAllCerts, new java.security.SecureRandom());
```

3. 重写HostnameVerifier

```
HostnameVerifier allHostsValid = new HostnameVerifier(){
    @Override
    public boolean verify(String hostname, SSLSession session) {
        return true;
    }

};
```
4. HostnameVerifier属性赋值

```
httpsURLConnection.setHostnameVerifier(allHostsValid);
```
### 获取http接口响应的小窍门
在服务端返回非成功（200）的状态码时，也可以通过==httpsURLConnection.getErrorStream==()获取返回的错误信息用于问题定位

```
BufferedReader responseReader = null;
if (code == 200) {
    responseReader = new BufferedReader(new InputStreamReader(httpsURLConnection.getInputStream(), "UTF-8"));
} else {
    responseReader = new BufferedReader(new InputStreamReader(httpsURLConnection.getErrorStream(), "UTF-8"));
}
String lines;
while ((lines = responseReader.readLine()) != null) {
    result.append(lines);
}
responseReader.close();
// 断开连接
httpsURLConnection.disconnect();
```
