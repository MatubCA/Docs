# Spring开发

## 一、Spring概述

### 1.EJB 存在的问题

![image-20220827213133259](E:\Pictures\Typora\image-20220827213133259.png)

1. **运行环境苛刻**

   `EJB（Enterprise Java Bean`）代码只能运行在服务器中的EJB容器中，但 `Tomcat` 没有 `EJB 容器`，而有 EJB 容器的服务器是闭源收费，如：Weblogic、Websphere，我们不能对服务器进行修改和定制。

2. **代码移植性差**

   EJB 代码运行在 Weblogic 或者 Websphere 中时，必须实现相应的接口，代码移植时，要对实现接口进行调整。

### 2.什么是Spring

Spring 是一个`轻量级`的`JavaEE解决方法`，整合了众多的优秀`设计模式`。它提供了一系列`底层容器和基础设施`，可以和大量常用的开源框架无缝`集成`。

- **轻量级**

  1. 对于运行环境没有额外要求。

     可以运行在闭源收费或者开源免费的服务器中，直接将代码运行在 Servlet 引擎中。

  2. 代码移植性高。

     不需要实现额外接口。

- **JavaEE解决方案**

  ![image-20220827213543233](E:\Pictures\Typora\image-20220827213543233.png)

  java 开发是分层开发的，不管是 struts2 还是 MyBatis 只解决了某一层的问题，而 Spring 则是解决了每一层的问题，所以 Spring 是框架之上的框架。

- **整合设计模式**

  1. 工厂
  2. 代理
  3. 模板
  4. 策略
  5. ...

- **框架的集成**

  不管是 MyBatis 还是 struts 都可以集成进 Spring "为我所用"！

### 3.设计模式

1. 广义

   面向对象设计中，**解决特定问题的经典代码**。

2. 狭义

   GOF 4位大师定义的**23种设计模式**。

### 4.工厂设计模式

#### 什么是工厂设计模式

不直接通过 `new` 的方式创建对象，而是`通过工厂类创建对象`，这样做的好处就是可以`解耦合`。

我们如果直接将接口的实现类`硬编码`在程序中，一旦实现类改变，调用者也要跟着改变，`不利于代码的维护`。

```java
// new 创建对象
UserService userService = new UserServiceImpl();
// 工厂类创建对象
UserService userService = BeanFactory.getUserservice();
```

#### 简单工厂的设计

1. 普通的javaWeb（使用junit代替controller）

   有登录、注册两个业务，调用 Service 的两个方法，Service 调用 Dao 的两个方法。

   ```java
   public class JavaBeanTest {
       private UserService userService = new UserServiceImpl();
       @Test
       public void test(){
           userService.login("张三","123");
   
           userService.register(new User("李四","4352"));
       }
   }
   ```

   可以看到 `UserService userService = new UserserviceImpl()`这行代码存在耦合。

2. 创建工厂，解耦合

   **工厂类：**

   ```java
   public class BeanFactory {
       public static UserService getUserService() {
           return new UserServiceImpl();
       }
   }
   ```

   **控制层：**

   ```java
   public class UserServiceImplTest {
   	//UserService userService = new UserServiceImpl();
       UserService userService = BeanFactory.getUserService();
       @Test
       public void test1() {  
           userService.login("张三","123");
   
           User user = new User("李四","456");
           userService.register(user);
       }
   }
   
   ```

3. 使用反射对工厂类进行解耦合

   ```java
   public class BeanFactory {
       public static UserService getUserService() {
   
           UserService userService = null;
           try {
               Class clazz = Class.forName("com.dong.UserServiceImpl");
               userService = (UserService) clazz.newInstance();
           } catch (ClassNotFoundException e) {
               e.printStackTrace();
           } catch (InstantiationException e) {
               e.printStackTrace();
           } catch (IllegalAccessException e) {
               e.printStackTrace();
           }
           return userService;
       }
   }
   ```

4. 使用小配置文件，对工厂类再次解耦合

   将 UserService 的实现类的全类名通过`配置文件`加载进来。

   ```java
   public class BeanFactory {
       private static Properties properties = new Properties();
   
       static {
           try {
               //1.获得IO输入流
               InputStream inputStream = BeanFactory.class.getResourceAsStream("/application.properties");
               //2.将文件内容封装到Properties集合中
               properties.load(inputStream);
   
               inputStream.close();
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
       
       public static UserService getUserService() {
           UserService userService = null;
           try {
               Class clazz = Class.forName(properties.getProperty("userService"));
               userService = (UserService) clazz.newInstance();
           } catch (ClassNotFoundException e) {
               e.printStackTrace();
           } catch (InstantiationException e) {
               e.printStackTrace();
           } catch (IllegalAccessException e) {
               e.printStackTrace();
           }
           return userService;
       }
   }
   ```

#### 通用工厂的设计

简单工厂是需要创建几个对象，就要定义几个工厂方法，对简单工厂进行抽象，转变为通用工厂。

```java
public class BeanFactory {
    private static Properties properties = new Properties();

    static {
        try {
            //1.获得IO输入流
            InputStream inputStream = BeanFactory.class.getResourceAsStream("/application.properties");
            //2.将文件内容封装到Properties集合中
            properties.load(inputStream);

            inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static Object getBean(String objectName) {
        Object obj = null;
        try {
            Class clazz = Class.forName(properties.getProperty(objectName));
            obj = clazz.newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return obj;
    }
}
```

### 5.总结

Spring本质：

**工厂 ApplicationContext（applicationContext.xml）。**

## 二、第一个Spring程序

### 1.版本控制

- JDK 1.8+
- Maven 3.5+
- IDEA 2018+
- SpringFramework5.1.4

当Maven是 `3.6`，IDEA是 `2019` 时会存在配合上的问题。

### 2.环境搭建

需要导入 Spring 的`依赖`以及创建S pring 的`核心配置文件`。

#### 依赖

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.1.4.RELEASE</version>
</dependency>
```

#### 配置文件

1. **配置文件放置的位置**

   `任意位置`，没有硬性要求，一般在 `resource` 目录下。

2. **配置文件的命名**

   没有硬性要求，建议 `application.xml`。

我们只需要在获取 Spring 工厂对象同时，传入 配置文件的位置即可。

![image-20220827225914348](E:\Pictures\Typora\image-20220827225914348.png)

### 3.Spring的核心API

Spring 的核心 API 就是 `ApplicationContext`，称为 `ApplicationContext工厂`或者`容器`，用于`对象的创建`。但是它是`接口`，主要实现类是：

- `ClassPathXmlApplicationContext`（**非Web环境**）
- `XmlWebApplicationContext`（**Web环境**）

使用接口的好处就是可以**屏蔽实现上的差异**。

需要注意的是：ApplicationContext 是一个`重量级资源`，ApplicationContext工厂的对象会占用`大量内存`，`所以一个应用只会创建一个工厂对象`。

只能创建一个对象，也就是说可能会存在`并发访问`的情况，所以ApplicationContext工厂一定是`线程安全`的。

### 4.程序开发

1. 创建实体类

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private Integer age;
}
```

2. 配置文件中配置

```xml
<bean id="user" class="com.dong.entity.User"/>
```

3. 工厂类获取对象

```java
@Test
public void test1(){
    ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext.xml");
    User user = (User) context.getBean("user");
    user.setName("John");
    user.setAge(20);
    System.out.println(user);
}
```

### 5.细节分析

#### 名词解释

Spring工厂创建的对象，叫做`bean`或者`组件`（component）。

#### Spring工厂相关方法

```java
@Test
public void test() {
    
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext.xml");
    
    //getBean方法重载id
    User user = (User)context.getBean("user");
    System.out.println(user);
    
    //getBean方法重载class,只能有一个叫做User的class
    User user = context.getBean(User.class);
    System.out.println(user);
    
    //getBean方法重载id和class
    User user = context.getBean("user", User.class);
    System.out.println(user);
    
    //获取Spring工厂配置文件中所有bean标签的id值
    String[] names = context.getBeanDefinitionNames();
    for (String name : names) {
        System.out.println(name);
    }
    
    //根据类型获得Spring配置文件中对应的id值
    String[] names = context.getBeanNamesForType(User.class);
    for (String name : names) {
        System.out.println(name);
    }
    
    //用于判断是否存在指定id值的bean
    boolean user = context.containsBeanDefinition("user");
    System.out.println(user);
    
    //用于判断是否存在指定id值得bean
    boolean user = context.containsBean("user");
    System.out.println(user);
}

```

#### 配置文件中注意的细节

##### class属性

bean 标签可以不配置 id 属性

```xml
<bean class="com.dong.User"/>
```

但是这个标签仍存在 id 值，如果打印一下 id 值，会发现 id值是：`com.dong.User#0`  (没有设置id的bean从0开始)，在这个 bean 只使用一次的情况下，可以这么写。

##### name属性

name 属性的作用是给 bean 设置别名。

```xml
context.getBean("id|name")-->Object
   
<bean id="" class="" 等效 <bean name="" class=""
```

**与 id 属性的不同点在于**：

1. 别名可以设置多个，id 只能设置一个。

   ```xml
   <bean name="p,p1..."
   ```

2. name属性没有命名要求，而id必须以字母开头，以数字、字母、下划线、连字符组成。不过 xml 发展到今天，id 命名限制已经不存在了。

3. 方法上的区别

   ```java
   // 用于判断是否存在指定id值的bean,不能判断name值
   boolean user = context.containsBeanDefinition("user");
   // 用于判断是否存在指定id值的bean,可以判断name值
   boolean user = context.containsBean("user");
   ```

### 6.Spring工厂的底层实现原理

**Spring工厂是可以调用对象私有的构造方法创建对象的**

<img src="E:\Pictures\Typora\image-20220827235132627.png" alt="image-20220827235132627"  />

### 7.思考

问题：在开发过程中,是不是所有的对象,都会交由Spring工厂来创建?

回答：理论上,是的。存在特例，实体对象(entity)是不会交由Spring创建,它由持久层框架来创建。

## 三、整合日志框架

### 1.为什么要整合日志

Spring与日志框架进行整合，日志框架就可以在控制台中或者指定路径，输出Spring框架运行过程中的一些重要的信息。便于了**解Spring框架的运行过程，利于程序的调试。**

### 2.整合日志

Spring1.2.3默认整合commons-logging.jar，Spring5.X默认整合日志框架 logback、log4j2。

我们使用 log4j 与 Spring5.x进行整合。因为 log4j 我们更加了解。

#### 依赖

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>

<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>1.7.36</version>
</dependency>

```

#### 配置文件

log4j 的配置文件叫做 log4j.properties

```properties
# resource目录根目录下
# 配置根
log4j.rootLogger = debug,console
# 日志输出到控制台显示
log4j.appender.console = org.apache.log4j.ConsoleAppender
log4j.appender.console.Target = System.out
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n
```

## 四、注入(Inject)

### 1.注入简介

#### 什么是注入？

通过Spring工厂及配置文件，为所创建对象的成员变量赋值。

#### 为什么要注入？

**通过编码的方式, 为成员变量赋值, 存在耦合**

![image-20220828002815317](E:\Pictures\Typora\image-20220828002815317.png)

#### 怎么注入？

1. 为类成员变量提供set, get方法

2. 配置Spring的配置文件

   ```xml
   <bean id="user" class="com.dong.entity.User">
       <property name="age" value="20" />
       <property name="name" value="张三"/>
   </bean>
   ```

#### 注入的好处

`解耦合`

### 2.Spring注入原理分析

![image-20220828003331919](E:\Pictures\Typora\image-20220828003331919.png)

**Spring底层会调用对象属性的set方法, 来完成成员变量的赋值, 这种方法也称为set注入**

### 3.Set注入

**Spring 底层通过 Set 方法为成员赋值的方式称为 Set 注入。**

针对不同类型的成员变量，在property 标签中，需要嵌套其它标签。

![](E:\Pictures\Typora\image-20220828003851573.png)

#### JDK内置类型

##### String+8种基本类型

```xml
<value>xxx</value>
```

```xml
<bean id="user" class="com.dong.entity.User">
    <property name="age">
        <value>20</value>
    </property>
    <property name="name">
        <value>John</value>
    </property>
</bean>
```

##### 数组和list集合

```xml
</property>
<property name="list">
    <list>
        <value>123@</value>
        <value>456@</value>
        <value>789@</value>
    </list>
</property>
```

##### Set集合

**注意Set集合的泛型, 根据泛型的不同, Set标签内部嵌套不同标签**

```xml
<!--泛型为String-->
<property name="set">
    <set>
        <value>111</value>
        <value>222</value>
        <value>333</value>
    </set>
</property>
```

##### Map集合

**一个entry标签代表一个键值对, key有key标签, value没有特殊标签, 因为key-value都是字符串, 所以使用value标签**

```xml
<property name="map">
    <map>
        <entry>
            <key><value>qqq</value></key>
            <value>www</value>
        </entry>
        <entry>
            <key><value>eeee</value></key>
            <value>dddd</value>
        </entry>
    </map>
</property>
```

##### Properties

**key-value都是字符串, 一个prop标签代表一个键值对**

```xml
<property name="pro">
    <props>
        <prop key="key1">value1</prop>
        <prop key="key2">value2</prop>
    </props>
</property>
```

#### 用户自定义类型

##### 第一种方式

1. 为成员变量提供get set方法

2. 配置文件中进行注入(赋值)

   ```xml
   <bean id="userService" class="com.dong.UserServiceImpl">
       <property name="userDao">
           <bean class="com.dong.UserDaoImpl"/>
       </property>
   </bean>
   ```

##### 第二种方式

第一种方式存在的问题

- 配置文件代码冗余
- 被注入的对象,多次创建,浪费内存(JVM)资源

1. 为成员变量提供get set方法

2. 配置文件进行注入

   ```xml
   <bean id="userDao" class="com.dong.UserDaoImpl"/>
   <bean id="userService" class="com.dong.UserServiceImpl">
       <property name="userDao">
         <ref bean="userDao"/>  
       </property>
   </bean>
   ```

#### Set注入的简化写法

##### 基于属性简化

```xml
JDK内置类型
<property name="username">
    <value>777</value>
</property>
<property name="password">
    <value>777</value>
</property>
简化:
<property name="username" value="777"/>
<property name="password" value="777"/>

自定义类型
<property name="userDao">
  <ref bean="userDao"/>
</property>
简化:
<property name="userDao" ref="userDao"/>
```

##### 基于p命名空间简化

在beans标签引入命名空间：

```xml
xmlns:p="http://www.springframework.org/schema/p"
```

```xml
JDK内置类型
<bean id="user" class="com.dong.User">
    <property name="username" value="777"/>
    <property name="password" value="777"/>
</bean>
简化:
<bean id="user" class="com.dong.User" p:username="777" p:password="777"/>

自定义类型
<bean id="userService" class="com.dong.UserServiceImpl">
    <property name="userDao" ref="userDao"/>
</bean>
简化:
<bean id="userService" class="com.dong.UserServiceImpl" p:userDao-ref="userDao"/>
```

### 4.构造注入

 **Spring 底层调用构造方法，通过配置文件，为成员变量赋值。**

#### 开发步骤

1. 提供有参构造方法

   ```java
   public class Customer {
       private String name;
       private int age;
       public Customer(String name, int age) {
           this.name = name;
           this.age = age;
       }
   }
   ```

2. Spring的配置文件的配置

   **一个constructor-arg标签代表一个参数**

   ```xml
   <bean id="customer" class = "com.dong.constructer.Customer">
       <constructor-arg>
           <value>张三</value>
       </constructor-arg>
       <constructor-arg>
           <value>111</value>
       </constructor-arg>
   </bean>
   ```

#### 构造方法重载

##### 参数个数不同时

**通过控制 constructor-arg 标签的数量，来指定调用，不同参数个数的构造方法。**

##### 参数个数相同时

**通过在标签引入 type 属性，进行类型的区分 <constructor-arg type="">。**

### 5.注入总结

在开发过程中 Set 注入更加常用，因为构造注入存在重载，比较麻烦，所以Spring底层大量使用了 Set 注入。

![image-20220828120924214](E:\Pictures\Typora\image-20220828120924214.png)



## 五、控制反转与依赖注入

### 1.控制反转(IOC)

**IOC**：(Inverse of Control)。

**控制** : 对于成员变量赋值的控制权 。

**控制反转** : 把对于成员变量赋值的控制权,从代码中反转(转移)到Spring工厂和配置文件中完成 。

**好处** : 解耦合 。

**底层实现** : 工厂设计模式。

![image-20220828125145958](E:\Pictures\Typora\image-20220828125145958.png)

### 2.依赖注入(DI)

**DI**：(Dependency Injection)

**注入** : 通过Spring工厂及配置文件，为对象(bean 组件)的成员变量赋值 

**依赖注入** : 当一个类需要另一个类时，就产生了依赖，我们可以把另一个类作为本类的成员变量，通过Spring配置文件进行注入(赋值) 

**好处** : 解耦合

![image-20220828125353031](E:\Pictures\Typora\image-20220828125353031.png)

## 六、Spring创建复杂对象

### 1.什么是复杂对象

复杂对象指的就是不能通过直接 new 构造方法创建的对象。比如：Connection、sqlSessionFactory等。

![image-20220828125858959](E:\Pictures\Typora\image-20220828125858959.png)

Spring工厂创建复杂对象有三种方式：

- **FactoryBean接口**
- **实例工厂**
- **静态工厂**

### 2.FactoryBean接口

#### 开发步骤

1. 实现FactoryBean接口，重写方法

   ```java
   //FactoryBean接口实现的方法
   
   public Object getObject(){
       //用于书写创建复杂对象的代码,并把复杂对象作为返回值返回
   }
   public Class getObjectType(){
       //返回所创建的复杂对象的Class对象
   }
   public Boolean isSingleton(){
       //创建一次 return true
       //每次调用都要创建 return false
   }
   ```

   ![image-20220828130414423](E:\Pictures\Typora\image-20220828130414423.png)

   **示例**：

   ```java
   //需要指明创建对象的泛型
   public class ConnectionFactoryBean implements FactoryBean<Connection> {
       //用于书写创建复杂对象的代码,并返回该对象
       @Override
       public Connection getObject() throws Exception {
           Class.forName("com.mysql.cj.jdbc.Driver");
           Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/student","root","00000000");
           return conn;
       }
   
       //返回所创建复杂对象的Class对象
       @Override
       public Class<?> getObjectType() {
           return Connection.class;
       }
   
       @Override
       public boolean isSingleton() {
           return false;
       }
   }
   ```

2. 配置文件中配置

   ```xml
   <bean id="conn" class="com.dong.FactoryBean.ConnectionFactoryBean"/>
   ```

#### 与创建简单对象的区别

**如果class中指定的类型,是FactoryBean接口的实现类,那么通过id值获得的对象,是这个类所创建的复杂对象**

![image-20220828130626837](E:\Pictures\Typora\image-20220828130626837.png)

![image-20220828130700683](E:\Pictures\Typora\image-20220828130700683.png)

#### 细节

- 怎么获得 FactoryBean 类型的对象

  ```java
  context.getBean("&conn")
  ```

- isSingleton 方法

  根据对象的特点, 返回 true 还是 fals。`能被共用`(sqlSessionFactory)，就创建一次，返回true;`不能被共用`(Connection)，每次创建，返回false。 

- mysql高版本连接时,指定SSL证书

  ```xml
  url="jdbc:mysql://localhost:3306/student?userSSL=false"
  ```

- 依赖注入的体会

  ```java
  @Override
  public Connection getObject() throws Exception {
      Class.forName("com.mysql.cj.jdbc.Driver");
      Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/student","root","00000000");
      return conn;
  }
  ```

  在上面的代码中很明显存在耦合--->"com.mysql.cj.jdbc.Driver"、"jdbc:mysql://localhost:3306/student"、"root"、"00000000"

  依赖于这些字符串, 所以我们可以进行`依赖注入`,来消除耦合：

  ```java
  //需要指明创建对象的泛型
  public class ConnectionFactoryBean implements FactoryBean<Connection> {
      private String driverName;
      private String url;
      private String username;
      private String password;
      ...get set
      //set注入
      @Override
      public Connection getObject() throws Exception {
          Class.forName(driverName);
          Connection conn = DriverManager.getConnection(url,username,password);
          return conn;
      }
      。。。
  }
  ```

  ```xml
  <bean id="conn" class="com.dong.FactoryBean.ConnectionFactoryBean">
      <property name="driverName" value="com.mysql.cj.jdbc.Driver"/>
      <property name="url" value="jdbc:mysql://localhost:3306/student" />
      <property name="username" value="root"/>
      <property name="password" value="00000000"/>
  </bean>
  ```

#### FactoryBean的实现原理

![image-20220828131300524](E:\Pictures\Typora\image-20220828131300524.png)

#### FactoryBean总结

**Spring中用于创建复杂对象的一种方式， Spring原生提供的, Spring整合其他框架大量使用了FactoryBean**

### 3.实例工厂

#### 为什么使用实例工厂

- 避免 Spring 的侵入

   使用FactoryBean，必须要实现 FactoryBean 接口。

- 整合遗留系统

  在开发过程中，我们已经`有了获取复杂对象的类`，这时,我们需要将这个类`整合`进 Spring 来帮助我们获得该对象。

#### 开发步骤

**因为已经有了获取复杂对象的方法, 我们只要在配置文件中进行配置即可**

```xml
<!--com.dong.factorybean.ConnectionFactory是已经有的创建复杂对象的类-->
<bean id="connFactory" class="com.dong.factorybean.ConnectionFactory"/>
<bean id="conn" factory-bean="connFactory" factory-method="getConnection"/>
```

### 4.静态工厂

#### 和实例工厂的区别

实例工厂中原有的创建复杂对象的方法不是静态方法 。

静态工厂中原有的创建复杂对象的方法是静态的 。

静态方法，Spring底层就可以`不用再创建对象`，直接`通过类名调用获取复杂对象的方法`。

#### 开发步骤

```xml
<bean id="conn" class="com.dong.factorybean.StaticConnectionFactory" factory-method="getConnection"/>
```

### 5.Spring 创建对象的总结

![image-20220828132335288](E:\Pictures\Typora\image-20220828132335288.png)



## 七、控制工厂创建对象的次数

### 1.为什么要控制创建次数

好处 : `节省内存资源`。

判断 : 

如果能被共用,线程安全,则只会创建一次 ，比如：SqlSessionFactory、DAO、Service 	

如果不能被共用,线程不安全,则会每次创建，比如：Connection、SQLSession

### 2.简单对象的控制

```xml
<bean id="scope" scope="singleton | prototype" class="com.dong.scope.Account"/>
singleton : 只会创建一次对象(默认)
prototype : 每次都会创建新的对象
```

### 3.复杂对象的控制

如果有 `isSingleton` 方，通过该方法控制创建次数，如果没有该方法，通过 `scope` 属性，来对对象创建次数进行控制。



## 八、对象的生命周期

### 1.什么是生命周期

对象`创建、存活、销毁`的完整的过程。

在以前通过 new 创建对象，调用对象，则该对象存活，直到被JVM的垃圾回收机制回收。现在由 Spring 负责对象的创建、存活、销毁，了解生命周期，`有助于使用好 Spring 为我们创建的对象`。

生命周期的三个主要阶段：

1. **创建阶段**
2. **初始化阶段**
3. **销毁阶段**

### 2.创建阶段

- `scope="singleton"`

  **Spring创建工厂的同时，创建对象**。

  注意：如果设置 `scope="singleton"`，但想要在获取对象的同时，创建对象，可以在 bean 标签中添加属性 `<bean ... lazy-init="true"/>`。

- `scope="prototype"`

  **Spring工厂在获取对象的同时，创建对象**。

### 3.初始化阶段

Spring 工厂在创建完对象后，调用对象的初始化方法，完成初始化操作。

**初始化方法提供**：程序员根据需求，提供初始化方法，完成初始化操作。有实现 `InitializingBean` 接口重写方法和提供普通初始化方法两种方式。 

**初始化方法调用**：Spring工厂进行调用。

- 实现 `InitializingBean` 接口，重写方法

  ```java
  //将初始化需求写在方法里面,完成初始化操作
  public void afterPropertiesSet(){}
  ```

- 或者在对象中提供一个普通的初始化方法

  ```java
  //名字随便起
  public void myInit(){}
  //配置文件中进行配置
  <bean id="product" class="xxx" init-method="myInit"/>
  ```

**细节分析**：

1. 如果一个对象既实现了 InitializingBean 接口, 又提供了普通的初始化方法时，怎么调用？谁先调用？

   **都会起作用**，先调用接口方法，再调用普通的初始化方法。

2. 如果对象中存在成员变量, 需要进行注入, 先注入还是先初始化?

   **注入一定会发生在初始化之前。**

3. 什么是初始化？

   资源的初始化: 数据库、IO、网络...

### 4.销毁阶段

Spring销毁对象之前，会调用对象的销毁方法，完成销毁操作。

Spring什么时候销毁所创建的对象：`context.close();`

**销毁方法的提供**：程序员根据需求，定义销毁方法，完成销毁操作。有实现 `DisposableBean`接口重写方法和提供普通的销毁方法两种方式。

**销毁方法的调用**： Spring工厂完成调用。

- 实现 `DisposableBean` 接口

  ```java
  //将销毁需求写在该方法中,完成销毁操作
  public void destroy(){}
  ```

- 在对象中提供一个普通的销毁方法

  ```java
  //在对象中定义方法,命名无要求
  public void myDestroy(){}
  //配置文件中进行配置
  <bean id="product" class="xxx"...destroy-method="myDestroy"/>
  ```

**细节分析**：

1. 销毁操作只适用于 `scope="singleton"` 时。

2. 什么是销毁

   主要是资源的释放。

### 5.总结

![image-20220828135912320](E:\Pictures\Typora\image-20220828135912320.png)



## 九、配置文件参数化

### 1.什么是配置文件参数化

把 Spring 配置文件中需要`经常修改`的`字符串信息`，`转移到一个更小的配置文件中`，比如：.properties，好处就是`利于维护`。

### 2.开发步骤

**以数据库配置为例**：

```xml
<bean id="conn" class="com.dong.factorybean.ConnectionFactoryBean">
    <property name="driverName" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/test"/>
    <property name="username" value="root"/>
    <property name="password" value="root"/>
</bean>
```

1. 提供一个小配置文件(db.properties)

   ```xml
   jdbc.driver = com.mysql.cj.jdbc.Driver
   jdbc.url = jdbc:mysql://localhost:3306/test
   jdbc.username = root
   jdbc.password = root
   ```

2. Spring 配置文件与小配置文件进行整合

   ```xml
   <context:property-placeholder location="classpath:/db.properties"/>
   <!--需要添加命名空间-->
   xmlns:context="http://www.springframework.org/schema/context"
   <!--在xsi:schemaLocation标签中,添加解析方法-->
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context-4.2.xsd
   ```

3. 在Spring配置文件中通过 `${key}` 获取小配置文件的值

   ```xml
   <bean id="conn" class="com.dong.factorybean.ConnectionFactoryBean">
       <property name="driverName" value="${jdbc.driver}"/>
       <property name="url" value="${jdbc.url}"/>
       <property name="username" value="${jdbc.username}"/>
       <property name="password" value="${jdbc.password}"/>
   </bean>
   ```



## 十、自定义类型转换器

### 1.类型转换器

作用：Spring 通过类型转换器把配置文件中`字符串类型`的数据，转换成对象中成员变量`对应类型`的数据，从而完成注入。

![image-20220828140843361](E:\Pictures\Typora\image-20220828140843361.png)

### 2.自定义类型转换器

当 Spring 内部没有提供特定的类型转换器时，而程序员在应用过程中还需要使用，那么就需要程序员自己定义类型转换器。

![image-20220828140939615](E:\Pictures\Typora\image-20220828140939615.png)

1. 实现 `Converter<s,t>` 接口

   ```java
   public class MyDateConverter implements Converter<String, Date> {
   
       /*
           convert:将String类型转换为Date
           param:source 代表配置文件中的日期字符串
           return:Date 返回Date类型,让Spring拿到转换后的数据
        */
       @Override
       public Date convert(String source) {
           Date date = null;
           try {
               SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
               date = sdf.parse(source);
           } catch (ParseException e) {
               e.printStackTrace();
           }
           return date;
       }
   }
   ```

2. 在 Spring 配置文件中进行配置

   - 将 MyDateConverter 对象创建出来

     ```xml
     <bean id="myDateConverter" class="com.dong.converter.MyDateConverter"/>
     ```

   - 类型转换器的注册

     目的：告知 Spring 框架，MyDateConverter 是一个类型转换器。

     ```xml
     <!--将MyDateConverter注入conversionService-->
     <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
         <property name="converters">
             <set>
                 <ref bean="myDateConverter"/>
             </set>
         </property>
     </bean>
     ```

### 3.细节

- MyDateConverter 中的日期格式, 可以通过依赖注入的方式, 由配置文件完成赋值

  ```java
  public class MyDateConverter implements Converter<String, Date> {
      private String pattern;
  
      public String getPattern() {
          return pattern;
      }
  
      public void setPattern(String pattern) {
          this.pattern = pattern;
      }
  
      /*
              convert:将String类型转换为Date
              param:source 代表配置文件中的日期字符串
              return:Date 返回Date类型,让Spring拿到转换后的数据
           */
      @Override
      public Date convert(String source) {
          Date date = null;
          try {
              SimpleDateFormat sdf = new SimpleDateFormat(pattern);
              date = sdf.parse(source);
          } catch (ParseException e) {
              e.printStackTrace();
          }
          return date;
      }
  }
  ```

  ```xml
  <bean id="myDateConverter" class="com.dong.converter.MyDateConverter">
      <property name="pattern" value="yyyy-MM-dd"/>
  </bean>
  ```

- `ConversionServiceFactoryBean` 定义的 id 属性值必须是 **`conversionService`**

- Spring 框架内置了日期类型装换器, 但格式有要求

  日期格式: 2022/04/16 (不支持: 2022-4-16)。



## 十一、后置处理Bean

### 1.什么是后置处理Bean

通过 BeanPostProcessor 接口中提供的方法，对 Spring 工厂所创建的对象，进行再加工。

没有加工时：

![image-20220828141959526](E:\Pictures\Typora\image-20220828141959526.png)

### 2.后置处理Bean的原理

![image-20220828142114367](E:\Pictures\Typora\image-20220828142114367.png)

实现 `BeanPostProcessor` 接口，重写规定的方法：

- `Object postProcessBeforeInitialization(Object bean,String beanName)`

  作用: Spring创建完对象，并进行注入后，可以运行 `Before` 方法进行加工。

  通过方法参数获得 Spring 创建好的对象；通过返回值将加工好的对象交给 Spring 工厂。

- `Object postProcessAfterInitialization(Object bean,String beanName)`

  作用: Spring执行完对象的初始化操作后，可以运行 `After` 方法进行加工。、

  通过方法参数获得 Spring 创建好的对象；通过返回值将加工好的对象交给 Spring 工厂。

实际开发中，很少处理 Spring 的初始化操作，这种情况下，没必要区 分Before、After，只需实现 `After` 即可。

`Before` 方法可以什么都不用写，但`必须将 bean 返回`，才能把对象传递给 `After` 方法。

### 3.开发步骤

1. 用一个类实现 `BeanPostProcessor` 接口

   ```java
   public class MyBeanPostProcessor implements BeanPostProcessor {
       @Override
       public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
           return bean;
       }
   
       @Override
       public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
           /*
           if (bean instanceof Category category) {
                category.setName("SWJ");
            }
           */
           if(bean instanceof Category){
               Category category = (Category) bean;
               category.setName("SWJ");
           }        
           return bean;
       }
   }
   ```

2. 在 Spring 配置文中进行配置

   ```xml
   <bean id="myBeanPostProcessor" class="com.dong.beanpost.MyBeanPostProcessor"/>
   ```

**细节**：

`BeanPostProcessor` 会对 Spring 工厂中`所有`创建的对象进行加工。



## 十二、静态代理模式

### 1.为什么需要静态代理

在解答为什么需要静态代理之前，需要搞明白以下几个问题：

1. 在 JavaEE 分层开发中，那个层对我们最重要？

   `DAO`-->`Service`-->`Controller`。

   在 JavaEE 分层开发中，最为重要的是 `Service` 层，以为它完成了业务的核心逻辑。

2. Service 层中包含哪些代码？

   Service 层 = `核心功能`(几十行 上百行代码) + `额为功能`(附加功能)

   - 核心功能：业务运算、DAO 调用...
   - 额外功能：不属于业务、可有可无、代码量小，比如：**事务**、**日志**、**性能**等。

3. 额外功能写在 Service 层好不好？

   **Service调用者**(Controller): 需要Service书写额外功能 。

   **软件设计者**: 不需要Service书写额外功能。

4. 现实生活中的解决方法

   ![image-20220828143848098](E:\Pictures\Typora\image-20220828143848098.png)

   房东现在不想有广告、看房的业务了, 太累了, 成本太高, 想只保留签合同、收钱的业务。

   但是, 如果没有广告、看房业务, 房客就不知道房东有房出租，该怎么解决呢?

   房东可以找一个中介，将广告、看房的业务交给中介，自己躺着收钱就好。如果对该中介不满意，还可以换一个中介，而签合同、收钱的业务没有变化。

   注意: 房东提供的方法与中介提供的方法必须一致，不能欺骗消费者是吧！

   ![image-20220828144147296](E:\Pictures\Typora\image-20220828144147296.png)

### 2.代理设计模式

#### 概念

通过代理类，为原始类增加额外功能。好处便是有`利于原始类的维护`。

#### 名词解释

- 原始类(目标类)：业务类(核心功能-->业务运算\DAO调用)。
- 原始方法(目标方法)：原始类中的方法。
- 额外功能(附加功能)：日志\事务\性能。

#### 代理开发的核心要素

**代理类 = 目标类 + 额外功能 + 与目标类实现相同的接口(方法一致)**

### 3.编码

**静态代理:** 有一个原始类就要有一个代理类。

```java
//实现与目标类相同的接口
public class UserServiceProxy implements UserService{
    private UserServiceImpl userService = new UserServiceImpl();
    @Override
    public void register(User user) {
        //代理内容
        System.out.println("-----代理功能register-----");
        //目标类内容
        userService.register(user);
    }

    @Override
    public boolean login(String username, String password) {
        System.out.println("----代理功能login-----");
        return userService.login("username","password");
    }
}
```

### 4.静态代理存在的问题

- 静态代理类文件过多，不利于项目管理。
- 维护性差，当给多个原始类添加相同的额外功能的时候，如果这个额外功能有变化，就需要更改所有的代理类。



## 十三、Spring动态代理开发

### 1.概念

通过代理类为原始类(目标类)增加额外功能，好处: 利于原始类的维护。

### 2.环境搭建

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.3.20</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.9.9.1</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.9.1</version>
</dependency>
```

### 3.开发步骤

1. 创建原始对象

   ```java
   public class UserServiceImpl implements UserService{
       @Override
       public void register(User user) {
           System.out.println("UserServiceImpl.register");
       }
       @Override
       public boolean login(String username, String password) {
           System.out.println("UserServiceImpl.login");
           return true;
       }
   }
   ```

   ```xml
   <bean id="userService" class="com.dong.proxy.UserServiceImpl"/>
   ```

2. 额外功能

   **创建类实现 MethodBeforeAdvice 接口**。

   额外功能书写在接口的实现中，在原始方法`执行之前`运行额外功能。

   ```java
   public class Before implements MethodBeforeAdvice {
       @Override
       public void before(Method method, Object[] args, Object target) throws Throwable {
           System.out.println("-----log:MethodBeforeAdvice-------");
       }
   }
   ```

   ```xml
   <bean id="before" class="com.dong.dynamic.Before"/>
   ```

3. 定义切入点

   **切入点**: 额外功能加入的位置(哪个方法)。

   **目的**: 由程序员根据自己需要，决定额外功能加入那个原始方法。

   ```xml
   <aop:config>
       <!--为所有的方法加上额外功能,切入点为所有方法-->
       <aop:pointcut id="before" expression="execution(* *(..))"/>
   </aop:config>
   ```

4. 组装

   整合二三步。

   ```xml
   <aop:config>
       <!--为所有的方法加上额外功能,切入点为所有方法-->
       <aop:pointcut id="bef" expression="execution(* *(..))"/>
       <!--额外功能定义在Before类,切入点是UserService中的所有方法-->
       <aop:advisor advice-ref="before" pointcut-ref="bef"/>
   </aop:config>
   ```

5. 调用

   **目的**: 获得 Spring 工厂创建的动态代理对象，并进行调用。

   **注意**：

   1. Spring 工厂通过原始对象的 id 值获得的是代理对象。

      `context.getBean("userService")`

   2. 获得代理对象后，可以通过声明接口类型，进行存储(代理对象和原始对象实现相同接口)。

      `UserService userService = context.getBean("userService")`

### 4.细节分析

1. **Spring 创建的动态代理类在哪里？**

   Spring 框架在运行时，通过动态字节码技术，在 JVM 中创建，运行在 JVM 内部。等程序结束后，会和 JVM 一起消失，所以不会和静态代理一样，类文件过多，影响项目管理。

2. **什么是动态字节码技术？**

   通过第三方字节码框架，在 JVM 中创建对应类的字节码，进而创建对象，当虚拟机结束，动态字节码跟着消失。

   ![image-20220828150154345](E:\Pictures\Typora\image-20220828150154345.png)

3. **动态代理编程简化代理的开发**

   在额外功能不改变的前提下，创建其他原始类的代理对象时，只要在`aop:advisor`标签中指定原始对象即可。

4. **动态代理额外功能维护性增强**

   在程序开发中遵循一个原则: `打开拓展,关闭修改` 。

   当我们对额外功能不满意时，我们不必去修改额外功能代码，只需新建一个额外功能，然后在配置文件中，修改标签即可。



## 十四、Spring动态代理详解

### 1.额外功能详解

#### MethodBeforeAdvice分析

```java
public class Before implements MethodBeforeAdvice {
    /*
        作用: 把需要在原始方法执行之前运行的额外功能,书写在before方法中

        Method: 增加额外功能的那个原始方法

        Object[]: 增加额外功能的那个原始方法的参数

        Object: 增加额外功能的原始方法所在的原始对象 
        
        这些参数根据需要进行使用
     */
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println("-----log:MethodBeforeAdvice-------");
    }
}
```

#### MethodInterceptor(方法拦截器)

```java
public class Arround implements MethodInterceptor {

    /*
        invoke方法: 将额外功能书写在invoke方法中,额外功能可以运行在原始方法之前 之后 以及前后都可以运行

        MethodInvocation: 额外功能所增加的那个原始方法

        invocation.proceed()--->运行对应的原始方法
        由这个方法,就可以区分额外功能的运行时机:
                额外功能写在invocation.proceed()之前,表示先运行额外功能,再运行原始方法
                额外功能写在invocation.proceed()之后.表示先运行原始方法,再运行额外功能
                额外功能写在invocation.proceed()之前和之后,表示额外功能前后都执行

         返回值: Object: 原始方法的返回值
                invocation.proceed()代表原始方法的运行,我们只要返回该方法的执行结果即可

     */
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {

        //额外方法
        Object proceed = invocation.proceed();
        //额外方法
        return proceed;
    }
}
```

```java
// Spring工厂配置与MethodBeforeAdvice一样
// 事务在原始方法执行之前之后,都要运行
// 额外功能可能运行在原始方法抛出异常的时候
@Override
public Object invoke(MethodInvocation invocation) throws Throwable {
    Object proceed = null;
    try {
        proceed = invocation.proceed();
    } catch (Throwable e) {
        System.out.println("原始方法抛出异常时运行额外方法!");
        e.printStackTrace();
    }
    return proceed;
}
```

**MethodInterceptor 影响原始方法的返回值**：
原始方法的返回值`(invocation.proceed())`,直接作为 `invoke` 方法的返回值返回,MethodInterceptor 不会影响原始方法的返回值。	

`invoke` 方法中,不要将原始方法的返回值作为 invoke 方法的返回值返回即可。

### 2.切入点详解

```xml
切入点决定额外功能加入的位置(哪些方法)

<aop:pointcut id="before" expression="execution(* *(..))"/>

1. execution(): 切入点函数
2. * *(..): 切入点表达式
```

#### 切入点表达式

1. 方法切入点表达式

   ![image-20220828151249804](E:\Pictures\Typora\image-20220828151249804.png)

   ```markdown
   * *(..)  --->所有方法
   
   *  -->修饰符返回值
   *  -->方法名
   ()  -->参数列表
   ..  -->对于参数没有要求(个数,类型都没有要求)
   ```

   - 定义login方法作为切入点

     ```markdown
     * login(..)
     ```

   - 定义login方法, 且login方法有两个字符串类型的参数, 作为切入点

     ```markdown
     * login(String,String)
     
     # 注意: 非java.lang包中的类型,必须写全限定名
     * register(com.dong.proxy.User)
     
     # 注意: ..可以和具体的参数连用
     * login(String,..)  -->login(String) login(String,String) login(String,com.dong.proxy.User)
     ```

   - 这种切入点表达式不精准(同名干扰)

     ![image-20220828151526789](E:\Pictures\Typora\image-20220828151526789.png)

   - 精准的切入点表达式

     ```markdown
     修饰符返回值 包.类.方法(参数)
     
     	*       com.baizhiedu.a.UserServiceImpl.login(String,String)
     ```

     ![image-20220828151558724](E:\Pictures\Typora\image-20220828151558724.png)

2. 类切入点表达式

   指定类作为额外功能的切入点，这个类中所有方法都会添加额外功能，在调用这个类时，会添加额外功能，在调用其他类时，不会添加额外功能。

   - 语法一

     ```markdown
     # UserServiceImpl类中所有方法加入额外功能
     * com.baizhiedu.a.UserServiceImpl.*(..)
     ```

   - 语法二

     ```markdown
     # 忽略包,给不同包中的同名类中的所有方法添加额外功能
     1. 类只存在一级包中 com.UserServiceImpl
     * *.UserServiceImpl.*(..)
     
     2. 类在多级包 com.baizhiedu.proxy.UserServiceImpl
     * *..UserServiceImpl.*(..)
     ```

3. 包切入点表达式

   指定包作为额外功能的切入点，包中所有类所有方法，都将加入额外功能。

   - 语法一

     ```markdown
     * com.baizhiedu.proxy.*.*(..)
     
     # 注意: 切入点包中的所有类,必须在proxy包中,不能在proxy包的子包中
     ```

   - 语法二

     ```markdown
     # 让当前包的子包生效
     
     * com.baizhiedu.proxy..*.*(..)
     ```

#### 切入点函数

**用于执行切入点表达式**

1. execution

   ```markdown
   最为重要的切入点函数,功能最全
   可以执行 方法切入点表达式 类切入点表达式 包切入点函数
   
   弊端: execution执行切入点表达式,书写麻烦
   
   注意: 其他切入点函数 简化的是execution书写复杂度,功能一致
   ```

2. args

   ```markdown
   作用: 主要用于方法参数的匹配
   
   切入点: 必须是两个字符串类型的参数
   
   execution(* *(String,String))
   args(String,String)
   ```

3. within

   ```markdown
   作用: 主要用于类\包切入点表达式的匹配
   
   切入点: UserServiceImpl这个类
   
   execution(* *..UserServiceImpl.*(..))
   
   within(*..UserServiceImpl)
   
   切入点: com.baizhiedu.proxy这个包
   
   execution(* com.baizhiedu.proxy..*.*)
   
   within(com.baizhiedu.proxy..)
   ```

4. @annotation

   ```markdown
   作用: 为具有特殊注解的方法加入额外功能
   
   切入点: 加有com.baizhiedu.Log这个注解的方法
   
   @annotation(com.baizhiedu.Log)
   ```

切入点函数的逻辑运算，指的是 整合多个切入点函数一起配合工作,进而完成更为复杂的需求：

- and与操作

  ```markdown
  案例: 方法名为login,同时有两个字符串参数
  
  1. execution(* login(String,String))
  
  2. execution(* login(..)) and args(String,String)
  
  注意: 与操作不能用于同种类型的切入点函数
  
  ```

- or或操作

  ```markdown
  案例: register方法和login方法作为切入点
  
  execution(* register(..)) or execution(* login(..))
  ```



## 十五、AOP编程

### 1.概念

- `AOP(Aspect Oriented Programing)`

  本质就是 Spring 的动态代理开发，通过代理类为原始类增加额外功能。

  以切面为基本单位的程序开发，通过切面间的相互协同，相互调用，完成程序的构建。

  切面 = 切入点 + 额外功能

  AOP 是在 OOP 的基础上进行开发的,AOP 不能取代 OOP。

- `OOP(Object Oriented Programing)`

  面向对象编程。

  以对象为基本单位的程序开发，通过对象间的彼此协同，相互调用，完成程序的构建。

- `POP(Producer Oriented Programing)`

  面向过程(方法\函数)编程。

  以过程为基本单位的程序开发，通过过程间的彼此协同，相互调用，完成程序的构建。

### 2.开发步骤

1. 原始对象
2. 额外功能(实现MethodInterceptor接口)
3. 切入点
4. 组装切面

### 3.名词解释

切面 = 切入点 + 额外功能

面 = 点 + 相同性质

![image-20220828152852685](E:\Pictures\Typora\image-20220828152852685.png)



## 十六、AOP底层实现原理

### 1.核心问题

1. AOP 如何创建动态代理类
2. Spring 工厂如何加工创建代理对象（通过原始对象的id值,获得的是代理对象）

Spring 的动态代理开发主要有两种：`JDK` 和 `CGlib`。

### 2.JDK动态代理

通过方法 `Proxy.newProxyInstance(ClassLoader,interfaces,InvocationHandler)` 创建代理对象，并返回代理对象。

#### 方法参数

- `InvocationHandler`

  ```markdown
  将额外功能写在InvocationHandler接口的invoke方法中,额外功能可以执行在原始方法执行之前\之后\前后\抛出异常
  
  Object invoke(Object proxy,Method method,Object[] args)
  
  返回值: 原始方法的返回值
  
  参数: proxy  -->代表代理对象,将Proxy.newProxyInstance创建好的代理对象传入invoke方法,现在基本不用,可以忽略
       method  -->额外功能所增加给的那个原始方法
       args  -->原始方法的参数
       
  原始方法的运行: method.invoke(userService,args),将运行结果作为原始方法的返回值返回
  ```

- `interfaces`

  ```markdown
  原始对象实现的接口
  作用: 代理类和原始类实现相同接口
  
  userService.getClass().getInterfaces()
  ```

- `ClassLoader`

  ```markdown
  类加载器的作用: 
  
  1. 通过类加载器将对应类的字节码文件加载进JVM
  2. 通过类加载器创建类的Class对象,进而创建这个类的对象
  
  过程: 编写User.java文件,编译为User.class文件,由类加载器将User.class文件加载进JVM,再由类加载器创建类的Class对象,才能创建这个类的对象
  
  获取类加载器: JVM为每一个类的.class文件,自动分配与之对应的ClassLoader
  
  在动态代理类的开发中,需要动态代理类,才能创建代理对象,但是,动态代理类是由动态字节码技术,也就是Proxy.newProxyInstance(interfaces,InvocationHandler),将创建的字节码直接写入JVM,没有.java文件,也没有.class文件,所以JVM不能给动态代理类分配ClassLoader,没有ClassLoader就无法创建类的Class对象,也就没办法创建类的对象,所以Proxy.newProxyInstance方法需要第三个参数,那就是类加载器
  
  解决办法: 借用一个ClassLoader,可以是任何一个类类加载器
  ```

#### 图解

![image-20220828182202299](E:\Pictures\Typora\image-20220828182202299.png)

#### 编码

```java
public class TestJDKProxy {
    public static void main(String[] args) {
        //1.创建原始对象
        UserService userService = new UserServiceImpl();
        //2.JDK创建动态代理
        //以内部类的方式实现InvocationHandler接口
        InvocationHandler handler = new InvocationHandler() {

            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                //额外方法
                System.out.println("----proxy log------");

                //原始方法运行
                Object ret = method.invoke(userService, args);
                return ret;
            }
        };
        UserService userServiceProxy = (UserService) Proxy.newProxyInstance(TestJDKProxy.class.getClassLoader(), userService.getClass().getInterfaces(), handler);
        userServiceProxy.register(new User());
        userServiceProxy.login("rad","123");
    }
}
```

### 3.CGlib动态代理

CGlib创建动态代理的原理: 父子继承关系创建代理对象，原始类作为父类，代理类作为子类，这样既可以保证两者方法一致，同时也可以在代理类中提供新的实现。

![image-20220828182323264](E:\Pictures\Typora\image-20220828182323264.png)

#### 编码

```java
public class TestCGlib {
    public static void main(String[] args) {
        //1.创建原始对象
        UserService userService = new UserService();
        /*
            2.通过CGlib的方式创建动态代理对象
            和JDK创建动态代理高度一致,将实现接口变成继承父类
            Proxy.newProxyInstance(ClassLoader,interfaces,InvocationHandler)

            Enhancer.setClassLoader()
            Enhancer.setSuperClass()
            Enhancer.setCallback()  -->需要实现MethodInterceptor -->实现intercept方法(和invoke方法一致)
            enhancer.create()  --> 创建代理对象

         */
        Enhancer enhancer = new Enhancer();
        enhancer.setClassLoader(TestCGlib.class.getClassLoader());
        enhancer.setSuperclass(userService.getClass());

        MethodInterceptor interceptor = new MethodInterceptor(){

            @Override
            public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                System.out.println("----log----");
                Object ret = method.invoke(userService,args);
                return ret;
            }
        };

        enhancer.setCallback(interceptor);
        UserService serviceProxy = (UserService) enhancer.create();
        serviceProxy.login("args","333");
        serviceProxy.register(new User());
    }
}
```

### 4.总结

1. JDK 创建代理对象 `Proxy.newProxyInstance` 通过接口实现来创建代理类
2. CGlib 创建动态代理 `enhancer` 通过继承父类来创建代理类



### 5.Spring工厂如何加工

Spring工厂如何加工原始对象成为代理对象？

#### 思路分析

![image-20220828182726350](E:\Pictures\Typora\image-20220828182726350.png)

#### 编码

```java
public class ProxyBeanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {

        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                System.out.println("-----new log-----");
                Object ret = method.invoke(bean, args);
                return ret;
            }
        };
        return Proxy.newProxyInstance(ProxyBeanPostProcessor.class.getClassLoader(),bean.getClass().getInterfaces(),handler);
    }
}
```

```xml
<bean id="proxyBeanPostProcessor" class="com.dong.factory.ProxyBeanPostProcessor"/>
```



## 十七、基于注解的AOP编程

### 开发步骤

1. 原始对象
2. 额外功能
3. 切入点
4. 组装切面

```markdown
将2 3 4步放在切面类中:

定义切面类: @Aspect
定义额外功能: @Around
			原始方法的运行: ProceedingJoinPoint.proceed()
定义切入点: @Around(* login(..))
```

```java
package com.dong.aspect;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;

@Aspect
public class MyAspect {
    @Around("execution(* login(..))")
    public Object around(ProceedingJoinPoint point) throws Throwable {

        System.out.println("----Aspect log--------");
        Object ret = point.proceed();

        return ret;
    }
}
```

```xml
<!--原始对象-->
<bean id="userService" class="com.dong.aspect.UserServiceImpl"/>
<!--
    切面
        1.额外功能
        2.切入点
        3.组装切面
-->
<bean id="around" class="com.dong.aspect.MyAspect"/>
<!--告知Spring基于注解进行AOP编程-->
<aop:aspectj-autoproxy/>
```

### 细节

#### 切入点复用

```java
//在切面类中定义一个函数,加上注解@Pointcut 将切入点函数提取出来,利于切入点的复用

@Aspect
public class MyAspect {

    @Pointcut("execution(* login(..))")
    public void myPointcut() {
    }

    @Around(value = "myPointcut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        System.out.println("----Aspect log--------");
        Object ret = point.proceed();

        return ret;
    }

    public Object around2(ProceedingJoinPoint point) throws Throwable {
        System.out.println("----new Aspect log------");
        Object ret = point.proceed();
        
        return ret;
    }
}
```

#### 动态代理的创建方式

```java
AOP的底层实现 动态代理的两种创建方式
1. JDK
2. CGlib

默认情况下,AOP编程,底层应用JDK动态代理创建方式
如果切换CGlib
		1. 基于注解的AOP开发
			<aop:aspectj-autoproxy proxy-target-class="true"/>
			false是默认情况,使用JDK创建代理对象
		2. 传统的AOP开发
			<aop:config proxy-target-class="true">
```



## 十八、AOP开发中的坑

### 问题

```java
//在同一个业务类中,一个业务方法调用另一个业务方法
//问题: login方法添加有额外功能
//	    register方法没有添加额外功能
public class UserServiceImpl implements UserService {
    @Override
    public void login(String username, String password) {
        System.out.println("UserServiceImpl.login");
        this.register(new User());
    }

    @Override
    public void register(User user) {
        System.out.println("UserServiceImpl.register");
    }
}
```

### 分析

```markdown
调用login方法的,是代理对象,而调用register方法的,是原始对象,所以没有加上额外功能

能不能在login方法中再获取一个代理对象,从而使代理对象调用register方法呢?

不能,我们知道Spring工厂是一个重量级资源,一个应用应当创建一个Spring工厂
```

### 解决

```markdown
使业务类再实现ApplicationContextAware接口
实现接口的setApplicationContext方法,这个方法的作用是获取创建的Spring工厂对象
```

### 编码

```java
public class UserServiceImpl implements UserService, ApplicationContextAware {
    private ApplicationContext context;
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.context = applicationContext;
    }

    @Override
    public void login(String username, String password) {
        System.out.println("UserServiceImpl.login");
        UserService userService = (UserService) context.getBean("userService");
        userService.register(new User());
    }

    @Override
    public void register(User user) {
        System.out.println("UserServiceImpl.register");
    }
}
```



## 十九、AOP阶段总结

![image-20220828192010540](E:\Pictures\Typora\image-20220828192010540.png)



## 二十、持久层整合

### 1.为什么整合

1. JavaEE 开发需要持久层进行数据库的访问操作。
2. JDBC、Hibernate、MyBatis 开发过程中存在大量的代码冗余。
3. Spring 基于`模板设计模式`对上述持久层技术进行了封装。

### 2.与那些持久层整合

1. JDBC

   提供有 JDBCTemplate 工具类。

2. Hibernate

   提供有 HibernateTemplate 工具类。

3. MyBatis

   提供 sqlSessionFactoryBean、MapperScannerConfigure 工具类。



## 二十一、整合MyBatis

### 1.MyBatis开发步骤的回顾

1. **创建实体类**

   ```java
   @Data
   @NoArgsConstructor
   @AllArgsConstructor
   public class User {
       private Integer id;
       private String name;
       private String password;
   }
   ```

2. **设置实体别名**

   在 MyBatis 的核心配置文件的 `<TypeAliases>` 标签中为实体类设置别名。在mapper文件中就可以书写别名。

   ```xml
   <typeAlias type="com.dong.entity.User" alias="user"/>
   ```

3. **创建表**

4. **创建DAO接口**

   也就是创建Mapper接口。

   ```java
   public interface UserMapper {
   
       int insertUser(User user);
   }
   
   ```

5. **实现Mapper接口**

   创建映射文件。

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <!--namespace 和mapper接口的全类名一致-->
   <mapper namespace="com.dong.mapper.UserMapper">
       <insert id="insertUser" parameterType="user">
           insert into user values (null, #{arg0}, #{arg1})
       </insert>
   ```

6. **注册mapper文件**

   在 MyBatis 的核心配置文件的 `<mappers>` 标签中注册mapper文件。

   ```xml
   <mappers>
       <mapper resource="mappers/UserMapper.xml"/>
   </mappers>
   ```

7. **MyBatisAPI调用**

   ```java
   @Test
   public void test10() throws IOException {
       InputStream input = Resources.getResourceAsStream("mybatis-config.xml");
       SqlSessionFactory build = new SqlSessionFactoryBuilder().build(input);
       
       SqlSession sqlSession = build.openSession(true);
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       
       mapper.insertUser("陀罗","123");
   }
   ```

   

### 2.MyBatis存在的问题

**配置繁琐、代码冗余**

### 3.整合Mybatis的思路

Spring 对于 Mybatis 的整合也就是对 MybatisAPI 调用的四行代码的整合。

首先通过 Spring 配置文件将 SqlSessionFactory 对象注册进 Spring容器中。

```java
InputStream input = Resources.getResourceAsStream("mybatis-config.xml");
SqlSessionFactory build = new SqlSessionFactoryBuilder().build(input);
```

Spring 通过 SqlSessionFactoryBean 封装这两行代码！

可以看到 SqlSessionFactory 对象是依赖于 MyBatis 的核心配置文件的。所以需要将 Mybatis 核心配置文件中相应的依赖通过 Spring 配置文件 注入SqlSessionFactoryBean 对象。

那 MyBatis 核心配置文件中都有哪些需要注入的依赖呢？

1. 实体别名
2. 数据源
3. mapper文件的注册

注意：Mybatis 核心配置文件 `<configuration>`标签下的标签都可以注入，这里只是对主要功能进行注入演示！

SqlSessionFactoryBean 对上述三个依赖分配了三个属性：

1. typeAliasesPackage：指定实体类所在包，为实体类自动创建别名，别名就是类名。
2. dataSource：用户自定义类型，需要引入连接池对象。
3. mapperLocations：通配设置，只要以Mapper.xml结尾都将被注册。

将这三个功能注入SqlSessionFactoryBean之后，mybatis-config.xml也就可以丢弃了。



接着需要通过Spring配置文件的将Mapper接口的代理类注册进Spring容器中。

```java
SqlSession sqlSession = build.openSession(true);
UserMapper usermapper = sqlSession.getMapper(UserMapper.class);
```

Spring 通过 MapperScannerConfigure 封装了这两行代码！

MapperScannerConfigure 需要两个依赖：

1. SqlSessionFactory
2. Mapper接口的class对象

MapperScannerConfigure 对象上述两个依赖分配了两个属性：

1. SqlSessionFactoryBeanName：通过value属性和 SqlSessionFactoryBean 关联起来。
2. basePackage：设置Mapper接口所在的包。

设置了Mapper 接口所在的包，但是我们怎么告诉 Spring 容器我需要的是哪个Mapper接口，以及又怎么从Spring 容器中拿到这个Mapper接口的代理对象呢？

这两个问题可以用一个办法解决，在Spring容器拿对象时，只要传入的id值为 "Mapper接口的首单词首字母小写"即可。

```java
// UserMapper
context.getBean("userMapper")
```

### 4.开发

Mybatis 的七部步编程，通过依赖注入之后，我们只需要完成以下四步即可：

1. 创建实体
2. 创建表
3. 创建DAO（Mapper）
4. 实现mapper文件

#### 环境搭建

```xml
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
    <version>5.3.22</version>
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
```

#### Spring配置文件

mapper映射文件放在resources目录下。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--创建连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/test" />
        <property name="username" value="root"/>
        <property name="password" value="root" />
    </bean>

    <!--创建SqlSessionFactory 依赖于SqlSessionFactoryBean来创建-->
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--引入连接池-->
        <property name="dataSource" ref="dataSource"/>
        <!--实体别名-->
        <property name="typeAliasesPackage" value="com.dong.entity"/>
        <!--注册mapper-->
        <property name="mapperLocations">
            <list>
                <value>classpath:com.dong.mapper/*Mapper.xml</value>
            </list>
        </property>
    </bean>

    <!--创建DAO对象-->
    <bean id="scanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--引入SqlSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"/>
        <!--DAO-->
        <property name="basePackage" value="com.dong.dao"/>
    </bean>

</beans>
```

#### 编码

1. 创建实体

   在 com.dong.entity 包中创建实体 User

   ```java
   @Data
   @NoArgsConstructor
   @AllArgsConstructor
   public class User {
       private Integer id;
       private String name;
       private String password;
   }
   ```

2. 创建表

3. 创建DAO(Mapper接口)

   在 com.dong.dao 包中常见 Mapper 接口

   ```java
   public interface UserDAO {
       void save(User user);
   }
   ```

4. 实现mapper文件

   在 resources 目录下创建包 com.dong.mapper，实现 mapper 文件

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.dong.dao.UserDAO">
       <insert id="save" parameterType="User">
           insert into user (name, password) values (#{name},#{password})
       </insert>
   </mapper>
   ```

#### 测试

```java
@Test
public void test1(){
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext.xml");
    UserDAO userDao = (UserDAO) context.getBean("userDAO");
    User user = new User();
    user.setName("王麻子");
    user.setPassword("12345");
    userDao.save(user);
}
```

### 5.整合细节分析

为什么在 Spring 整合 Mubatis 中没有开启事务的代码，但是数据能插入成功呢？

事务由Connection对象控制（Connection.setAutoCommmit()），谁控制了Connection对象，谁就控制了事务。

MyBatis中Connection 对象由连接池对象（JDBC、Druid（封装了JDBC创建连接对象的功能））进行控制，创建Connection，设置了自动提交事务（Connection.setAutoCommmit(true)）

在实际开发中，需要手动提交事务（多条sql一起成功，一起失败），Spring 通过事务控制接管了事务之后，我们就可以手动提交事务了。



## 二十二、Spring事务处理

### 1.什么是事务

保证业务操作完整性的一种数据库机制。

事务的特点：

1. A 原子性
2. C 一致性
3. I 隔离性
4. D 持久性

### 2.如何控制事务

- JDBC

  开启事务：Connection.setAutoCommit(false)

  提交事务：Connection.commit()

  回滚事务：Connection.rollback()

- MyBatis

  开启事务：MyBatis自动开启事务

  提交事务：sqlSession.commit()

  回滚事务：sqlSession.rollback()

  sqlSession中封装了Connection对象。

控制事务的底层，都是Connection对象完成的。

### 3.Spring控制事务开发

事务处理是一种额外功能，所以通过AOP的方式进行开发。

#### 原始对象

- 原始对象提供的原始方法，只关注核心功能（业务处理+DAO调用）
- Service 依赖于 DAO ，所以将DAO作为Service的成员变量，通过注入的方式进行赋值。

#### 额外功能

思路：我们可以将事务的开启放在原始方法运行之前，将事务的提交放在原始方法运行之后。然后将整个代码用try代码块包裹起来，一旦出现异常，进行捕获，然后进行事务的回滚。

Spring将上述代码封装在了DataSourceTransactionManager对象中，但是对事务控制需要Connection对象，而Connection对象封装在连接池对象中，所以需要将连接池对象注入给Spring控制事务对象。

#### 切入点

Spring提供了一个注解供我们使用@Transactional

- 加在类上：类中所有方法都将会添加事务
- 加在方法上：这个方法会添加事务

#### 组装切面

通过标签<tx:annotation-driven transaction-manager="">来完成。

1. 切入点：会扫描@Transactional来得知切入点
2. 额外功能：在属性transaction-manager添加额外功能对象。

### 4.Spring控制事务的编码

#### 搭建环境

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-tx</artifactId>
    <version>5.1.4.RELEASE</version>
</dependency>
```

#### 编码

创建原始对象：

```java
// 定义切面
@Transactional
public class UserServiceImpl implements UserService{
    private UserDAO userDAO;
    public UserDAO getUserDAO() {
        return userDAO;
    }
    public void setUserDAO(UserDAO userDAO) {
        this.userDAO = userDAO;
    }
    @Override
    public void register(User user) {
        userDAO.save(user);
    }
}
```

Spring配置文件进行配置：

原始对象中的UserDAO是通过MapperScannerConfigure获取的，只要DAO接口的首单词首字母小写即可。

tx标签以 tx 结尾。

```xml
// 注册原始对象 注入UserDAO
<bean id="userService" class="com.dong.service.UserServiceImpl">
    <property name="userDAO" ref="userDAO" />
</bean>
// 额外功能 注入连接池对象
<bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>
// 组装
<tx:annotation-driven transaction-manager="dataSourceTransactionManager"/>
```

#### 细节

```xml
<tx:annotation-driven transaction-manager="dataSourceTransactionManager" proxy-target-class="false"/>
```

有proxy-target-class属性：默认false表示使用JDK代码，true表示使用CGlib代理。

足见底层就是AOP方式。

## 二十三、Spring的事务属性

### 1.什么是事务属性

属性：描述事务特征的一系列值

事务属性：描述事务特征的一系列值

1. 隔离属性
2. 传播属性
3. 只读属性
4. 超时属性
5. 异常属性

### 2.如何添加事务属性

在@Transactional注解中添加属性

1. 隔离属性：@Transactional(isolation=)
2. 传播属性：@Transactional(propagation=)
3. 只读属性：@Transactional(readOnly=)
4. 超时属性：@Transactional(timeout=)
5. 异常属性：@Transactional(rollbackFor=,noRollbackFor=)

属性之前使用逗号隔开。

### 3.事务属性详解

#### 1.隔离属性

隔离属性的概念：描述了事务解决并发问题的特征

什么是并发？

多个事务（用户）同一时间（有细微时间的前后差异），访问操作相同的数据。

并发产生的问题以及解决办法：

1. 脏读

   一个事务读取了另一个事务中更新但没有提交的数据。因为可能会事务回滚，所以造成了数据不一致的问题。

   解决：@Transactional(isolation=Isolation.READ_COMMITTED)

2. 不可重复读

   **一个事务**中多次读取相同的数据，但是读取的结果不一样。因为可能同一时间段内另一个事务更新了数据，所以造成数据不一致的问题。

   解决：@Transactional(isolation=Isolation.REPEATABLE_READ)。本质上是在表中一行读数据的过程中添加了**行锁**。别的事务只能等待。

3. 幻影读

   一个事务中多次对整表进行统计查询，但是结果不一样。因为可能同一时间段内另一个事务插入或者更新了数据，所以造成数据不一致的问题。

   解决：@Transactional(isolation=Isolation.SERIALIZABLE)。本质上是在表中统计数据的过程中添加了**表锁**。别的事务只能等待。

总结：

- 并发安全：SERIALIZABLE>REPEATABLE_READ>READ_COMMITTED
- 运行效率：READ_COMMITTED>REPEATABLE_READ>SERIALIZABLE

不是所有数据库对隔离属性的值都是支持的：

| 隔离属性的值              | MySQL | Oracle |
| ------------------------- | ----- | ------ |
| ISOLATION_READ_COMMITTED  | ✔     | ✔      |
| ISOLATION_REPEATABLE_READ | ✔     | ❌      |
| ISOLATION_SERIALIZABLE    | ✔     | ✔      |

Oracle通过多版本比对的方式，解决不同重复读的问题。

如果不设置隔离属性的值，Spring会设置默认隔离属性（ISOLATION_DEFAULT）并根据不同的数据有不同的实现：

- MySQL：REPEATABLE_READ（使用命名 select @@tx_isolation查看）
- Oracle：READ_COMMITTED

隔离属性在实战中的建议：

其实在实战中，并发情况非常罕见（需要超级用户群体），所以使用Spring默认指定的隔离属性即可。

如果真遇见了并发问题，可以使用乐观锁（在Mybatis中通过拦截器自定义开发）。

#### 2.传播属性

-  传播属性的概念

  概念：描述了事务解决嵌套问题的特征。

  事务的嵌套：一个大事务中包含若干个小事务。

  问题：大事务中融入了很多小事务，它们彼此影响，最终导致外部的大事务，丧失了事务的原子性。

- 传播属性的值及其用法

  | 传播属性的值  | 外部不存在事务 | 外部存在事务               | 用法                                                    | 备注         |
  | ------------- | -------------- | -------------------------- | ------------------------------------------------------- | ------------ |
  | REQUIRED      | 开启新的事务   | 融合到外部事务中           | @Transactional(propagation = Propagation.REQUIRED)      | 增删改方法   |
  | SUPPORTS      | 不开启新的事务 | 融合到外部事务中           | @Transactional(propagation = Propagation.SUPPORTS)      | 查找方法     |
  | REQUIRES_NEW  | 开启新的事务   | 挂起外部事物，创建新的事务 | @Transactional(propagation = Propagation.REQUIRES_NEW)  | 日志记录方法 |
  | NOT_SUPPORTED | 不开启新的事务 | 挂起外部事物               | @Transactional(propagation = Propagation.NOT_SUPPORTED) | 不常用       |
  | NEVER         | 不开启新的事务 | 抛出异常                   | @Transactional(propagation = Propagation.NEVER)         | 不常用       |
  | MANDATORY     | 抛出异常       | 融合到外部事务中           | @Transactional(propagation = Propagation.MANDATORY)     | 不常用       |

- 默认的传播属性

  REQUIRED是传播属性默认的值。

- 传播属性的使用方式

  - 在增删改方法中，可以不用指定传播属性的值。
  - 在查找方法中，需要显示指定传播属性的值为SUPPORTS

#### 3.只读属性

针对只进行查询操作的业务方法，可以加入只读属性，提高运行效率。

默认值：false

#### 4.超时属性

指定了事务等待的最长时间。

为什么事务进行等待？

当前事务访问数据时，可能访问的数据被别的事务进行了加锁处理，当前事务只能等待。

单位：秒 Transactional(timeout=2)

超时属性的默认值：-1(超时时间由不同数据库来指定)

#### 5.异常属性

Spring 处理事务过程中，默认情况下：

- 对于RuntimeException及其子类，采用的是回滚策略。
- 对于Exception以及子类，采用的是提交策略。

如果想让Spring对RuntimeException采用提交策略：@Transactional(noRollback={java.lang.RuntimeException.class，xxx})。

如果想让Spring对Exception采用回滚策略：@Transactional(Rollback={java.lang.Exception.class，xxx})。

但是在实际开发中，并不对异常属性进行重新指定策略。使用默认策略。

### 4.事务属性常见配置总结

1. 隔离属性	默认值
2. 传播属性    Required(默认值)   增删改   Supports 查询操作
3. 只读属性    readOnly  false      增删改   true         查询操作
4. 超时属性    默认值-1
5. 异常属性    默认值

总结：

- 对于增删改操作  @Transactional
- 对于查询操作     @Transactional(propagation=Propagation.SUPPORTS，readOnly=true)

### 5.基于标签的事务配置方法

// TODO





## 二十四、MVC框架整合思想

### 1.环境搭建

```html
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-web</artifactId>
  <version>5.3.22</version>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-core</artifactId>
  <version>5.3.22</version>
</dependency>
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.11</version>
  <scope>test</scope>
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
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-tx</artifactId>
  <version>5.3.22</version>
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
  <version>5.3.22</version>
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
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.3.22</version>
</dependency>
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.24</version>
</dependency>
```

### 2.为什么整合MVC框架

1. 在分层开发中，DAO由MyBatis提供，Service层由AOP接管，而整合MVC框架就是为了提供Controller层的功能。
2. 请求响应的处理。
3. 接受请求参数。
4. 控制程序运行流程。
5. 视图解析。

### 3.可以整合的MVC框架

1. struts1
2. webWork
3. jsf
4. struts2
5. springMVC（重点）

### 4.Spring整合MVC框架的核心思路

#### 准备工厂

Web开发中怎么创建工厂？

在非Web开发中我们使用一下代码获取工厂对象：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("/applicationContext.xml")
```

前面说过ApplicationContext是一个接口，在Web环境中接口的实现为：WebXmlApplicationContext。

所以在Web环境中获取工厂对象：

```java
ApplicationContext context = new WebXmlApplicationContext("/applicationContext.xml")
```

如何保证工厂的唯一以及共用？

我们知道Spring工厂是一个重量级资源，一个工程最好创建一次，所以就涉及到工厂对象的共用问题。

- 共用：我们可以把工厂对象放入Web的域对象中，在request、Session、ServletContext(application)三个域对象中，因为application代表着工程，整个工程都有效，所以我们将工厂对象放入application域中。（ServletContext.setAttribute("xxx"，context)）
- 唯一：在一个工程中，ServletContext对象只会创建一次，所以我们可以将ServletContext和Spring工厂关联起来，将spring工厂的创建放入监听ServletContext的监听器对象ServletContextListener中。只要ServletContext一创建，ServletContextListener就会调用Spring工厂的创建方法。

Spring将执行上述思路的代码封装进了ContextLoaderListener中。

ContextLoaderListener的配置：

```xml
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!--设置配置文件-->
<context-param>
	<param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
```

#### 代码整合

Controller的主要功能：

1. 接受Client请求参数
2. 调用Service
3. 业务流程跳转

Controller依赖于Service，所以将进行Service注入。



## 二十五、Spring整合Struts2



## 二十六、SSM整合



























