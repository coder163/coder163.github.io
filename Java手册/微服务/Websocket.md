
### 1. Websocket初识

#### 1.1. 参考文章
http://www.voidcn.com/article/p-nyvobiji-bcc.html

#### 1.2. HTTP长连接和短连接

短连接的操作步骤是：

- 建立连接——数据传输——关闭连接...建立连接——数据传输——关闭连接

长连接的操作步骤是：

- 建立连接——数据传输...（保持连接）...数据传输——关闭连接

长连接和短连接的优点

- 长连接可以省去较多的TCP建立和关闭的操作，减少浪费，节约时间
- 短连接对于服务器来说管理较为简单，存在的连接都是有用的连接，不需要额外的控制手段。

长连接和短连接的缺点

- 长连接：存活功能的探测周期太长,随着客户端连接越来越多，server早晚有扛不住的时候
- 如果客户请求频繁，将在TCP的建立和关闭操作上浪费时间和带宽。

#### 1.3. 轮询

是指浏览器通过JavaScript启动一个定时器，然后以固定的间隔给服务器发请求，询问服务器有没有新消息。这个机制的缺点一是实时性不够，二是频繁的请求会给服务器带来极大的压力。


#### 1.4. 消息队列中点对点与发布订阅

##### 1.4.1. 背景知识

JMS一个在 Java标准化组织（JCP）内开发的标准（代号JSR 914）。2001年6月25日，Java消息服务发布JMS 1.0.2b，2002年3月18日Java消息服务发布 1.1.Java消息服务（Java Message Service，JMS）应用程序接口一个Java平台中关于面向消息中间件（MOM）的API，用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信。点对点与发布订阅最初是由JMS定义的。这两种模式主要区别或解决的问题就是发送到队列的消息能否重复消费(多订阅)

##### 1.4.2. JMS中定义

JMS规范目前支持两种消息模型：点对点（point to point， queue）和发布/订阅（publish/subscribe，topic）。

点对点：

![mark](http://imgs.coder163.com/coder16/180418/CeLc5hL87g.png?imageslim)

- 消息生产者生产消息发送到queue中，然后消息消费者从queue中取出并且消费消息。这里要注意：
- 消息被消费以后，queue中不再有存储，所以消息消费者不可能消费到已经被消费的消息。
- Queue支持存在多个消费者，但是**对一个消息而言，只会有一个消费者可以消费。**

发布/订阅

消息生产者（发布）将消息发布到topic中，同时有多个消息消费者（订阅）消费该消息。和点对点方式不同，**发布到topic的消息会被所有订阅者消费。**

![mark](http://imgs.coder163.com/coder16/180418/79C4h751F0.png?imageslim)


总结:

- queue实现了负载均衡，一个消息只能被一个消费者接受，当没有消费者可用时，这个消息会被保存直到有 一个可用的消费者，一个queue可以有很多消费者，他们之间实现了负载均衡，所以Queue实现了一个可靠的负载均衡。
- topic实现了发布和订阅，当你发布一个消息，所有订阅这个topic的服务都能得到这个消息，所以从1到N个订阅者都能得到一个消息的拷贝，只有在消息代理收到消息时有一个有效订阅时的订阅者才能得到这个消息的拷贝。

#### 1.5. WebSocket协议

WebSocket是HTML5新增的协议，它的目的是在浏览器和服务器之间建立一个不受限的双向通信的通道，比如说，服务器可以在任意时刻发送消息给浏览器。目前的Web服务绝大部分都是基于HTTP的，因此为了使得WebSocket能够被广泛使用，WebSocket决定使用HTTP来作为初始的握手（handshake）。WebSocket的握手基于HTTP中的协议升级机制，当服务端收到这个HTTP的协议升级请求后，如果支持WebSocket协议则返回HTTP状态码101。

> PS：[参考资料-知乎](https://www.zhihu.com/question/20215561)

##### 1.5.1. Websocket连接的创建过程

WebSocket连接必须由浏览器发起，因为请求协议是一个标准的HTTP请求，格式如下：

```
GET ws://localhost:3000/ws/chat HTTP/1.1
Host: localhost
Upgrade: websocket
Connection: Upgrade
Origin: http://localhost:3000
Sec-WebSocket-Key: client-random-string
Sec-WebSocket-Version: 13
```

> - GET请求的地址不是类似/path/，而是以ws://开头的地址；
> - 请求头Upgrade: websocket和Connection: Upgrade表示这个连接将要被转换为WebSocket连接；
> - Sec-WebSocket-Key是用于标识这个连接，并非用于加密数据；
> - Sec-WebSocket-Version指定了WebSocket的协议版本。

服务器如果接受该请求，就会返回如下响应

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: server-random-string
```

> - 101表示本次连接的HTTP协议即将被更改，更改后的协议就是Upgrade: websocket指定的WebSocket协议。
> - 版本号和子协议规定了双方能理解的数据格式，以及是否支持压缩等等。如果仅使用WebSocket的API，就不需要关心这些


消息有两种，一种是文本，一种是二进制数据。通常，我们可以发送JSON格式的文本

##### 1.5.2. WebSocket的浏览器兼容性问题

目前相当一部分浏览器不支持WebSocket协议，譬如IE浏览器只在IE10或者更高版本支持WebSocket。另外，一些受限的代理也可能会禁止HTTP的协议升级，从而阻碍WebSocket的握手与使用。因此为了协助client与server进行消息格式的协商，WebSocket在握手的时候保留了一个子协议字段。

STOMP是面向文本的消息传送协议。STOMP客户端与支持STOMP协议的消息代理进行通信。STOMP使用不同的命令，如连接，发送，订阅，断开等进行通信


##### 1.5.3. Spring与STOMP

根据Java WebSocket API规范（JSR-356），Spring 4提供了一个包含在模块spring-websocket和spring-messaging中的解决方案

STOMP中定义了三种消息：

1. SEND：client向server发送消息
2. SUBSCRIBE：client向server订阅某种类型的消息
3. MESSAGE：server向client分发消息

Spring的spring-messaging模块支持STOMP协议，包含了消息处理的关键抽象。下面是一个简单的消息处理示意图：

![mark](http://imgs.coder163.com/coder16/180417/182aJJL995.png?imageslim)


关键实体的作用如下：

1. Message：消息，里面带有header和payload。
2. MessageHandler：处理client消息的实体。
3. MessageChannel：解耦消息发送者与消息接收者的实体。举个例子，client可以发送消息到channel，而不用管这条消息最终被谁处理。
4. Broker：存放消息的中间件，client可以订阅broker中的消息。

### 2. Websocket初探

#### 2.1. 软件环境
1. Eclipse版本：Version: Mars.2 Release (4.5.2)
2. SpringBoot：2.0.0.RELEASE
3. JDK：1.8.0_151_64位


#### 2.2. 配置文件

SpringBoot配置：application.properties

```properties
application.properties
```


SpringBoot配置：application.properties

```properties
server.port=8080
logging.level.org.springframework=info
logging.level.root=info
```

核心依赖：pom.xml

```xml
<dependencies>
	<dependency>
		<groupId>org.projectlombok</groupId>
		<artifactId>lombok</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-websocket</artifactId>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-devtools</artifactId>
		<scope>runtime</scope>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
		<scope>test</scope>
	</dependency>
</dependencies>
```

日志配置：logback-spring.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration>
    <!--定义一个带颜色的控制台输出日志PATTERN-->
    <property name="CONSOLE_LOG_PATTERN"
              value="时间:%date{yyyy-MM-dd HH:mm:ss} 级别: %blue(%-5level) 进程: %magenta(%thread) 类: %green(%logger) 信息: %msg%n"/>
    <!--定义一个不带颜色的文件日志输出格式,因为文件不能带颜色-->
    <property name="ERRINFOFILE_LOG_PATTERN"
              value="时间:%date{yyyy-MM-dd HH:mm:ss} 级别: %-5level 进程: %thread 类: %logger 信息: %msg%n"/>

    <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender">
        <layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>
                ${CONSOLE_LOG_PATTERN}
            </pattern>
        </layout>
    </appender>
    <root level="info">
        <appender-ref ref="consoleLog" />
    </root>
</configuration>
```
#### 2.3. 服务器端

 websocket核心类

```java
package com.example.demo.websocket;

import java.util.concurrent.CopyOnWriteArraySet;

import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.ServerEndpoint;

import org.springframework.stereotype.Component;

import lombok.extern.slf4j.Slf4j;

/**
 * WebSocket主要的消息类
 * @author 侯叶飞
 */
@ServerEndpoint(value = "/api/websocket")
@Component
@Slf4j
public class WebSocket {
	/*每个浏览器连接都会有一个新的会话对象*/
	private Session session;
	/*用来存储每个会话的session,静态的不会被实例化*/
	private static CopyOnWriteArraySet<WebSocket> webSocketSets = new CopyOnWriteArraySet<>();

	@OnOpen
	public void onOpen(Session session) {
		this.session = session;
		webSocketSets.add(this);
		log.info("【websocket消息】有新的连接, 总数:{}", webSocketSets.size());
	}

	@OnClose
	public void onClose() {
		webSocketSets.remove(this);
		log.info("【websocket消息】连接断开, 总数:{}", webSocketSets.size());
	}

	@OnError
	public void onError(Throwable e, Session session) {
		webSocketSets.remove(this);
		log.info("【websocket消息】连接出错或未关闭socket：" + e.getMessage());

	}

	@OnMessage
	public void onMessage(String message, Session session) {
		log.info("【websocket消息】收到客户端发来的消息:{}", message);


	}
	public void radioMessage(String message){
		for(WebSocket ws:webSocketSets){
			ws.session.getAsyncRemote().sendText("广播："+message);
		}
	}
}

```

websocket配置类

```java
package com.example.demo.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

/**
 * WebSocket Tomcat的配置,如果是war包这个需要去掉
 * @author 侯叶飞
 */
@Configuration
public class WebsocketConfig {
	@Bean
	public ServerEndpointExporter serverEndpointExporter() {
		return new ServerEndpointExporter();
	}
}
```

 SpringBoot启动类

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableScheduling;

@SpringBootApplication
@EnableScheduling
public class RedisDemoApplication {

	public static void main(String[] args) throws InterruptedException {
		SpringApplication.run(RedisDemoApplication.class, args);
	}
}

```

#### 2.4. 客户端

 index.html：

```HTML
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>整合示例</title>

</head>
<body>
	<a href="javascript:connection('ws://localhost:8080/api/websocket')">建立连接</a>
	<br />
	<div id="showMsg"></div>

	<!-- 引入自己的js -->
	<script type="text/javascript" src="app.js"></script>
</body>
</html>
```

 app.js：

```JavaScript

/* 创建一个方法,用来连接我们的websocket服务 */
function connection(url) {
	if ("WebSocket" in window) {
		console.log("您的浏览器支持 WebSocket!");
		// 打开一个 web socket
		var ws = new WebSocket(url);
		console.info(ws.readyState);

		ws.onmessage = function(evt) {
			var received_msg = evt.data;
			console.log("数据已接收..."+received_msg);
			showMessage(received_msg);
		};

	}else {
		// 浏览器不支持 WebSocket
		alert("您的浏览器不支持 WebSocket!");
	}
};

function showMessage(data) {
	var div = document.getElementById("showMsg");
	div.innerHTML = div.innerHTML + data+"<br />"
};



```

> 备注：完整的JS代码可以参考-[菜鸟教程-websocket](http://www.runoob.com/html/html5-websocket.html)





### 3. Websocket-消息实时推送

#### 3.1. 实时推送的流程代码

```java
MessageListenerAdapter listenerAdapterWebsocket(MessageReceiver receiver) {
	//配置redis的监听,指定处理接收到订阅频道发送过来消息的处理方法
	 return new MessageListenerAdapter(receiver, "receiveWebsocket");
}
public void receiveWebsocket(String str){
	//接收到redis的广播消息,调用Websocket的消息推送
	webSockets.sendMsg(str);
 }
public synchronized void sendMsg(String message){
	//判断需要广播的类型
	//将指定的消息广播出去
}

//Redis的广播代码
public void  sendData(Object obj,SocketDataTypeEnum em){
	//将接收到的信息进行Redis广播
}

```


### 4. Websocket获取HttpSession

### 5. 环境配置
1. SpringBoot2.0全家桶
2. 特别说明一下，现在很多服务器都支持websocket,我这次写代码用的是SpringBoot内置的tomcat

### 6. 应用场景

1. 如果只是单纯的配置一个建立连接发送消息，并不难,具体客户端编写参考这个 [菜鸟教程-websocket](http://www.runoob.com/html/html5-websocket.html)
2. 我需要解决的问题是：当客户端和服务器建立连接时，将登录的用户信息从httpSession中取出,这个问题的难点在于，websocket的请求和http请求完全不相关，所以没有办法直接获取HttpSession

### 7. 实施思路
1. 服务器端的编码实现，具体有两套方案
    1. 使用继承的方式，这种方式，我在看Spring官方介绍文档时貌似也是这种方式实现的
    2. 使用注解的方式：很蛋疼的是我们的项目使用的是这种方式
2. 具体思路
    1. 虽然websocket的请求和http请求完全不相关,但是如果基于注解的话，EndPoint支持读取一个配置
    2. 我当时的想法就是在配置中拦截或者获取HttpSession,事实证明思路是正确的，但是当我按照这个思路去百度谷歌发现获取的一律都是null，然后我就崩溃了
### 8. 具体编码实现

只贴核心代码,完整项目在本文的底部,我放在了github上

先上核心的服务器端消息处理类

```Java
/**
 * WebSocket主要的消息类
 * @author 侯叶飞
 */
 //onfigurator = WebsocketConfig.class 该属性就是我上面提到我们可以自己配置的东西
@ServerEndpoint(value = "/api/websocket", configurator = WebsocketConfig.class)
@Component
@Slf4j
public class WebSocket {
	/*每个浏览器连接都会有一个新的会话对象*/
	private Session session;
	/*用来存储每个会话的session,静态的不会被实例化*/
	private static CopyOnWriteArraySet<WebSocket> webSocketSets = new CopyOnWriteArraySet<>();
	/**
	 * 主要用来监听连接建立，config用来获取WebsocketConfig中的配置信息
	 * @param session
	 * @param config
	 */
	@OnOpen
	public void onOpen(Session session, EndpointConfig config) {
		log.info("config:{}", config.getUserProperties().get("name"));
		log.info("session:{}", config.getUserProperties().get("sessionid"));
		this.session = session;
		webSocketSets.add(this);
		log.info("【websocket消息】有新的连接, 总数:{}", webSocketSets.size());
	}

	@OnClose
	public void onClose() {
		webSocketSets.remove(this);
		log.info("【websocket消息】连接断开, 总数:{}", webSocketSets.size());
	}

	@OnError
	public void onError(Throwable e, Session session) {
		webSocketSets.remove(this);
		log.info("【websocket消息】连接出错或未关闭socket：" + e.getMessage());

	}

	@OnMessage
	public void onMessage(String message, Session session) {
		for(WebSocket ws:webSocketSets){
			ws.session.getAsyncRemote().sendText("广播："+message);
		}
		log.info("【websocket消息】收到客户端发来的消息:{}", message);
	}
}
```

下面代码就是核心的配置类

```Java
/**
 * 主要的配置类
 *  本类必须要继承Configurator,因为@ServerEndpoint注解中的config属性只接收这个类型
 * @author 侯叶飞
 *
 */
@Configuration
@Slf4j
public class WebsocketConfig extends ServerEndpointConfig.Configurator {

	private static final String HttpSession = null;
	/* 修改握手,就是在握手协议建立之前修改其中携带的内容 */
	@Override
	public void modifyHandshake(ServerEndpointConfig sec, HandshakeRequest request, HandshakeResponse response) {
		/*如果没有监听器,那么这里获取到的HttpSession是null*/
		StandardSessionFacade ssf = (StandardSessionFacade) request.getHttpSession();
		if (ssf != null) {
			HttpSession session = (HttpSession) request.getHttpSession();
			sec.getUserProperties().put("sessionid", session);
			log.info("获取到的SessionID：{}",session.getId());
		}
		sec.getUserProperties().put("name", "小强");
		super.modifyHandshake(sec, request, response);
	}
	@Bean
	public ServerEndpointExporter serverEndpointExporter() {
	    //这个对象说一下，貌似只有服务器是tomcat的时候才需要配置,具体我没有研究
		return new ServerEndpointExporter();
	}
}
```

仅仅有上面的配置获取的肯定是null,至于原因，网上说法不一，我也不确定，解决方案如下

```Java
/**
 * 监听器类:主要任务是用ServletRequest将我们的HttpSession携带过去
 * @author 侯叶飞
 */
@Component //此注解千万千万不要忘记，它的主要作用就是将这个监听器纳入到Spring容器中进行管理,相当于注册监听吧
@Slf4j
public class RequestListener implements ServletRequestListener {
    @Override
    public void requestInitialized(ServletRequestEvent sre)  {
    	//将所有request请求都携带上httpSession
        HttpSession session = ((HttpServletRequest) sre.getServletRequest()).getSession();
        log.info("将所有request请求都携带上httpSession {}",session.getId());
    }
    public RequestListener() {}

    @Override
    public void requestDestroyed(ServletRequestEvent arg0)  {}
}
```

此外在我谷歌的过程中有博客提到需要添加一个WebListener的注解，结果发现基于springboot的话，如果不加也不影响

***
GitHub项目地址：[Demo](https://github.com/coder163/demo)























