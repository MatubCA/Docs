# JavaWeb









## 一、Tomcat

下图展示了Servlet在Web应用程序中的位置：



### [#](https://www.ydlclass.com/doc21xnv/javaweb/servlet/#二、servlet核心技术)二、Servlet核心技术

#### [#](https://www.ydlclass.com/doc21xnv/javaweb/servlet/#_1、servlet加载时机)**1、Servlet加载时机**


## 三、request



## 四、Cookie&Session



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

