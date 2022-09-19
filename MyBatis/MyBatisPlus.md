## 1. 概述

代码回顾mybatis用法。资料-》代码文件夹。

mybatis xml配置太过于繁琐 写基本sql

•MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

•官网： https://mp.baomidou.com/

![image-20211018190541640](E:\Pictures\Typora\image-20211018190541640.4b394fe5.png)

版本

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus</artifactId>
    <version>3.4.0</version>
</dependency>
```

## [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_2-快速入门)2. 快速入门

需求：SpringBoot 整合 MyBatis-Plus，并实现根据Id查询功能。

```text
①数据库环境准备
②创建SpringBoot工程，引入MyBatis-Plus起步依赖
③编写DataSource相关配置
④编写mapper
⑤测试
```

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_2-1-数据库环境准备)2.1 数据库环境准备

```text
详见附件
```

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_2-2-创建springboot工程-引入mybatis-plus起步依赖)2.2 创建SpringBoot工程，引入MyBatis-Plus起步依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>mybatis-plus-test</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>mybatis-plus-test</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- mysql  驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.26</version>
        </dependency>
        <!--mybatisplus起步依赖-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.0</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

**注意**：mysql驱动包和本地mysql版本对应。

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_2-3-编写datasource相关配置)2.3 编写DataSource相关配置

application.yml

```yaml
# datasource
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mp?characterEncoding=utf-8&serverTimezone=Asia/Shanghai
    username: root
    password: root
    driver-class-name: com.mysql.jdbc.Driver
```

注意：链接的数据库是8 时区必须加上。驱动com.mysql.cj.jdbc.Driver

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_2-4-编码)2.4 编码

实体类

```java
@TableName("tb_user") // 指定表名
@Data
public class User {
    private Long id;
    private String userName;
    private String password;
    private String name;
    private Integer age;
    private String email;
}
```

编写mapper

```java
/**
 * 使用mp定义Mapper，需要让Mapper接口继承 BaseMapper接口。
 */

public interface UserMapper extends BaseMapper<User> {
}
```

启动类增加 @MapperScan 注解

```java
package com.example.mybatisplustest;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ComponentScan;

@SpringBootApplication
@MapperScan("com.ydl.mp.mapper")
public class MybatisPlusTestApplication {

    public static void main(String[] args) {
        SpringApplication.run(MybatisPlusTestApplication.class, args);
    }

}
```

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_2-5-测试)2.5 测试

测试代码中创建测试类 com.ydl.mp.UserMapperTest

```java
package com.example.mybatisplustest.mapper;

import com.example.mybatisplustest.MybatisPlusTestApplication;
import com.example.mybatisplustest.pojo.User;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@SpringBootTest(classes = MybatisPlusTestApplication.class)
@RunWith(SpringRunner.class)
public class UserMapperTest {
    @Autowired
    private UserMapper userMapper;
    /**
     * 根据id查询
     */
    @Test
    public void testSelectById() {

        User user = userMapper.selectById(1L);
        System.out.println(user);

    }
}
```

## [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-crud)3. CRUD

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-1-添加)3.1 添加

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-1-1-方法解析)3.1.1 方法解析

![image-20201008114929998](data:image/png;base64,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)

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-1-2-测试)3.1.2 测试

```java
@Test
    public void testInsert() {
        User user = new User();
        //user.setId(6L);
        user.setUserName("itlils");
        user.setPassword("ydl");
        int count = userMapper.insert(user);
        System.out.println(count);
    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-1-3-说明1-实体类上的注解-tablefield)3.1.3 说明1, 实体类上的注解 @TableField

```properties
1)  @TableField("user_name") 指定映射关系
实体类的属性名和数据库的字段名自动映射：
	 *  名称一样
	 *  数据库字段使用_分割，实体类属性名使用驼峰名称
   否则需要使用 @TableField("user_name") 指定映射关系
2)  忽略某个字段的查询和 插入
	  @TableField(exist = false) 
3)  设置id生成策略:AUTO 数据库自增
    @TableId(type = IdType.AUTO)
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-1-4-说明2-配置)3.1.4 说明2, 配置

```yaml
mybatis-plus:
  global-config:
    db-config:
      # 表名前缀
      table-prefix: tb_
      # id生成策略 数据库自增
      id-type: auto
  configuration:
    # 日志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

```properties
##mybatis-plus
mybatis-plus.global-config.db-config.table-prefix=tb_
mybatis-plus.global-config.db-config.id-type=auto
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-1-5-具体使用如下)3.1.5 具体使用如下

```java
/**
 * 实体类的属性名和数据库的字段名自动映射：
 *  1. 名称一样
 *  2. 数据库字段使用_分割，实体类属性名使用驼峰名称
 */

@TableName("tb_user")
@Data
public class User {


    //设置id生成策略:AUTO 数据库自增
    @TableId(type = IdType.AUTO)
    private Long id;
    //@TableField("user_name")
    private String userName;

    private String password;
    private String name;
    private Integer age;
    private String email;

    //不希望该值存入数据库
   // @TableField(exist = false)
   // private String info;

}
```

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-2-删除)3.2 删除

![image-20201008114022226](E:\Pictures\Typora\image-20201008114022226.9df9b5ab.png)

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-2-1-根据id删除)3.2.1 根据id删除

```java
 int count = userMapper.deleteById(8L);
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-2-1-根据id集合批量删除)3.2.1 根据id集合批量删除

```java
 List ids = new ArrayList();
        ids.add(6);
        ids.add(7);

userMapper.deleteBatchIds(ids);
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-2-1-根据map构造条件-删除)3.2.1 根据map构造条件，删除

```java
 Map<String, Object> map = new HashMap<>();

        //delete from tb_user where user_name = ? and age = ?
        map.put("user_name","itlils");
        map.put("age","18");

userMapper.deleteByMap(map);
```

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_3-3-更新)3.3 更新

![image-20201008114912504](E:\Pictures\Typora\image-20201008114912504.f2350f7c.png)

```java
@Test
    public void testUpdateById() {
        User user = new User();
        user.setId(2L);
        user.setPassword("1111111");
        int count = userMapper.updateById(user);
    }
```

## [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-查询)4 查询

![image-20201008115031886](E:\Pictures\Typora\image-20201008115031886.38b5a1a8.png)

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-1-分页查询)4.1 分页查询

配置 拦截器

```java
@Configuration
public class PageConfig {

    /**
     * 3.4.0之前的版本用这个
     * @return
     */
   /* @Bean
    public PaginationInterceptor paginationInterceptor(){
        return  new PaginationInterceptor();
    }*/

    /**
     * 3.4.0之后提供的拦截器的配置方式
     * @return
     */
   @Bean
   public MybatisPlusInterceptor mybatisPlusInterceptor(){
       MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
       mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
       return mybatisPlusInterceptor;
   }
}
```

查询

```java
/**
     * 分页查询：
     *  1. 当前页码：currentPage
     *  2. 每页显示条数：size
     *
     *  注意：使用mp的分页要设置一个拦截器！！！
            */
    @Test
    public void testSelectPage() {
        int current = 1;//当前页码
        int size = 5;//每页显示条数
        IPage<User> page = new Page(current,size);
        userMapper.selectPage(page,null);

        List<User> records = page.getRecords();//当前页的数据
        long pages = page.getPages();//总页数 2
        long total = page.getTotal();//总记录数 4
        System.out.println(records);
        System.out.println(pages);
        System.out.println(total);
    }
```

### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-条件构造器查询)4.2 条件构造器查询

```java
/**
 * 基础比较查询
 *
 * Wrapper:
 *  1.QueryWrapper   查询
 *      LambdaQueryWrapper
 *  2.UpdateWrapper  更新
 *      LambdaUpdateWrapper
 *
 */
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-1-基础查询)4.2.1 基础查询

通过 QueryWrapper 指定查询条件

sql：select * from tb_user where password='123456' and age>18

```text
eq( ) :  等于 =
ne( ) :  不等于 <>
gt( ) :  大于 >
ge( ) :  大于等于  >=
lt( ) :  小于 <
le( ) :  小于等于 <=
between ( ) :  BETWEEN 值1 AND 值2 
notBetween ( ) :  NOT BETWEEN 值1 AND 值2 
in( ) :  in
notIn( ) ：not in
```

```java
@Test
    public void testWrapper1(){
        //1.创建查询条件构建器
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        //2.设置条件
        wrapper.eq("user_name","lisi")
                .lt("age",23)
                .in("name","李四","王五");
        //select * from tb_user where user_name = ? and age < ? and name in (?,?)
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-2-逻辑查询-or)4.2.2 逻辑查询 or

```text
or( ) ：让紧接着下一个方法用or连接 
```

```java
@Test
    public void testWrapper2(){
        //1.创建查询条件构建器
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        //2.设置条件
        wrapper.eq("user_name","lisi")
                .or()
                .lt("age",23)
                .in("name","李四","王五");
        /*
            select * from tb_user where user_name = ? or age < ? and name in (?,?)
         */
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-3-模糊查询-like)4.2.3 模糊查询 like

```text
like
notLike
likeLeft
likeRight
```

```java
/**
     * 模糊查询
     */
    @Test
    public void testWrapper3(){
        //1.创建查询条件构建器
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        //2.设置条件
        wrapper.likeLeft("user_name","zhang");
        /*
            SELECT id,user_name,password,name,age,email
             from tb_user
             where user_name like ?
             %zhang
         */
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-4-排序查询)4.2.4 排序查询

```text
orderBy
orderByAsc
orderByDesc
```

```java
@Test
    public void testWrapper4(){
        //1.创建查询条件构建器
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        //2.设置条件
        wrapper.eq("user_name","lisi")
                .or()
                .lt("age",23)
            	.or()
                .in("name","李四","王五")
                //.orderBy(true,true,"age")
                .orderByDesc("age");
        /*
            select * from tb_user where user_name = ? or age < ? and name in (?,?) order by age asc
         */
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-5-select-指定需要查询的字段)4.2.5 select：指定需要查询的字段

```java
@Test
    public void testWrapper5(){
        //1.创建查询条件构建器
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        //2.设置条件
        wrapper.eq("user_name","lisi")
                .or()
                .lt("age",23)
            	.or()
                .in("name","李四","王五")
                //.orderBy(true,true,"age")
                .orderByDesc("age")
                .select("id","user_name");
        /*
            select id,user_name from tb_user where user_name = ? or age < ? and name in (?,?) order by age asc
         */
        List<User> users = userMapper.selectList(wrapper);
        System.out.println(users);
    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-6-分页条件查询)4.2.6 分页条件查询

```java
@Test
    public void testWrapper6(){

        int current = 1;//当前页码
        int size = 2;//每页显示条数
        //1. 构建分页对象
        Page<User> page = new Page<>(current,size);
        //2. 构建条件对象
        QueryWrapper<User> wrapper = new QueryWrapper();
        wrapper.lt("age",23);
        userMapper.selectPage(page,wrapper);
        List<User> records = page.getRecords();
        long total = page.getTotal();
        long pages = page.getPages();
        System.out.println(records);
        System.out.println(total);//2
        System.out.println(pages);//1

    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-7-lambdaquerywrapper-消除代码中的硬编码)4.2.7 LambdaQueryWrapper：消除代码中的硬编码

```java
@Test
    public void testWrapper7(){
        LambdaQueryWrapper<User> wrapper = new LambdaQueryWrapper<>();
        wrapper.eq(User::getUserName,"zhangsan");
        userMapper.selectOne(wrapper);
    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-8-条件-删除)4.2.8 条件 删除

![image-20201008121325431](E:\Pictures\Typora\image-20201008121325431.ac0e0543.png)

```java
 @Test
    public void testWrapper8(){
        QueryWrapper<User> wrapper = new QueryWrapper<>();
        wrapper.eq("user_name","bbb");
        userMapper.delete(wrapper);
    }
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_4-2-9-条件-update)4.2.9 条件 update

![image-20201008121332555](E:\Pictures\Typora\image-20201008114912504.f2350f7c.png)

```java
@Test
    public void testWrapper9(){
        UpdateWrapper<User> wrapper = new UpdateWrapper<>();
        //条件
        wrapper.eq("user_name","lisi")
                .set("password","22222");
        //update tb_user set password = ? where user_name = ?
        userMapper.update(null,wrapper);
    }
    @Test
    public void testWrapper10(){
        UpdateWrapper<User> wrapper = new UpdateWrapper<>();
    //条件
        wrapper.eq("user_name","lisi");
    //update tb_user set password = ?,age = ? where user_name = ?
        User user = new User();
        user.setPassword("3333");
        user.setAge(33);
        userMapper.update(user,wrapper);
}
```

## [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_5-service-封装)5 service 封装

MVC架构

前端----》controller---->service（impl）------->dao(mapper)

Mybatis-Plus 为了开发更加快捷，对业务层也进行了封装，直接提供了相关的接口和实现类。我们在进行业务层开发时，可以继承它提供的接口和实现类，使得编码更加高效

```text
- 1. 定义接口继承IService
- 2. 定义实现类继承ServiceImpl<Mapper，Entity> 实现定义的接口
```

接口

```java
public interface _UserService extends IService<User> {
}
```

实现类封装

```java
@Service
public class _UserServiceImpl extends ServiceImpl<UserMapper, User> implements _UserService {}
```

```java
package com.example.mybatisplustest.service;

import com.baomidou.mybatisplus.core.conditions.update.UpdateWrapper;
import com.example.mybatisplustest.MybatisPlusTestApplication;
import com.example.mybatisplustest.pojo.User;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@SpringBootTest(classes = MybatisPlusTestApplication.class)
@RunWith(SpringRunner.class)
public class UserServiceTest {

    @Autowired
    private UserService userService;

    @Test
    public void testFind(){
        UpdateWrapper<User> wrapper = new UpdateWrapper<>();
        wrapper.eq("id",1);
        User one = userService.getOne(wrapper);
        System.out.println(one);
    }
}
```

## [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_6-逆向工程-代码生成器)6. 逆向工程-代码生成器

AutoGenerator 是 MyBatis-Plus 的代码生成器，通过 AutoGenerator 可以快速生成 Entity、Mapper、Mapper XML、Service、Controller 等各个模块的代码，极大的提升了开发效率。

6.1 导入坐标

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>mybatis-plus-test</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>mybatis-plus-test</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- mysql  驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.26</version>
        </dependency>
        <!--mybatisplus起步依赖-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.4.0</version>
        </dependency>
        <!--mp 代码生成器-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.4.0</version>
        </dependency>

        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.30</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

#### [#](https://www.ydlclass.com/doc21xnv/frame/mybatisplus/#_6-2-执行main-方法)6.2 执行main 方法

```java
// 演示例子，执行 main 方法控制台输入模块表名回车自动生成对应项目目录中
package com.example.mybatisplustest;

import com.baomidou.mybatisplus.core.exceptions.MybatisPlusException;
import com.baomidou.mybatisplus.core.toolkit.StringPool;
import com.baomidou.mybatisplus.core.toolkit.StringUtils;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.InjectionConfig;
import com.baomidou.mybatisplus.generator.config.*;
import com.baomidou.mybatisplus.generator.config.po.TableInfo;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class CodeGenerator {
    /**
     * <p>
     * 读取控制台内容
     * </p>
     */
    public static String scanner(String tip) {
        Scanner scanner = new Scanner(System.in);
        StringBuilder help = new StringBuilder();
        help.append("请输入" + tip + "：");
        System.out.println(help.toString());
        if (scanner.hasNext()) {
            String ipt = scanner.next();
            if (StringUtils.isNotBlank(ipt)) {
                return ipt;
            }
        }
        throw new MybatisPlusException("请输入正确的" + tip + "！");
    }

    public static void main(String[] args) {
        // 代码生成器
        AutoGenerator mpg = new AutoGenerator();

        // 全局配置
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        System.out.println(projectPath);
        gc.setOutputDir(projectPath + "/src/main/java");
        gc.setAuthor("ydl");
        gc.setOpen(false);
        // gc.setSwagger2(true); 实体属性 Swagger2 注解
        mpg.setGlobalConfig(gc);

        // 数据源配置
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/mp");
        // dsc.setSchemaName("public");
        dsc.setDriverName("com.mysql.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("root");
        mpg.setDataSource(dsc);

        // 包配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName(scanner("模块名"));
        pc.setParent("com.ydl");
        mpg.setPackageInfo(pc);

        // 自定义配置
        InjectionConfig cfg = new InjectionConfig() {
            @Override
            public void initMap() {
                // to do nothing
            }
        };

        // 如果模板引擎是 freemarker
        String templatePath = "/templates/mapper.xml.ftl";
        // 如果模板引擎是 velocity
        // String templatePath = "/templates/mapper.xml.vm";

        // 自定义输出配置
        List<FileOutConfig> focList = new ArrayList<>();
        // 自定义配置会被优先输出
        focList.add(new FileOutConfig(templatePath) {
            @Override
            public String outputFile(TableInfo tableInfo) {
                // 自定义输出文件名 ， 如果你 Entity 设置了前后缀、此处注意 xml 的名称会跟着发生变化！！
                return projectPath + "/src/main/resources/mapper/" + pc.getModuleName()
                        + "/" + tableInfo.getEntityName() + "Mapper" + StringPool.DOT_XML;
            }
        });
        /*
        cfg.setFileCreate(new IFileCreate() {
            @Override
            public boolean isCreate(ConfigBuilder configBuilder, FileType fileType, String filePath) {
                // 判断自定义文件夹是否需要创建
                checkDir("调用默认方法创建的目录，自定义目录用");
                if (fileType == FileType.MAPPER) {
                    // 已经生成 mapper 文件判断存在，不想重新生成返回 false
                    return !new File(filePath).exists();
                }
                // 允许生成模板文件
                return true;
            }
        });
        */
        cfg.setFileOutConfigList(focList);
        mpg.setCfg(cfg);

        // 配置模板
        TemplateConfig templateConfig = new TemplateConfig();

        // 配置自定义输出模板
        //指定自定义模板路径，注意不要带上.ftl/.vm, 会根据使用的模板引擎自动识别
        // templateConfig.setEntity("templates/entity2.java");
        // templateConfig.setService();
        // templateConfig.setController();

        templateConfig.setXml(null);
        mpg.setTemplate(templateConfig);

        // 策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        //strategy.setSuperEntityClass("你自己的父类实体,没有就不用设置!");
        strategy.setEntityLombokModel(true);
        strategy.setRestControllerStyle(true);
        // 公共父类
        //strategy.setSuperControllerClass("你自己的父类控制器,没有就不用设置!");
        // 写于父类中的公共字段
        strategy.setSuperEntityColumns("id");
        strategy.setInclude(scanner("表名，多个英文逗号分割").split(","));
        strategy.setControllerMappingHyphenStyle(true);
        strategy.setTablePrefix(pc.getModuleName() + "_");
        mpg.setStrategy(strategy);
        mpg.setTemplateEngine(new FreemarkerTemplateEngine());
        mpg.execute();
    }
}
```