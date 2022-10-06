sql语法

- DDL(Data Definition Language):数据定义语言,用来定义数据库对象:数据库\表\列等
- DML(Data Manipulation Language):数据操作语言,用于对数据库表中的数据进行增删改
- DQL(Data Query Language):数据查询语言,用于查询数据库表中的数据
- DCL(Data Control Language):数据控制语言,用来定义数据库的访问权限和安全级别,及创建用户

DDL_数据库:

1. 查询

   ```sql
   show databases;-- 查询所有数据库
   ```

2. 创建

   ```sql
   create database 数据库名;-- 创建数据库
   create database if not exists 数据库名;-- 判断,如果数据库不存在则创建
   ```

3. 删除

   ```sql
   drop database 数据库名;-- 删除数据库
   drop database 数据库名;-- 判断,如果存在则删除
   ```

4. 使用数据库

   ```sql
   select database();-- 查看当前使用的数据库
   use 数据库名;-- 使用数据库
   ```

DDL_表:

1. 查询

   ```sql
   show tables;-- 查询当前数据库下所有表的名称
   desc 表名;-- 查询表结构
   ```

2. 创建

   ```sql
   create table table_name(
       字段1 类型1,
       字段2 类型2,
       字段3 类型3,
       ...
       字段n 类型n
   );
   ```

3. 修改

   ```sql
   alter table 表名 rename to 表名;-- 修改表名
   alter table 表名 add 字段名 类型;-- 添加一列
   alter table 表名 modify 字段 类型;-- 修改列的数据类型
   alter table 表名 change 字段 新字段 类型;-- 修改列名和数据类型
   alter table 表名 drop 字段名;-- 删除列名
   ```

4. 删除

   ```sql
   drop table 表名;-- 删除表
   drop table if exists 表名;-- 判断,如果存在则删除
   ```

DML:

1. 添加

   ```sql
   insert into tableName(fieldName1,fieldName2,...) values(value1,value2,...)-- 给指定列添加数据
   insert into tableName values(value1,value2,...)-- 给全部列添加数据
   
   # 批量操作
   insert into tableName(fieldName1,fieldName2,...) values(field1,field2,...),(field1,field2,...),(field1,field2,...)...
   insert into tableName values (field1,field2,...),(field1,field2,...),(field1,field2,...)...
   ```

2. 修改

   ```sql
   update tableName set field1=value1,field2=value2,... where ...
   ```

3. 删除

   ```sql
   delete from tableName where ...
   ```

DQL:

```sql
select 
	字段列表
from
	表名列表
where
	条件列表
group by
	分组字段
having
	分组后条件
order by
	排序字段
limit
	分页限定
```

1. 基础查询

   ```sql
   select 字段列表 from 表名;-- 查询多个字段
   select * from 表名;-- 查询全部字段
   select distinct 字段列表 from 表名;-- 去除重复记录
   as -- 为查询字段起别名,as也可以省略
   ```

2. 条件查询

   ```sql
   select 字段列表 from 表名 where 条件列表;
   ```

   条件:

   | 符号             | 功能                                   |
   | ---------------- | -------------------------------------- |
   | >                | 大于                                   |
   | <                | 小于                                   |
   | >=               | 大于等于                               |
   | <=               | 小于等于                               |
   | =                | 等于                                   |
   | <>或!=           | 不等于                                 |
   | between...and... | 在某个范围之间(包含两边)               |
   | in(...)          | 多选一                                 |
   | like 占位符      | 模糊查询,_:单个任意字符,%:多个任意字符 |
   | is null          | 是null                                 |
   | is not null      | 不是null                               |
   | and或&&          | 与                                     |
   | or或\|\|         | 或                                     |
   | not或!           | 非                                     |

3. 分组查询

   ```sql
   select 字段列表 from 表名 [where 分组前条件限定] group by 分组字段名 [having 分组后条件过滤]
   ```

   注意:分组之后,查询的字段为聚合函数和分组字段,查询其它字段没有意义

   where和having的区别:

   - 执行时机不同:where是分组之前进行限定,having是分组之后对结果进行过滤.
   - 用于判断的条件不同:where不能对聚合函数进行判断

   执行顺序:where>聚合函数>having

4. 排序查询

   ```sql
   select 字段列表 from 表名 order by 排序字段1,排序字段2,...;-- 只有前面的排序方式结果相同才会开始后面的排序方式
   ```

   排序方式:

   - asc:升序排序(默认)
   - desc:降序排序 

5. 分页查询

   ```sql
   select 字段列表 from 表名 limit 其实索引,查询条目数;
   ```

   起始索引:从0开始,(当前页码-1)*显示的条目数

   limit是mysql的方言,oracle用rownumber,SqlServer用top

   







数据类型

![image-20220926181706291](E:\Pictures\Typora\image-20220926181706291.png)

聚合函数

1. 概念

   将一列数据作为一个整体,进行纵向计算

2. 函数分类

   | 函数名                  | 功能                   |
   | ----------------------- | ---------------------- |
   | count(列名)或者count(*) | 统计不为null的记录数量 |
   | max(列名)               | 最大值                 |
   | min(列名)               | 最小值                 |
   | sum(列名)               | 和                     |
   | avg(列名)               | 平均值                 |

3. 语法

   ```sql
   select 聚合函数名(列名) from 表名;-- null值不参与函数运算
   ```



约束

1. 概念

   约束是作用于表中列上的规则,用于限制数据的完整性,保证数据库中数据的正确\有效\完整

2. 分类

   | 约束名称 | 描述                                                      | 关键字      |
   | -------- | --------------------------------------------------------- | ----------- |
   | 非空约束 | 保证列中所有数据不能有null值                              | not nuull   |
   | 唯一约束 | 保证列中所有数据各不相同                                  | unique      |
   | 主键约束 | 主键是一行数据的唯一标识,要求非空且唯一                   | primary key |
   | 检查约束 | 保证列中的数据满足某一条件                                | check       |
   | 默认约束 | 保存数据时,未指定值则采用默认值                           | default     |
   | 外键约束 | 外键用来让两个表中的数据产生链接,保证数据的一致性和完整性 | foreign key |

   注意:mysql不支持检查约束

   自增:列是数字类型并且具有唯一约束,可以设置自增(auto_increment),一般设置主键自增



多表查询

1. 连接查询

   将多张表合在一起进行查询

   - 内连接(两张表的交集)

     ```sql
     select 字段列表 from 表1,表2,... where 条件;-- 隐式内连接
     select 字段列表 from 表1 [inner] join 表2 on 条件;-- 显示内连接
     ```

   - 外连接

     ```sql
     select 字段列表 from 表1 left [outer] join 表2 on 条件;-- 左外连接:表1以及交集部分
     select 字段列表 from 表1 right [outer] join 表2 on 条件;-- 右外连接:表2以及交集部分
     ```

2. 子查询

   查询中嵌套查询,嵌套查询即为子查询

   - 单行单列

     作为条件值,使用条件语句进行判断

     ```sql
     select 字段列表 from 表 where 字段名=(子查询);
     ```

   - 多行单列

     作为条件值,使用in等关键字进行条件判断

     ```sql
     select 字段列表 from 表 where 字段名 in(子查询); 
     ```

   - 多行多列

     作为虚拟表,将查询结果作为数据源

     ```sql
     select 字段列表 from (子查询) where 条件; 
     ```

     
