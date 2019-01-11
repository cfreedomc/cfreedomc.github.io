---
title: Dubbo系列-7-Dubbo高可用
date: 2018-02-04
tags: dubbo
---
![图片](http://pl5cg4rhb.bkt.clouddn.com/dubbo7page.png)
>本篇主要介绍Dubbo的高可用功能,主要包含注册中心异常,Dubbo负载均衡以及在高并发以及网络错误下Dubbo提供的解决方案;

<!-- more -->
## zookeeper宕机和dubbo直连
* zookeeper宕机

当消费者通过zookeeper找到提供者并成功调用后,如果在此刻zookeeper宕机是不会影响连接的,因为会存在本地缓存,消费者通过本地缓存直接调用提供者
* dubbo直连
  * 指定消费者引用的url可以进行dubbo直连
```
@Reference(url = "127.0.0.1:28080")
UserService userService;
```
ps:
zookeeper宕机后,消费者是可以继续调用的,因为有本地缓存
dubbo是支持直连的


---
## Dubbo的负载均衡
* dubbo的负载均衡类型
  * Random LoanBalance 基于**权重**随机负载均衡
    * 根据权重随机

![图片](http://pl5cg4rhb.bkt.clouddn.com/randomLoanBalance.png)
  * RoundRobin LoadBalance 基于权重的**轮询**负载均衡
    * 在轮询基础上增加权重

![图片](http://pl5cg4rhb.bkt.clouddn.com/roundRobinLoanBalance.png)
  * LeastActive LoadBalance 最少活跃数负载均衡
    * 调用延迟最低的服务

![图片](http://pl5cg4rhb.bkt.clouddn.com/leastActive%20LoanBalance.png)
  * ConsistentHash LoadBalance 一致性Hash
    * 根据Hash值决定调用机器

![图片](http://pl5cg4rhb.bkt.clouddn.com/ConsistentHashLoanBalance.png)
* 权重的配置
  * 消费者配置
```
-- 接口级别
<dubbo:reference interface="..." loadbalance="roundrobin" />
-- 方法级别
<dubbo:reference interface="...">
    <dubbo:method name="..." loadbalance="roundrobin"/>
</dubbo:reference>
```
  * 提供者配置
```
-- 接口级别
<dubbo:service interface="..." loadbalance="roundrobin" />
-- 方法级别
<dubbo:service interface="...">
    <dubbo:method name="..." loadbalance="roundrobin"/>
</dubbo:service>
```

---
## 服务降级
* 服务降级概念
  * 当服务器访问量巨大时,无法正常的完成所有服务的响应,可以通过简单处理或关闭服务器中某些不重要的服务,来满足核心业务的高可用;
* 服务降级类型
  * mock=force:Return+null
    * 直接在消费者端,强制对某个服务返回null;
  * mock=fail:Return+null
    * 需要调用提供者,当提供者返回失败时(超时等情况)返回null;
* 服务降级操作
  * 在dubbo的监控系统中可以设置
    * 屏蔽:mock=force
    * 容错:mock=fail

---
## 服务容错
* 概念
  * 服务调用错误后的处理手段；
* 服务容错类型
  * Failover Cluster
    * 失败自动切换重试其他服务器；
  * Failfast Cluster
    * 快速失败，只发起一次，适合于非幂等性操作；
  * Failsafe Cluster
    * 失败吞掉异常，忽略，但在审计日志中展现；
  * Failback Cluster
    * 失败自动重试；
  * Forking Cluster
    * 并行调用，一次成功则成功；
  * Broadcast Cluster
    * 串行调用，全成功则成功；
* Hystrix整合
  * 待续

