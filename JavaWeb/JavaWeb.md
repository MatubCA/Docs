# JavaWeb









## 一、Tomcat

下图展示了Servlet在Web应用程序中的位置：

![img](E:\Pictures\Typora\webp.webp)

### [#](https://www.ydlclass.com/doc21xnv/javaweb/servlet/#二、servlet核心技术)二、Servlet核心技术

#### [#](https://www.ydlclass.com/doc21xnv/javaweb/servlet/#_1、servlet加载时机)**1、Servlet加载时机**

## 二、Servlet

### 什么是Servlet

```markdown
1. Servlet是JavaEE规范（接口）之一。
2. Servlet是JavaWeb三大组件之一。三大组件分别是：Servlet程序、Filter过滤器、Listener监听器。
3. Servlet是运行在服务器上的Java小程序。它可以接受客户端发来的请求，并响应数据给客户端。
```





### 创建Servlet

1. 通过实现Servlet接口创建Servlet

   - 步骤

     ```markdown
     1. 编写一个类去实现Servlet接口。
     2. 实现service方法，处理请求，响应数据。
     3. 在web.xml中或者配置servlet程序的访问地址（或者使用注解@WebServlet）
     ```

   - 编码

     ```java
     @WebServlet("/test1")
     public class MyServlet_01 implements Servlet {
     
         @Override
         public void init(ServletConfig servletConfig) throws ServletException {
             System.out.println("进入init方法！");
         }
         @Override
         public ServletConfig getServletConfig() {
             return null;
         }
         @Override
         public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
             System.out.println("进入Service方法！");
         }
     
         private void doPOST() {
             System.out.println("进入doPOST方法！");
         }
     
         private void doGET() {
             System.out.println("进入doGET方法！");
         }
     
         @Override
         public String getServletInfo() {
             return null;
         }
     
         @Override
         public void destroy() {
             System.out.println("销毁该Servlet！");
         }
     }
     ```

   - 路由配置

     - web.xml

       ````xml
       <servlet>
         <!--Servlet程序的别名-->
         <servlet-name>MyServlet_01</servlet-name>
         <!--Servlet程序的全类名-->
         <servlet-class>com.dong.MyServlet_01</servlet-class>
       </servlet>
       <servlet-mapping>
         <servlet-name>MyServlet_01</servlet-name>
         <!--url访问路劲-->
         <url-pattern>/test1</url-pattern>
       </servlet-mapping>
       ````

     - 注解

       ```java
       @WebServlet("/test1")
       ```

       

1. 通过继承HttpServlet类创建Servlet

   - 环境准备

     ```xml
     <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>javax.servlet-api</artifactId>
       <version>4.0.1</version>
     </dependency>
     ```

   - 步骤

     ```markdown
     1. 编写一个类去继承HttpServlet类
     2. 根据业务需要重写doGET和doPOST方法
     3. 在web.xml中配置servlet程序的访问地址
     ```

   - 编码

     ```java
     @WebServlet("/test2")
     public class MyServlet_02 extends HttpServlet {
         @Override
         protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             System.out.println("进入doGet方法！");
         }
     
         @Override
         protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
             System.out.println("进入doPOST方法！");
         }
     }
     ```

   - 路由配置

     - web.xml

       ```xml
       <servlet>
         <!--Servlet程序的别名-->
         <servlet-name>MyServlet_02</servlet-name>
         <!--Servlet程序的全类名-->
         <servlet-class>com.dong.MyServlet_02</servlet-class>
       </servlet>
       <servlet-mapping>
         <servlet-name>MyServlet_02</servlet-name>
         <!--url访问路劲-->
         <url-pattern>/test2</url-pattern>
       </servlet-mapping>
       ```

     - 注解

       ```java
       @WebServlet("/test2")
       ```





### Servlet类的继承体系

![image](E:\Pictures\Typora\202208102114485.png)


- 继承层次解释

  ```markdown
  1. GenericServlet：
  
     GenericServlet实现了Servlet接口中除了service方法外的其他四个方法，这几个方法我们就可以不用去重写了
  
  2. HttpServlet：
  
     HttpServlet实现了Service方法，通过service方法将处理不同的HTTP请求方法，并交由相应的处理方法去处理，如doGET、doPOST等
  
  3. 自定义Servlet：
  
     我们只需要去重写这些处理方法，完成对应的业务就行
  ```

  

- GP分发处理

  ```java
  @Override
  public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
      System.out.println("Hello Servlet！");
      //将ServletRequest转换为HttpServletRequest
      HttpServletRequest request = (HttpServletRequest) servletRequest;
      //获取请求方法
      String method = request.getMethod();
      if ("GET".equals(method)){
          doGET();
      }else if("POST".equals(method)){
          doPOST();
      }
  }
  ```





### Servlet的生命周期

```markdown
1. 执行Servlet构造器方法

2. 执行innit初始化方法

   1、2两步是在第一次访问的时候，创建Servlet程序时会调用

3. 执行service方法

   第3步每次访问时都会调用，处理请求和响应

4. 执行destroy销毁方法

   在web工程停止的时候调用
```





### 什么是ServletConfig

```markdown
1. 是Servlet程序的配置信息类
2. Servlet和ServletConfig对象都是由Tomcat负责创建
3. Servlet程序是第一次访问的时候创建的，ServletConfig是每个Servlet程序创建时，就创建一个对应的ServletConfig对象
```





### ServletConfig

- 三大作用

  ```markdown
  1. 可以获得Servlet程序的别名（servlet-name）的值
  2. 获取初始化参数init-param
  3. 获取ServletContext对象
  ```

  

- 编码

  **Web.xml**

  ```xml
  <servlet>
    <servlet-name>MyServlet_03</servlet-name>
    <servlet-class>com.dong.servlet.MyServlet_03</servlet-class>
    <!--初始化参数-->
    <init-param>
      <param-name>name</param-name>
      <param-value>张三</param-value>
    </init-param>
  </servlet>
  <servlet-mapping>
    <servlet-name>MyServlet_03</servlet-name>
    <url-pattern>/test3</url-pattern>
  </servlet-mapping>
  ```

  **Servlet:**

  ```java
  @Override
  protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      System.out.println("进入方法！");
      //获取Servlet的别名
      System.out.println(getServletConfig().getServletName());
      //获取初始化参数的值
      System.out.println(getServletConfig().getInitParameter("name"));
      //获取ServletContext对象
      System.out.println(getServletConfig().getServletContext());
  }
  ```





### 细节

- getServletConfig

  ```markdown
  在HttpServlet的继承类中,可以直接获取ServletContext对象，通过 `getServletContext()` 方法，也可以通过ServeletConfig获取
  因为HttpServlet继承自GenericServlet, 而GenericServlet实现了ServletConfig接口, 所以, 通过继承HttpServlet而来的类中, 都会存在ServletConfig
  ```

  **源码:**

  ```java
  public ServletContext getServletContext() {
      ServletConfig sc = this.getServletConfig();
      if (sc == null) {
          throw new IllegalStateException(lStrings.getString("err.servlet_config_not_initialized"));
      } else {
          return sc.getServletContext();
      }
  }
  ```

  

- init

  ````markdown
  任何继承GenericServlet的类的int方法中,都要调用父类的int方法.这是因为在int方法中传递着ServletConfig实例,我们都知道GenericServlet实现了ServletConfig实例
  ````

  ```java
   @Override
   public void init(ServletConfig servletConfig) throws ServletException {
       super.init(config);
   }
  ```





### 什么是ServletContext

```markdown
1. ServletContext是一个接口，表示Servlet上下文对象
2. 一个web工程，只有一个ServletContext对象实例
3. ServletContext对象是一个域对象
4. ServletContext是在web工程部署启动的时候创建，在web工程停止的时候销毁
```





###  什么是域对象

域对象，是可以像Map一样存取数据的对象，ServletContext的域对象是整个web工程

| 操作   | 存数据         | 取数据         | 删除              |
| ------ | -------------- | -------------- | ----------------- |
| Map    | put()          | get()          | remove()          |
| 域对象 | setAttribute() | getAttribute() | removeAttribute() |





### ServletContext

- 作用

  ```markdown
  1. 获取web.xml中配置的上下文参数context-param
  2. 获取当前的工程路径，格式：/工程路径
  3. 获取工程部署后在服务器硬盘上的绝对路径
  4. 像Map一样存取数据
  ```

  

- 编码

  **xml:**

  ```xml
  <context-param>
    <!--上下文参数，属于整个web工程-->
    <param-name>username</param-name>
    <!--参数值-->
    <param-value>context</param-value>  
  </context-param>
  <context-param>
    <param-name>password</param-name>
    <param-value>1111</param-value>
  </context-param>
  ```

  **Servlet:**

  ```java
  @Override
  protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      //1、获取上下文参数
      System.out.println(getServletContext().getInitParameter("username"));
      System.out.println(getServletContext().getInitParameter("password"));
      //2、获取当前的工程路径
      System.out.println(getServletContext().getContextPath());
      //3、获取工程部署后在服务器硬盘上的绝对路径
      System.out.println("工程部署路径："+getServletContext().getRealPath("/"));
      //工程下servlet目录下的MyServlet_04文件路径
      System.out.println(getServletContext().getRealPath("/servlet/MyServlet_04"));
      //4、像Map一样存取数据
      getServletContext().setAttribute("ServletContext","你好ServletContext！");
      System.out.println(getServletContext().getAttribute("ServletContext"));
  }
  ```





### 什么是HttpServletRequest

```markdown
每次只要有请求进入Tomcat服务器，Tomcat服务器就会把请求过来的HTTP协议信息解析好封装到Request对象中。然后传递到service方法（doGET和doPOST方法）中给我们使用。我们可以通过HttpServletRequest对象，获取到所有请求的信息
```





### HttpServletRequest方法

```markdown
1. getRequestURI()·····································获取请求的资源路径
2. getRequestURL()·····································获取请求的统一资源定位符（绝对路径）
3. getRemoteHost()·····································获取客户端的IP地址
4. getHeader()············································获取请求头
5. getParameter()········································获取请求的参数
6. getParameterValues()································获取请求的参数（多个值时使用）
7. getMethod()············································获取请求的方式
8. setAttribute(key,value)·······························设置域对象
9. getAttribute(key)······································获取域对象
10. getRequestDispatcher()·····························获取请求转发对象
```





### 请求的中文乱码问题

```markdown
如果不对请求进行处理，获取的表单数据中的中文在控制台上输出，就会乱码
```

- 方法1

  ```java
  //先获取请求参数，将参数以iso8859-1进行编码，再以utf-8进行解码
  
  @Override
  protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      //获取请求参数
      String username = req.getParameter("username");
      //1、先用iso8859-1进行编码
      //2、再用utf-8进行解码
      String name = new String(username.getBytes(StandardCharsets.ISO_8859_1), StandardCharsets.UTF_8);
  }
  ```

  

- 方法2

  ```java
  //直接设置请求体的字符集为utf-8
  
  @Override
  protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
      req.setCharacterEncoding("UTF-8");
  }
  ```





### 请求的转发

- 什么是请求转发

  ```markdown
  服务器收到请求后，从一个资源跳到另一个资源的操作
  ```

  

- 请求转发的特点

  ```markdown
  1. 浏览器地址栏没有变化
  2. 它们是一次请求
  3. 共享Request域中的数据
  4. 可以转发到WEB-INF目录下
  5. 不可以访问工程以外的资源
  ```

  ![image](E:\Pictures\Typora\202208102116882.png)

  


- 编码

  ```java
  req.getRequestDispatcher("/test5").forward(req,resp);
  ```





### 请求的重定向

- 什么是请求的重定向

  ```markdown
  客户端给服务器发送请求，服务器给了客户端一个新地址，然后客户端去访问新地址
  ```

  

- 请求重定向的特点

  ```markdown
  1. 浏览器地址栏变化
  2. 是两次请求
  3. 不能共享Request域中数据
  4. 不能访问WEB-INF下的资源
  5. 可以访问工程外的资源
  ```

  ![image](E:\Pictures\Typora\202208102116775.png)

  


- 编码

  - 方法1

    ```java
    //设置响应状态码，表示重定向
    resp.setStatus(302);
    //设置响应头，说明新的地址在哪里
    resp.setHeader("Location","http://localhost:8080");
    ```

    

  - 方法2

    ```java
    resp.sendRedirect("http://localhost:8080");
    ```

    



### 什么是HttpServletResponse

```markdown
每次请求进入，Tomcat服务器都会创建一个Response对象传递给Servlet程序去使用，表示所有响应的信息，我们需要设置返回客户端的信息，可以通过这个类的对象来设置
```





### 两个输出流的说明

| 字节流 | getOutputStream | 常用于下载（传递二进制数据） |
| :----: | :-------------: | :--------------------------: |
| 字符流 |    getWriter    |       常用于回传字符串       |

**注意: 两个流不能同时使用**





### 往客户端回传数据

- 显式的往客户端回传数据

  ```java
  //先获取输出流对象，利用输出流对象将数据传回客户端
  
  resp.getWriter().println(req.getAttribute("key1"));
  ```

  

- out.writer和out.print的区别

  ```markdown
  1. out.writer
  
     只能输出字符串
  
  2. out.print
  
     可以输出对象
  ```





### 响应的中文乱码问题

- 方法1

  ```markdown
  //设置响应体字符集为UTF-8，通过响应头，设置浏览器也使用UTF-8字符集
  
  resp.setCharacterEncoding("UTF-8"); 
  resp.setHeader("Content-Type", "text/html; charset=UTF-8");
  ```

  

- 方法2

  ```java
  //同时设置服务器和客户端都使用UTF-8字符集，还设置了响应头.此方法在获取流对象之前调用才有效
  
  resp.setContentType("text/html; charset=UTF-8");
  ```

  

## 三、request



## 四、Cookie&Session

## 五、JSP

### 什么是JSP

- jsp 的全换是 java server pages。Java 的服务器页面
- jsp 的主要作用是代替 Servlet 程序回传 html 页面的数据
- Servlet 程序回传 html 页面数据是一件非常繁锁的事情，开发成本和维护成本都极高，用JSP进行简化
- 相当于将Servlet封装了一层，JSP会解析成Servlet

Servlet回传html页面数据

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    resp.setContentType("text/html;charset=UTF-8");
    PrintWriter out = resp.getWriter();
    out.write("<!DOCTYPE html>\r\n");
    out.write(" <html lang=\"en\">\r\n");
    out.write(" <head>\r\n");
    out.write(" <meta charset=\"UTF-8\">\r\n");
    out.write(" <title>Title</title>\r\n");
    out.write(" </head>\r\n");
    out.write(" <body>\r\n");
    out.write(" 这是 html 页面数据 \r\n");
    out.write(" </body>\r\n");
    out.write("</html>\r\n");
    out.write("\r\n");
}
```

JSP回传html页面

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %> 
<html> 
<head> 
    <title>Title</title>
</head> 
<body> 
    这是 html 页面数据
</body>
</html>
```

### JSP的本质

- JSP就是Servlet程序

- 当我们第一次访问 jsp 页面的时候。Tomcat 服务器会帮我们把 jsp 页面翻译成为一个 java 源文件。并且对它进行编译成 为.class 字节码程序，jsp类继承于HttpJspBase，而HttpJspBase直接继承于HttpServlet，所以JSP间接继承了HttpServlet

- 相当于是将回传的html数据那部分抽离出来，使我们专注于业务，减少繁琐、重复的工作

### JSP的三种语法

1. JSP头部的page指令

   ```jsp
   <!--jsp 的 page 指令可以修改 jsp 页面中一些重要的属性，或者行为-->
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <%@ page import="java.util.Map" %>
   ```

   | 属性         | 说明                                                         |
   | ------------ | ------------------------------------------------------------ |
   | language     | 表示 jsp 翻译后是什么语言文件。暂时只支持 java               |
   | contentType  | 表示 jsp 返回的数据类型是什么                                |
   | pageEncoding | 表示当前 jsp 页面文件本身的字符集                            |
   | import       | 跟 java 源代码中一样。用于导包，导类                         |
   | autoFlush    | 设置当 out 输出流缓冲区满了之后，是否自动刷新缓冲级区。默认值是 true |
   | buffer       | 设置 out 缓冲区的大小。默认是 8kb                            |
   | errorPage    | 设置当 jsp 页面运行时出错，自动跳转去的错误页面路径          |
   | isErrorPage  | 设置当前 jsp 页面是否是错误信息页面。默认是 false。如果是 true 可以 |
   | session      | 设置访问当前 jsp 页面，是否会创建 HttpSession 对象。默认是 true |
   | extends      | 设置 jsp 翻译出来的 java 类默认继承谁                        |

   

2. JSP中常用脚本

   + 声明脚本

     ```java
     // 可以给 jsp 翻译出来的 java 类定义属性和方法甚至是静态代码块,内部类等
     
     /*
     	格式
     */
     <%! 声明 java 代码 %>
     
     /*
     	示例
     */
     <%--1、声明类属性--%>
     <%!
     private Integer id;
     private String name;
     private static Map<String,Object> map;
     %>
     <%--2、声明static静态代码块--%>
     <%!
     static {
         map = new HashMap<String, Object>();
         map.put("id","001");
         map.put("name","张三");
         map.put("age","20");
     }
     %>
     <%--3、声明类方法--%>
     <%!
     public int test(){
         return 2022;
     }
     %>
     <%--4、声明内部类--%>
     <%!
     public static class Test{
         private Integer id = 10;
         private String name = "李四";
     }
     %>
     ```

     

   + 表达式脚本

     ```java
     // 用于主在 jsp 页面上输出数据
     
     /*
     	格式
     */
     <%=表达式%>
         
     /*
     	示例
     */
     <%=2022%>
     <%=22.22%>
     <%="这是个字符串"%>
     <%=map%>
     <%=request.getParameter("username")%>
         
     /*
     	表达式脚本的特点
     */
     1. 所有的表达式脚本都会被翻译到_jspService() 方法中
     2. 表达式脚本都会被翻译成为 out.print()输出到页面上
     3. 由于表达式脚本翻译的内容都在_jspService() 方法中,所以_jspService()方法中的对象都可以直接使用
     4. 表达式脚本中的表达式不能以分号结束
     ```

     

   + 代码脚本

     ```java
     // 可以在 jsp 页面中，编写我们自己需要的功能（写的是 java 语句）
     
     /*
     	格式
     */
     <% 
        java 语句
     %>
     
     /*
     	示例
     */
     <%--for循环--%>
     <%
         for (int i=0;i<3;i++){
     %>
             <h1>第<%=i+1%>行</h1>
     <%
         }
     %>
     <%--jspService方法中内容都可以写--%>
     <%
         System.out.println(request.getParameter("username"));
     %>
         
     /*
     	脚本代码的特点
     */
     1. 代码脚本翻译之后都在_jspService 方法中
     2. 代码脚本由于翻译到_jspService()方法中，所以在_jspService()方法中的现有对象都可以直接使用
     3. 还可以由多个代码脚本块组合完成一个完整的 java 语句
     4. 代码脚本还可以和表达式脚本一起组合使用，在 jsp 页面上输出数据
     ```

     

3. JSP中的三种注释

   - html注释

     ```java
     <!-- 这是html注释 -->
     ```

     

   - java注释

     ```java
     // 单行java注释 
     /* 多行java注释 */
     ```

     

   - jsp注释

     ```java
     // 在jsp中推荐使用jsp注释，可以注释掉所有代码
     
     <%-- 这是jsp注释 --%>
     ```

     



### jsp九大内置对象

jsp 中的内置对象，是指 Tomcat 在翻译 jsp 页面成为 Servlet 源代码后，内部提供的九大对象，叫内置对象

| 对象名      | 说明               |
| ----------- | ------------------ |
| request     | 请求对象           |
| response    | 响应对象           |
| pageContext | jsp的上下文对象    |
| session     | 会话对象           |
| application | ServletContext对象 |
| config      | ServletConfig对象  |
| out         | jsp的输出流对象    |
| page        | 指向当前的jsp对象  |
| exception   | 异常对象           |





### jsp的四大域对象

| 对象        | 所属类                | 说明                                                       |
| ----------- | --------------------- | ---------------------------------------------------------- |
| pageContext | PageContextImpl 类    | 当前 jsp 页面范围内有效                                    |
| request     | HttpServletRequest 类 | 一次请求内有效                                             |
| session     | HttpSession 类        | 一个会话范围内有效（打开浏览器访问服务器，直到关闭浏览器） |
| application | ServletContext 类     | 整个 web 工程范围内都有效（只要web工程不停止，数据都在）   |

四个域在使用上存在优先顺序，按域范围的从小到大排序

pageContext——>request——>session——>application





### out输出和response,getWriter的区别

![image](E:\Pictures\Typora\2872089-20220630142601979-92398625.png)


一般统一使用out输出，两种输出混合使用的话，out输出会追加到response输出的后面，会打乱顺序

### 常用标签

## 7.1、jsp静态包含

**格式：**

```jsp
<%@ include file=""%>
```

file 属性指定你要包含的jsp页面的路径 地址中第一个斜杠/ 表示为http://ip:port/工程路径/ 映射到代码的web目录

**静态包含的特点：**

1. 静态包含不会翻译被包含的jsp页面
2. 静态包含其实是把被包含的jsp页面的代码拷贝到包含的位置执行输出

## 7.2、动态包含

**格式：**

```jsp
<jsp:include page=""></jsp:include>
```

**动态包含的特点：**

1. 动态包含会把包含的jsp页面也翻译成为java代码

2. 动态包含底层代码使用如下代码去调用被包含的jsp页面执行输出

   ```java 
   JspRuntimeLibrary.include(request, response, "/include/footer.jsp", out, false);
   ```

3. 动态包含，还可以传递参数

   ```jsp
   <jsp:include page="/include/footer.jsp"> 
       <jsp:param name="username" value="bbj"/> 
       <jsp:param name="password" value="root"/>
   </jsp:include>
   ```

**动态包含的底层原理：**

![image](E:\Pictures\Typora\2872089-20220630142507104-72231875.png)


## 7.3、转发

**格式：**

```jsp
<jsp:forward page=""></jsp:forward>
```

是请求转发标签，它的功能就是请求转发

### 示例

## 8.1、输出表格，里面有10个学生的信息

Servlet

```java
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    ArrayList<User> usersList = new ArrayList<>();
    //模拟数据
    for (int i=0;i<10;i++){
        usersList.add(new User(i,"name"+i,12+i,"phone"+i));
    }
    req.setAttribute("user",usersList);
    req.getRequestDispatcher("jsp/e.jsp").forward(req,resp);
}
```

jsp

```jsp
<body>
<%
    List<User> users = (List<User>) request.getAttribute("user");
%>
<table>
    <tr>
        <td>编号</td>
        <td>姓名</td>
        <td>年龄</td>
        <td>电话</td>
        <td>操作</td>
    </tr>
    <%for (User user : users) {%>
    <tr>
        <td><%=user.getId()%></td>
        <td><%=user.getName()%></td>
        <td><%=user.getAge()%></td>
        <td><%=user.getPhone()%></td>
        <td>添加、删除</td>
    </tr>
    <%}%>
</table>
</body>
```



























## 六、EL表达式

## 七、Filter

### 什么是监听器

```markdown
1. Listener 监听器它是 JavaWeb 的三大组件之一。JavaWeb 的三大组件分别是：Servlet 程序、Filter 过滤器、Listener 监 听器
2. Listener 它是 JavaEE 的规范，就是接口
3. 监听器的作用是，监听某种事物的变化。然后通过回调函数，反馈给客户（程序）去做一些相应的处理
```





### 监听器的种类

```markdown
监听器主要用于监听三个域对象的变化Session、Context、Request
```

- 监听 Session、request、context 的创建与销毁

  ```markdown
  HttpSessionLister、ServletContextListener、ServletRequestListene
  ```

- 监听对象属性变化

  ```markdown
  HttpSessionAttributeLister、ServletContextAttributeListener、ServletRequestAttributeListener
  ```

- 监听Session 内的对象

  ```markdown
  HttpSessionBindingListener 和 HttpSessionActivationListener
  ```

  



### ServletContext监听器

```markdown
1. ServletContextListener 它可以监听 ServletContext 对象的创建和销毁
2. ServletContext 对象在 web 工程启动的时候创建，在 web 工程停止的时候销毁
3. 监听到创建和销毁之后都会分别调用 ServletContextListener 监听器的方法反馈
```





### 实现监听器

- 步骤

  ```markdown
  1. 编写一个类去实现 ServletContextListener
  2. 实现其两个回调方法
  3. 到web.xml 中去配置监听器
  ```

- 编码

  **Servlet：**

  ```java 
  public class Listener_01 implements ServletContextListener {
      @Override
      public void contextInitialized(ServletContextEvent servletContextEvent) {
          System.out.println("ServletContext对象创建了！");
      }
      @Override
      public void contextDestroyed(ServletContextEvent servletContextEvent) {
          System.out.println("ServletContext对象销毁了！");
      }
  }
  ```

  **web.xml：**

  ```xml
  <!--配置监听器,一般放在Servlet前面-->
  <listener>
    <listener-class>com.dong.listener.Listener_01</listener-class>
  </listener>
  ```

  





## 八、Listener

