# SpringMVC

## 一、引言

### 1.什么是SpringMVC

> 概念：SpringMVC 是基于 Spring Framework 衍生而来的一个 MVC 框架。主要解决了原有MVC框架开发过程中，控制器（controller）的问题。
>

#### MVC架构

SpringMVC 是一种 MVC 架构，MVC 是一种架构思想，在 JavaEE 开发中用于多 Web 开发。

应用 MVC 框架，会把项目划分为三个层次：

- View：JSP
- Model：Service+DAO
- Controller：Servlet

MVC分层开发，体现了面向对象、各司其职的设计思想，有利于后续项目的维护。

#### 基于SpringFramework

- 通过工厂（容器）创建对象，解耦合（IOC、DI）
- 通过 AOP 方式，为原始类添加额外功能
- 方便与第三方框架继承
  - Mybatis
  - JPA
  - MQ
  - ES
  - Redis
  - ...

SpringMVC 基于 Spring 就可以将 SpringFramework 的优点继承下来。

#### 原有MVC开发中控制器的问题

回答这个问题，首先我们应该明确两个问题的答案：

1. **原有MVC开发中控制器使用哪些技术实现？**
   - Servlet（基于Java Model2模式）
   - Struts2 中的 Action
2. **这些技术在实现过程中存在哪些问题？**

- 控制器的核心作用

  - 接受用户请求
  - 调用业务功能（Service）
  - 并根据处理结果控制程序的运行流程

  ![image-20220913105529665](E:\Pictures\Typora\image-20220913105529665.png)

- **控制器的核心代码**

  ![image-20220913110017094](E:\Pictures\Typora\image-20220913110017094.png)
  
- **现有控制器存在的问题**

  - **接受client请求参数过程中存在的问题**

    ```markdown
    1. 代码冗余
    
    2. 只能接受字符串类型数据，其它类型需要手工进行转换
    
    3. 无法自动封装对象
    ```

    ![image-20220913110926361](E:\Pictures\Typora\image-20220913110926361.png)

  - **调用业务对象（Service）过程中存的问题**

    通过new的方式创建对象，会存在耦合
  
    ```java
      UserService userService = new UserServiceImpl;// 存在耦合
    	UserService.login("name","password")
    ```
  
  - **流程跳转（页面跳转）过程中存在的问题**
  
    ```markdown
    1. 代码冗余
    
    2. 跳转路径存在耦合
    
    3. 与视图层技术耦合
    ```
  
    ![image-20220913111506240](E:\Pictures\Typora\image-20220913111506240.png)
  
    ![image-20220913112741603](E:\Pictures\Typora\image-20220913112741603.png)

### 2.SpringMVC的学习要点

#### 2.1SpringMVC的三种开发模式

- **传统视图开发**

  ```markdown
  1. 通过作用域（request、session）进行数据传输
  
  2. 通过视图层技术进行数据展示（JSP、Freemarker、Thymeleaf）
  ```

- **前后端分离开发**

  ```markdown
  1. 多种新的请求发送发送
  
  2. Restful的访问
  
  3. 通过HttpMassageConverter进行数据响应
  ```

- **Spring5 WebFlux开发**

  ```markdown
  1. 替换传统Web开发的一种新的Web开发方式
  
  2. 通过NettyServer，进行Web通信
  ```

#### 2.2控制器开发

对于控制器的开发也就是主要解决原有控制器存在的三个问题

```markdown
1. 接受client用户请求

2. 调用业务对象

3. 流程跳转
```

## 二、第一个SpringMVC程序的开发

### 1.版本控制

```markdown
1. JDK 1.8
2. Maven 3.6
3. Idea 2021+
4. Spring Framework 5.1.4
5. Tomcat 8.5.29
6. Mysql 5.7.18
```

### 2.环境搭建

#### 2.1Idea设置

1. 创建多模块项目，设置Maven（创建新项目会启用idea默认的idea）、JDK
2. 使用父子工程结构，创建父工程管理依赖、管理项目结构，删除父项目src文件夹
3. 创建子项目，选择父项目
4. 关联父子项目，在父项目中存在`<modules>`标签，代表子项目，在子项目中存在`<parent>`标签，代表父项目。
5. 补全子项目项目结构（点击src文件夹新建目录会有提示）
6. 修复web.xml（默认Servlet2.0），我们需要更高版本，删除web.xml，在项目结构中找到fact，找到当前项目，找到部署描述符，先删除原有的描述符，然后新建（注意路径）。
7. 设置子项目的pom文件，设置JDK版本，子项目的依赖和插件统一交由父项目完成，所以子项目中的可以删除
8. 添加Tomcat，在部署中选择普通目录（exploded），便于调试，添加虚拟机参数避免控制台启动时乱码`-Dfile.encoding=UTF-8` ，**选择重新部署**和**更新类和资源**。

#### 2.2添加依赖

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.13.2</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.1.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.24</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.1.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.29</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.1.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.6</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.2.8</version>
</dependency>
<dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>1.30</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
    <version>5.1.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>5.1.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope><!--仅编译-->
</dependency>
<dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
<dependency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>javax.servlet.jsp-api</artifactId>
    <version>2.3.3</version>
    <scope>provided</scope>
</dependency>
```

#### 2.3配置文件初始化

- **web.xml**

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
      
      <servlet>
          <servlet-name>servlet</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <!--指定SpringMVC配置文件的路径-->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:dispatcher.xml</param-value>
          </init-param>
          <!--在Tomcat启动时创建-->
          <load-on-startup>1</load-on-startup>
      </servlet>
      
      <servlet-mapping>
          <servlet-name>servlet</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

- **dispatcher.xml**

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
                             http://www.springframework.org/schema/beans/spring-beans.xsd
                             http://www.springframework.org/schema/context
                             http://www.springframework.org/schema/context/spring-context-4.2.xsd
                             http://www.springframework.org/schema/mvc
                             http://www.springframework.org/schema/mvc/spring-mvc.xsd
                             http://www.springframework.org/schema/tx
                             http://www.springframework.org/schema/tx/spring-tx.xsd">
  
      <!--设置注解扫描的路径-->
      <context:component-scan base-package="com.dong"/>
  
      <!--引入SpringMVC的核心功能-->
      <mvc:annotation-driven/>
  </beans>
  ```

#### 2.4配置文件初始化细节

- **DispatcherServlet**

  ```markdown
  1. DispatcherServlet称为前端控制器(中央控制器)
  
  2. DispatcherServlet的核心作用：
  	1.用于创建Spring工厂(容器)
  			ApplicationContext context = ClassPathXmlApplicationContext("/applicationContext.xml") // 所以我们要在web.xml中设置配置文件的路径。
  			因为DispatcherServlet封装的Spring工厂(容器)只能读取xml，所以无法迁移到纯注解编程
    2. 控制SpringMVC内部的运行流程
  ```

- **SpringMVC的配置文件(dispatcher.xml)**

  ![image-20220913162622871](E:\Pictures\Typora\image-20220913162622871.png)
  
  ![image-20220913163056382](E:\Pictures\Typora\image-20220913163056382.png)
  

### 3.编码开发

![image-20220913164014890](E:\Pictures\Typora\image-20220913164014890.png)

**开发流程**：

1. 定义一个类，加上@Controller注解。
2. 提供一个控制方法，参数为HttpServletRequest、HttpServletResponse，返回值为String。在方法上加上@RequestMapping注解，指定访问路径。
3. 把对应JSP页面的路径作为返回值返回。

### 4.多服务方法

- Servlet作为控制器，一个类中只能提供一个服务方法
- SpringMVC作为控制器，一个类中可以提供多个服务方法

### 5.注意

SpringMVC开发中的Controller也称为Handler（SpringMVC内部叫法） 

### 6.细节分析

#### 6.1一种类型的SpringMVC控制器被创建几次？

- Servlet被创建的次数

  一种类型的Servlet，只会被Tomcat创建一次，所以Servlet是单实例的。

- Servlet是单实例并不是单例设计模式（单例设计模式构造函数私有）。

- SpringMVC的控制器被Spring工厂创建的次数

  可以创建一次也可以创建多次，由Scope注解(默认singleton)

  ```java
  @Controller
  @Scope(singleton | prototype)
  public class FirstController{
    //...
  }
  ```

- 默认情况下SpringMVC的控制器只会被创建一次, 会存在线程安全问题

#### 6.2@RequestMapping

作用: 为控制器方法提供外部URL访问路径

支持: 由`<mvc:annotation-driven/>`引入的RequestMappingHandlerMapping进行支持.

细节:

- 路径分割符可以忽略

  ```java
  @RequestMapping("first")
  
  // 忽略第一个路径分隔符
  @RequestMapping("first/second")
  ```

- 在一个方法上映射多个访问路径

  ```java
  @RequestMapping(value={"first","second"})
  ```

- 在Controller类上加@RequestMapping注解

  ```java
  localhost:8989/01/user/first
  
  @RequestMapping("/user")
  @Controller
  public class Controller{
    
    @RequestMapping("/first")
    public String First(HttpServletRequest req,HttpServletResponse resp){
      //...
    }
  }
  ```

  设计目的:

  按照功能, 进行不同模块的区分, 有利于项目的管理

- RequestMapping限定用户的请求方式

  - 请求方式

    前端向后端发送数据的方式(get | post)

    两种请求方式的区别:

    ```markdown
    1. GET请求:通过请求行(地址栏)提交数据(QueryString),明文提交数据,不安全,提交数据量小( < 2048字节)
    
    		https:localhost:8989/01/user/first?username=张三&password=123
    		
    2. POST请求:通过请求体提交数据,密文提交(非加密,只是用户看不见),相对安全,数据量大(理论上没有限制)
    ```

  - 两种请求发起方式的区别

    ```markdown
    1. GET请求
    
    	浏览器地址栏:     localhost:8989/basic/firstController/first
    	
    	超链接:          <a href="${pageContext.request.contextPath}/firstController/first">超链接</a>
    	
    	表单:            <from action="${pageCOntext.request.contextPath}/firstController/first"></from>   
    	
    	JS:						 location.href=;
    									
    	ajax:					 $.ajax({url:url,type:"get",...})	
    	
    	专属工具或者库:	POSTMAN | POSTWOMAN | RestfulToolKits | RestTemplate | HttpClient | OKHttp | NsMutableURLRequest
    	
    2. POST请求
    
    	表单:          <from action="${pageCntext.request.contextPath}/firstController/first" method="post"></from>
    	
    	ajax:         $.ajax({url:url,type:"post",...})	
    	
    	专属工具库:    POSTMAN | POSTWOMAN | RestfulToolKits | RestTemplate | HttpClient | OKHttp | NsMutableURLRequest
    
    ```

  - @RequestMapping如何限定请求方式

    ![image-20220914131026489](E:\Pictures\Typora\image-20220914131026489.png)

  - Http协议中的其它请求方式

    ![image-20220914131602851](E:\Pictures\Typora\image-20220914131602851.png)

  - @RequestMapping限定方法参数

    服务方法可以任意选取域对象参数

    ![image-20220914144711993](E:\Pictures\Typora\image-20220914144711993.png)

  - 那种方式更为常用

    ```markdown
    1. 前四种因为与ServletAPI耦合，不推荐使用
    
    2. 第五种，不能接受用户参数，不推荐使用
    ```

### 7.视图解析器

```markdown
1. 视图解析器用于页面跳转
	
	@Controller
	@RequestMapping("/user")
	public class FirstController{
	@RequestMapping("/first")
		public String m1(){
			// 1. 接受用户参数
						...
			   2. 调用业务方法
			   		...
			   3. 视图跳转
			   return "/index.jsp";
		}
	}
```

目前页面跳转存在的问题：

​	跳转路径与实际路径存在耦合

- 视图解析器(ViewResolver)

  通过视图解析器解决跳转路径与实际路径耦合的问题

  思路分析：

  将原来页面跳转中变化的提取出来，只留下不变的东西。

  ![image-20220914153955229](E:\Pictures\Typora\image-20220914153955229.png)

  通过视图解析器ViewResolver进行跳转路径的拼接。

  ```xml
  <bean id="resolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <!--前缀-->
  	<property name="refix" value=""/>
    <!--后缀-->
    <property name="suffix" value=""/>
  </bean>
  ```

- 注解开发

  ```java
  @Configuration
  public class AppConfig {
      // 视图解析器在配置Bean中的配置
      @Bean
      public InternalResourceViewResolver resolver() {
          InternalResourceViewResolver resolver = new InternalResourceViewResolver();
          resolver.setPrefix("/");
          resolver.setSuffix(".jsp");
          return resolver;
      }
  }
  ```

### 8.SpringMVC配置文件的默认设置

![image-20220914193409438](E:\Pictures\Typora\image-20220914193409438.png)



## 三、SpringMVC控制器开发详解

### 1.核心要点

1. 接受Client请求参数
2. 调用业务方法
3. 页面跳转

### 2.接收Client请求参数详解

#### 2.1回顾

![image-20220914194047550](E:\Pictures\Typora\image-20220914194047550.png)



#### 2.2基于ServletAPI接受用户参数

![image-20220914194619894](E:\Pictures\Typora\image-20220914194619894.png)

#### 2.3基于简单变量接收Client参数

简单变量：8种基本数据类型+String类型的变量，把这些类型的变量作为控制器方法的形参，用于接受client提交的数据

思路分析：

![image-20220914195110996](E:\Pictures\Typora\image-20220914195110996.png)

发送数据的name属性值要与参数的名字一致。

- 细节分析

  - SpringMVC支持常见类型（8中基本数据类型及包装器以及String类型）的自动转换

    ![image-20220915123739981](E:\Pictures\Typora\image-20220915123739981.png)

  - 基本数据类型尽量使用包装器

    ![image-20220915124125009](E:\Pictures\Typora\image-20220915124125009.png)

#### 2.4基于POJO类型接受Client参数

- 什么是POJO

  ![image-20220915124751866](E:\Pictures\Typora\image-20220915124751866.png)

- 使用场景

  ![image-20220915125130543](E:\Pictures\Typora\image-20220915125130543.png)

- 注意1

  ![image-20220915125549727](E:\Pictures\Typora\image-20220915125549727.png)

- 注意点2

  ![image-20220915125857653](E:\Pictures\Typora\image-20220915125857653.png)

#### 2.5基于一组简单变量接受Client参数

![image-20220915130353747](E:\Pictures\Typora\image-20220915130353747.png)

- 细节

  能不能用集合接受一组参数

  ![image-20220915131056705](E:\Pictures\Typora\image-20220915131056705.png)

#### 2.6接受一组POJO类型的对象的请求参数

通过一个包装类进行封装

![image-20220916161336493](E:\Pictures\Typora\image-20220916161336493.png)

#### 2.7SpringMVC接受Client请求参数的总结

![image-20220916162243869](E:\Pictures\Typora\image-20220916162243869.png)

### 3.@RequestParam注解

 作用：用于修饰控制器方法的形参

```java
@RequestMapping("/param1")
public String param1(@RequestParam String name,@RequestParam String password){
  
}
```

#### 3.1@RequestParam注解详解

- 解决请求参数和方法形参名字不一致的问题

  ![image-20220916163318120](E:\Pictures\Typora\image-20220916163318120.png)

- 注意事项

  ![image-20220916163417087](E:\Pictures\Typora\image-20220916163417087.png)

- @RequestParam的required属性

  ![image-20220916164538198](E:\Pictures\Typora\image-20220916164538198.png)

- RequestParam的defaultValue属性

  - 客户端没有提交数据的时候，给对应的形参设置默认值

    ![image-20220916165042800](E:\Pictures\Typora\image-20220916165042800.png)

  - 解决控制器方法形参，使用包装类的问题

    使用了defaultValue属性之后，就算控制器没有提交数据，使用int接受也不会报错，因为我们可以给它的默认值设置为0

  - 未来开发中的使用场景

    ![image-20220916165651108](E:\Pictures\Typora\image-20220916165651108.png)

### 4.中文请求参数乱码的问题

#### 4.1回顾JavaWeb开发中中文乱码解决方案

- GET请求乱码解决方案

  ![image-20220916170121209](E:\Pictures\Typora\image-20220916170121209.png)

- POST请求乱码解决方案

  ![image-20220916170509997](E:\Pictures\Typora\image-20220916170509997.png)

#### 4.2SpringMVC解决中文字符集乱码

![image-20220916170720518](E:\Pictures\Typora\image-20220916170720518.png)

web.xml

```xml
<filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### 5.SpringMVC的类型转换器

#### 5.1内置类型转换器

SpringMVC提供了内置类型转换器，把客户端提交的字符串类型参数，转换为控制器方法需要的数据类型

SpringMVC只提供了常见类型的转换器：8中基本数据类型+常见集合等

![image-20220916171917159](E:\Pictures\Typora\image-20220916171917159.png)

- 原理分析

  ![image-20220916173558807](E:\Pictures\Typora\image-20220916173558807.png)

  ![image-20220916173644703](E:\Pictures\Typora\image-20220916173644703.png)

#### 5.2自定义类型转换器

![image-20220916174945254](E:\Pictures\Typora\image-20220916174945254.png)

![image-20220916175116171](E:\Pictures\Typora\image-20220916175116171.png)

### 6.接收其它请求参数

#### 6.1动态参数收集

- 分析

  ![image-20220916175545790](E:\Pictures\Typora\image-20220916175545790.png)

- 单值动态参数收集

  ![image-20220916175601868](E:\Pictures\Typora\image-20220916175601868.png)

- 多值动态参数收集

  ![image-20220916180013171](E:\Pictures\Typora\image-20220916180013171.png)

  MultiValueMap的key为参数名，value是一个list集合，存储多个参数值

- 应用场景

  ![image-20220916180810957](E:\Pictures\Typora\image-20220916180810957.png)

#### 6.2接受Cookie数据

- Servlet中获取方式

  ![image-20220916181036147](E:\Pictures\Typora\image-20220916181036147.png)

- SpringMVC中获取方式

  ![image-20220916181203860](E:\Pictures\Typora\image-20220916181203860.png)

#### 6.3接受请求头数据

- 什么是请求头

  ![image-20220916181930130](E:\Pictures\Typora\image-20220916181930130.png)

- 获取请求头方式

  - Servlet

    ```java
    String value = request.getHeader("key");
    ```

  - SpringMVC

    ![image-20220916182245577](E:\Pictures\Typora\image-20220916182245577.png)

## 四、SpringMVC控制器开发详解二

### 1.核心要点

前面学习了SpringMVC接受请求参数

这章将学习SpringMVC调用业务对象

### 2.SpringMVC调用业务对象（SSM整合）

#### 2.1思路分析

![image-20220916183432206](E:\Pictures\Typora\image-20220916183432206.png)

![image-20220916183459762](E:\Pictures\Typora\image-20220916183459762.png)

#### 2.2编码

// TODO

#### 2.3父子工厂（父子容器）拆分

- 现有SSM整合存在的问题

  ![image-20220916185455616](E:\Pictures\Typora\image-20220916185455616.png)

  ![image-20220916185553276](E:\Pictures\Typora\image-20220916185553276.png)

- 解决方案

  DispatcherServlet中封装着Spring工厂对象（webApplicationContext）

  对MVC和非MVC的配置文件进行拆分

  ![image-20220916190235415](E:\Pictures\Typora\image-20220916190235415.png)

  Controller由MVC创建，Service由Spring创建

  一个原则：子工厂没有的东西可以去父工工厂获取，但是父工厂没有的动词不能去子工厂获取，所以Controller可以获取Service

  ![image-20220916191235145](E:\Pictures\Typora\image-20220916191235145.png)

- 编码

  //TODO

#### 2.4父子容器问题

- 问题

  在现有的父子容器开发中，没有设置上事务

- 原因

  设置了相同的包扫描路径，都会创建Controller和Service，Client会先去子容器中寻找。而子容器中并没有设置事务。

  ![image-20220916192050209](E:\Pictures\Typora\image-20220916192050209.png)

- 解决方案

  ![image-20220916192606473](E:\Pictures\Typora\image-20220916192606473.png)

### 3.SpringMVC控制器调用业务对象总结（SSM）

#### 3.1完整编码总结

- web.xml

  ![image-20220916192853182](E:\Pictures\Typora\image-20220916192853182.png)

- dispatcher.xml

  ![image-20220916192943102](E:\Pictures\Typora\image-20220916192943102.png)

- applicationContext.xml

  ![image-20220916193041552](E:\Pictures\Typora\image-20220916193041552.png)

- DAO

  ![image-20220916193139329](E:\Pictures\Typora\image-20220916193139329.png)

- Service

  ![image-20220916193212826](E:\Pictures\Typora\image-20220916193212826.png)

- Controller

  ![image-20220916193251600](E:\Pictures\Typora\image-20220916193251600.png)

## 五、SpringMVC控制器开发详解三

### 1.核心要点

前面学习了SpringMVC作用中的接收用户请求参数和调用业务对象，这章将学习流程跳转

### 2.JavaWeb中流程跳转的回顾

#### 2.1JavaWeb流程跳转的核心代码

![image-20220916193711084](E:\Pictures\Typora\image-20220916193711084.png)

#### 2.2JavaWeb页面跳转方式的回顾

![image-20220916194025530](E:\Pictures\Typora\image-20220916194025530.png)

Session：状态、购物车、验证码...

![image-20220916194841787](E:\Pictures\Typora\image-20220916194841787.png)

如果传输的是对象类型，需要把对象中的数据取出，将这些数据拼接

#### 2.3SpringMVC的四种跳转方式

![image-20220916195421128](E:\Pictures\Typora\image-20220916195421128.png)

#### 2.4控制器forward页面

![image-20220916195655472](E:\Pictures\Typora\image-20220916195655472.png)

#### 2.5控制器redirect页面

![image-20220916195834801](E:\Pictures\Typora\image-20220916195834801.png)

#### 2.6控制器forward控制器

- 应用场景

  ![image-20220916200320128](E:\Pictures\Typora\image-20220916200320128.png)

- 编码

  ![image-20220916200437621](E:\Pictures\Typora\image-20220916200437621.png)

#### 2.7控制器redirect控制器

  ![image-20220916200727810](E:\Pictures\Typora\image-20220916200727810.png)

在JavaWeb中需要在路径中添加应用名，而SpringMVC中不用添加

### 3.Web作用域处理

#### 3.1JavaWeb中作用域回顾

- 三种作用域及其使用场景

  ![image-20220916201325240](E:\Pictures\Typora\image-20220916201325240.png)

#### 3.2SpringMVC中作用域处理

- 基本使用方式及其存在的问题

  ![image-20220916201810320](E:\Pictures\Typora\image-20220916201810320.png)

  ![image-20220916202122830](E:\Pictures\Typora\image-20220916202122830.png)

  ![image-20220916202410588](E:\Pictures\Typora\image-20220916202410588.png)

- SpringMVC中request作用域的处理

  - 代码

    ![image-20220916202548783](E:\Pictures\Typora\image-20220916202548783.png)

  - Model、ModelMap的相关细节

    ```markdown
    1. 通过Model、ModelMap进行作用域的处理，就可以解决视图模板耦合的问题
    ```

    ![image-20220916203157550](E:\Pictures\Typora\image-20220916203157550.png)

    SpringMVC通过视图解析器识别不同的视图模板

    ```markdown
    2. SpringMVC中提供的Model和ModelMap这两种方式处理request作用域，它们的区别是什么
    ```

    ![image-20220916224812412](E:\Pictures\Typora\image-20220916224812412.png)

    ```markdown
    3. 为什么不直接使用BindingAwareModelMap？
    ```

    ![image-20220916225920078](E:\Pictures\Typora\image-20220916225920078.png)

    ```markdown
    4. SpringMVC开发中为什么会提供两种方式？Model和ModelMap那种方式更为推荐？
    ```

    ![image-20220916230341316](E:\Pictures\Typora\image-20220916230341316.png)

    ![image-20220916225659118](E:\Pictures\Typora\image-20220916225659118.png)

    ```markdown
    5. 如果redirect跳转，数据该如何传递？
    ```

    ![image-20220916230744779](E:\Pictures\Typora\image-20220916230744779.png)

- SpringMVC中Session作用域的处理

  - 基本使用及其存在的问题

    ![image-20220916231021720](E:\Pictures\Typora\image-20220916231021720.png)

  - @SessionAttributes注解

    可以在控制器类上加入该注解，选择性的为Session中存储数据

    - 存储数据

      ![image-20220916231519575](E:\Pictures\Typora\image-20220916231519575.png)

      ![image-20220916231714964](E:\Pictures\Typora\image-20220916231714964.png)

    - 注意

      ![image-20220916232029051](E:\Pictures\Typora\image-20220916232029051.png)

    - 删除数据

      ![image-20220916232340170](E:\Pictures\Typora\image-20220916232340170.png)

- SpringMVC中application作用域的处理

  ![image-20220916233103968](E:\Pictures\Typora\image-20220916233103968.png)

  - 为什么SpringMVC没有提供application作用域

    ![image-20220916233356452](E:\Pictures\Typora\image-20220916233356452.png)

- 特殊操作

  - @ModelAttribute注解

    ![image-20220916234025439](E:\Pictures\Typora\image-20220916234025439.png)

  - 使用场景

    ![image-20220916234633563](E:\Pictures\Typora\image-20220916234633563.png)

  - 注意细节

    - 细节一

      ![image-20220916235120625](E:\Pictures\Typora\image-20220916235120625.png)

    - 细节二

      ![image-20220916235203211](E:\Pictures\Typora\image-20220916235203211.png)

- ModelAndView技术

  - 什么是ModelAndView【了解】

    ![image-20220917000316250](E:\Pictures\Typora\image-20220917000316250.png)

    ![image-20220917000752115](E:\Pictures\Typora\image-20220917000752115.png)

  - 总结目前控制器方法的返回值

    ![image-20220917001157664](E:\Pictures\Typora\image-20220917001157664.png)

### 4.视图控制器

####  4.1什么是视图控制器

![image-20220917001325057](E:\Pictures\Typora\image-20220917001325057.png)

#### 4.2受保护的视图模板该如何访问

所有视图模板只能通过控制器的forward进行访问

![image-20220919085203152](E:\Pictures\Typora\image-20220919085203152.png)

使用试图控制器，简单的创建空的只用于跳转到JSP的控制器方法，在标签中设置

注意：视图控制器的path属性值不能和@RequestMapping设置的路径冲突

#### 3.视图控制器的redirect跳转

![image-20220919085827827](E:\Pictures\Typora\image-20220919085827827.png)

### 5.静态资源处理

#### 5.1什么是静态资源

项目中非java代码部分的内容，图片、js、css

在这章学习之前，我们无法访问静态资源

#### 5.2原因

用户请求被DispatcherServlet接收之后，DispatcherServlet会去查找是否存在同名的控制器方法，找到之后，实例化对象，调用控制器方法，但是现在很明显是找不到，所以会报404错误

## Thymeleaf



# 统一异常处理



# 拦截器















S pring工厂是一个重量级资源，创建一次

所以需要将可能初始化的类都要注册进容器，初始化容器的时候，也会将这些组件初始化





Install-Module PSReadLine -Scope AllUsers -AllowPrerelease -Force -Verbose





\#设置执行权限 Set-ExecutionPolicy RemoteSigned -scope CurrentUser 

#从网络下载脚本并安装 Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh') 

#( 如果出现错误提示，是因为访问不了目标地址，使用如下地址安装 ) Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://cdn.yulinyige.com/script/scoop-installs.ps1')



Install-Module posh-git 

Install-Module oh-my-posh







#这是开启默认配置的 Set-Prompt 

#设置主题，Agnoster 是主题名  Set-Theme Agnoster



$PROFILE

oh-my-posh --print-shell




Install-Module -Name PSReadLine -Scope AllUsers -Force -SkipPublisherCheck


Install-Module posh-git -Scope AllUsers

Install-Module oh-my-posh -Scope AllUsers



Set-PoshPrompt -Theme agnoster
