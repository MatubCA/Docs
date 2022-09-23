## SpringMVC的概述



## SpringMVC入门

### 环境搭建

```xml
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>4.0.1</version>
  <scope>provided</scope>
</dependency>

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.2.10.RELEASE</version>
</dependency>
```

### 注册SpringMVC

> 可以通过XMl配置文件以及继承类的方式注册SpringMVC





贯穿整个Spring的理念是“解耦”，而在SpringMVC中Spring提供了很多需要硬编码的类和方法，而在SpringBoot中，

Spring被称为配置地狱，虽然SpringMVC提供了很多简化配置类，但是那种方法明显是与Spring的一个重要思想“解耦”是背道而驰的，所以在SpringBoor中，通过约定优于配置的思想，对于很难多配置设置了默认值，我们可以很容易的将那些需要经常改变的配置信息，放到配置文件中，达到了解耦与简化的一个平衡点。
