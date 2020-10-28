# 微服务开发入门教学

## 教程说明
微服务调用入门教程



## 获取程序代码
由于最小的微服务系统需要至少2个应用参与，这里我们就分别拉取一个client一个server两个应用。

* 获取Client代码：
```bash
git clone "https://start.aliyun.com/git/type=maven-project&language=java&architecture=none&bootVersion=2.3.4.RELEASE&baseDir=client&groupId=com.example&artifactId=client&name=client&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.client&packaging=jar&javaVersion=1.8&dependencies=sca-nacos-discovery,web,actuator,cloud-feign&demos=nacosdiscoveryconsumer/client.git" /home/shell/client
```

* 获取Server工程：
```bash
git clone "https://start.aliyun.com/git/type=maven-project&language=java&architecture=none&bootVersion=2.3.4.RELEASE&baseDir=server&groupId=com.example&artifactId=server&name=server&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.server&packaging=jar&javaVersion=1.8&dependencies=sca-nacos-discovery,web,actuator&demos=nacosdiscoveryprovider/server.git" /home/shell/server
```


完成以上操作后，你将会获得链各个工程的代码，如下：<br>
![image](https://img.alicdn.com/tfs/TB1njut0AL0gK0jSZFtXXXQCXXa-212-558.png)



## 修改配置
要实现微服务的注册与发现功能，需要分别向client和server配置注册中心的地址。

### 修改服务端配置

* 打开 <tutorial-editor-open-file filePath="/home/shell/client/src/main/resources/application.properties">client 的 application.properties</tutorial-editor-open-file> 文件：

* 为client配置注册中心地址<br>
将 `spring.cloud.nacos.discovery.server-addr` 的值改为：`mse-6d50f4f0-p.nacos-ans.mse.aliyuncs.com:8848`；<br>
该注册中心为平台提供的免费注册中心服务实例；

* 修改 Web 访问端口<br>
将 `server.port` 的值改为：`60000`；<br>
出于安全性和其他平台限制的考虑，目前外部只能使用6\[0-5\]000六个端口。

* 修改 Web 管理端口<br>
将 `management.server.port` 的值改为：`8081`；<br>
本案例不使用这个端口，只是避免端口冲突。

### 修改客户端配置

* 打开 <tutorial-editor-open-file filePath="/home/shell/server/src/main/resources/application.properties">server 的 application.properties</tutorial-editor-open-file> 文件：

* 为client配置注册中心地址<br>
将 `spring.cloud.nacos.discovery.server-addr` 的值改为：`mse-6d50f4f0-p.nacos-ans.mse.aliyuncs.com:8848`；<br>
该注册中心为平台提供的免费注册中心服务实例，服务端和客户端要使用一个注册中心才能相互发现；

* 修改 Web 访问端口<br>
将 `server.port` 的值改为：`61000`；<br>
出于安全性和其他平台限制的考虑，目前外部只能使用6\[0-5\]000六个端口。同时不要和服务端发生端口冲突。

* 修改 Web 管理端口<br>
将 `management.server.port` 的值改为：`8082`；<br>
本案例不使用这个端口，只是避免端口冲突。

* 修改OpenFeign方式调用的服务名称<br>
打开文件 <tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/EchoService.java">EchoService</tutorial-editor-open-file> <br>
将其中的 `nacos-discovery-provider-sample` 替换为 `server`

* 修改RestTemplate方式调用的服务名称<br>
打开文件 <tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/RestTemplateController.java">RestTemplateController</tutorial-editor-open-file> <br>
将其中的 `nacos-discovery-provider-sample` 替换为 `server`

## 功能&代码说明

### server 端
server程序作为服务的提供者只提供一个
<tutorial-editor-open-file filePath="/home/shell/server/src/main/java/com/example/server/demos/nacosdiscoveryprovider/EchoServiceController.java">EchoServiceController</tutorial-editor-open-file> 
服务。<br>
该服务只有一个方法，接收字符串型的消息，并返回 `"[ECHO] : " + message` 内容;<br>
EchoServiceController 通过 http 协议对外提供服务能力，所以这个服务也可以直接使用http的客户端访问，例如你的浏览器；<br>
server 程序，通过 nacos 实现应用的注册。

### client 端
client程序，消费server端提供的服务
client 通过注册中心发现服务端应用。<br>
client 提供2中方式调用 server。
* OpenFeign<br>
<tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/OpenFeignController.java">OpenFeignController</tutorial-editor-open-file> 
* RestTemplate<br>
<tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/RestTemplateController.java">RestTemplateController</tutorial-editor-open-file> 

## 编译打包

### 编译服务端程序

* 进入服务端目录
```bash
cd /home/shell/server
```

* 执行编译命令
```bash
mvn clean package -Dmaven.test.skip
```
> tipc: 第一次编译，因为本地 maven 仓库缺少相关的类库，编译可能会花1~2分钟的时间

### 编译客户端程序
* 进入服务端目录
```bash
cd /home/shell/client
```

* 执行编译命令
```bash
mvn clean package -Dmaven.test.skip
```

> tips: 如果修改代码以后，需要重新执行编译命令

## 运行程序

我们使用原生 java -jar 命令直接启动应用

### 启动服务端
1. 开启新 console 窗口：<br>
![image](https://img.alicdn.com/tfs/TB1yWaS0uL2gK0jSZPhXXahvXXa-386-96.png)

2. 在新窗口中执行命令
```bash
java -jar /home/shell/server/target/server-0.0.1-SNAPSHOT.jar
```
看到下面的反馈则表示启动成功<br>
![image](https://img.alicdn.com/tfs/TB1eLr_nggP7K4jSZFqXXamhVXa-1397-452.png)


### 启动客户端
1. 开启新 console 窗口：<br>
![image](https://img.alicdn.com/tfs/TB1yWaS0uL2gK0jSZPhXXahvXXa-386-96.png)

2. 在新窗口中执行命令
```bash
java -jar /home/shell/client/target/client-0.0.1-SNAPSHOT.jar
```
看到下面的反馈则表示启动成功<br>
![image](https://img.alicdn.com/tfs/TB1aX9V0xv1gK0jSZFFXXb0sXXa-1366-501.png)


## 访问应用程序
### 直接访问服务端程序
由于服务端使用http协议提供服务，所以可以直接使用浏览器访问，我们先来验证服务端的功能。<br>
前面已经为服务端设置了61000的端口，我们先链接该端口。<br>
点击平台右上角的网页预览按钮，选择 61000 端口：<br>
![image](https://img.alicdn.com/tfs/TB1COaJ0vb2gK0jSZK9XXaEgFXa-307-296.png)<br>
由于在打开的窗口中没有指定路径，所以首次打开会有错误提示，需要在打开的窗口中，追加服务路径：
![image](https://img.alicdn.com/tfs/TB1y7520uL2gK0jSZFmXXc7iXXa-558-99.png)<br>
看到上面的内容代表服务端可以正常工作。

<tutorial-web-preview port="61000">打开浏览器</tutorial-web-preview>

### 访问客户端程序
由于客户端提供2种调用方式，这里也分别请求者两个入口，参照访问服务端的方式，选择 60000 端口访问客户端。

* Openfeign<br>
追加如下路径`/feign/echo/{message}` 其中 {message} 可以替换为你需要的信息，你可以看到下面的效果：<br>
![image](https://img.alicdn.com/tfs/TB1mnqW0xv1gK0jSZFFXXb0sXXa-618-80.png)
* RestTemplate<br>
追加如下路径`/call/echo/{message}` 其中 {message} 可以替换为你需要的信息，你可以看到下面的效果：<br>
![image](https://img.alicdn.com/tfs/TB1.8940pP7gK0jSZFjXXc5aXXa-562-76.png)