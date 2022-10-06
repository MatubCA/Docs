# MyBatis

## 一、搭建MyBatis

### 1.环境搭建

```xml
<!--数据库驱动-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.29</version>
</dependency>
<!--MyBatis-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>
<!--单元测试-->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
```

### 2.创建核心配置文件

MyBatis 核心配置文件 `mybatis-config.xml`，命名没有要求，放置位置在 `resources` 目录下。用于`连接数据库`以及`全局配置`。

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE configuration  
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-config.dtd">  
<configuration>  
	<!--设置连接数据库的环境-->  
	<environments default="development">  
		<environment id="development">  
			<transactionManager type="JDBC"/>  
			<dataSource type="POOLED">  
				<property name="driver" value="com.mysql.cj.jdbc.Driver"/>  
				<property name="url" value="jdbc:mysql://localhost:3306/test"/>  
				<property name="username" value="root"/>  
				<property name="password" value="root"/>  
			</dataSource>  
		</environment>  
	</environments>  
	<!--引入映射文件-->  
	<mappers>  
		<mapper resource="mappers/UserMapper.xml"/>  
	</mappers>  
</configuration>
```

### 3.创建Mapper接口

Mapper 接口一般放在 `mapper` 包中，命名格式为：实体类名 + Mapper。

接口里定义操作数据库的方法, 相当于以前的 DAO 层, 只是我们不再创建实现类。



```java
public interface UserMapper {

    int insertUser();
	...
}
```

### 4.创建映射文件

映射文件一般放在 `resources` 目录下的 `mappers` 包里, 命名格式为：实体类名 + Mapper.xml。

映射文件中编写 SQL 语句, **一个映射文件对应一张表**, 也**对应着一个实体类**。

**Mapper 接口的全类名要和映射文件的命名空间一致**。

**Mapper 接口中的方法名和映射文件中的 SQL 标签的 id 属性保持一致**。

```xml
<?xml version="1.0" encoding="UTF-8" ?>  
<!DOCTYPE mapper  
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace 是 Mapper 接口的全类名-->
<mapper namespace="com.dong.mapper.UserMapper"> 
    <!--id 属性名是 Mapper 接口中定义的方法名-->
	<!--int insertUser();-->  
	<insert id="insertUser">  
		 insert into user values (null,张三,12345) 
	</insert>  
</mapper>
```

### 5.调用MyBatisAPI

`SqlSessionFactory.openSession(true);`:表示自动提交事务。如果不设置自动提交事务，需要在方法调用结束后，手动提交事务 `sqlSession.commit()`。

```java
@Test
public void test6() throws IOException{
    // 获取核心配置文件的输入流
    InputStream input = Resources.getResourceAsStream("mybatis-config.xml");
    // 创建 SqlSessionFactoryBuilder 对象,调用 build 方法,获取 SqlSessionFactory 对象
    SqlSessionFactory build = new SqlSessionFactoryBuilder().build(input);
    // 获取 SqlSession
    SqlSession sqlSession = build.openSession(true);
    // 通过代理模式创建 UserMapper 的代理类对象
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    // 调用Mapper接口方法
    mapper.insertUser();
    // sqlSession.commit()
}
```

### 6.添加log4j

1. 添加依赖

   ```xml
   <!--log4j-->
   <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.12</version>
   </dependency>
   ```

2. 在 resources 目录下创建 log4j.xml 配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
   <log4j:configuration>
       <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
           <param name="Encoding" value="UTF-8" />
           <layout class="org.apache.log4j.PatternLayout">
               <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS} %m (%F:%L) \n" />
           </layout>
       </appender>
       <logger name="java.sql">
           <level value="debug" />
       </logger>
       <logger name="org.apache.ibatis">
           <level value="info" />
       </logger>
       <root>
           <level value="debug" />
           <appender-ref ref="STDOUT" />
       </root>
   </log4j:configuration>
   ```

### 7.图解

![image-20220829102522384](E:\Pictures\Typora\image-20220829102522384.png)

## 二、配置文件详解

配置文件的一些标签可以不写, 但顺序一定不能乱：

properties-->settings-->typeAliases-->typeHandlers-->objectFactor-->objectWrapperFactor-->reflectorFactory-->plugin-->environment-->databaseIdProvider0-->mappers。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--标签顺序不能乱-->
<configuration>
    <!--引入properties文件，此时就可以${属性名}的方式访问属性值-->
    <!--<properties resource="jdbc.properties"></properties>-->

    <settings>
        <!--将表中字段的下划线自动转换为驼峰-->
        <!--<setting name="mapUnderscoreToCamelCase" value="true"/>-->
        <!--开启延迟加载-->
        <!--<setting name="lazyLoadingEnabled" value="true"/>-->
    </settings>

    <typeAliases>
        <!--
        typeAlias：设置某个具体的类型的别名
        属性:
        type：需要设置别名的类型的全类名
        alias：设置此类型的别名，且别名不区分大小写。若不设置此属性，该类型拥有默认的别名，即类名
        -->

        <!--<typeAlias type="com.atguigu.mybatis.bean.User"></typeAlias>-->
        <!--<typeAlias type="com.atguigu.mybatis.bean.User" alias="user"></typeAlias>-->
        <!--以包为单位，设置改包下所有的类型都拥有默认的别名，即类名且不区分大小写-->
        <!--<package name="com.atguigu.mybatis.bean"/>-->
    </typeAliases>


    <environments default="development">
        <!--数据库连接基本信息-->
        <!--
        environments：设置多个连接数据库的环境
        属性:
        default：设置默认使用的环境的id
        -->
        <environment id="development">
        <!--
         environment：设置具体的连接数据库的环境信息
         属性:
         id：设置环境的唯一标识，可通过environments标签中的default设置某一个环境的id，表示默认使用的环境
        -->
            <transactionManager type="JDBC"/>
            <!--
            transactionManager：设置事务管理方式
            属性:
            type：设置事务管理方式，type="JDBC|MANAGED"
            type="JDBC"：设置当前环境的事务管理都必须手动处理
            type="MANAGED"：设置事务被管理，例如spring中的AOP
            -->
            <dataSource type="POOLED">
            <!--
             dataSource：设置数据源
             属性:
             type：设置数据源的类型，type="POOLED|UNPOOLED|JNDI"
             type="POOLED"：使用数据库连接池，即会将创建的连接进行缓存，下次使用可以从缓存中直接获取，不需要重新创建
             type="UNPOOLED"：不使用数据库连接池，即每次使用连接都需要重新创建
             type="JNDI"：调用上下文中的数据源
            -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <!--映射文件-->
    <mappers>
        <mapper resource="mappers/UserMapper.xml"/>
        <!--以包为单位添加映射文件,映射文件和mapper接口必须在同一包下(resources和java合并后的同一包)-->
        <!--<package name="com.dong.mapper"/>-->
    </mappers>
</configuration>
```

## 三、MyBatis的增删改查

### 1.添加

parameterType属性表示参数类型，DAO接口中传入方法的参数类型。在增删查改中都可以添加该属性。和@Param类似，根据参数类型进行匹配。

```xml
<!--int insertUser() id(自增)、name、password-->
<insert id="insertUser" parameterType="user">
	insert into user values (null,'大白','12345')
</insert>
```

### 2.删除

```xml
<!-- int deleteUser()-->
<delete id="deleteUser">
	delete from user where id=1
</delete>
```

### 3.查询实体

查询语句需要指明结果映射（resultType），意思是查询出的结果映射为什么结构。可以在核心配置文件中的`<typeAliases>`标签中设置别名。

```xml
<!--User selectUser()-->
<select id="selectUser" resultType="com.dong.entity.User">
	select * from user where id=10
</select>
```

### 4.查询集合

```xml
<!--List<User> selectUsers()-->
<select id="selectUsers" resultType="com.dong.entity.User">
	select * from user
</select>
```

### 5.修改

```xml
<!--int updataUser()-->
<updata id="updataUser">
	updata user set name='张三' where id=5
</updata>
```

### 6.细节

- select 标签必须设置属性 `resultType` 或者 `resultMap`，用于设置实体类和数据库表的映射关系。
  - `resultType`: 自动映射，用于属性名与表中字段一致的情况。
  - `resultMap`: 自定义映射，用于一对多或多对一或属性名与表中字段不一致的情况。
- **当查询的数据为多条的时候，不能使用实体类作为返回值**，只能使用集合，否则抛出异常 TooManyResultsException。

## 四、MyBatis获取参数的两种方式

获取参数表示在调用 Mapper 接口中的方法时，传入的参数。

### 1.#{}

- 是 sql 的参数占位符。
- MyBatis 在处理 `#{}` 的时候,会把 #{} 替换为 `?` ,调用 `PerparedStatement` 的 `setXxx()` 方法`按序`来赋值。
- 使用 #{} 可以有效防止 SQL注入。
- 自动添加单引号。

### 2.${}

- 是 properties 文件中的变量占位符。
- 它可以用于标签属性值和 sql 内部，属于静态文本替换。
- ${driver}替换成 `com.mysql.cj.jdbc.Driver`。
- 若为字符串或日期类型的字段进行赋值时,需手动添加单引号。

## 五、参数类型

### 1.单个字面量参数

若 mapper 接口中方法的参数是单个参数,可以使用 #{} 或 ${} 以任意参数名名称获取

```xml
<!--int deleteUser(int userId);-->
<delete id="deleteUser">
    delete from user where id=#{userId}
</delete>
```

### 2.多个字面量参数

- 若 mapper 接口中方法的参数是多个参数,MyBatis 会自动的将这些参数放入`map`集合中。
  1. 以arg0,arg1,...为键，以参数为值。
  2. 以param1,param2,...为键，以参数为值。
- 以 #{} 或 ${} 访问 map 集合中的键，就可以拿到值。
- arg 是以 `arg0开始`,param 以 `param1开始`。

```xml
<!--int updateUser(String name,int userId);-->
<update id="updateUser">
    update user set name = #{arg0} where id=#{arg1}
</update>
```

### 3.map集合类型参数

若 mapper 接口的方法参数为多个时，可以手动创建一个map集合，将参数放入map集合中，使用 #{} 或 ${} 访问 map 集合对应的键就可以拿到相应的值。

```xml
<!--int insertUserMap(Map<String,Object> map);-->
<insert id="insertUserMap">
    insert into user values (null,#{name},#{password})
</insert>
```

### 4.实体类型参数

若 mapper 接口方法参数为实体类型时,使用 #{} 或 ${} 访问实体对象的属性名拿到属性值。

```xml
<!--int insertUserUser(User user);-->
<insert id="insertUserUser">
    insert into user values (null,#{name},#{password})
</insert>
```

### 5.@Param

- 可以通过 `@Param` 注解标识 mapper 接口中的方法参数，会将这些参数放入 map （ParamNameResolver进行封装）集合中
  - 以@Param注解的value属性值为键,以参数为值
  - 以param1,param2,...为键,以参数为值
- 只需通过 #{} 或 ${} 访问集合中的键,就能拿到相应的参数值

```xml
<!--User selectUserAN(@Param("name") String name, @Param("password") String password);-->
<select id="selectUserAN" resultType="com.dong.entity.User">
    select * from user where name=#{name} and password=#{password}
</select>
```

## 六、MyBatis的查询功能

### 1.返回单个实体对象

```xml
<!--User selectUser(int userId);-->
<select id="selectUser" resultType="com.dong.entity.User">
    select * from user where id=#{userId}
</select>
```

### 2.返回多个对象进Lis集合

```xml
<!-- List<User> selectUsers();-->
<select id="selectUsers" resultType="com.dong.entity.User">
    select * from user
</select>
```

### 3.返回单个对象进Map集合

注意将 resultType 设置为 map。

```xml
<!--Map<String,Object> selectMap();-->
<select id="selectMap" resultType="map">
    select *from user where id=#{userId}
</select>
```

### 4.返回多个数据进Map集合

- 方法一

  ```xml
  <!--List<Map<String,Object>> selectUserListMap();-->
  <select id="selectUserListMap" resultType="map">
      select *from user
  </select>
  ```

- 方法二

  使用 `@MapKey("")`设置 Map 的键，值是每条数据所对应的map集合。

  ```xml
  <!--@MayKey("id")
      <Map<String,Object> selectUserListMap();-->
  <select id="selectUserListMap" resultType="map">
      select *from user
  </select>
  ```
  

## 七、特殊SQL的执行

### 1.模糊查询



### 2.批量删除

只能使用 `${}` ，如果使用 `#{}` 会将解析后 `delete from user where id in ('1,2,3')`中的 `'1,2,3'`看做一个整体，也就是只有id为'1,2,3'的数据才会被删除。

这是因为 `#{}`会自动添加单引号。

```xml
<!--int deleteMore(@Param("userId") String userId);-->
<delete id="deleteMore">
    delete from user where id in (${userId})
</delete>

/*
	调用
*/
mapper.deleteMore("1,2,3");
```

### 3.动态设置表明

只能使用 `${}`，因为表名不能添加单引号。

```xml
<!--List<User> selectBytable(@Param("table") String table);-->
<select id="selectBytable" resultType="com.dong.entity.User">
    select * from ${table}
</select>
```

### 4.获取自增的主键

给表中添加记录的同时，也会将该记录所在的自增主键值赋值给实体类属性。

比如：

普通插入对象时，创建的实体类的 id 为空（为了保持表中主键自增）:

```xml
<!--int insertUser(String name,String password);-->
<insert id="insertUserUser">
    insert into user values (null,#{name},#{password})
</insert>

/*
	调用
*/
User user = new User(null,"奥特曼","1009");
userMapper.insertUserUser(user);
System.out.println(user);

/*
	输出
*/
User{id=null, name='奥特曼', password='1009'}
```

获取自增主键，并赋值给实体类属性:

```xml
<!--int insertUser(String name,String password);-->
<insert id="insertUserUser" useGeneratedKeys="true" keyProperty="id">
    insert into user values (null,#{name},#{password})
</insert>

/*
	调用
*/
User user = new User(null,"奥特曼","1009");
userMapper.insertUserUser(user);
System.out.println(user);

/*
	输出
*/
User{id=17, name='奥特曼', password='1009'}
```

## 八、自定义resultMap





## 九、动态SQL

动态 SQL 是MyBatis 的一个特色，是一种根据特定条件动态拼装 SQL 语句的功能。

### 1.if

根据条件判断子句是否执行。太爽了！

`if` 标签通过 `test` 属性的表达式进行判断，如果表达式为 `true` ，标签内容将被执行，否则不执行。

`1=1`是一个恒等式，不影响结果，有了 `1=1` 可以在 `where` 后面拼接 `and`。

```xml
<!--List<User> selectUsers(@Param("name") String name,@Param("password") String password);-->
<select id="selectUsers" resultType="com.dong.entity.User">
    select * from user where 1=1
    <if test="name!=null and name!=''">
        and name=#{name}
    </if>
    <if test="password!=null and password!=''">
        and password=#{password}
    </if>
</select>
```

如果 name 和 password 表达式都为 false，则两个标签中 SQL 语句都不会执行，将会查询所有记录。

### 2.where

- 若 where 标签中的 if 条件都不满足，则 where 标签没有任何功能，即不会添加 where 关键字。
- 若 where 标签中的 if 条件满足，则 where 标签会自动添加 where 关键字，并将条件最前方多余的 and/or 去掉。

效果和上面代码的效果一致。

```xml
<!--List<User> selectUsers(@Param("name") String name,@Param("password") String password);-->
<select id="selectUsers" resultType="com.dong.entity.User">
    select * from user
    <where>
        <if test="name!=null and name!=''">
            and name=#{name}
        </if>
        <if test="password!=null and password!=''">
            and password=#{password}
        </if>
    </where>
</select>
```

注意：where标签不能去掉条件后多余的and/or。

### 3.trim

trim 用于去掉或者添加表标签中的内容。

属性：

- prefix：执行之前添加
- suffix：执行之后添加
- prefixOverrides：去掉最前面的相应内容
- suffixOverrides：去掉最后面的响应内容

效果和上面代码一致。

```xml
<!--List<User> selectUsers(@Param("name") String name,@Param("password") String password);-->
<select id="selectUsers" resultType="com.dong.entity.User">
    select * from user
    <trim prefix="where" suffixOverrides="and|or" >
        <if test="name!=null and name!=''">
             name=#{name} and
        </if>
        <if test="password!=null and password!=''">
            and password=#{password} or
        </if>
    </trim>
</select>
```

### 4.choose、when、otherwise

when 至少要有一个，otherwise 至多只有一个。

相当于 `if` a `else if` b `else if` c `else` d

```xml
<!--List<User> selectUsers(@Param("name") String name,@Param("password") String password);-->
<select id="selectUsers" resultType="com.dong.entity.User">
    select * from user
        <where>
            <choose>
                <when test="name!=null and name!=''">
                    name=#{name}
                </when>
                <when test="password!=null and password!=''">
                    password=#{password}
                </when>
            </choose>
        </where>
</select>
```

### 5.foreach

属性：

- collection：设置要循环的数组或集合
- item：表示集合或数组中的每一个数据
- separator：设置循环体之间的分隔符，分隔符前后默认有一个空格，如`,`
- open：设置 foreach 标签中的内容的开始符
- close：设置 foreach 标签中的内容的结束符

```xml
<!--int deleteFor(@Param("args") Integer[] args);-->
<delete id="deleteFor">
    delete
    from user
    where id in
    <foreach collection="args" item="arg" separator="," open="(" close=")">
        #{arg}
    </foreach>
</delete>
```

是对Integer数组参数的遍历。相当于：

```sql
delete from user where id in(15,16,17)
```

### 5.SQL片段

- `<sql>`标签：声明标签
- `<include>`标签：引入标签

```xml
// 截取sql片段
<sql id="user">name,password</sql>
// 引入sql片段
<!--List<User> selectBytable(@Param("table") String table);-->
<select id="selectBytable" resultType="com.dong.entity.User">
    select <include refid="user"></sql> from user
</select>
```

## 十、MyBatis的缓存

### MyBatis一级缓存

- 一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次通过SqlSession查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问
- 条件:同一个SqlSession连续两次查询,并且查询条件相同,第二次查询才会启用缓存,如果两次查询之间使用增\删\改语句,将会清空一级缓存.

- 使一级缓存失效的四种情况：

1. 1. 不同的SqlSession对应不同的一级缓存
   2. 同一个SqlSession但是查询条件不同
   3. 同一个SqlSession两次查询期间执行了任何一次增删改操作（缓存只是提高查询速度的，不能影响查询结果）  不管有没有影响缓存中的数据，只要增删改，就会把一级缓存清空
   4. 同一个SqlSession两次查询期间手动清空了缓存

### MyBatis二级缓存

- 二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取

- 二级缓存开启的条件

1. 1. 在核心配置文件中，设置全局配置属性`cacheEnabled="true"`，默认为true，不需要设置
   2. 在映射文件中设置标签
   3. 二级缓存必须在SqlSession关闭或提交之后有效
   4. 查询的数据所转换的实体类类型必须实现序列化的接口

- 使二级缓存失效的情况：两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效

### 二级缓存相关配置

- 在mapper配置文件中添加的cache标签可以设置一些属性
- eviction属性：缓存回收策略

- - LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。
  - FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。
  - SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。
  - WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。
  - 默认的是 LRU

- flushInterval属性：刷新间隔，单位毫秒

- - 默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句（增删改）时刷新

- size属性：引用数目，正整数

- - 代表缓存最多可以存储多少个对象，太大容易导致内存溢出

- readOnly属性：只读，true/false

- - true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了很重要的性能优势。

  - false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是false

### MyBatis缓存查询的顺序

- 先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用
- 如果二级缓存没有命中，再查询一级缓存
- 如果一级缓存也没有命中，则查询数据库
- SqlSession关闭之后，一级缓存中的数据会写入二级缓存

### 整合第三方缓存

#### 环境搭建

```xml
<!-- Mybatis EHCache整合包 -->
<dependency>
	<groupId>org.mybatis.caches</groupId>
	<artifactId>mybatis-ehcache</artifactId>
	<version>1.2.1</version>
</dependency>
<!-- slf4j日志门面的一个具体实现 -->
<dependency>
	<groupId>ch.qos.logback</groupId>
	<artifactId>logback-classic</artifactId>
	<version>1.2.3</version>
</dependency>
```



## 十一、MyBatis逆向工程





## 十二、分页插件





