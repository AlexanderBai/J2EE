### 一、认识Listener

- 监听器（Listener）就是在application、session、request三个对象创建、销毁或者往其中添加、修改、删除属性（attribute）时自动执行代码的功能组件
- 通俗来说就是监听application、session、request三个的对象和属性的变化
- 由上可知监听器分为三类
  - ServletContext监听器
  - Session监听器
  - Request监听器



### 二、Listen监听Context

- 编写ServletContextListener

```java
package com.alexanderbai.listener;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

/**
 * @ClassName ListenerDemo01
 * @Description TODO
 * @Author AlexanderBai
 * @data 2019/3/28 23:32
 **/
public class ListenerDemo01 implements ServletContextListener {

    public ListenerDemo01() {

    }

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("web应用初始化");

    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("web应用销毁");
    }
}
```

```xml
<listener>
    <listener-class>com.alexanderbai.listener.ListenerDemo01</listener-class>
</listener>
```

- 编写ServletContextAttributeListener

  ```java
  package com.alexanderbai.listener;
  
  import javax.servlet.ServletContextAttributeEvent;
  import javax.servlet.ServletContextAttributeListener;
  
  /**
   * @ClassName ListenerDemo02
   * @Description TODO
   * @Author AlexanderBai
   * @data 2019/3/28 23:32
   **/
  public class ListenerDemo02 implements ServletContextAttributeListener {
      @Override
      public void attributeAdded(ServletContextAttributeEvent scae) {
          System.out.println("增加属性 ");
          System.out.println("属性是" + scae.getName());
          System.out.println("值是" + scae.getValue());
      }
  
      @Override
      public void attributeRemoved(ServletContextAttributeEvent scae) {
          System.out.println("移出属性");
      }
  
      @Override
      public void attributeReplaced(ServletContextAttributeEvent scae) {
          System.out.println("替换属性");
      }
  }
  ```

  ```xml
  <%--
    Created by IntelliJ IDEA.
    User: AlexanderBai
    Date: 2019/3/30
    Time: 15:55
    To change this template use File | Settings | File Templates.
  --%>
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
      <title>ListenerDemo01</title>
  </head>
  <body>
      <%
          application.setAttribute("name","AlexanderBai");
          application.setAttribute("test","TEST");
          application.removeAttribute("test");
      %>
  </body>
  </html>
  ```

  

  ```xml
  <listener>
      <listener-class>com.alexanderbai.listener.ListenerDemo02</listener-class>
  </listener>
  ```

### 三、Listener监听Session

- 编写HTTPSessionListener

  ```java
  package com.alexanderbai.listener;
  
  import javax.servlet.http.HttpSessionEvent;
  import javax.servlet.http.HttpSessionListener;
  
  /**
   * @ClassName ListenerDemo03
   * @Description TODO
   * @Author AlexanderBai
   * @data 2019/3/28 23:32
   **/
  public class ListenerDemo03 implements HttpSessionListener {
      @Override
      public void sessionCreated(HttpSessionEvent se) {
          System.out.println("session创建");
      }
  
      @Override
      public void sessionDestroyed(HttpSessionEvent se) {
          System.out.println("session销毁");
      }
  }
  ```

  ```xml
  <listener>
          <listener-class>com.alexanderbai.listener.ListenerDemo03</listener-class>
  </listener
  ```

  

- 编写HTTPSessionAttributeListener

  ```java
  package com.alexanderbai.listener;
  
  import javax.servlet.http.HttpSessionAttributeListener;
  import javax.servlet.http.HttpSessionBindingEvent;
  
  /**
   * @ClassName ListenerDemo04
   * @Description TODO
   * @Author AlexanderBai
   * @data 2019/3/28 23:32
   **/
  public class ListenerDemo04 implements HttpSessionAttributeListener {
      @Override
      public void attributeAdded(HttpSessionBindingEvent se) {
          System.out.println("session 增加属性");
          System.out.println("属性是" + se.getName());
          System.out.println("值是" + se.getValue());
      }
  
      @Override
      public void attributeRemoved(HttpSessionBindingEvent se) {
          System.out.println("session移出属性");
      }
  
      @Override
      public void attributeReplaced(HttpSessionBindingEvent se) {
          System.out.println("session替换属性");
      }
  }
  ```

  ```xml
      <listener>
          <listener-class>com.alexanderbai.listener.ListenerDemo04</listener-class>
      </listener>
  ```

  ```jsp
  	<%
          session.setAttribute("name","session");
          session.setAttribute("testSesson","TESTSESSION");
          session.removeAttribute("testSesson");
      %>
  ```

### 四、Listener监听Request

- 编写ServletRequestListener

```java
package com.alexanderbai.listener;

import javax.servlet.ServletRequestEvent;
import javax.servlet.ServletRequestListener;

/**
 * @ClassName ListenerDemo05
 * @Description TODO
 * @Author AlexanderBai
 * @data 2019/3/28 23:32
 **/
public class ListenerDemo05 implements ServletRequestListener {
    @Override
    public void requestDestroyed(ServletRequestEvent sre) {
        System.out.println("销毁了一个request");
    }

    @Override
    public void requestInitialized(ServletRequestEvent sre) {
        System.out.println("创建了一个request");

    }
}
```

```xml
 	<listener>
        <listener-class>com.alexanderbai.listener.ListenerDemo05</listener-class>
    </listener>
```



- 编写ServletContextAttributeListener

  ```java
  package com.alexanderbai.listener;
  
  import javax.servlet.ServletContextAttributeEvent;
  import javax.servlet.ServletContextAttributeListener;
  
  /**
   * @ClassName ListenerDemo06
   * @Description TODO
   * @Author AlexanderBai
   * @data 2019/3/28 23:32
   **/
  public class ListenerDemo06 implements ServletContextAttributeListener {
      @Override
      public void attributeAdded(ServletContextAttributeEvent scae) {
          System.out.println("request增加属性");
          System.out.println("属性是" + scae.getName());
          System.out.println("值是" + scae.getValue());
      }
  
      @Override
      public void attributeRemoved(ServletContextAttributeEvent scae) {
          System.out.println("request移除属性");
      }
  
      @Override
      public void attributeReplaced(ServletContextAttributeEvent scae) {
          System.out.println("request替换属性");
      }
  }
  ```

  ```xml
      <listener>
          <listener-class>com.alexanderbai.listener.ListenerDemo06</listener-class>
      </listener>
  ```

  ```jsp
  	<%
          request.setAttribute("test1",request);
          request.setAttribute("test2","2");
          request.setAttribute("test3","3");
          request.removeAttribute("test2");
      %>
  ```

  

### 五、Listener统计在线人数

```java
package com.alexanderbai.listener;

import javax.servlet.ServletContext;
import javax.servlet.http.HttpSessionEvent;
import javax.servlet.http.HttpSessionListener;

/**
 * @ClassName OnlineCountListenr
 * @Description TODO
 * @Author AlexanderBai
 * @data 2019/3/28 23:32
 **/
public class OnlineCountListenr implements HttpSessionListener {
    @Override
    public void sessionCreated(HttpSessionEvent se) {
        //Returns the ServletContext to which this session belongs.
        ServletContext servletContext=se.getSession().getServletContext();
        Integer onLineCount= (Integer) servletContext.getAttribute("onLineCount");
        if (null == onLineCount) {
            servletContext.setAttribute("onLineCount", 1);
        } else {
            onLineCount++;
            servletContext.setAttribute("onLineCount", onLineCount);
            System.out.println("新增一位用户!!!");
        }
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        //Returns the ServletContext to which this session belongs.
        ServletContext servletContext=se.getSession().getServletContext();
        Integer onLineCount= (Integer) servletContext.getAttribute("onLineCount");
        if (null == onLineCount) {
            servletContext.setAttribute("onLineCount", 1);
        } else {
            onLineCount--;
            servletContext.setAttribute("onLineCount", onLineCount);
            System.out.println("一位用户离线!!!");
        }
    }
}
```

```xml
    <listener>
        <listener-class>com.alexanderbai.listener.OnlineCountListenr</listener-class>
    </listener>
```

```jsp
<%--
  Created by IntelliJ IDEA.
  User: AlexanderBai
  Date: 2019/3/30
  Time: 15:55
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>ListenerDemo01</title>
</head>
<body>

当前在线人数：${onLineCount}


</body>
</html>
```

###六、自定义Session扫描器