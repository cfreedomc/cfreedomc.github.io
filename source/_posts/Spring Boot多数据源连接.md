---
title: Spring Boot使用系列-多数据源
date: 2019-2-10
tags: spring
---
![图片](https://i.loli.net/2019/03/04/5c7d2ed7d287c.jpg)
>在很多系统的应用中我们不可避免的需要用到多数据源的情况,如果用最原始的jdbc当然是可以实现的,但是在现在高度使用spring家族的前提下如何使用spring boot结合多数据源值得关注,所以这边进行了简单的记录;

<!-- more -->

### 多数据源概述
在很多系统的应用中我们不可避免的需要用到多数据源的情况,如果用最原始的jdbc当然是可以实现的,但是在现在高度使用spring家族的前提下如何使用spring boot结合多数据源值得关注,所以这边进行了简单的记录;
### 多数据源配置
* 去除spring boot的自动配置
```
@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class,
      DataSourceTransactionManagerAutoConfiguration.class, JdbcTemplateAutoConfiguration.class})
public class Application {
  ...
}
```

* 自定义数据源
```
@Bean
@ConfigurationProperties("wy.datasource")
public DataSourceProperties wyDataSourceProperties(){
   return  new DataSourceProperties();
}
@Bean
public DataSource wyDataSource(){
   DataSourceProperties dataSourceProperties=wyDataSourceProperties();
   System.out.println("wy dataSource: {}"+dataSourceProperties.getUrl());
   return dataSourceProperties.initializeDataSourceBuilder().build();
}

@Bean
@Resource
public PlatformTransactionManager wyTxManager(DataSource wyDataSource){
   return new DataSourceTransactionManager(wyDataSource);
}


@Bean
@ConfigurationProperties("zy.datasource")
public DataSourceProperties zyDataSourceProperties(){
   return  new DataSourceProperties();
}
@Bean
public DataSource zyDataSource(){
   DataSourceProperties dataSourceProperties=zyDataSourceProperties();
   System.out.println("zy dataSource: {}"+dataSourceProperties.getUrl());
   return dataSourceProperties.initializeDataSourceBuilder().build();
}

@Bean
@Resource
public PlatformTransactionManager zyTxManager(DataSource zyDataSource){
   return new DataSourceTransactionManager(zyDataSource);
}
```

* 配置文件
```
wy.datasource.url=jdbc:mysql://localhost:3306/hr
wy.datasource.username=root
wy.datasource.password=123456

zy.datasource.url=jdbc:mysql://localhost:3306/test
zy.datasource.username=root
zy.datasource.password=123456
```

### 多数据源事务使用
* 需要对应在使用配置value
```
@Transactional(value = "zyTxManager")
public void test(){
  ...
}
```

