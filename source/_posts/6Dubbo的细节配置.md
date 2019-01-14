---
title: Dubbo系列-6-Dubbo配置细节介绍
date: 2018-05-10
tags: dubbo
---
![图片](http://pl5cg4rhb.bkt.clouddn.com/dubbo-6.jpg)
>本文主要介绍了Dubbo主要的细节属性配置以及相关配置的优先级等情况;


<!-- more -->
### 配置文件的优先级
![图片](http://pl5cg4rhb.bkt.clouddn.com/dubbo-6-1.png)
在dubbo属性配置中
* -D的直接加载是优先级最高的
* 随后是xml格式的dubbo(在这里xml格式的dubbo.xml是等价于spring boot里的application.properties的)
* 最后是dubbo.properties文件
### 启动时检查
该功能主要是用于当没有提供者出现,消费者端也可以模拟启动
```
#消费端不检查某个服务
dubbo.reference.com.foo.BarService.check=false
#消费端引用统一不检查
dubbo.reference.check=false

dubbo.consumer.check=false
#注册中心不检查,
#官方:关闭注册中心启动时检查 (注册订阅失败时报错)
#实际操作,一般都会检查不断重连
dubbo.registry.check=false
```


### 超时设置 timeout属性
超过超时时间会认为连接失败,主要的是要了解配置超时属性的优先级(如timeout,retries, loadbalance, actives等属性)
* 细粒度高的设置优先
* 细粒度相同,消费端优先(消费端都不介意,提供者就不要多BB了)

![图片](http://pl5cg4rhb.bkt.clouddn.com/dubbo-6-2.png)
### 重试次数retries
重试次数注意点:
* 如果设置3,其真实重试次数就是3+1,最开始的一次不计入总次数中
* 支持重试的服务要求其操作是幂等的操作,这点很重要,如是insert操作等是不推荐重试
* 如果有提供者集群,dubbo默认配置会调用2次失败后切换
### 版本控制
可以通过在service和reference中配置version,可以约定提供者和消费者的调用版本

### stub本地存根
[链接](http://dubbo.apache.org/zh-cn/docs/user/demos/local-stub.html)
![图片](http://pl5cg4rhb.bkt.clouddn.com/dubbo-6-3.png)
存根的目的:希望消费者在调用服务时做出自己的一点贡献,比如常用的参数校验等情况,保证参数正常后再真正的调用远程的服务
配置:
```
<dubbo:service interface="com.foo.BarService" stub="com.foo.BarServiceStub" />
```
存根编写的注意事项:
* Stub 必须有可传入 Proxy 的构造函数
* 在 interface 旁边放一个 Stub 实现，它实现 BarService 接口，并有一个传入远程 BarService 实例的构造函数
```
package com.foo;
public class BarServiceStub implements BarService { 
    private final BarService barService;
    
    // 构造函数传入真正的远程代理对象
    public (BarService barService) {
        this.barService = barService;
    }
 
    public String sayHello(String name) {
        // 此代码在客户端执行, 你可以在客户端做ThreadLocal本地缓存，或预先验证参数是否合法，等等
        try {
            return barService.sayHello(name);
        } catch (Exception e) {
            // 你可以容错，可以做任何AOP拦截事项
            return "容错数据";
        }
    }
}
```

