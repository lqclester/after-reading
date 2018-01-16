---
title: spring中的mybaits配置
date: 2017-09-28 16:13:05
categories:
- coding
- spring
tags:
- spring
- mybatis
comments: false
---
> spring中的mybaits配置，支持多数据源

<!-- more -->
# mybaits+spring
## step1
引入依赖
```
	<!-- mybatis -->
	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis-spring</artifactId>
		<version>${mybatis.spring.version}</version>
	</dependency>

	<dependency>
		<groupId>org.mybatis</groupId>
		<artifactId>mybatis</artifactId>
		<version>${mybatis.version}</version>
	</dependency>
```

## step2:引入数据源
class还可以用阿里的DruidDataSource,如果多个数据源只需要id不一样。
```
	<bean id="dataSource1" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="${db.driverClassName}"></property>
		<property name="url" value="${db.url}"></property>
		<property name="username" value="${db.username}"></property>
		<property name="password" value="${db.password}"></property>
		<property name="maxActive" value="${db.maxActive}"></property>
    	<property name="maxWait" value="${db.maxWait}"></property>
    	<property name="timeBetweenEvictionRunsMillis" value="${db.evictionRun}"></property>
    	<property name="defaultAutoCommit" value="${db.defaultAutoCommit}"></property>
    	<property name="testOnBorrow" value="true"></property>
		<property name="testOnReturn" value="true"></property>
		<property name="testWhileIdle" value="true"></property>
		<property name="validationQuery" value="SELECT 1"></property>
		<property name="validationQueryTimeout" value="10"></property>
	</bean>
```

## step3: 配置sessionFactory
每个基于 MyBatis 的应用都是以一个 SqlSessionFactory。不同的sqlSessionFactory用不同的dataSource,dao放置的路径也分开。spring里使用的是`SqlSessionFactoryBean`[SqlSessionFactoryBean](http://www.mybatis.org/spring/zh/factorybean.html)

`configuration`主要用在[配置修改](http://www.mybatis.org/mybatis-3/zh/configuration.html)，如`cacheEnabled`(	该配置影响的所有映射器中配置的缓存的全局开关。默认开)

`mapperLocations`属性使用一个资源位置的 list。 这个属性可以用来指定 MyBatis 的 XML 映射器文件的位置。 它的值可以包含 Ant 样式来加载一个目录中所有文件, 或者从基路径下 递归搜索所有路径。

```
	<bean id="dataSource1SqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean" name="dataSource1SqlSessionFactory">
		<property name="dataSource" ref="dataSource1"></property>
		<property name="mapperLocations" value="classpath*:com/myproject/dao/dataSource1/*.xml" />
		<property name="configuration">
    		<bean class="org.apache.ibatis.session.Configuration">
     			<property name="mapUnderscoreToCamelCase" value="true"/>
   			</bean>
  		</property>
	</bean>
```

## step4：配置MapperScannerConfigurer
`basePackage` 需要受用MyBatis-Spring映射
`sqlSessionFactoryBeanName` 指定使用的factory,包含了xml的位置等信息
`annotationClass` 属性指定了要寻找的注解名称(`@Repository`)
```
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="com.myproject.dao.dataSource1" />
	<property name="sqlSessionFactoryBeanName" value="dataSource1SqlSessionFactory" />
	<property name="annotationClass" value="org.springframework.stereotype.Repository" />
</bean>
```
