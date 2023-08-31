---
title: Mybaits连接MySQL8.0版本的配置
date: 2022-04-13 00:15:50.985
updated: 2022-09-05 00:15:50.985
url: https://hhdxw.top/archives/36
categories: 
- JAVA EE
tags: 
- JAVA EE
---

# Mybaits连接MySQL8.0版本的配置问题

## 特别要注意 properties 文件中不能有**任何**错误，以及空格

## 1.更改 maven 文件

```java
<dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.28</version>
</dependency>
```

## 2.修改 Maven 配置文件

mysql 8.0 以后的版本驱动为 

```java
com.mysql.cj.jdbc.Dirver
```

url 一般为

```java
jdbc:mysql://localhost:3306/mybatis 
```

如果不能用的话，添加以下代码

```java
?useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT&nullCatalogMeansCurrent = true
```

来添加编码集

## 3.使用 xml 配置方式

```java
<environments default="mysql">
<!--       配置mysql环境-->
        <environment id="mysql">
<!--            配置事务-->
            <transactionManager type="JDBC"></transactionManager>
<!--            配置连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8&amp;serverTimezone=GMT&amp;nullCatalogMeansCurrent = true"/>
                <property name="username" value="root"/>
                <property name="password" value="8520"/>
            </dataSource>
        </environment>
    </environments>
```

## 4.使用的是 properties 文件的配置方式

```java
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT&nullCatalogMeansCurrent = true
jdbc.username=root
jdbc.password=8520
```

```java
	<properties resource="db.properties"/>
	
<!--    配置环境-->
    <environments default="mysql">
<!--       配置mysql环境-->
        <environment id="mysql">
<!--            配置事务-->
            <transactionManager type="JDBC"></transactionManager>
<!--            配置连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
```