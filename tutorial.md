# 微服务开发入门教学

## 教程说明
通过该教程，你将会：
* 使用 spring cloud alibaba 搭建一个最小化的微服务集群。
* 并完成客户端和服务端之间的调用示例。

案例学习时间预计15分钟左右。

## 获取程序代码
本节，你将通过 git 命令下载程序代码

### 获取客户端程序代码
请使用下面的命令获取客户端程序代码
```bash
git clone "https://start.aliyun.com/git/type=maven-project&language=java&architecture=none&bootVersion=2.3.4.RELEASE&baseDir=client&groupId=com.example&artifactId=client&name=client&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.client&packaging=jar&javaVersion=1.8&dependencies=sca-nacos-discovery,web,actuator,cloud-feign&demos=nacosdiscoveryconsumer/client.git" /home/shell/client
```

### 获取服务端程序工程
请使用下面的命令获取服务端程序代码
```bash
git clone "https://start.aliyun.com/git/type=maven-project&language=java&architecture=none&bootVersion=2.3.4.RELEASE&baseDir=server&groupId=com.example&artifactId=server&name=server&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.server&packaging=jar&javaVersion=1.8&dependencies=sca-nacos-discovery,web,actuator&demos=nacosdiscoveryprovider/server.git" /home/shell/server
```

----
完成上面的操作以后，请手动触发刷新一下文件目录：<br>
![image](https://img.alicdn.com/tfs/TB1VQWqnwgP7K4jSZFqXXamhVXa-213-90.png)

完成以上操作后，你将会获得链各个工程的代码，如下：<br>
![image](https://img.alicdn.com/tfs/TB1JZzuqk9l0K4jSZFKXXXFjpXa-170-182.png)


## 修改配置
本节，你讲修改代码的一些基本配置，让程序可以运行。<br>
请认真按照本节的引导操作。在完成修改后，一定要记得保存哦。

### 修改服务端配置

* 打开 <tutorial-editor-open-file filePath="/home/shell/server/src/main/resources/application.properties">服务端的 application.properties</tutorial-editor-open-file> 文件：

* 为服务端配置注册中心地址<br>
将 `spring.cloud.nacos.discovery.server-addr` 的值改为：`mse-6d50f4f0-p.nacos-ans.mse.aliyuncs.com:8848`；<br>
> 该注册中心为平台提供的免费注册中心服务实例；

* 修改 Web 访问端口<br>
将 `server.port` 的值改为：`61000`；<br>
> 出于安全性和其他平台限制的考虑，目前外部只能使用6\[0-5\]000六个端口。<br>
> 注意，不是 `management.server.port`！！。

* 保存文件<br>
完成修改以后，别忘了保存哦
> tips: 如何保存文件？
> * mac 用户：cmd + s
> * windows | linux 用户：ctrl + s

### 修改客户端配置

* 打开 <tutorial-editor-open-file filePath="/home/shell/client/src/main/resources/application.properties">客户端的 application.properties</tutorial-editor-open-file> 文件：

* 为client配置注册中心地址<br>
将 `spring.cloud.nacos.discovery.server-addr` 的值改为：`mse-6d50f4f0-p.nacos-ans.mse.aliyuncs.com:8848`；<br>
> 服务端和客户端要使用一个注册中心才能相互发现哦；

* 修改 Web 访问端口<br>
将 `server.port` 的值改为：`60000`；<br>
> 要避免和服务端发生端口冲突，而61000已经被分配给了服务端，这里就用60000。

* 修改 Web 管理端口<br>
将 `management.server.port` 的值改为：`8082`；<br>
> 本案例不使用这个端口，只是避免端口冲突。

* 修改OpenFeign方式调用的服务名称<br>
打开文件 <tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/EchoService.java">EchoService</tutorial-editor-open-file> <br>
将其中的 `nacos-discovery-provider-sample` 替换为 `server`

* 修改RestTemplate方式调用的服务名称<br>
打开文件 <tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/RestTemplateController.java">RestTemplateController</tutorial-editor-open-file> <br>
将其中的 `nacos-discovery-provider-sample` 替换为 `server`

* 保存文件<br>
客户端修改了3个文件，别忘了都保存哦

> 对 EchoService 和 RestTemplateController 修改，会在下一节做详细说明；

## 功能&代码说明

本节主要是对内容的说明和介绍，没有对项目的操作内容；

### 服务端
在本案例中，服务端只提供一个服务，即：
<tutorial-editor-open-file filePath="/home/shell/server/src/main/java/com/example/server/demos/nacosdiscoveryprovider/EchoServiceController.java">EchoServiceController</tutorial-editor-open-file> 
<br>
该服务只有一个方法，接收字符串型的消息，并返回 `"[ECHO] : " + message` 内容。这里的逻辑实现，并不具有太多的业务意义，只是对服务端逻辑执行的演示。

可以看到， 在 EchoServiceController 上增加了 `@RestController` 注解。熟悉 spring 的同学应该知道，这代表了被标注类是一个 Rest 风格的 http 接口。<br>
在 echo 方法上，由于标注了 `@GetMapping("/echo/{message}")` 注解，所以可以通过 `http://ip:port/echo/{message}` 来直接访问。<br>
其中的 \{message\} 可以被替换为你需要的任何消息。<br>
后面章节会对具体的访问做演示。


### 客户端
客户端程序自身并没有业务逻辑的实现，而是通过调用服务端的业务服务来实现业务，所以在本案例中需要重点关注如何使用客户端调用服务端。

在本案例中，客户端通过 nacos 的注册中心功能实现对服务端的发现。所以你会发现在客户端里并没有配置任何 `服务端的地址` 信息。<br>
参考：<tutorial-editor-open-file filePath="/home/shell/client/src/main/resources/application.properties">客户端的 application.properties</tutorial-editor-open-file>


本案例中的客户端通过两种方式调用服务端，所谓的”两种方式“具体来说是两种消费客户端的编程模型：
#### 使用 OpenFeign 方式<br>
参考：<tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/OpenFeignController.java">OpenFeignController</tutorial-editor-open-file>，<tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/EchoService.java">EchoService</tutorial-editor-open-file><br>

* `OpenFeignController` 作为web调用的入口，用于接收前端的调用请求，并向内调用业务与服务实现功能。<br>
* `EchoService` 是应用中对服务端所提供服务的引用接口。<br>
在这个接口上，标注了 `@FeignClient` 注解，以表示这个接口的服务是哪个服务端提供的。<br>
`@FeignClient` 注解的值代表了服务端应用的 `服务端应用名`，以便于在多个不通过的服务提供者之间确定具体的服务。

####  使用 RestTemplate 方式<br>
参考：<tutorial-editor-open-file filePath="/home/shell/client/src/main/java/com/example/client/demos/nacosdiscoveryconsumer/RestTemplateController.java">RestTemplateController</tutorial-editor-open-file><br>
RestTemplate 是 spring 对所有 restful 服务调用的封装。<br>
在 RestTemplateController 中，通过 restTemplate.getForObject 来调用服务端的 EchoServiceController.echo 方法。<br>
通过前文的操作，将 getForObject 的第一个参数改为 `"http://server/echo/" + message`。这看起来像是一个标准的url地址，但是其中的`server`并不是域名，而是服务提供者的应用名。<br>

----
前文说到，客户端并不关注`服务端的地址`，但是需要关注`服务端应用名`，这两个概念是有区别的：
* `服务端应用名`：是一个逻辑概念，代表可以被独立部署的一套应用程序；
* `服务端的地址`：是物理概念，是实际部署以后具体的物理地址，例如IP；

每个`服务端应用名`可以部署多份实例，每个实例都有自己的`服务端的地址`。


## 编译打包
本节，你需要使用 maven 的命令将程序代码编译打包为可执行的 jar 包

### 编译服务端程序

* 进入服务端目录
```bash
cd /home/shell/server
```

* 执行编译命令
```bash
mvn clean package -Dmaven.test.skip
```
> tips: 第一次编译，因为本地 maven 仓库缺少相关的类库，编译可能会花1~2分钟的时间

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

本节，你将使用 java 命令来运行上一步打包完成的 jar 文件

### 启动服务端
1. 开启新 console 窗口：<br>
![image](https://img.alicdn.com/tfs/TB1yWaS0uL2gK0jSZPhXXahvXXa-386-96.png)

2. 在新窗口中执行命令
```bash
java -jar /home/shell/server/target/server-0.0.1-SNAPSHOT.jar
```
看到下面的反馈则表示启动成功<br>
![image](https://img.alicdn.com/tfs/TB1dAzYl5DsXe8jSZR0XXXK6FXa-620-192.png)


### 启动客户端
1. 开启新 console 窗口：<br>
![image](https://img.alicdn.com/tfs/TB1yWaS0uL2gK0jSZPhXXahvXXa-386-96.png)

2. 在新窗口中执行命令
```bash
java -jar /home/shell/client/target/client-0.0.1-SNAPSHOT.jar
```
看到下面的反馈则表示启动成功<br>
![image](https://img.alicdn.com/tfs/TB1XVX8p_M11u4jSZPxXXahcXXa-608-195.png)


## 访问应用程序

本节，你将通过浏览器访问在上一节运行起来的服务端程序和客户端程序

### 直接访问服务端程序
由于服务端使用http协议提供服务，所以可以直接使用浏览器访问，我们先来验证服务端的功能。<br>
前面已经为服务端设置了60000的端口，我们先链接该端口。<br>
点击平台右上角的网页预览按钮，选择 60000 端口：<br>
![image](https://img.alicdn.com/tfs/TB1PyCqnwgP7K4jSZFqXXamhVXa-331-320.png)<br>
由于在打开的窗口中没有指定路径，所以首次打开会有错误提示，需要在打开的窗口中，追加服务路径：
![image](https://img.alicdn.com/tfs/TB1FJ.W0xz1gK0jSZSgXXavwpXa-617-89.png)<br>
看到上面的内容代表服务端可以正常工作。

### 访问客户端程序
由于客户端提供2种调用方式，这里也分别请求者两个入口，参照访问服务端的方式，选择 61000 端口访问客户端。

* Openfeign<br>
追加如下路径`/feign/echo/{message}` 其中 {message} 可以替换为你需要的信息，你可以看到下面的效果：<br>
![image](https://img.alicdn.com/tfs/TB1in780EY1gK0jSZFCXXcwqXXa-664-78.png)
* RestTemplate<br>
追加如下路径`/call/echo/{message}` 其中 {message} 可以替换为你需要的信息，你可以看到下面的效果：<br>
![image](https://img.alicdn.com/tfs/TB19WR8p_M11u4jSZPxXXahcXXa-666-73.png)

## 附录
如果你觉得还不过瘾，想在自己的环境中学习和调试前面的代码，可以在这里获得：
* <a target="_blank" href="https://start.aliyun.com/bootstrap.html/#!type=maven-project&language=java&architecture=none&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=provider&name=provider&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.provider&dependencies=sca-nacos-discovery,web,actuator&demos=nacosdiscoveryprovider">服务端程序</a>
* <a target="_blank" href="https://start.aliyun.com/bootstrap.html/#!type=maven-project&language=java&architecture=none&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=client&name=client&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.client&dependencies=sca-nacos-discovery,web,actuator,cloud-feign&demos=nacosdiscoveryconsumer">客户端程序</a>

