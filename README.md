Spring Boot With Apache Dubbo
=============================
主要介绍如何在Spring Boot中整合Apache Dubbo的使用，Starter详细介绍在这里：

# Apache Dubbo是什么
Apache Dubbo是Alibaba将Dubbo捐献给Apache的开源产品，详细介绍 http://dubbo.incubator.apache.org/#!/?lang=zh-cn


# 注册中心-Registry
目前主要是支持Redis、ZooKeeper和Consul这三个注册中心，主要是这两个服务非常普遍，同时由于Docker的流行，启动这三者个服务也非常简单。

### Redis
使用Redis注册中心，需要将在pom.xml中添加对应的redis客户端，代码如下：

```xml
     <dependency>
         <groupId>redis.clients</groupId>
         <artifactId>jedis</artifactId>
     </dependency>
```

对应的配置项为： spring.dubbo.registry = redis://localhost:6379

###  ZooKeeper
使用ZooKeeper注册中心，需要在pom.xml中添加zookeeper需要的jar包，代码如下：

```xml
     <dependency>
         <groupId>org.apache.curator</groupId>
         <artifactId>curator-framework</artifactId>
         <version>2.12.0</version>
     </dependency>
     <dependency>
         <groupId>com.101tec</groupId>
         <artifactId>zkclient</artifactId>
         <version>0.10</version>
     </dependency>
```

对应的配置项为： spring.dubbo.registry = zookeeper://127.0.0.1:2181

多个zookeeper的配置项为: spring.dubbo.registry = zookeeper://192.168.0.2:2181,192.168.0.3:2181

# 如何测试

* 首先使用IntelliJ IDEA导入项目
* 调用docker-compose启动对应的注册中心: docker-compose up -d
* 启动 SpringBootDubboServerApplication
* 启动 SpringBootDubboClientApplication
* 打开浏览器访问 http://localhost:2080


# Spring DevTools注意事项
由于Spring DevTools采用不一样的classloader的机制，所以会导致Dubbo Consumer Bean无法赋值到指定的@Component上，请使用以下规则：

在 src/main/resources/META-INF/spring-devtools.properties 在添加以下代码进行DevTools的classloader屏蔽：
```properties
restart.exclude.target-classes=/target/classes/
```
关于hotspot的模式下，相关Java代码调整后理解生效，可以考虑： http://dcevm.github.io/

如果你的应用是纯Dubbo服务，没有涉及到Web页面，不建议你添加spring-devtools，如果添加了后，
可以通过以下配置项关闭livereload服务，这样可以保证不必要的live reload服务启动。
```properties
spring.devtools.livereload.enabled=false
```

# Todo

* DubboConsumerBuilder: 快速构建Dubbo Consumer
* zipkin: https://github.com/jessyZu/dubbo-zipkin-spring-starter
