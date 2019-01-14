---
title: Dubbo系列-5-Dubbo配置方式介绍
date: 2018-05-04
tags: dubbo
---
![图片](http://pl5cg4rhb.bkt.clouddn.com/dubbo5page.png)
>本文主要介绍了Dubbo主要支持的配置类型,并作出简单的事例以及分析;

<!-- more -->
---
## 配置类型
Dubbo主要提供了4种配置方式,分别是以下:
* xml配置
* 属性配置
* API配置
* 注解配置

细节可以查询dubbo官方文档介绍:[dubbo官网链接](http://dubbo.apache.org/zh-cn/docs/user/configuration/xml.html)

---
## 四种配置方式
### xml方式
* 配置前置条件([dubbo文档链接](http://dubbo.apache.org/zh-cn/docs/user/configuration/xml.html))
  * 创建dubbo.*.xml配置文件
  * 程序启动加载对应xml的配置文件
* .xml举例
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.3.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">  
    <dubbo:application name="hello-world-app"  />  
    <dubbo:registry address="multicast://224.5.6.7:1234" />  
    <dubbo:protocol name="dubbo" port="20880" />  
    <dubbo:service interface="org.apache.dubbo.demo.DemoService" ref="demoServiceLocal" />  
    <dubbo:reference id="demoServiceRemote" interface="org.apache.dubbo.demo.DemoService" />  
</beans>
```
### 属性方式
* 配置前置条件
  * 创建dubbo.properties配置文件
  * spring boot会自动读取properties文件内容
* .properties举例
```
dubbo.application.name=foo
dubbo.application.owner=bar
dubbo.registry.address=10.20.153.10:9090
```
### API方式
* 配置前置条件
  * 创建ConfigClass或在boot的main函数编写
* 举例
```
// 服务实现
XxxService xxxService = new XxxServiceImpl();
 
// 当前应用配置
ApplicationConfig application = new ApplicationConfig();
application.setName("xxx");
 
// 连接注册中心配置
RegistryConfig registry = new RegistryConfig();
registry.setAddress("10.20.130.230:9090");
registry.setUsername("aaa");
registry.setPassword("bbb");
 
// 服务提供者协议配置
ProtocolConfig protocol = new ProtocolConfig();
protocol.setName("dubbo");
protocol.setPort(12345);
protocol.setThreads(200);
```
### 注解方式
* 配置前置条件
  * 启动Application增加@EnableDubbo
* 举例
```
@Service(timeout = 1000)
@Component
public class UserServiceImpl implements UserService {
```


---
## 配置方式比较
### 配置能力比较

* xml:
  * 优势:所有的功能都能通过xml的方式进行配置,如方法级别的timeout等
  * 缺点:
    * 需要专门编写xml文件,编写相对繁琐
    * 就算不整合spring boot,原有spring也支持这种方式配置
    * 效率低
* 属性:
  * 优势:统一配置
  * 缺点:仅用于缺省配置情况加多
* 注解:
  * 优势:方便
  * 缺点:不支持方法级别的配置,效率低
* API:
  * 优势:配置形式清楚,所有配置都支持,效率高
  * 缺点:配置比xml更加繁琐
### 使用场景推荐
* xml方式
  * 用于普通服务提供方或消费方(日常使用)
* 属性方式:
  * 多用于缺省配置(公共内容)
* API方式:
  * API 仅用于 OpenAPI, ESB, Test, Mock 等系统集成，普通服务提供方或消费方，请采用[XML 配置](http://dubbo.apache.org/zh-cn/docs/user/configuration/xml.html)方式使用 Dubbo
* 注解方式:
  * dubbo2.5.7支持,适用于普通服务提供方或消费方;




