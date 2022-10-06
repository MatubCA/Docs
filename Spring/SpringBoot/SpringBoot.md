



SpringBoot根据“约定由于配置”的思想，对很对的配置都会设置默认值，我们不必去显示的创建配置文件来进行配置，但当我们想进行更改配置或者添加配置的时候，我们可以很轻松的进行添加或者修改。

![image-20220923094427710](E:\Pictures\Typora\image-20220923094427710.png)

创建SpringBoot的项目可以通过在idea和官网创建的两种方式

SpringBoot打包方式是jar，可以直接在命令行进行运行。

1. package
2. java -jar xxx.jar

依赖于spring-boot-maven-plugin插件，会将所有需要的依赖打入jar包

parent管理依赖版本

starter场景启动器，导入对应场景的所有常用的依赖

SpringBoot官方提供的starter命名spring-boot-starter-xxx

自定义的starter命名为xxx-spring-boot-starter

更换依赖：将在starter中的依赖排除掉，在pom文件中新建新的依赖即可

配置文件：

- properties
- yaml
- yml

同一目录优先级：properties > yml  > yaml

yaml语法：

- 大小写敏感
- value之前要有一个空格

读取yaml文件数据：

1. @Value

2. 注入Environment（封装配置信息），根据该对象的getProperty命令获取

3. 注入自定义的封装类，根据该对象的getProperty命令获取

   ```java
   @Componrnt
   @ConfigurationProperties(perfix= "user" )
   public class User{
       
   }
   ```

设置多环境配置文件：

- 可以创建多个配置文件
- 可以在同一个yml中通过`"---"`将多个环境隔离开来，以spring.profiles属性设置不同的环境名：pro生产环境；dev开发环境；test测试环境
- 在配置文件中使用spring.profiles.active属性设置当前使用的环境
- 对于properties配置文件，设置多个环境需要创建多个配置文件：application-xxx.properties
- 然后在主配置文件中使用spring.profiles.active选择开启的环境

我们在通过命令行的方式启动项目的时候，可以通过在jar后面添加参数去临时修改比如：环境的选择、端口等等的值

命令：java -jar xxx.jar --spring.profiles.active=xxx



打包项目的时候，需要注意的几个点：

- 打包之前clean
- 配置项目的字符集编码



maven多环境和SpringBoot多环境的关联：

- maven多环境配置

  ![image-20220923120825209](E:\Pictures\Typora\image-20220923120825209.png)

- SprungBoot多环境配置

  ![image-20220923120836663](E:\Pictures\Typora\image-20220923120836663.png)

- maven与SpringBoot多环境的整合

  ![image-20220923120851779](E:\Pictures\Typora\image-20220923120851779.png)

  

多级配置文件

1. file：config/application.yaml
2. file：application.yaml
3. classpath：config/application.yaml
4. classpath：application.yaml

从上到下优先级越低

1，2级设置打包阶段的通用属性，3，4级设置开发阶段的通用属性







整合第三方框架

整合mybatis，创建DAO接口，设置@mapper注解，用于Spring容器进行扫描创建代理对象，在配置文件中添加配置信息了，SpringBoot2.4.3之前内置集成的是mysql8以前的版本，需要设置时区





SpringBoot自动装配原理：

在我们使用Spring时，我们需要手动将我们需要使用的对象，创建处理，并交由Spring容器进行管理，但是在SpringBoot中，我们可以不用去手动创建Bean，而是由SpringBoot进行自动装配，按需加载！

这是因为SpringBoot提供了一套SPI，交由不同的依赖去实现，SpringBoot会自动扫描外部引用jar包的META-INF/Spring-factories文件，



