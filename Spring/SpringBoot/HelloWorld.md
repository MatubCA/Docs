# HelloWorld

第一个SpringBoot程序！



## 一、创建SpringBoot项目

![image-20220812123110911](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208121231098.png)

选择新建SpringBoot项目中添加的依赖

![image-20220812123152154](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208121231190.png)

## 二、创建controller

```java 
@RestController
@RequestMapping("/controller")
public class ControllerDemo {
    @GetMapping("/hello")
    public String hello(){
        return "Hello World!";
    }
}
```



## 三、运行

运行SpringBoot服务器，在浏览器中输入`localhost:8080/controller/hello`

![image-20220812124331017](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208121243048.png)



## 四、注意事项

其他类的位置不能高于主程序类：可以在主程序在包中，可以在主程序所在包的子包中



## 五、注解解析

- `@SpringBootApplication`

  ```java 
  @Target({ElementType.TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Inherited
  @SpringBootConfiguration
  @EnableAutoConfiguration
  @ComponentScan(...)
  public @interface SpringBootApplication {...}
  ```

  前面几个是元注解，主要看`@SpringBootConfiguration`、 `@EnableAutoConfiguration`和 `@ComponentScan()`这三个注解

  - `@SpringBootConfiguration`

    该注解表示一个类声明了一个或多个`@Bean`方法，并且由Spring容器进行管理，在运行时为这些`Bean`生成`Bean`定义和服务请求

  - `@EnableAutoConfiguration`

    启用SpringBootApplicationContext的自动配置，尝试猜测和配置可能需要的`Bean`，通常根据类路径和定义的`Bean`来应用自动配置类	

  - `@ComponentScan()`

    配置组件扫描指令，与`@Comfiguration`类一起使用

  表示这是一个主程序类

- `@RestController`

  ```java 
  @Target({ElementType.TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Controller
  @ResponseBody
  public @interface RestController {
      @AliasFor(
          annotation = Controller.class
      )
      String value() default "";
  }
  ```

  前面几个是元注解，主要看`@Controller`和 `@ResponseBody`这两个注解

  - `@Controller`

    代表这个类是一个控制器（controller）

  - `@ResponseBody`

    将控制器返回的方法通过适当的转换器，转换成指定的格式后，写入到response对象的body区。通常用来返回JSON和XML数据。在使用此注解后，将不再走视图解析器，而是直接将数据写入输入流。效果等同于通过response对象输出指定格式的数据

- `@RequestMapping()`

  接收用户传入的请求

- `GetMapping()`

  接收GET方法的请求



## 六、Pom解析

```xml
<!--从仓库查找父级-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.7.2</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
<!--项目描述-->
<groupId>com.dong</groupId>
<artifactId>springboot-helloworld</artifactId>
<version>0.0.1-SNAPSHOT</version>
<name>springboot-helloworld</name>
<description>springboot-helloworld</description>
<!--JDK版本-->
<properties>
    <java.version>1.8</java.version>
</properties>
<!--依赖-->
<dependencies>
    <!--Web场景启动器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!--Lombok-->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <!--test-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
<!--插件-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <excludes>
                    <exclude>
                        <groupId>org.projectlombok</groupId>
                        <artifactId>lombok</artifactId>
                    </exclude>
                </excludes>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## Banner

> 在resources目录下新建banner.txt，可以自定义SpringBoot项目运行时出场图标

生成banner的网站：

https://www.bootschool.net/ascii

http://www.network-science.de/ascii/

https://www.kammerl.de/ascii/AsciiSignature.php





## 配置文件

SpringBoot很多配置都有默认值，如果想要替换默认值，可以自己在配置文件中对默认配置进替换。

SpringBoot的配置文件有两种：properties和yaml（yml）

同一文件优先级：properties > yml > yaml

yaml基本语法

yaml数据格式

获取配置文件的值：@Value、Evironment、@ConfigurationProperties



pro



