知识储备

- web容器
- web服务器
- web资源
- web应用程序
- 应用程序服务器（应用服务器）
- HTTP 服务器

### 一、Filter简介

- Filter，称之为过滤器，是web服务器提供的一种servlet技术。

- 当浏览器用户请求web资源时，过滤器可以进行筛选性过滤。如过实现URL级别的权限访问控制、滤敏感词汇、设置请求与响应的编码格式等。

![1553871561325](assets/1553871561325.png)

### 二、Filter是如何实现拦截的

####1、请求时拦截

- 用户请求web资源不是直接获取web资源，而是向web服务器递交自己的请求，若我们的web项目编写了Filter接口的实现类，则在web服务器调用service（也就是允许请求获取资源，但此时过滤器还并没有放行请求）之前，过滤器会对请求进行处理，有web.xml中的配置来实现具体对哪个web资源得到请求进行过滤

####2、响应时拦截

- 当请求获得web资源，并准备离开服务器返回客户端浏览器时，过滤器也会对响应进行处理。

#### 3、拦截操作

- 在过滤器中重写doFilter()方法，编写代码，实现具体的拦截操作。同时也可以在此方法中调用下一个过滤器的doFilter()方法，实现过滤器链

###三、Filter开发入门

#### 1、Filter开发步骤

**Filter的开发一般有两个步骤**

>- 编写Java类实现Filter接口，并重写doFilter()方法。
>- 在web.xml文件中使用<filter>和<filter-mapping>元素对编写的Filter进行注册，并设置Filter所能拦截的资源



**FIlter的三种典型应用**

>- 可以filter中根据条件判断是否调用chain.doFilter(request,response)方法，即是否让目标资源执行
>- 在让目标资源执行之前，对request和/response做预处理，再让目标资源执行
>- 在目标资源执行之后，捕获目标资源的执行结果，从而实现一些特殊的功能

- 案例

```java
package com.alexanderbai.filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

/**
 * @Author AlexanderBai
 * @data 2019/3/29 21:17
 */
public class FilterDemo01 implements Filter {

    public void init(FilterConfig config) throws ServletException {
        System.out.println("----过滤器初始化-----");
    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
            throws ServletException, IOException {
        //对request和response做一些预处理
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("text/html,charset=utf-8");

        System.out.println("req.getLocalPort() = " + req.getLocalPort());
        System.out.println("req.getLocalAddr() = " + req.getLocalAddr());
        System.out.println("req.getLocalName() = " + req.getLocalName());
        System.out.println("req.getRemoteAddr() = " + req.getRemoteAddr());


        System.out.println("Filter执行前！！！");
        chain.doFilter(req,resp);
        System.out.println("Filter执行后！！！");
    }

    public void destroy() {
        System.out.println("----过滤器被销毁-----");
    }
}
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!-- 过滤器配置-->
    <filter>
        <filter-name>FilterDemo01</filter-name>
        <filter-class>com.alexanderbai.filter.FilterDemo01</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>FilterDemo01</filter-name>
        <!-- " /* "表示拦截所有的请求-->
        <url-pattern>/*</url-pattern>
    </filter-mapping>

</web-app>
```

####2、Filter链

![1553870331240](assets/1553870331240.png)

- 在web服务器中可以开发**多个过滤器**，这些过滤器组合在一起称为Filter链

- web服务器根据Filter在**web.xml中注册的顺序**，决定先调用哪一个Filter。

- 当第一个Filter的doFilter()方法被调用时，web服务器会创建一个代表**Filter链的FilterChain**对象传给该方法。在doFilter()方法中如果调用了Filter的doFilter()方法，则服务器会检查是否还有Filter，如果有则调用第二个Filter，如果没有则调用目标资源。

  ```java
   public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
              throws ServletException, IOException {
        
          chain.doFilter(req,resp);
  
      }
  ```


### 四、FIlter的生命周期

```java
package com.alexanderbai.filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

/**
 * @Author AlexanderBai
 * @data 2019/3/29 21:17
 */

public class FilterDemo01 implements Filter {

    public void init(FilterConfig config) throws ServletException {
    }

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
            throws ServletException, IOException {
        chain.doFilter(req,resp);
    }

    public void destroy() {
    }
}
```

#### 1、过滤器的创建

- 与Servlet一样Filter的**创建和销毁都是由web服务器负责**，与Servlet区别的是在应用启动的时候就装载Filter，而Servlet可以进行装载次序的设置。
- web容器创建好Filter实例对象之后，调用Filter的init()方法（**init方法只会被调用一次**），接着把Filter对象保存进应用级的集合中去（**FIlter对象常驻web容器**），等待用户的资源访问。
- 当用户的资源访问被Filter的**url-pattern**拦截时，web容器会直接取出Filter对象并调用doFilter()方法。

#### 2、过滤器的销毁

- 当web服务器停止（应用程序停止）或web服务器重新装载应用程序时会调用**destroy()**方法，**destroy()只执行一次**

#### 3、FilterConfig接口

```java
public void init(FilterConfig config) throws ServletException {
    //获取过滤器名称
        System.out.println("config.getFilterName() = " + config.getFilterName());
    
    //    获取过滤器初始化参数，若没有则返回null
    System.out.println("config.getInitParameter(\"name\") = " + config.getInitParameter("name"));
    System.out.println("config.getInitParameter(\"Java\") = " + config.getInitParameter("Java"));
    
    //    返回Servlet上下文对象的引用
    System.out.println("config.getServletContext() = " + config.getServletContext());
        
    //返回过滤器的所有初始化参数的名字的枚举类型
    Enumeration<String> initParameterNames=config.getInitParameterNames();
        while (initParameterNames.hasMoreElements()) {
            System.out.println(initParameterNames.nextElement());
        }
        System.out.println("----过滤器初始化-----");
}
```

### 五、Filter的部署

#### 1、注册Filter

- 开发好Filter之后需要在web.xml中进行注册，这样才能被web服务器调用

  ```xml
  	<filter>
          <description>FilterDemo01过滤器</description>
          <filter-name>FilterDemo01</filter-name>
          <filter-class>com.alexanderbai.filter.FilterDemo01</filter-class>
  
          <init-param>
              <description>FilterDemo01过滤器初始化参数</description>
              <param-name>name</param-name>
              <param-value>AlexanderBai</param-value>
          </init-param>
          <init-param>
              <param-name>Java</param-name>
              <param-value>like</param-value>
          </init-param>
  
      </filter>
  ```

#### 2、映射Filter

- 在web.xml中注册之后，还要在web.xml中映射Filter

>```xml
><filter-mapping>
>        <filter-name>FilterDemo01</filter-name>
>    	<!-- " /* "表示拦截所有的请求-->
>        <url-pattern>/*</url-pattern>
></filter-mapping>
>```
>
>- **`<url-pattern>`设置 filter 所拦截的请求路径(过滤器关联的URL样式)**

<hr>

```xml
<filter-mapping>
        <filter-name>FilterDemo01</filter-name>
        <url-pattern>/index.html</url-pattern>
    
        <dispatcher>REQUEST</dispatcher>
        <dispatcher>INCLUDE</dispatcher>
        <dispatcher>FORWARD</dispatcher>
        <dispatcher>ERROR</dispatcher>
</filter-mapping>
```

**dispatcher可以去以下值：**

- **REQUEST：**当用户直接访问页面时，web服务器会调用过滤器。若目标资源是通过include()或forword()方法访问时，那么过滤器就不会被调用。
- **INCLUDE：**如果请求是通过include()方法访问时，过滤器就会被调用。除此之外，过滤器不会被调用。
- **FORWARD：**如果请求是通过forward()方法访问时，过滤器就会被调用。除此之外，过滤器不会被调用。
- **ERROR：**如果目标资源是通过声明式异常处理机制调用时，过滤器就被调用。除此之外，过滤器不会被调用。









