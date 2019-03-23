#Http请求



###一、新建动态web项目

######1、新建动态web

######2、新建一个Servlet

######3、添加Servlet架包

######4、新建一个用于登录的HTML文件，该文件必须置于web目录下，Tomcat默认WEB-INF目录下的文件不可访问

######4、在WEB-INF目录下配置web.xml，实现HTML文件到Servlet的映射

```xml
<servlet>
    <servlet-name>LoginServlet</servlet-name>
    <servlet-class>LoginServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>LoginServlet</servlet-name>
    <url-pattern>/login</url-pattern>
  </servlet-mapping>
```

###二、部署项目

######1、选中整个项目方式

①、配置Tomcat服务器

②、单击项目，以服务器方式运行

③、跳转到页面后显示404（因为此时URL为：localhost：/项目名称/,如:http://localhost/j2ee/），要手动添加请求页面在访问

######2、只选中请求页面方式

①、单击对应的请求页面（一般是HTML文件），以服务器方式运行

②、可见跳转到浏览器之后，自动显示请求页面:http://localhost/j2ee/login.html

###三、浏览器请求

######1、请求的URL为：localhost：/项目名称/请求页面

###### 2、若出现页面不可正常显示，排查：

①、查看服务器端口

②、web.xml中地址映射是否正确

③、是否有具体要跳转的页面

######3、注意请求方法

###四、服务器获取请求参数

###### 1、根据Http请求方式获取请求参数

```java
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		String name=req.getParameter("name");
		String password=req.getParameter("password");
		System.out.println(name);
		System.out.println(password);
	}
```

######2、注意传参与form表单的name属性值相同

```html
<form action="login" method="post">
		账号: <input type="text" name="name"> <br>
		密码: <input type="password" name="password"> <br>
		<input type="submit" value="登录">
</form>
```

### 五、实现HTML到Servlet的两种映射方式

####1、上述1.4所讲的web.xml

####2、使用注解（简单描述）

> 实现向浏览器response登录情况
>
> HTML仍然使用4.2的form表单，在服务器端进行验证。

```java
import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet({ "/LoginServlet", "/login" })//此处使用注解实现HTML到Servlet的映射路径
public class LoginServlet extends HttpServlet {
	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
			throws ServletException, IOException {
		String name=req.getParameter("name");
		String password=req.getParameter("password");
		System.out.println(name);
		System.out.println(password);

		String html;
		if(name.equals("AlexanderBai")&&password.equals("123456")) {
			html="<div style='color:green'>success</div>";
		}else {
			html="<div style='color:red'>fail</div>";
		}
		PrintWriter out=resp.getWriter();
		out.println(html);
	}
}
```

### 六、Servlet中文问题

####1、HTML的head标签中注明向服务器提交的编码方式

```html
<meta http-equiv="Content-Type" content="text/html" charset="UTF-8">
```

####2、服务器进行相应的解码（接受参数）和编码（response）

在请求方法相对应的服务器端处理方法中添加如下代码

```java
req.setCharacterEncoding="UTF-8";//告诉服务器已utf-8解码
resp.setCharacterEncoding="UTF-8";//以utf-8编码方式响应
```

### 七、Servlet跳转

####1、服务器端跳转（请求转发）

Http请求到服务器，服务器发现匹配的Servlet，当Servlet执行相应的步骤之后去执行**request**的**getRequestDispatcher()**方法，把同一个请求转发到另一个对象（HTML、JSP、Servlet等）

```java
req.getRequestDispatcher("success.html").forward(req,resp);
```

####2、客服端跳转（重定向）

> Http请求到服务器，服务器发现匹配的Servlet，当Servlet执行相应的步骤之后去执行**sendRedirect()**方法（注意这里用的是**response**的方法），立即向客户端返回这个响应（在响应头中包含新请求所对应的URL），并告诉客户端必须再发送一个新的请求。
>
> 紧接着客户端收到响应后再发送一个请求，服务器端收到新的请求后做出相应的处理。以上两个请求互不干扰、相互独立。

```java
resp.sendRedirect("fail.html");
```

#### 3、案例

> **LonginServlet.java**
>
> ```java
> import java.io.IOException;
> import java.io.PrintWriter;
> 
> import javax.servlet.ServletException;
> import javax.servlet.annotation.WebServlet;
> import javax.servlet.http.HttpServlet;
> import javax.servlet.http.HttpServletRequest;
> import javax.servlet.http.HttpServletResponse;
> 
> @WebServlet({ "/LoginServlet", "/login" })
> public class LoginServlet extends HttpServlet {
> 	@Override
> 	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
> 			throws ServletException, IOException {
> 		
> 		req.setCharacterEncoding("utf-8");//告诉服务器已utf-8解码
> 		resp.setCharacterEncoding("UTF-8");//以utf-8编码方式响应
> 		
> 		String name=req.getParameter("name");
> 		String password=req.getParameter("password");
> 		System.out.println(name);
> 		System.out.println(password);
> 
> 		String html;
> 		if(name.equals("AlexanderBai")&&password.equals("123456")) {
> 			req.getRequestDispatcher("success.html").forward(req, resp);
> 		}else {
> 			resp.sendRedirect("fail.html");
> 		}
> 	}
> }
> ```

> **longin.html**
>
> ```html
> <!DOCTYPE html>
> <html>
> <head>
> <meta charset="UTF-8">
> <title>登录页面</title>
> </head>
> <body>
> 	<form action="login" method="post">
> 		账号: <input type="text" name="name"> <br>
> 		密码: <input type="password" name="password"> <br>
> 		<input type="submit" value="登录">
> 	</form>
> </body>
> </html>
> ```

> **success.html**
>
> ```html
> <div style='color:green'>login success</div>
> ```

> **fail.html**
>
> ```html
> <div style='color:green'>login fail</div>
> ```

#### 4、过程路径

![1552659723712](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1552659723712.png)

![1552660524337](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1552660524337.png)

### 八、JSP

####1、认识JSP

JSP文件其实也是一个Servlet(**继承HTTPServlet**)，JSP文件会被服务器转译为java文件，可以看出转译后的**.java**继承了**HttpJspBase**，而**HttpJspBase**继承了**HttpServlet**，所以JSP文件也是一个Servlet，之后再编译运行



>- **hello.jsp**
>
>```jsp
><%@ page import="java.util.Date" %>  <%-- 这一行是JSP的<@ page指令 --%>
><%--
>Created by IntelliJ IDEA.
>User: AlexanderBai
>Date: 2019/3/21
>Time: 13:25
>To change this template use File | Settings | File Templates.
>--%>
><%@ page contentType="text/html;charset=UTF-8" language="java" %>
><html>
><head>
><title>Hello JSP</title>
></head>
><body>
><%=new Date().toString()%>
></body>
></html>
>```
>
>- ```jsp
>  <%@ page import="java.util.Date" %> 
>  <%-- 这一行是JSP的<@ page指令  import 导入的包以逗号分隔-->
>  ```
>```
>
>```
>
>```
>
>```
>
>- ```jsp
>  <%=new Date().toString()%> <%--  <%= %>相当于Servlet中的response，getWriter()进行输出 --%>
>  <%--  response.getWriter().println(new Date().toString());  --%>
>  ```
>```
>
>```
>
>```
>
>```



![1552913547651](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1552913547651.png)

![1552913467971](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1552913467971.png)

> - 转译后的.java文件位置
>
> > 【Eclipse】
> >
> > - <workspace所在目录>\.metadata\.plugins\org.eclipse.wst.server.core\tmpX\work\Catalina\<项目名称>
> > - G:\program\Java\Eclipse\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\work\Catalina\localhost\j2ee\org\apache\jsp
>
> >- 【tomcat】
> >- <tomcat所在目录>\work\Catalina\localhost\<项目名称>
> >
> >

#### 2、JSP页面元素

- 静态内容
  - 就是html,css,javascript等内容
- 指令
  - 以<%@开始 %> 结尾，比如<%@page import="java.util.*"%>
- 表达式 <%=%>
  - 用于输出一段html
- Scriptlet
  - 在<%%> 之间，可以写任何java 代码
-  声明
  - 在<%!%> 之间可以声明字段或者方法。但是不建议这么做。
-  动作
  - <jsp:include page="FileName" > 在jsp页面中包含另一个页面。
- 注释 <%-- -- %>
  - 不同于 html的注释 <!-- --> 通过jsp的注释，浏览器也看不到相应的代码，相当于在servlet中注释掉了

![1553148897103](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553148897103.png)

```java
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %><%--
  Created by IntelliJ IDEA.
  User: AlexanderBai
  Date: 2019/3/21
  Time: 14:13
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Demo</title>
</head>
<body>
    <%
        List<String> list=new ArrayList<>();
        list.add("Today");
        list.add("is");
        list.add("a");
        list.add("great");
        list.add("day.");
    %>

    <table width="200px" align="center" border="1" cellspacing="0">
        <%for (String str : list) {%>
        <tr>
            <td>
                <%= str%>
            </td>
        </tr>
        <%}%>
    </table>
</body>
</html>
```



#### 3、JSP include

>- demo.jsp
>
>```jsp
><%@ page import="java.util.List" %>
><%@ page import="java.util.ArrayList" %><%--
>Created by IntelliJ IDEA.
>User: AlexanderBai
>Date: 2019/3/21
>Time: 14:13
>To change this template use File | Settings | File Templates.
>--%>
><%@ page contentType="text/html;charset=UTF-8" language="java" %>
><html>
><head>
><title>Demo</title>
></head>
><body>
><%
>List<String> list=new ArrayList<>();
>list.add("Today");
>list.add("is");
>list.add("a");
>list.add("great");
>list.add("day.");
>%>
>
><table width="200px" align="center" border="1" cellspacing="0">
><%for (String str : list) {%>
><tr>
><td>
><%= str%>
></td>
></tr>
><%}%>
></table>
><jsp:include page="footer.jsp">
><jsp:param name="year" value="2019"/>
></jsp:include>
></body>
></html>
>```
>
>- footer.jsp
>
>```jsp
><%--
>Created by IntelliJ IDEA.
>User: AlexanderBai
>Date: 2019/3/21
>Time: 14:27
>To change this template use File | Settings | File Templates.
>--%>
><%@ page contentType="text/html;charset=UTF-8" language="java" %>
><html>
><head>
><title>footer</title>
></head>
><body>
><hr>
><p style="text-align: center">
>copyrigth@<%=request.getParameter("year")%>
></p>
></body>
></html>
>```
>
>- 运行结果
>
>![1553150400585](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553150400585.png)
>
>- ```php+HTML
>  因为指令 <%@include 会导致两个jsp合并成为同一个java文件，所以就不存在传参的问题，demo.jsp 里定义的变量，直接可以在footer.jsp中访问。
>  而动作<jsp:include />其实是对footer.jsp进行了一次独立的访问，那么就有传参的需要。
>  ```
>```
>
>```
>
>```
>
>```
>
>```
>
>```
>
>```
>
>```

#### 4、cookie与session

#####（1）、认识cookie

- cookie是一种**浏览器与服务器交互的数据**，平常我们登录各个网站，会显示多长时间内保持登录状态，就是有cookie实现。
- cookie由**服务器创建**，但不会保存在服务器上，创建好之后发送给浏览器，浏览器**保存在用户本地**
- 再次访问网站时，带上这个cooki

> - **setCookie.jsp**
>
>   ```jsp
>   <%--
>     Created by IntelliJ IDEA.
>     User: AlexanderBai
>     Date: 2019/3/21
>     Time: 14:55
>     To change this template use File | Settings | File Templates.
>   --%>
>   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>   <html>
>   <head>
>       <title>setCookie</title>
>   </head>
>   <body>
>       <%
>           Cookie cookie = new Cookie("name", "Alexander");
>           cookie.setMaxAge(60);//一分钟
>           cookie.setPath("/");//表示访问服务器的所有应用都会提交这个Cookie到服务器，如果其值是/a，你们就表示仅仅能访问/a路径的时候才会提交
>           response.addCookie(cookie);
>       %>
>       <a href="getCookie.jsp">跳转到获取Cookie的页面</a>
>   </body>
>   </html>
>   ```
>
> - **getCookie.jsp**
>
>   ```jsp
>   <%--
>     Created by IntelliJ IDEA.
>     User: AlexanderBai
>     Date: 2019/3/21
>     Time: 14:55
>     To change this template use File | Settings | File Templates.
>   --%>
>   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>   <html>
>   <head>
>       <title>getCookie</title>
>   </head>
>   <body>
>   
>       <%
>           Cookie[] cookies=request.getCookies();
>           if (cookies != null) {
>               for (int i = 0; i < cookies.length; i++) {
>                   out.print(cookies[i].getName()+":"+cookies[i].getValue()+"<br>");
>               }
>           }
>       %>
>   </body>
>   </html>
>   ```
>
> - ![1553153012112](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553153012112.png)

#####（2）、cookie原理示意图

![](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553154152116.png) 

#####（3）、认识session（会话）

- 指用户打开一个网站，不管在**这个网站中**浏览了多少页面，点击了多少链接，知道用户关闭浏览器为止，都属于同一个会话
- 比如我们登录了淘宝网之后，不管我们跳到哪一个商店或是有多少货物添加了购物车，始终都是在自己所属的session里面，实质上session起到一个跟踪用户的作用。

> - **setSession.jsp**
>
>   ```jsp
>   <%--
>     Created by IntelliJ IDEA.
>     User: AlexanderBai
>     Date: 2019/3/21
>     Time: 14:55
>     To change this template use File | Settings | File Templates.
>   --%>
>   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>   <html>
>   <head>
>       <title>setSession</title>
>   </head>
>   <body>
>       <%
>           session.setAttribute("name","AlexanderBai");
>       %>
>       <a href="getSession.jsp">跳转到获取Session的页面</a>
>   </body>
>   </html>
>   ```
>
> - **getSession.jsp**
>
>   ```jsp
>   <%--
>     Created by IntelliJ IDEA.
>     User: AlexanderBai
>     Date: 2019/3/21
>     Time: 14:55
>     To change this template use File | Settings | File Templates.
>   --%>
>   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>   <html>
>   <head>
>       <title>getSession</title>
>   </head>
>   <body>
>       <%
>           String name= (String) session.getAttribute("name");
>       %>
>       session中的name：
>       <%=
>          name
>       %>
>   </body>
>   </html>
>   ```

#####（4）、session原理示意图

![1553155409479](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553155409479.png)

- 有些时候浏览器可以关闭cookie，这是没有cookie，为防止一直创建session对象，可以使用

  ```java
  response.encodeURL("getSession.jsp")
  ```

  > **response.encodeURL方法会把getSession.jsp这个url转换为getSession.jsp;jsessionid=22424AEA86ADBE89F335EEB649D997A8**

- **sessionid:**session标识，由服务器创建session时创建，在Tomcat中称为jse

- 这时**JSESSIONID**作为cookie有效，JSESSIONID由服务器创建，

- [cookie、session、sessionid、jsessionid的区别与联系](http://www.cnblogs.com/fnng/archive/2012/08/14/2637279.html)

#### 5、JSP作用域

![1553156611706](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553156611706.png)

> - pageContext
>
>   - setContext.jsp
>
>   ```jsp
>   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>       <%
>           pageContext.setAttribute("name","AlexanderBai");
>       %>
>       <%=pageContext.getAttribute("name")%>
>   ```
>
>   - getContex t.jsp
>
>   ```jsp
>   <%@ page contentType="text/html;charset=UTF-8" language="java" %
>       <%=pageContext.getAttribute("name")%>
>   ```

> - RequestContext
>
>   - setContext.jsp
>
>     ```jsp
>     <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>         <%
>             request.setAttribute("name","AlexanderBai");
>         %>
>     ```
>
>   - getContex t.jsp
>
>     ```jsp
>     <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>         <%=Request.getAttribute("name")%>
>     ```
>
>     -   如果发生了**服务端跳转**，从setContext.jsp跳转到getContext.jsp，这其实，**还是一次请求**。 所以在getContext.jsp中，可以取到在requestContext中设置的值
>
>       这也是一种**页面间传递数据的方式**  
>
>       > - 客服端跳转
>       >
>       > - ```
>       >   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>       >       <%
>       >           request.setAttribute("name","AlexanderBai");
>       >       %>
>       >    <jsp:forward page="getContext.jsp"/> 
>       >   ```
>       >    
>       > - 服务器端跳转
>       >    
>       > - ```jsp
>       >   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>       >       <%
>       >           request.setAttribute("name","AlexanderBai");
>       >           response.sendRedirect("getContext.jsp");
>       >       %>
>       >   ```



> - SessionRequestContext
>
>   - setContext.jsp
>
>     ```jsp
>     
>     <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>     
>         <%
>             session.setAttribute("name","AlexanderBai");
>             response.sendRedirect("getContext.jsp");
>         %>
>     ```
>
>   - getContex t.jsp
>
>     ```jsp
>     <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>         <%=session.getAttribute("name")%>
>     ```

> - ApplicationContext
>
> - 在JSP中使用application对象， application对象是**ServletContext接口的实例**
>   也可以通过 request.getServletContext()来获取。
>   所以 application == request.getServletContext() 会返回true
>   `application映射的就是web应用本身。`
>
>   - setContext.jsp
>
>   ```jsp
>   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>       <%
>           application.setAttribute("name","AlexanderBai");
>       %>
>       <%=application.getAttribute("name")%>
>   ```
>
>   - getContex t.jsp
>
>   ```jsp
>   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
>       <%=application.getAttribute("name")%>
>   ```

#### 6、JSP隐式对象

![1553160108243](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553160108243.png)

- page.jsp

  ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  page:<%=page%>
  <br>
  this:<%=this%>
  ```

- **config**

- >- web.xml
  >
  >```
  ><?xml version="1.0" encoding="UTF-8"?>
  ><web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  >      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  >      xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
  >      version="4.0">
  >
  > <!-- 把testconfig.jsp配置为一个Servlet-->
  > <servlet>
  >     <servlet-name>testconfig</servlet-name>
  >     <jsp-file>/testconfig.jsp</jsp-file>
  >
  >     <!-- 配置初始化参数-->
  >     <init-param>
  >         <param-name>database-ip</param-name>
  >         <param-value>127.0.0.1</param-value>
  >     </init-param>
  > </servlet>
  >
  > <!--将路径testconfig映射到testconfig.jsp -->
  > <servlet-mapping>
  >     <servlet-name>testconfig</servlet-name>
  >     <url-pattern>/testconfig</url-pattern>
  > </servlet-mapping>
  > 
  ></web-app>
  >```
  >
  >- testconfig.jsp
  >
  >```jsp
  ><%@ page contentType="text/html;charset=UTF-8" language="java" %>
  >database-ip:<%=config.getInitParameter("database-ip")%>
  >```

- exception

  > - try.jsp
  >
  > ```jsp
  > 
  > <%@page
  >        contentType="text/html;charset=UTF-8" language="java"
  >        errorPage="catch.jsp"
  > %>
  > <%
  >        int[] a = new int[10];
  >        a[20]=2;
  > %>
  > ```
  >
  > - catch.jsp
  >
  > ```jsp
  > 
  > <%@ page
  >        contentType="text/html;charset=UTF-8" language="java"
  >        isErrorPage="true"
  > %>
  > <%=exception%> 
  > ```


####7、JSTL

> - set 
> - out
> - remove

  ```jsp
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  
  <%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"  %>
  
  <c:set var="name" value="${'AlexanderBai'}" scope="request"/>
  
  通过标签获取name：<c:out value="${name}"/><br>
  
  <c:remove var="name" scope="request"/><br>
  
  通过标签获取name：<c:out value="${name}"/><br>
  ```

- if-else

  - JSTL 通过**<c: if test** 进行条件判断，但是没有**<c:else** 语句，所以常使用**<c: if取反**

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
           pageEncoding="UTF-8" import="java.util.*"%>
  
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
  
  <c:set var="hp" value="${10}" scope="request" />
  
  <c:if test="${hp<5}">
      <p>这个英雄要挂了</p>
  </c:if>
  
  <c:if test="${!(hp<5)}">
      <p>这个英雄觉得自己还可以再抢救抢救</p>
  </c:if>
  
  <%
      pageContext.setAttribute("AlexanderBai", null);
      pageContext.setAttribute("lastwords", "");
      pageContext.setAttribute("items", new ArrayList());
  %>
  
  <c:if test="${empty AlexanderBai}">
      <p>没有装备武器</p>
  </c:if>
  <c:if test="${empty lastwords}">
      <p>挂了也没有遗言</p>
  </c:if>
  <c:if test="${empty items}">
      <p>物品栏为空</p>
  </c:if>
  ```

- choose

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
  	pageEncoding="UTF-8" import="java.util.*"%>
  
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
  
  <c:set var="hp" value="${3}" scope="request" />
  
  <c:choose>
      <c:when test="${hp<5}">
  		<p>这个英雄要挂了</p>
  	</c:when>
  	<c:otherwise>
  		<p>这个英雄觉得自己还可以再抢救抢救</p>
  	</c:otherwise>
  </c:choose>
  ```

- for Each

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
  	pageEncoding="UTF-8" import="java.util.*"%>
  
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
  
  <%
  	List<String> heros = new ArrayList<String>();
  	heros.add("塔姆");
  	heros.add("艾克");
  	heros.add("巴德");
  	heros.add("雷克赛");
  	heros.add("卡莉丝塔");
  	request.setAttribute("heros",heros);
  %>
  
  <!-- 使用jsp中的for循环来遍历List -->
  <table width="200px" align="center" border="1" cellspacing="0">
  <tr>
      <td>编号</td>
      <td>英雄</td>
  </tr>
    
  <%
  int i =0;
  for (String hero : heros) {
  i++;
  %>
   
  <tr>
      <td><%=i%></td>
      <td><%=hero%></td>
  </tr>
   
  <%}%>
    
  </table>
  
  <br>
  <!-- 使用JSTL中的c:forEach 循环来遍历List -->
  <table width="200px" align="center" border="1" cellspacing="0">
  <tr>
      <td>编号</td>
      <td>英雄</td>
  </tr>
    
  <c:forEach items="${heros}" var="hero" varStatus="st"  >
  	<tr>
  	    <td><c:out value="${st.count}" /></td>
  	    <td><c:out value="${hero}" /></td>
  	</tr>
  </c:forEach>
  </table>
  ```

  > - 运行结果
  >
  > | 编号 |   英雄   |
  > | :--: | :------: |
  > |  1   |   塔姆   |
  > |  2   |   艾克   |
  > |  3   |   巴德   |
  > |  4   |  雷克赛  |
  > |  5   | 卡莉丝塔 |
  >
  > | 编号 |   英雄   |
  > | :--: | :------: |
  > |  1   |   塔姆   |
  > |  2   |   艾克   |
  > |  3   |   巴德   |
  > |  4   |  雷克赛  |
  > |  5   | 卡莉丝塔 |

- for Tokens

  - 用于字符串拆分，可以指定多个分割符

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8" import="java.util.*"%>
   
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
   
  <c:set var="heros" value="塔姆,艾克;巴德|雷克赛!卡莉丝塔" /> 
  
  <c:forTokens items="${heros}" delims=":;|!" var="hero">
  	<c:out value="${hero}" /> <br />
  </c:forTokens>
  ```

- fmt:formNUmber 格式化数字

  - **<fmt:formatNumber** 表示格式化数字
  - **minFractionDigits** 小数点至少要有的位数
  - **maxFractionDigits** 小数点最多能有的位数

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8" import="java.util.*"%>
      
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
  <%@ taglib uri="http://java.sun.com/jsp/jstl/fmt"  prefix='fmt' %>  
  
  <c:set var="money" value="888.8" />
  <c:set var="pi" value="3.1415926" />
  最少两个小数点:
  <fmt:formatNumber type="number" value="${money}" minFractionDigits="2"/>
  <br>
  最多两个小数点:
  <fmt:formatNumber type="number" value="${pi}" maxFractionDigits="2" />
  ```

- frm:formatDate 格式化日期

  -  **<fmt:formatDate** 表示格式化日期

  | **yyyy** 表示年份 | a 表示是上午还是下午 |
  | :---------------: | :------------------: |
  |  **MM** 表示月份  |   **HH** 表示小时    |
  |  **dd** 表示日期  |   **mm** 表示分钟    |
  | **E **表示星期几  |    **ss** 表示秒     |
  |  **S** 表示毫秒   |    **z** 表示时区    |

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8" import="java.util.*"%>
      
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
  <%@ taglib uri="http://java.sun.com/jsp/jstl/fmt"  prefix='fmt' %>  
  
  <%
  	Date now = new Date();
  	pageContext.setAttribute("now",now);
  %>
  
  完整日期: <fmt:formatDate value="${now}" pattern="G yyyy年MM月dd日 E"/><br>
  完整时间: <fmt:formatDate value="${now}" pattern="a HH:mm:ss.S z"/><br>
  常见格式: <fmt:formatDate value="${now}" pattern="yyyy-MM-dd HH:mm:ss"/>
  ```

  运行结果：![1553309769371](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553309769371.png)

- fn:

#### 8、EL

- 作用域优先级
  - 根据pageContext>request>session>application的顺序

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>
 
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
 
<c:set var="name" value="${'AlexanderBai-pageContext'}" scope="page" />
<c:set var="name" value="${'AlexanderBai-request'}" scope="request" />
<c:set var="name" value="${'AlexanderBai-session'}" scope="session" />
<c:set var="name" value="${'AlexanderBai-application'}" scope="application" />
 
4个作用域都有name,优先获取出来的是 ： ${name}
```

运行结果：![1553310060044](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553310060044.png)

-  结合JSTL的**<c:forEach**

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8" import="java.util.*"%>
   
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
   
  <%
      List<String> heros = new ArrayList<String>();
      heros.add("塔姆");
      heros.add("艾克");
      heros.add("巴德");
      heros.add("雷克赛");
      heros.add("卡莉丝塔");
      request.setAttribute("heros",heros);
  %>
   
  <table width="200px" align="center" border="1" cellspacing="0">
  <tr>
      <td>编号</td>
      <td>英雄</td>
  </tr>
     
  <c:forEach items="${heros}" var="hero" varStatus="st"  >
      <tr>
          <td>${st.count}</td>
          <td>${hero}</td>
      </tr>
  </c:forEach>
  </table>
  ```

运行结果：![1553310437642](C:\Users\AlexanderBai\AppData\Roaming\Typora\typora-user-images\1553310437642.png)

- eq（equal的缩写）:进行条件判断

  ```jsp
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8" import="java.util.*"%>
   
  <%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
  
  <%
  request.setAttribute("killNumber", "10");
  %>
  
  c:if 的用法，运行结果：
  <c:if test="${killNumber>=10}">
  超神
  </c:if>
  <c:if test="${killNumber<10}">
  还没超神
  </c:if>
  <br>
  c:choose 的用法，运行结果：
  
  <c:choose>
      <c:when test="${killNumber>=10}">
      	超神
  	</c:when>
  	<c:otherwise>
  		还没超神
  	</c:otherwise>
  </c:choose>
  <br>
  EL表达式eq的用法，运行结果：
  ${killNumber ge 10? "超神":"还没超神" }
  ```

  



  

  

  

  

  

  

  





