### MyBatis-plus简介

MyBatis-plus是一个Mybatis的增强工具，在Mybatis的基础上只做增强不做改变，为简化开发、提高效率而生

![image-20220928235321410](E:\Pictures\Typora\image-20220928235321410.png)

### Mybatis-plus的特性

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 SQL 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

### 回顾SpringBoot整合MyBatis开发过程

1. 创建SpringBoot工程
2. 勾选配置使用的技术（MybatisFramework、MySQLDriver）
3. 设置datasource相关属性
4. 定义数据层接口映射配置
5. 编写sql语句（选择使用注解或者使用映射文件）

### SpringBoot整合MyBatis-plus

1. 创建SpringBoot工程
2. 勾选配置使用的技术（MySQLDriver）
3. 添加mybatis-plus的starter（依赖于mybatis，所以不用导入mybatis依赖）
4. 设置datasource相关配置（继承BaseMapper，书写泛型）
5. 定义数据层接口映射配置

### API

mybatis-plus封装了常用CURD操作，对于简易的CURD可以直接调用mybatis-plus为我们提供的方法即可

#### mapper接口

![image-20220929011135206](E:\Pictures\Typora\image-20220929011135206.png)

常用的CRUD全部包含了

需要注意的是，mybatisPlus支持主键自动生成功能，是根据雪花算法计算出的long型的id值，所以需要将实体类的id值设置为Long型，数据库表中的id字段设置为bigint类型

对于修改方法，只修改你更新了的字段，对于没有指定的字段默认不修改，免去了使用动态sql的麻烦

mapper接口的接口中的方法相当于是一个总入口，对于分页查询还是条件查询都需要来这个入口进行执行

#### Ipage接口

Ipage用于分页查询，将分页条件传入Ipage的实现类Page中，再将page传入mapper接口实现类的selectPage方法即可完成分页查询

Page的构造函数需要传入两个形参，第一个是查询页码，第二个是这一页查询的个数

Page定义了多种方法，用于获取查询出来的数据

![image-20220929014446947](E:\Pictures\Typora\image-20220929014446947.png)

使用Ipage的前提是设置分页拦截，本质上分页查询就是在普通查询语句末尾追加limit语句，而分页拦截的意义便在于此，前面设置的Page相当于limit，我们需要使用分页拦截，才能使分页功能完整

```java
@Bean
public MybatisPlusInterceptor getMybatisPlusInterceptor() {
    //1.定义拦截器
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    //2.添加具体的拦截器（分页）
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor());
    return interceptor;
}
```

#### Wrapper

Wrapper用于封装条件查询中的那个条件

![image-20220929015748529](E:\Pictures\Typora\image-20220929015748529.png)

可以看到有很多条件封装类，我们使用条件查询使用QueryWrapper这个类

在AbstractWrapper里面定义了非常多的条件方法

![image-20220929021247597](E:\Pictures\Typora\image-20220929021247597.png)

这些方法分为范围查询（lt，gt，le，ge，between），分组查询（group），模糊查询（Like）

例如：

```java
QueryWrapper queryWrapper = new QueryWrapper;
// 表示条件为：age小于18
queryWrapper.lt("age",18);
```

可以使用lambda的形式，进行多个条件的连用

```java
LambdaQueryWrapper<User> lambdaQueryWrapper = new LambdaQueryWrapper();
// 表示条件：age小于30，大于20
lambdaQueryWrapper.lt(User::age,30).gt(User::age,20);
```

上面情况，相当于两个条件之间使用and连接，当然我们也可以使用.or()方法，将两个条件进行或运算

对于条件查询的对空判断，在Mybatis中，我们需要使用动态sql，使用if标签进行判断，但是在MybatisPlus中，我们只需要在条件方法中传入第一个参数，判断当前条件值是否为空

在前端进行条件查询时，对于某些值，可能要设置上下界值，当然用户也可能并不会设置完整的上下界值，所以我们需要设置上下界的非空判断

在Mybatis中，条件查询的非空判断我们需要借助动态sql，使用if标签进行判断，但在MybatisPlus中，我们只需要在条件方法中，添加一个boolean类型的参数，表示对当前值的判断

```java
lambdaQueryWrapper.lt(null！=user.getAge1(),User::age,30).gt(null! =user.getAge2(),User::age,20);
```

Wrapper的实现类QueryWrapper和LambdaQueryWrapper还可以设置sql语句的字段列表，表示需要查询的字段，通过select()方法，将需要查询的字段列表传入该方法中

还可以分组查询，groupBy()方法、模糊查询like()，



### 注解

@TableField()

- 位置：实体类的成员属性之上
- 作用：处理实体类属性名与表的字段名不一致的情况
- 属性：
  - value：表示该实体类成员变量对应的表中的字段名
  - exist：表示是否将该字段添加进数据库，false表示不添加，实体类中有，数据库没有，比如设置用户是否登录的变量
  - select：设置为false表示该变量对应的数据库字段不参与查询，比如密码，避免产生安全隐患

@TableName()

- 位置：实体类之上
- 作用：处理实体类名与表名不一致的问题
- 属性：
  - value：设置与该实体类对应的表名

@TableId()

- 位置：作用于id属性
- 作用：设置id的自动生成策略
- 属性：
  - type：设置id生成策略
  - 属性值：
    - AUTO(0)：使用数据库的id自增生成策略
    - NONE(1)：不设置id生成策略
    - INPUT(2)：手动输入id
    - ASSIGN(3)：雪花算法生成id（兼容数值型和字符串型）
    - ASSIGN_UUID(4)：UUID算法生成id

@TableLogic

- 位置：作用于表示逻辑删除的属性
- 作用：物理删除可能导致与该记录关联的数据变成脏数据，而且在一些统计操作中，仍然需要该记录关联的数据，在这种情况下我们使用逻辑删除，只删除该记录（并没有真正物理删除记录，只是设置逻辑删除变量为已删除），不删除该记录关联的数据
- 属性：
  - value：表示记录存在的表示
  - delval：表示已删除的表示
- 前提：在表中设置表示逻辑删除的字段

@Version

- 位置：作用于表示乐观锁的实体类属性
- 作用：在数据库中放入一个表示乐观锁的字段，每次对记录的操作，都将导致version+1，避免随后的其他用户对其再次更改（更改的前提是version要相同），如果verison不同，将不进行修改
- 前提：我们的思想是，在第一个用户修改记录之后，需要将version+1，这就要用到拦截器，所以需要在配置类中设置乐观锁拦截器

对于表名、字段名、id生成策略，逻辑删除都可以在配置文件中进行全局的配置



