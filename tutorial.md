# 微服务开发入门教学

## 教程说明
微服务调用入门教程



## 获取程序代码
由于最小的微服务系统需要至少2个应用参与，这里我们就分别拉取一个client一个server两个应用。

获取Client代码：
```bash
git clone "https://start.aliyun.com/git/type=maven-project&language=java&architecture=none&bootVersion=2.3.4.RELEASE&baseDir=client&groupId=com.example&artifactId=client&name=client&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.client&packaging=jar&javaVersion=1.8&dependencies=sca-nacos-discovery,web,actuator,cloud-feign&demos=nacosdiscoveryconsumer/client.git" client
```

获取Server工程：
```bash
git clone "https://start.aliyun.com/git/type=maven-project&language=java&architecture=none&bootVersion=2.3.4.RELEASE&baseDir=server&groupId=com.example&artifactId=server&name=server&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.server&packaging=jar&javaVersion=1.8&dependencies=sca-nacos-discovery,web,actuator&demos=nacosdiscoveryprovider/server.git" server
```

## 修改配置
要实现微服务的注册与发现功能，需要分别向client和server配置注册中心的地址。

### 修改服务端配置

* 打开 <tutorial-editor-open-file filePath="/home/shell/client/src/main/resources/application.properties">client 的 application.properties</tutorial-editor-open-file> 文件：

* 为client配置注册中心地址<br>
将spring.cloud.nacos.discovery.server-addr的值改为：`mse-6d50f4f0-p.nacos-ans.mse.aliyuncs.com:8848`；<br>
该注册中心为平台提供的免费注册中心服务实例；

* 修改访问端口<br>
将 `server.port` 的值改为：`60000`；<br>
出于安全性和其他平台限制的考虑，目前外部只能使用600000五个端口。

### 修改客户端配置

* 打开 <tutorial-editor-open-file filePath="/home/shell/server/src/main/resources/application.properties">server 的 application.properties</tutorial-editor-open-file> 文件：

* 为client配置注册中心地址<br>
将 `spring.cloud.nacos.discovery.server-addr` 的值改为：`mse-6d50f4f0-p.nacos-ans.mse.aliyuncs.com:8848`；<br>
该注册中心为平台提供的免费注册中心服务实例，服务端和客户端要使用一个注册中心才能相互发现；

* 修改访问端口<br>
将 `server.port` 的值改为：`60000`；<br>
出于安全性和其他平台限制的考虑，目前外部只能使用600000五个端口，同时不要和服务端发生端口冲突。
