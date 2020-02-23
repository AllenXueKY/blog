---
title: Java_EE与HTTP的邂逅
date: 2020-02-23 18:08:15
tags: [JavaWeb]
category: JavaWeb
---
## 一、HTTP的介绍
### 1、概念
**Hyper Text Transfer Protocol 超文本传输协议**
* 传输协议：定义了客户端和服务器端通信时，发送数据的格式
* 特点：
	* 基于TCP/IP的高级协议
	* 默认端口号:80
	* 基于请求/响应模型的:一次请求对应一次响应
	* 无状态的：每次请求之间相互独立，不能交互数据

* 历史版本：
	* 1.0：每一次请求响应都会建立新的连接
	* **1.1：复用连接**

![image](Java_EE与HTTP的邂逅/1.png)

### 2、请求消息数据格式
**（1）请求行**

        请求方式 请求url 请求协议/版本
        GET /login.html	HTTP/1.1

* 请求方式：
	* HTTP协议有7中请求方式，常用的有2种
		* GET：
			* 请求参数在请求行中，在url后。
			* 请求的url长度有限制的
			* 不太安全
		* POST：
			* 请求参数在请求体中
			* 请求的url长度没有限制的
			* 相对安全
 
**（2）请求头：客户端浏览器告诉服务器一些信息**

请求头名称: 请求头值
* 常见的请求头：
	* `User-Agent`：浏览器告诉服务器，我访问你使用的浏览器版本信息
		* 可以在服务器端获取该头的信息，解决浏览器的兼容性问题

	* `Referer：http://localhost/login.html`
		* 告诉服务器，我(当前请求)从哪里来？
			* 作用：
				1. 防盗链；
				2. 统计工作；
    ![image](Java_EE与HTTP的邂逅/4.png)

**（3）请求空行**

空行，就是用于分割POST请求的请求头和请求体的。

**（4）请求体(正文)**

* 封装POST请求消息的请求参数的

* 字符串格式：
```
	POST /login.html	HTTP/1.1
	Host: localhost
	User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:60.0) Gecko/20100101 Firefox/60.0
	Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
	Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
	Accept-Encoding: gzip, deflate
	Referer: http://localhost/login.html
	Connection: keep-alive
	Upgrade-Insecure-Requests: 1
	
	username=zhangsan	

```
![image](Java_EE与HTTP的邂逅/2.png)
### 3、响应消息数据格式
**响应消息：服务器端发送给客户端的数据**

**（1）响应行**
* 组成：协议/版本 响应状态码 状态码描述
* 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态。
	* 状态码都是3位数字 
	* 分类：

	
	1xx：服务器就收客户端消息，但没有接受完成，等待一段时间后，发送1xx多状态码
	2xx：成功。代表：200
	3xx：重定向。代表：302(重定向)，304(访问缓存)
	4xx：客户端错误。
		* 代表：
			* 404（请求路径没有对应的资源） 
			* 405：请求方式没有对应的doXxx方法
	5xx：服务器端错误。
        * 代表：500(服务器内部出现异常)
			
	
**（2）响应头**
* 格式：`头名称： 值`
* 常见的响应头：
	* `Content-Type`：服务器告诉客户端本次响应体数据格式以及编码格式
	* `Content-disposition`：服务器告诉客户端以什么格式打开响应体数据
		* 值：
			* `in-line`:默认值,在当前页面内打开
			* `attachment;filename=xxx`：以附件形式打开响应体。文件下载

**（3）响应空行**

空行，就是用于分割响应头和响应体的。

**（4）响应体:传输的数据**

* 响应字符串格式：
```
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 101
Date: Wed, 06 Jun 2018 07:08:42 GMT

<html>
<head>
<title>$Title$</title>
</head>
<body>
hello , response
</body>
</html>

```
![image](Java_EE与HTTP的邂逅/3.png)

## 二、Java_EE中的Request（请求）和Response（响应）
### 1. request对象和response对象的原理
* request和response对象是**由服务器创建**的。我们来使用它们
* **request对象是来获取请求消息**，**response对象是来设置响应消息**
![image](Java_EE与HTTP的邂逅/7.png)
### 2. request对象继承体系结构	
	ServletRequest		--	接口
		|	继承
	HttpServletRequest	-- 接口
		|	实现
	org.apache.catalina.connector.RequestFacade 类(tomcat)

### 3. request功能
**（1）获取请求消息数据**

	1. 获取请求行数据
		* GET /day14/demo1?name=zhangsan HTTP/1.1
		* 方法：
			1. 获取请求方式 ：GET
				* String getMethod()  
			2. (*)获取虚拟目录：/day08
				* String getContextPath()
			3. 获取Servlet路径: /demo1
				* String getServletPath()
			4. 获取get方式请求参数：name=zhangsan
				* String getQueryString()
			5. (*)获取请求URI：/day08/demo1
				* String getRequestURI():		/day14/demo1
				* StringBuffer getRequestURL()  :http://localhost/day14/demo1

				* URL:统一资源定位符 ： http://localhost/day14/demo1	中华人民共和国
				* URI：统一资源标识符 : /day14/demo1					共和国
			
			6. 获取协议及版本：HTTP/1.1
				* String getProtocol()

			7. 获取客户机的IP地址：
				* String getRemoteAddr()
			
	2. 获取请求头数据
		* 方法：
			* (*)String getHeader(String name):通过请求头的名称获取请求头的值
			* Enumeration<String> getHeaderNames():获取所有的请求头名称
		
	3. 获取请求体数据:
		* 请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
		* 步骤：
			1. 获取流对象
				*  BufferedReader getReader()：获取字符输入流，只能操作字符数据
				*  ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据

			2. 再从流对象中拿数据
	
**（2）其他功能**

![image](Java_EE与HTTP的邂逅/5.png)

	1. 获取请求参数通用方式：不论get还是post请求方式都可以使用下列方法来获取请求参数
		1. String getParameter(String name):根据参数名称获取参数值    username=zs&password=123
		2. String[] getParameterValues(String name):根据参数名称获取参数值的数组  hobby=xx&hobby=game
		3. Enumeration<String> getParameterNames():获取所有请求的参数名称
		4. Map<String,String[]> getParameterMap():获取所有参数的map集合

		* 中文乱码问题：
			* get方式：tomcat 8 已经将get方式乱码问题解决了
			* post方式：会乱码
				* 解决：在获取参数前，设置request的编码request.setCharacterEncoding("utf-8");
	
	2. 请求转发：一种在服务器内部的资源跳转方式
		1. 步骤：
			1. 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)
			2. 使用RequestDispatcher对象来进行转发：forward(ServletRequest request, ServletResponse response) 

		2. 特点：
			1. 浏览器地址栏路径不发生变化
			2. 只能转发到当前服务器内部资源中。
			3. 转发是一次请求


	3. 共享数据：
		* 域对象：一个有作用范围的对象，可以在范围内共享数据
		* request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
		* 方法：
			1. void setAttribute(String name,Object obj):存储数据
			2. Object getAttitude(String name):通过键获取值
			3. void removeAttribute(String name):通过键移除键值对

	4. 获取ServletContext：
		* ServletContext getServletContext()

	
### 4、案例：简单的用户登录
* 用户登录案例需求：

    	1.编写login.html登录页面
    		username & password 两个输入框
    	2.使用Druid数据库连接池技术,操作mysql，day14数据库中user表
    	3.使用JdbcTemplate技术封装JDBC
    	4.登录成功跳转到SuccessServlet展示：登录成功！用户名,欢迎您
    	5.登录失败跳转到FailServlet展示：登录失败，用户名或密码错误
![image](Java_EE与HTTP的邂逅/6.png)
* 开发步骤：<br>
**1、创建项目，导入html页面，配置文件，jar包**<br>
**2、创建数据库环境**
    * sql语句如下：
    ```
    CREATE DATABASE day14;
    USE day14;
    CREATE TABLE USER(
    
    	id INT PRIMARY KEY AUTO_INCREMENT,
    	username VARCHAR(32) UNIQUE NOT NULL,
    	PASSWORD VARCHAR(32) NOT NULL
    );
    ```
    **3、创建包com.allen.domain,创建类User**

        *包名按照自己的习惯*
    * 实体类代码如下：
    ```
    	package com.allen.domain;
    	/**
    	 * 用户的实体类
    	 */
    	public class User {
    	
    	    private int id;
    	    private String username;
    	    private String password;
    	
    	
    	    public int getId() {
    	        return id;
    	    }
    	
    	    public void setId(int id) {
    	        this.id = id;
    	    }
    	
    	    public String getUsername() {
    	        return username;
    	    }
    	
    	    public void setUsername(String username) {
    	        this.username = username;
    	    }
    	
    	    public String getPassword() {
    	        return password;
    	    }
    	
    	    public void setPassword(String password) {
    	        this.password = password;
    	    }
    	
    	    @Override
    	    public String toString() {
    	        return "User{" +
    	                "id=" + id +
    	                ", username='" + username + '\'' +
    	                ", password='" + password + '\'' +
    	                '}';
    	    }
    	}
    ```
    **4、创建包com.allen.util,编写工具类JDBCUtils**
	```
	package com.allen.util;

	import com.alibaba.druid.pool.DruidDataSourceFactory;
	
	import javax.sql.DataSource;
	import javax.xml.crypto.Data;
	import java.io.IOException;
	import java.io.InputStream;
	import java.sql.Connection;
	import java.sql.SQLException;
	import java.util.Properties;
	
	/**
	 * JDBC工具类 使用Durid连接池
	 */
	public class JDBCUtils {
	
	    private static DataSource ds ;
	
	    static {
	
	        try {
	            //1.加载配置文件
	            Properties pro = new Properties();
	            //使用ClassLoader加载配置文件，获取字节输入流
	            InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
	            pro.load(is);
	
	            //2.初始化连接池对象
	            ds = DruidDataSourceFactory.createDataSource(pro);
	
	        } catch (IOException e) {
	            e.printStackTrace();
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	
	    /**
	     * 获取连接池对象
	     */
	    public static DataSource getDataSource(){
	        return ds;
	    }
	
	
	    /**
	     * 获取连接Connection对象
	     */
	    public static Connection getConnection() throws SQLException {
	        return  ds.getConnection();
	    }
	}
	```
    **5、创建包com.allen.dao,创建类UserDao,提供login方法**
	```
	package com.allen.dao;

	import com.allen.domain.User;
	import com.allen.util.JDBCUtils;
	import org.springframework.dao.DataAccessException;
	import org.springframework.jdbc.core.BeanPropertyRowMapper;
	import org.springframework.jdbc.core.JdbcTemplate;
	
	/**
	 * 操作数据库中User表的类
	 */
	public class UserDao {
	
	    //声明JDBCTemplate对象共用
	    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
	
	    /**
	     * 登录方法
	     * @param loginUser 只有用户名和密码
	     * @return user包含用户全部数据,没有查询到，返回null
	     */
	    public User login(User loginUser){
	        try {
	            //1.编写sql
	            String sql = "select * from user where username = ? and password = ?";
	            //2.调用query方法
	            User user = template.queryForObject(sql,
	                    new BeanPropertyRowMapper<User>(User.class),
	                    loginUser.getUsername(), loginUser.getPassword());
	
	
	            return user;
	        } catch (DataAccessException e) {
	            e.printStackTrace();//记录日志
	            return null;
	        }
	    }
	}
    ```
    **6、编写com.allen.web.servlet.LoginServlet类**
	```
	package com.allen.web.servlet;

	import com.allen.dao.UserDao;
	import com.allen.domain.User;
	
	import javax.servlet.ServletException;
	import javax.servlet.annotation.WebServlet;
	import javax.servlet.http.HttpServlet;
	import javax.servlet.http.HttpServletRequest;
	import javax.servlet.http.HttpServletResponse;
	import java.io.IOException;
	
	
	@WebServlet("/loginServlet")
	public class LoginServlet extends HttpServlet {
	
	
	    @Override
	    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	        //1.设置编码
	        req.setCharacterEncoding("utf-8");
	        //2.获取请求参数
	        String username = req.getParameter("username");
	        String password = req.getParameter("password");
	        //3.封装user对象
	        User loginUser = new User();
	        loginUser.setUsername(username);
	        loginUser.setPassword(password);
	
	        //4.调用UserDao的login方法
	        UserDao dao = new UserDao();
	        User user = dao.login(loginUser);
	
	        //5.判断user
	        if(user == null){
	            //登录失败
	            req.getRequestDispatcher("/failServlet").forward(req,resp);
	        }else{
	            //登录成功
	            //存储数据
	            req.setAttribute("user",user);
	            //转发
	            req.getRequestDispatcher("/successServlet").forward(req,resp);
	        }
	
	    }
	
	    @Override
	    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
	        this.doGet(req,resp);
	    }
	}
    ```
    **7、编写FailServlet和SuccessServlet类**
    ```
	@WebServlet("/successServlet")
	public class SuccessServlet extends HttpServlet {
	    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	        //获取request域中共享的user对象
	        User user = (User) request.getAttribute("user");
	
	        if(user != null){
	            //给页面写一句话
	
	            //设置编码
	            response.setContentType("text/html;charset=utf-8");
	            //输出
	            response.getWriter().write("登录成功！"+user.getUsername()+",欢迎您");
	        }
	
	
	    }		


	@WebServlet("/failServlet")
	public class FailServlet extends HttpServlet {
	    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	        //给页面写一句话
	
	        //设置编码
	        response.setContentType("text/html;charset=utf-8");
	        //输出
	        response.getWriter().write("登录失败，用户名或密码错误");
	
	    }
	
	    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
	        this.doPost(request,response);
	    }
	}
    ```
    **8、login.html中form表单的action路径的写法**
	    * 虚拟目录+Servlet的资源路径
    **9、BeanUtils工具类，简化数据封装**
    
    	* 用于封装JavaBean的
    	* JavaBean：标准的Java类
    		* 要求：
    			1. 类必须被public修饰
    			2. 必须提供空参的构造器
    			3. 成员变量必须使用private修饰
    			4. 提供公共setter和getter方法
    		* 功能：封装数据
    	* 概念：
    		成员变量：
    		属性：setter和getter方法截取后的产物
    			例如：getUsername() --> Username--> username
    	* 方法：
    		1. setProperty()
    		2. getProperty()
    		3. populate(Object obj , Map map):将map集合的键值对信息，封装到对应的JavaBean对象中

### 5、Response对象
#### 功能：设置响应消息
* 设置响应行
    * 格式：`HTTP/1.1 200 ok`
	* **设置状态码：setStatus(int sc)**
* 设置响应头：`setHeader(String name, String value)` 
* 设置响应体：
	* 使用步骤：
		* 获取输出流
			* 字符输出流：`PrintWriter getWriter()`

			* 字节输出流：`ServletOutputStream getOutputStream()`

		* 使用输出流，将数据输出到客户端浏览器


### 6、Response相关案例
#### （1）完成重定向
* 重定向：资源跳转的方式
![image](Java_EE与HTTP的邂逅/9.png)
* 代码实现：
```
//1. 设置状态码为302
response.setStatus(302);
//2.设置响应头location
response.setHeader("location","/day08/responseDemo2");


//简单的重定向方法
response.sendRedirect("/day08/responseDemo2");
```
* **重定向(redirect)的特点**:
	* 地址栏发生变化
	* 重定向可以访问其他站点(服务器)的资源
	* 重定向是两次请求。不能使用request对象来共享数据
* **转发(forward)的特点**：
	* 转发地址栏路径不变
	* 转发只能访问当前服务器下的资源
	* 转发是一次请求，可以使用request对象来共享数据

*以上两个特点可能会出面试题：forward 和  redirect 区别*
	
	
* 路径写法：

        * 路径分类
         1、相对路径：通过相对路径不可以确定唯一资源
        		* 如：`./index.html`
        	（1）不以/开头，以.开头路径
        
        	（2）规则：找到当前资源和目标资源之间的相对位置关系
        			* `./`：当前目录
        			* `../`:后退一级目录
        2、绝对路径：通过绝对路径可以确定唯一资源
        		* 如：`http://localhost/day08/responseDemo2`		`/day08/responseDemo2`
        	（1）以/开头的路径
        
        	（2）规则：判断定义的路径是给谁用的？判断请求将来从哪儿发出
            	a、给客户端浏览器使用：需要加虚拟目录(项目的访问路径)
        			* 建议虚拟目录动态获取：request.getContextPath()
        			* <a> , <form> 重定向...
        		b、给服务器使用：不需要加虚拟目录
        			* 转发路径

#### （2）服务器输出字符数据到浏览器
* 步骤：
	* 获取字符输出流
	* 输出数据
```
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

@WebServlet("/responseDemo4")
public class ResponseDemo4 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //获取流对象之前，设置流的默认编码：ISO-8859-1 设置为：GBK
         //response.setCharacterEncoding("utf-8");

        //告诉浏览器，服务器发送的消息体数据的编码。建议浏览器使用该编码解码
        response.setHeader("content-type","text/html;charset=utf-8");

       //简单的形式来设置编码
        response.setContentType("text/html;charset=utf-8");


        //1、获取字符输出流
        PrintWriter writer = response.getWriter();
        //2、输出数据
        //writer.write("<h1>Hello response</h1>");
        writer.write("你好 response");
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}

```
* 注意：
	* 乱码问题：

    		1. PrintWriter pw = response.getWriter();获取的流的默认编码是ISO-8859-1
    		2. 设置该流的默认编码
    		3. 告诉浏览器响应体使用的编码

    		//简单的形式，设置编码，是在获取流之前设置
    		response.setContentType("text/html;charset=utf-8");
![image](Java_EE与HTTP的邂逅/8.png)
#### （3）服务器输出字节数据到浏览器
* 步骤：
	1. 获取字节输出流
	2. 输出数据
```
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/responseDemo5")
public class ResponseDemo5 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html;charset=utf-8");
        //1、获取字节输出流
        ServletOutputStream outputStream = response.getOutputStream();
        //2、输出数据
        outputStream.write("你好".getBytes("utf-8"));

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}

```

#### （4）简单的验证码生成器
* 验证码本质：图片
* 验证码的目的：防止恶意表单注册
* 代码实现请访问[https://blog.csdn.net/qq_34504626/article/details/104463928](https://blog.csdn.net/qq_34504626/article/details/104463928)


### 7、ServletContext对象
#### （1）概念
代表整个web应用，可以和程序的容器(服务器)来通信

#### （2）获取
* 通过request对象获取
	`request.getServletContext();`
* 通过HttpServlet获取
	`this.getServletContext();`

#### （3）功能
* **获取MIME类型**：
	* MIME类型:在互联网通信过程中定义的一种文件数据类型
		* 格式： 大类型/小类型   `text/html	`	`image/jpeg`

	* 获取：`String getMimeType(String file) `
* 域对象：共享数据
	* `setAttribute(String name,Object value)`
	* `getAttribute(String name)`
	* `removeAttribute(String name)`

	* **ServletContext对象范围：所有用户所有请求的数据**
* 获取文件的真实(服务器)路径
	* 方法：`String getRealPath(String path)  `
	```	 
    String b = context.getRealPath("/b.txt");//web目录下资源访问
     System.out.println(b);

    String c = context.getRealPath("/WEB-INF/c.txt");//WEB-INF目录下的资源访问
    System.out.println(c);

    String a = context.getRealPath("/WEB-INF/classes/a.txt");//src目录下的资源访问
    System.out.println(a);
    ```


### 8、简单案例
* 文件下载需求：
	* 页面显示超链接
	* 点击超链接后弹出下载提示框
	* 完成图片文件下载

* 分析：

    	1、超链接指向的资源如果能够被浏览器解析，则在浏览器中展示，如果不能解析，则弹出下载提示框。不满足需求
    	2、任何资源都必须弹出下载提示框
    	3、使用响应头设置资源的打开方式：
    		* `content-disposition:attachment;filename=xxx`

* 步骤：

    	1. 定义页面，编辑超链接href属性，指向Servlet，传递资源名称filename
    	2. 定义Servlet
    		1. 获取文件名称
    		2. 使用字节输入流加载文件进内存
    		3. 指定response的响应头： content-disposition:attachment;filename=xxx
    		4. 将数据写出到response输出流


* 问题：
	* 中文文件问题

        	* 解决思路：
        		1. 获取客户端使用的浏览器版本信息
        		2. 根据不同的版本信息，设置filename的编码方式不同

* 代码实现：

1、简单的html页面定义
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

    <a href="/day08/img/1.jpg">图片1</a>
    <hr>
    <a href="/day08/downloadServlet?filename=九尾.jpg">图片1</a>
    <hr>
</body>
</html>
```
2、DownLoadUtils工具类定义（根据不同浏览器的解析方式解决中文乱码问题）
```
package com.allen.web.utils;

import sun.misc.BASE64Encoder;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;


public class DownLoadUtils {

    public static String getFileName(String agent, String filename) throws UnsupportedEncodingException {
        if (agent.contains("MSIE")) {
            // IE浏览器
            filename = URLEncoder.encode(filename, "utf-8");
            filename = filename.replace("+", " ");
        } else if (agent.contains("Firefox")) {
            // 火狐浏览器
            BASE64Encoder base64Encoder = new BASE64Encoder();
            filename = "=?utf-8?B?" + base64Encoder.encode(filename.getBytes("utf-8")) + "?=";
        } else {
            // 其它浏览器
            filename = URLEncoder.encode(filename, "utf-8");
        }
        return filename;
    }
}

```
3、DownloadServlet.java
```
package com.allen.web.download;

import com.allen.web.utils.DownLoadUtils;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletOutputStream;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.FileInputStream;
import java.io.IOException;

@WebServlet("/downloadServlet")
public class DownloadServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1、获取请求参数，文件名称
        String filename = request.getParameter("filename");
        //2、使用字节输入流加载文件进内存
        //2.1、找到文件服务器路径
        ServletContext servletContext = this.getServletContext();
        String realPath = servletContext.getRealPath("/img/" + filename);
        //2.2、用字节流关联
        FileInputStream fls = new FileInputStream(realPath);

        //3、设置response的响应头
        //3.1、设置响应头类型：content-type
        String mimeType = servletContext.getMimeType(filename);
        response.setHeader("content-type",mimeType);
        //3.2、设置响应头打开方式:content-disposition

        //解决中文文件名问题
        //1、获取user-agent请求头
        String agent = request.getHeader("user-agent");
        //2、使用工具类方法编码文件名即可
        filename = DownLoadUtils.getFileName(agent, filename);

        response.setHeader("content-disposition","attachment;filename="+filename);
        //4、将输入流的数据写出到输出流中
        ServletOutputStream sos = response.getOutputStream();
        byte[] buff = new byte[1024 * 8];
        int len = 0;
        while ((len = fls.read(buff)) != -1){
            sos.write(buff,0,len);
        }
        fls.close();

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}

```
![image](Java_EE与HTTP的邂逅/10.png)