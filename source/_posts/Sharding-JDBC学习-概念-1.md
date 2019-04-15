---
title: Sharding-JDBC学习-概念-1
date: 2019-4-15
tags: jdbc
---
> 了解Sharding-JDBC的基本概念

<!-- more -->
## Sharding-JDBC概述
#### 为何要用sharding-jdbc
* 系统庞大,数据量的剧增,传统的单一数据节点的解决方案,已经在性能,可用性以及运维成本上已经很难满足目前互联网海量数据的场景,所以分库分表的需求非常急迫;
* 使用类似mycat的中间件,需要单独部署,受网络等因素影响;

#### 什么是sharding-jdbc
Sharding-jdbc是数据库中间件解决方案,现在是包含在ShardingSphene下,是一套开源的分布式数据库中间件解决方案组成的生态圈,目前组成:
* Sharding-JDBC	
* Sharding-Proxy
* Sharding-Sidecar(plan)

#### 解决问题
* 标准化的数据分片
* 分布式事务
* 数据库治理功能

#### 目的

* 在目前NoSQL以及NewSQL盛行的年代,关系数据库的使用场景仍然无法替代,所以Sharding-JDBC目的是给关系型数据库带来更好的体验,即一种增量而非颠覆(focus on its increment instead of a total overturn);

---

## Sharding-JDBC特点

#### 特点

* 轻量级Java框架,在java的JDBC层提供额外服务,采用jar形式提供服务无需额外部署和依赖;
* 兼容现有的JDBC以及ORM框架;
	* 使用任何基于Java的ORM框架,如JPA,Hibernate,Mybatis,Spring JDBC Template或直接使用JDBC;
	* 基于任何第三方数据库连接池,如DBCP,C3P0,BoneCP,Druid,HikariCP等;
	* 支持任意实现JDBC规范的数据库,目前支持MySQL,Oracle,SQLServer和PostgreSQL;
	![](https://raw.githubusercontent.com/cfreedomc/picture/master/img/sharding-jdbc-1.png)


