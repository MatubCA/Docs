# 核心API

## String

### 字符串的不可变性

对于修改字符串的的值，修改后的变量将会指向堆中新的 **String** 对象

![image-20220821215750653](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208212157686.png)

String 的定义：

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
    //...
}
```

字符串不可变的原因：

1. 字符数组被 `final` 修饰，并且没有提供给外部修改这个字符串的方法
2. `String` 类被 `final` 修饰，不能被继承，避免了子类修改 `String` 的不可变性

为什么将字符串设计为不可变的？

- **缓存**：`String` 会大量使用，而大量创建 `String` 是非常消耗资源的，Java 提供了字符串的缓存功能，大大节省堆空间。通过**字符串常量池**，两个内容相同的变量，可以在池中指向同一个字符串对象
- **安全**：字符串经常用来存储敏感信息，如用户名、密码等。如果这些东西能被随意修改，那将毫无安全性可言。
- **线程安全**：多个线程访问、或者多个线程之间共享字符串，数据不会被改变，保证了线程安全。
- **hashcode 缓存**：`String` 类的 `hashcode()` 被重写，在第一次调用 `hashcode` 时，计算和缓存哈希值，并从那时起返回相同的值
- **性能**：不管是字符串常量池还是hashcode缓存，都能大大提高性能

### String、StringBuffer、StringBuilder

1. 可变性
   - String时不可变的
   - `StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串，不过没有使用 `final` 和 `private` 关键字修饰，最关键的是这个 `AbstractStringBuilder` 类还提供了很多修改字符串的方法比如 `append` 方法。
2. 线程安全
   - String 不可变，所以线程安全
   - `StringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。
   - `StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。
3. 性能
   - 每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象
   - `StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。
   - 相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

总结：

1. 操作少量的数据: 适用 `String`
2. 单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`
3. 多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`

### 字符串常量池

为什么使用常量池：

因为我们是经常使用字符串的，如果每次创建一个字符串，都在堆中开辟空间，将会极大地消耗内存，并且很多字符串都是相同的。所有Java提供了字符串常量池，用于`避免字符串的重复创建`。

字符串常量池的作用机制：

- 当使用**字面量创建字符串**时，会先去字符串常量池中寻找，如果池中存在相同内容的字符串对象的引用，则直接返回该引用。否则，创建字符串对象，将该对象的引用放入常量池，并返回引用。
- 使用 new 创建字符串时：//TODO

字符串常量池的位置：

Java7 前 String Pool 位于`运行时常量池`，属于永久代。Java7 将字符串常量池移入`堆`中，因为永久代空间有限。Java8 彻底移除永久代，使用元空间取代了永久代，常量池又从堆中移入了`元空间`。

`intern` 方法：

当一个字符串调用 `intern()` 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 `equals()` 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。

```java
// 在堆中创建字符串对象”Java“
// 将字符串对象”Java“的引用保存在字符串常量池中
String s1 = "Java";
// 直接返回字符串常量池中字符串对象”Java“对应的引用
String s2 = s1.intern();
// 会在堆中在单独创建一个字符串对象
String s3 = new String("Java");
// 直接返回字符串常量池中字符串对象”Java“对应的引用
String s4 = s3.intern();
// s1 和 s2 指向的是堆中的同一个对象
System.out.println(s1 == s2); // true
// s3 和 s4 指向的是堆中不同的对象
System.out.println(s3 == s4); // false
// s1 和 s4 指向的是堆中的同一个对象
System.out.println(s1 == s4); //true
```

### 常用方法

#### subString()的改变

subString()方法的作用：

**对字符串进行截取。**

JDK7前后的区别：

**JDK之前：**指向同一个String对象

![image-20220822110917765](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208221109798.png)

**JDK之后：**指向不同的String对象

![image-20220822111539456](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208221115491.png)

JDK7之前，subString方法的问题：

- **导致性能问题**，如果我们只需要截取一小段字符，但是我们仍需要引用原字符串，如果字符串很长，一直在被引用得不到回收，可能会造成内存泄漏。
- **改变了 String 不可变的特性**，对字符串进行操作之后，仍指向同一对象。



#### replace、replaceFirst、replaceAll

- replace(CharSequence target, CharSequencereplacement)：用replacement替换所有的target，**两个参数都是字符串**。
- replaceAll(String regex, String replacement)：用replacement替换所有的regex匹配项，regex很明显是个**正则表达式**，replacement是**字符串**。
- replaceFirst(String regex, String replacement)：基本和replaceAll相同，区别是只替换第一个匹配项。



#### 字符串拼接

`+` 和 `=+` 的重载：

字符串的拼接可以使用"`+`"，但是底层是使用`StringBuilder.append`进行拼接的。

Java 并不支持运算符重载，使用 `+` 进行拼接，其实是 Java 提供的一个语法糖。

字符串拼接的方式：

1. `+`，注意不要再循环中使用 `+` 进行拼接，因为每次循环都要创建一个`StringBuilder`对象，影响性能。
2. `concat`
3. `StringBuffer`
4. `StringBuilder`
5. `StringUtils.join`:开源类库方法
5. `Java 8` 中提供的 `StringJoiner类`，为了丰富Stream，对 `List` 进行拼接

int 类型转换为 String 类型：

1. 使用 `+` 将 `int` 类型变量和 `String` 类型变量进行拼接
2. `String.valueOf()`,注意 `String.valueOf()` 底层也是调用 `Integer.toString()`
3. `Integer.toString()`

## 枚举类

### 为什么使用枚举？

枚举用于定义一些固定的值。当然也可以使用常量，但是常量在某些场景中并不适用，或者可能并不那么好用，所有我们将枚举这个概念抽象出来，形成了枚举类。

常量能做的，枚举可以做，常量不能做的，枚举也能做。

特点：

- 枚举类被`final`修饰，因此枚举类不能被继承；
- 枚举类默认继承了`Enum`类，java不支持多继承，因此枚举类不能继承其他类；
- **⭐每一个枚举项都是该枚举类的一个对象**
- 枚举类的构造器是`private`修饰的，因此其他类不能通过构造器来获取对象；
- 枚举类的成员变量是`static`修饰的，可以用类名.变量来获取对象；

- 枚举项必须在第一行
- 枚举类中可以定义抽象方法，有抽象方法必须重写



### 定义枚举

在枚举项中没有书写get、set方法

```java
public enum Season_Enum {
    SPRING("春"){
        @Override
        void show() {
            System.out.println("Spring");
        }
    },SUMMER("夏"){
        @Override
        void show() {
            System.out.println("Summer");
        }
    },AUTUMN("秋"){
        @Override
        void show() {
            System.out.println("Autumn");
        }
    },WRITE("冬"){
        @Override
        void show() {
            System.out.println("Write");
        }
    };

    private String name;

    Season_Enum(String name) {
        this.name = name;
    }

    abstract void show();
}
```



