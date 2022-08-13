# IO流

🌈以流的形式表示数据传输



| 分类       | 字节输入流           | 字节输出流            | 字符输入流        | 字符输出流         |
| ---------- | -------------------- | --------------------- | ----------------- | ------------------ |
| 抽象基类   | InputStream          | OutputStream          | Reader            | Writer             |
| 访问文件   | FileInputStream      | FileOutputStream      | FileReader        | FileWriter         |
| 访问数组   | ByteArrayInputStream | ByteArrayOutputStream | CharArrayReader   | CharArrayWriter    |
| 访问管道   | PipedInputStream     | PipedOutputStream     | PipedReader       | PipedWriter        |
| 访问字符串 |                      |                       | StringReader      | StringWriter       |
| 缓冲流     | BufferedInputStream  | BufferedOutputStream  | BufferedReader    | BufferedWriter     |
| 转换流     |                      |                       | InputStreamReader | OutputStreamWriter |
| 对象流     | ObjectInputStream    | ObjectOutputStream    |                   |                    |
|            | FilterInputStream    | FilterOutputStream    | FilterReader      | FilterWriter       |
| 打印流     |                      | PrintStream           |                   | PrintWriter        |
| 推回输入流 | PushbackInputStream  |                       | PushbackReader    |                    |
| 特殊流     | DataInputStream      | DataOutputStream      |                   |                    |



## File类

🌈`java.io.File`类是对文件或文件夹进行操作的类，将现实中的文件、文件夹或者不存在的文件、文件夹抽象成File类。File类只能对文件本身进行操作，不能对文件内容操作。它是对文件与路径的抽象。与IO没有关系。

🌈File对象可以作为参数传递给流的构造器



### 路径

- 绝对路径

  从盘符开始，固定路径

- 相对路径

  相对于某个位置开始

- 路径分隔符

  - Windows和DOS默认使用`"\"`表示
  - 因为java程序支持跨平台，File类中提供了一个常量，用于匹配不同系统的路径分隔符
  - `File.separator`



### 构造方法

![image-20220813153444865](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208131535345.png)

```java 
public class Construct {
    public File getFile(String pathname){
        return new File(pathname);
    }
    public File getFile(String path1,String path2){
        return new File(path1,path2);
    }
    public File getFile(File file,String path){
        return new File(file,path);
    }
    public File getFile(URI uri){
        return new File(uri);
    }
}

/*
	Test
*/
@Test
public void test7(){
    String path1 = "E:\\Desktop\\a.txt";
    File file = Construct.getFile(path1);
    System.out.println(file);
    
    String path2 = "b.txt";
    File file1 = Construct.getFile(path1, path2);
    System.out.println(file1);
    
    File file2 = Construct.getFile(file, path2);
    System.out.println(file2);
}

/*
	输出
*/
E:\Desktop\a.txt
E:\Desktop\a.txt\b.txt
E:\Desktop\a.txt\b.txt
```



### 常用的方法

🌈抽象路径名 = File类对象

🌈在对File对象操作之前，判断文件或文件夹是否存在

❗ `delete()`方法只能删除空的文件目录

```java
/*
	获取功能
*/
String getName() // 返回由此抽象路径名表示的文件或目录的名称。
    
String getPath() // 将此抽象路径名转换为路径名字符串。
    
String getAbsolutePath() // 返回此抽象路径名的绝对路径名字符串。
    
String 	getParent() // 返回此抽象路径名的父 null的路径名字符串，如果此路径名未命名为父目录，则返回null。
    
long length() // 返回由此抽象路径名表示的文件的长度。
    
long lastModified() // 返回此抽象路径名表示的文件上次修改的时间。
    
String[] list() // 返回一个字符串数组，命名由此抽象路径名表示的目录中的文件和目录。
    
File[] listFiles() // 返回一个抽象路径名数组，表示由该抽象路径名表示的目录中的文件。
    
/*
	重命名
*/
boolean renameTo(File dest) // 重命名由此抽象路径名表示的文件。
    
/*
	判断功能
*/
boolean isDirectory() // 测试此抽象路径名表示的文件是否为目录。

boolean isFile() // 测试此抽象路径名表示的文件是否为普通文件。
    
boolean exists() // 测试此抽象路径名表示的文件或目录是否存在。
    
boolean canRead() // 测试应用程序是否可以读取由此抽象路径名表示的文件。
    
boolean canWrite() // 测试应用程序是否可以修改由此抽象路径名表示的文件。
    
boolean isHidden() // 测试此抽象路径名命名的文件是否为隐藏文件。
    
/*
	创建功能
*/
boolean createNewFile() // 当且仅当具有该名称的文件尚不存在时，原子地创建一个由该抽象路径名命名的新的空文件。
    
boolean mkdir() // 创建由此抽象路径名命名的目录。
    
boolean mkdirs() // 创建由此抽象路径名命名的目录，包括任何必需但不存在的父目录。
    
/*
	删除功能
*/
boolean delete() // 删除由此抽象路径名表示的文件或目录。
```

🌈删除文件

```java 
// 删除多级目录
public static void deleteDir(File file){
    File[] files = file.listFiles();
    for (File file1 : files) {
        if (file1.isFile())
            file1.delete();
        else
            deleteDir(file1);
    }
    file.delete();
}
```

🌈查找文件夹中的文件以及个数，例如：txt = 3

```java 
public static void selectFile(File file){
    File[] files = file.listFiles();
    HashMap<String, Integer> map = new HashMap<>();
    for (File file1 : files) {
        if (file1.isFile()){
            // 不转义的话，点可以匹配所有字符
            String endName = file1.getName().split("\\.")[1];
            if (map.containsKey(endName)){
                Integer integer = map.get(endName);
                integer ++;
                map.put(endName, integer);
            }else{
                map.put(endName,0);
            }
        }else{
            selectFile(file);
        }
    }
    System.out.println(map);
}
```

 

## Stream流

🌈对元素集合进行流水线操作，可以简化操作。只能对流上的元素进行操作，不能对源上的数据修改。与IO没有关系。

🌈分三个步骤：

- 获得Stream流

  - List集合

    ```java 
    // list集合
    public void listDemo(List<String> list){
        list.stream().filter(s -> s.startsWith("张")).forEach(System.out::println);
    }
    ```

    

  - Set集合

    ```java 
    // set集合
    public void setDemo(Set<String> set){
        set.stream().filter(s -> s.startsWith("张")).forEach(System.out::println);
    }
    ```

    

  - Map集合

    🌈Map集合拆分成Set集合`keySet()`或`entrySet()`再进行获取Stream

    ```java 
    // map集合
    public void mapDemo(Map<String, String> map){
            map.keySet().stream().filter(s -> s.startsWith("张")).forEach(System.out::println);
    }
    ```

    ```java 
    // map集合
    public void mapDemo2(Map<String,String> map){
        map.entrySet().stream().filter(entry-> entry.getKey().equals("张")).forEach(System.out::println);
    }
    ```

    

  - 数组

    🌈通过Arrays类的静态方法`stream(Object[])`获取Stream对象

    ```java 
    // 数组
    public void arrayDemo(int[] arr){
        Arrays.stream(arr).filter(i -> i > 5).forEach(System.out::println);
    }
    ```

    

  - 普通参数

    🌈通过Stream类的静态方法`of(Object)`获取Stream对象

    ```java 
    // 普通参数
    public void paramDemo(String... args){
        Stream.of(args).filter(s -> s.startsWith("张")).forEach(System.out::println);
    }
    ```

    

- 中间操作

  - `filter()`过滤操作

    ```java 
    // filter 过滤操作
    public void filterDemo(List<String> list){
        list.stream().filter(s -> s.startsWith("张")).forEach(System.out::println);
    }
    ```

    

  - `limit()`截取指定参数的个数

    ```java 
    // limit 截取指定参数个数
    public void limitDemo(List<String> list){
        list.stream().limit(2).forEach(System.out::println);
    }
    ```

    

  - `skip()`跳过指定参数个数

    ```java 
    // skip 跳过指定参数个数
    public void skipDemo(List<String> list){
        list.stream().skip(2).forEach(System.out::println);
    }
    ```

    

  - `concat()`静态方法，合并流

    ```java 
    // concat 静态方法，合并流
    public void concatDemo(List<String> list1,List<String> list2){
        Stream.concat(list1.stream(), list2.stream()).forEach(System.out::println);
    }
    ```

    

  - `distinct`去除重复元素

    ```java 
    // distinct 去除重复元素
    public void distinctDemo(List<String> list){
        list.stream().distinct().forEach(System.out::println);
    }
    ```

    

- 终止操作

  - `forEach()`打印输出

    ```java 
    // foreach
    public void forEachDemo(List<String> list){
        list.stream().forEach(System.out::println);
    }
    ```

    

  - `count()`返回元素个数

    ```java
    // count 返回流上元素个数
    public void countDemo(List<String> list){
        long count = list.stream().filter(s -> s.startsWith("张")).count();
    }
    ```

    

  - `collect`

    🌈`collect()`负责收集元素，`Collectors.toList()`负责创建集合，存储元素

    ```java ]
    // collect 收集流上元素 Collectors.toXXX()方法创建集合，存储元素
    public void collectDemo(List<String> list){
        List<String> list1 = list.stream().filter(s -> s.startsWith("张")).collect(Collectors.toList());
    }
    ```

    

## 字节流

   

## 字符流



## 缓冲流



## 转换流



## 序列化流



## 打印流



## Properties类































