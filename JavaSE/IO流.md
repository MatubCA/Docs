# IO流

🌈以流的形式抽象数据传输

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

🌈流的分类：

- 按操作的数据单位不同：字节流`(8bit)`，字符流`(16bit)`
- 按流的流向不同：输入流，输出流
- 按流的角色不同：节点流，处理流
  - 节点流：直接从数据源或目的地读写数据
  - 处理流：将流连接在已经存在的流上





> ## File类

🌈`java.io.File`类是对文件或文件夹进行操作的类，将现实中的文件、文件==夹或者不存在==的文件、文件`夹抽象成F`ile类。File类只能对文件本身进行操作，不能对文件内容操作。它是对文件与路径的抽象。与IO没有关系。

🌈File对象可以作为参数传递给流的构造器



### 路径

- 绝对路径

  从盘符开始，固定路径

- 相对路径

  相对于某个位置开始

- 路径分隔符

  - Windows和DOS默认使用`"\\"`表示，也可以使用`"/"`
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

![image-20220818122924914](C:\Users\苏无及\AppData\Roaming\Typora\typora-user-images\image-20220818122924914.png)

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

 ![image-20220817210903160](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208172109249.png)

![image-20220817211359024](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208172113054.png)

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

    

## IO抽象基类

###  输入流

InputStream和Reader是所有输入流的基类

#### InputStream

常用方法：

<img src="https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208141223135.png" alt="image-20220814122311032" style="zoom:200%;" />

- `read()`：返回0-255返回的int字节值，到末尾返回-1
- `read(byte[] b)`返回实际读取个数，到末尾返回-1

#### Reader

常用方法：

![image-20220814122430278](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208141224356.png)

- `read()`返回0-65535`(两个字节的Unicode码)`的int整数，到末尾返回-1

IO资源不属于内存资源，GC无法回收，需要显示关闭资源

### 输出流

OutputStream和Writer是所有输出流的基类

#### OutputStream

常用方法：

![image-20220814122507627](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208141225700.png)

- `close()`需要先刷新，再关闭流
- `writer(int b)`写入参数b的8个低位，24个高位将被省略

#### Writer

常用方法：

![image-20220814122536294](https://raw.githubusercontent.com/MatubCA/Image/main/img2/202208141225365.png)

- 需要先刷新，再关闭流
- 字符流直接操作字符，可以用字符串取代字符数组
- `writer(int c)`写入参数的16个低位，16个高位将被省略
- 在写入数据时，使用`OutputStream(File)`或者`Writer(File)`，该目录下的同名文件将被覆盖
- 使用`OutputStream(File,true)`或者`Writer(File,true)`,目录下的同名文件将不被覆盖，而是追加到末尾



## 节点流

节点流也叫文件流

#### 读取文件

1. 创建一个流对象，将已经存在的文件加载进流
2. 创建一个临时存放数据的数组
3. 将流中的数据读入数组
4. 关闭资源

```java 
// new String,String是java.lang包下的
public static void fileWriter()  {
    FileReader fileReader = null;
    try {
        fileReader = new FileReader(new File("E:\\Desktop\\a.txt"));
        char[] chars = new char[1024];
        int len;
        while((len = fileReader.read(chars)) != -1) {
            System.out.print(new String(chars ,0 ,len));
        }
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        if (fileReader != null) {
            try {
                fileReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 写入文件

1. 创建流对象，建立存放数据的文件
2. 将数据写入流
3. 关闭资源

```java 
public static void fileWriter(){
    FileWriter fileWriter = null;
    try {
        fileWriter = new FileWriter(new File("E:\\Desktop\\a.txt"));
        fileWriter.write("我他妈来啦！");
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        if (fileWriter != null) {
            try {
                fileWriter.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 注意点

- 在写入数据时，使用`OutputStream(File)`或者`Writer(File)`，该目录下的同名文件将被覆盖
- 使用`OutputStream(File,true)`或者`Writer(File,true)`,目录下的同名文件将不被覆盖，而是追加到末尾
- 读取文件时判断保存数据的文件是否存在，不存在将报错
- 字节流操作字节文件：.mp3、.avi、.mp4...
- 字符流只能操作普通的文本文件





## 缓冲流

为了提高数据读写速度，Java提供了带缓冲的流类，在使用缓冲流时，内部会创建缓冲区数组，默认大小8kb

```java 
// 文件复制
public static void copyFile(String starFile, String endFile) {
    /*
     * Description: 自动判断文件类型，进行复制
     * @author: 苏无及
     * @date: 2022/8/14 8:22
     * @param:[starFile, endFile]
     * @return:void
     */
    BufferedInputStream in = null;
    BufferedOutputStream out = null;
    BufferedReader reader = null;
    BufferedWriter writer = null;
    File file = new File(starFile);
    // 判断源文件是否存在
    if (file.exists()) {
        String str = file.getName().split("\\.")[1];
        // 字符文件
        if (str.equals("txt") || str.equals("java") || str.equals("c") || str.equals("cpp")) {
            try {
                reader = new BufferedReader(new FileReader(starFile));
                writer = new BufferedWriter(new FileWriter(endFile));
                String s;
                // 每次读一行数据
                while ((s = reader.readLine()) != null) {
                    writer.write(s);
                    // 写入行分割符
                    writer.newLine();
                }
                // 强制刷出缓冲区数据
                writer.flush();
            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                closeFile(reader,writer);
            }
        } else {
            // 字节文件
            try {
                in = new BufferedInputStream(new FileInputStream(starFile));
                out = new BufferedOutputStream(new FileOutputStream(endFile));
                int len;
                while ((len = in.read()) != -1) {
                    out.write(len);
                }
                out.flush();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                closeFile(in,out);
            }
        }
    } else {
        System.out.println("没有此文件，即将创建！");
        try {
            System.out.println("是否创建成功："+file.createNewFile());
            copyFile(file.getName(),endFile);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
// 关闭字符缓冲
public static void closeFile(BufferedReader reader,BufferedWriter writer){
    if (reader != null) {
        try {
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    if (writer != null) {
        try {
            writer.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
    
// 关闭字节缓冲
public static void closeFile(BufferedInputStream in,BufferedOutputStream out){
    if (in != null) {
        try {
            in.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    if (out != null) {
        try {
            out.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

- 缓冲流是一种处理流，要连接在节点流之上
- 向流中读取数据时，会先读取到缓冲区中，等到8kb的缓冲区满了，才读取下一个8kb的数组
- 向流中写入数据时，会先写入到缓冲区中，等到缓冲区满了，才会一次性将数据写入文件
- 只要关闭最外层的流，也会相应关闭内层节点流
- `flush()`手动将缓冲中的内容写入文件
- 在内存中进行缓冲区的数据交换，提高速度



## 转换流

- InputStreamReader：将InputStream转换为Reader
- OutputStreamWriter：将Writer转换为OutputStream
- 节点流数据是字符时，转换为Reader读取更加高效
- 常用来解决文件编码问题

```java 
public static void IOR(){
    BufferedReader in = null;
    BufferedWriter out = null;
    try {
        in = new BufferedReader(new InputStreamReader(new FileInputStream("E:\\Desktop\\a.txt"),"GBK"));
        out = new BufferedWriter(new OutputStreamWriter(new FileOutputStream("E:\\Desktop\\b.txt"),"GBK"));
        String str;
        while((str = in.readLine()) != null) {
            out.write(str);
            out.flush();
        }
        out.flush();
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        BufferedStream.closeFile(in,out);
    }
}
```



## 标准输入、输出流

- 标准输入：键盘输入
- 标准输出：控制台输出
- 通过`setIn`和`setOut`来对默认设备进行改变

```java 
// 键盘输入，小写转化为大写
public static void main(String[] args) {
    System.out.println("输入数据(以exit结束):");
    BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
    String str = null;
    try {
        while ((str = bufferedReader.readLine()) != null) {
            if (str.equals("exit")) {
                System.out.println("安全退出");
                break;
            } else {
                System.out.println(str.toUpperCase());
                System.out.println("继续输入:");
            }
        }
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        if (bufferedReader != null) {
            try {
                bufferedReader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```



## 打印流

将基本数据类型转化为字符串输出

- PrintStream：打印字节
- PrintWriter：打印字符
- 提供了一系列的`print()`和`ptintln()`重载方法，用于输出不同类型的数据
- 具有自动`flush`功能

```java 
public static void printStreamMethod(){
    PrintStream print = null;
    try {
        print = new PrintStream(new FileOutputStream(new File("E:\\desktop\\c.txt")),true);
        if (print != null){
            // 将标准输出流改成文件
            System.setOut(print);
        }
        System.out.println("ahahhahahahah");
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    }finally {
        if (print != null){
            print.close();
        }
    }
}
```





## 对象流

介绍：

只有字节流：ObjectInputstream、ObjectOutputstream。可以将对象写入文件，也可以从文件中读入对象，用于对象的持久化。



### 序列化和反序列化

- 序列化：将对象写入数据源，使用ObjectOutputstream类的**writeObject()**方法
- 反序列化：从数据源中还原对象，使用ObjectInputStream类的**readObject()**方法

序列化：

```java
public static void method1() throws IOException {
    // 1. 创建需要序列化的对象
    Student student1 = new Student("张三", 20);
    // 2. 获得ObjectOutputStream对象
    ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("E:\\Desktop\\a.txt"));
    // 3. 调用writeObject方法
    oos.writeObject(student1);
}
```

反序列化：

```java
public static void method2() throws ClassNotFoundException, IOException {
    // 1. 获得ObjectInputStream对象
    ObjectInputStream ois = new ObjectInputStream(new FileInputStream("E:\\Desktop\\a.txt"));
    // 2. 调用readObject方法
    Student student = (Student) ois.readObject();
    System.out.println(student);
}
```



















































