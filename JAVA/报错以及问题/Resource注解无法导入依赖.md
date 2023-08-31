---
title: Resource注解无法导入依赖使用
date: 2022-03-15 00:15:53.726
updated: 2022-09-05 00:15:53.726
url: https://hhdxw.top/archives/40
categories: 
- JAVA EE
tags: 
- JAVA EE
---

最近在学习Spring的过程中遇到了一个棘手的问题：使用Resource注解时报错了。

![image-20220315214739187](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220315214739187.png)

当时上面的**@Resource**爆红，忘了截图

问题出现了，那么怎么解决呢。在百度上找到答案说：这是因为没有导入tomcat的包,tomcat里面默认继承了这个包,只要在**pom.xml**增加这个包的依赖，问题就解决了

```java
 <dependency>
      <groupId>javax.annotation</groupId>
      <artifactId>jsr250-api</artifactId>
      <version>1.0</version>
</dependency>
```

![image-20220315215743445](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220315215743445.png)

但是我用了这个方法没有用，依旧爆红，后来我换了**JDK**从16，17，1.6，1.8，在最后**JDK9**能用了