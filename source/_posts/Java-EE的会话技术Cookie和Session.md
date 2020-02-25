---
title: Java_EE的会话技术Cookie和Session
date: 2020-02-25 10:55:46
tags: [JavaWeb]
category: JavaWeb
---
## 一、会话技术
1、**会话：一次会话中包含多次请求和响应。**

* 一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止

2、功能：**在一次会话的范围内的多次请求间，共享数据**

3、方式：
* 客户端会话技术：**Cookie**
* 服务器端会话技术：**Session**


## 二、Cookie
### 1、概念
**客户端会话技术，将数据保存到客户端**

### 2、快速入门
* 使用步骤：
	* **创建Cookie对象，绑定数据**
		* `new Cookie(String name, String value) `
	* **发送Cookie对象**
		* `response.addCookie(Cookie cookie) `
	* **获取Cookie，拿到数据**
		* `Cookie[]  request.getCookies()`  


### 3、实现原理
* 基于**响应头set-cookie**和**请求头cookie**实现
![image](Java_EE的会话技术Cookie和Session/1.png)

### 4、cookie的细节
（1） **一次可不可以发送多个cookie?**
* 可以
* 实现：可以**创建多个Cookie对象**，**使用response调用多次addCookie方法**发送cookie即可。
<hr>

（2） **cookie在浏览器中保存多长时间？**
1. **默认情况下，当浏览器关闭后，Cookie数据被销毁**
2. 持久化存储：
	* `setMaxAge(int seconds)`
		* **正数**：将Cookie数据写到硬盘的文件中。持久化存储。并指定cookie存活时间，时间到后，cookie文件自动失效
		* **负数：默认值**
		* **零：删除cookie信息**
<hr>

（3） **cookie能不能存中文？**
* 在tomcat 8 之前 cookie中不能直接存储中文数据。
	* **需要将中文数据转码---一般采用URL编码**
* **在tomcat 8 之后，cookie支持中文数据**。特殊字符还是不支持，建议使用URL编码存储，URL解码解析
<hr>

（4） **cookie共享问题**
* 假设在一个tomcat服务器中，部署了多个web项目，那么在这些web项目中cookie能不能共享？
	* **默认情况下cookie不能共享**
	* `setPath(String path)`:设置cookie的获取范围。默认情况下，设置当前的虚拟目录
		* **如果要共享，则可以将path设置为"/"**


* **不同的tomcat服务器间cookie共享问题**？
	* `setDomain(String path)`:如果设置一级域名相同，那么多个服务器之间cookie可以共享
		* `setDomain(".baidu.com")`,**那么tieba.baidu.com和news.baidu.com中cookie可以共享**

### 5、Cookie的特点和作用
* **cookie存储数据在客户端浏览器**
* **浏览器对于单个cookie 的大小有限制(4kb) 以及 对同一个域名下的总cookie数量也有限制(20个)**

* 作用：
	* **cookie一般用于存出少量的不太敏感的数据**
	* 在**不登录**的情况下，完成**服务器对客户端的身份识别**



### 6、案例：记录上一次访问时间
#### (1)需求
	1. 访问一个Servlet，如果是第一次访问，则提示：您好，欢迎您首次访问。
	2. 如果不是第一次访问，则提示：欢迎回来，您上次访问时间为:显示时间字符串

#### (2)分析：
* 可以采用Cookie来完成
* **在服务器中的Servlet判断是否有一个名为lastTime的cookie**
	* 有：不是第一次访问
		* 响应数据：欢迎回来，您上次访问时间为:2020年2月24日10:25:20
		* 写回Cookie：lastTime=2020年2月25日10:25:01
	* 没有：是第一次访问
		* 响应数据：您好，欢迎您首次访问
		* 写回Cookie：lastTime=2020年2月25日10:25:01
![image](Java_EE的会话技术Cookie和Session/2.png)

#### (3)代码实现
```
package com.allen.cookie;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.net.URLDecoder;
import java.net.URLEncoder;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 在服务器中的Servlet判断是否有一个名为lastTime的cookie
 1. 有：不是第一次访问
     1. 响应数据：欢迎回来，您上次访问时间为...
     2. 写回Cookie：lastTime=...
 2. 没有：是第一次访问
     1. 响应数据：您好，欢迎您首次访问
     2. 写回Cookie：lastTime=...
 */
@WebServlet("/cookieTest")
public class CookieTest extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //设置响应的消息体的数据格式以及编码
        response.setContentType("text/html;charset=utf-8");
        //1、获取所有Cookie
        Cookie[] cookies = request.getCookies();
        boolean flag = false;//没有cookie为lastTime
        //2、遍历cookie数组
        if (cookies != null){
            for (Cookie cookie : cookies) {
                //3、获取Cookie的名称
                String name = cookie.getName();
                //4、判断名称是否有：last Time
                if ("lastTime".equals(name)){
                    //有该Cookie，不是第一次访问

                    flag = true;//有该Cookie
                    //响应数据
                    //获取cookie的value，时间
                    String value = cookie.getValue();
                    System.out.println("解码前："+value);
                    //URL解码：
                    value = URLDecoder.decode(value,"utf-8");
                    System.out.println("解码后："+value);
                    response.getWriter().write("<h1>欢迎回来，您上次访问的时间为：" + value + "</h1>");


                    //设置Cookie的value
                    //获取当前时间的字符串，重新设置Cookie的值，重新发送cookie
                    Date date = new Date();
                    SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
                    String str_date = sdf.format(date);
                    System.out.println("编码前："+str_date);
                    //URL编码
                    str_date = URLEncoder.encode(str_date,"utf-8");
                    System.out.println("编码后："+str_date);
                    cookie.setValue(str_date);
                    //设置cookie的存活时间
                    cookie.setMaxAge(60*60*24*30);//一个月
                    response.addCookie(cookie);

                    break;
                }
            }
        }
        if (cookies == null || cookies.length == 0 || flag == false){
            //没有，第一次访问

            //设置Cookie的value
            //获取当前时间的字符串，重新设置Cookie的值，重新发送cookie
            Date date = new Date();
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
            String str_date = sdf.format(date);
            System.out.println("编码前："+str_date);
            //URL编码
            str_date = URLEncoder.encode(str_date,"utf-8");
            System.out.println("编码后："+str_date);
            Cookie cookie = new Cookie("lastTime",str_date);
            cookie.setValue(str_date);
            //设置cookie的存活时间
            cookie.setMaxAge(60*60*24*30);//一个月
            response.addCookie(cookie);

            response.getWriter().write("<h1>您好，欢迎您首次访问</h1>");

        }
    }


    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}


```

## 三、Session
### 1、概念
**服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中**。HttpSession

### 2、快速入门
* 获取HttpSession对象：
	`HttpSession session = request.getSession();`
* 使用HttpSession对象：
    * `Object getAttribute(String name)  `
	* `void setAttribute(String name, Object value)`
	* `void removeAttribute(String name)  `

### 3、原理
* **Session的实现是依赖于Cookie的。**
![image](Java_EE的会话技术Cookie和Session/3.png)

### 4、细节
（1） **当客户端关闭后，服务器不关闭，两次获取session是否为同一个**？
* **默认情况下。不是。**
* 如果需要相同，则可以创建Cookie,键为JSESSIONID，**设置最大存活时间，让cookie持久化保存**。
```
 Cookie c = new Cookie("JSESSIONID",session.getId());
 c.setMaxAge(60*60);
 response.addCookie(c);
```
<hr>

（2） **客户端不关闭，服务器关闭后，两次获取的session是同一个吗？**
* **不是同一个，但是要确保数据不丢失tomcat自动完成以下工作：**
	* **session的钝化**：
		* 在服务器正常关闭之前，将session对象系列化到硬盘上
	* **session的活化**：
		* 在服务器启动后，将session文件转化为内存中的session对象即可。
*IDEA中无法完成活化*
<hr>

（3） **session什么时候被销毁？**
* 服务器关闭
* session对象调用`invalidate()` 。
* session默认失效时间 30分钟
	* **选择性配置修改**
	```
	<session-config>
        <session-timeout>30</session-timeout>
    </session-config>
    ```

### 5、session的特点
 1. **session用于存储一次会话的多次请求的数据，存在服务器端**
 2. **session可以存储任意类型，任意大小的数据**

* **Session与Cookie的区别：**
	* **session存储数据在服务器端，Cookie在客户端**
	* **session没有数据大小限制，Cookie有**
	* **session数据安全，Cookie相对于不安全**


## 四、案例：验证码
### 1、案例需求
	1. 访问带有验证码的登录页面login.jsp
	2. 用户输入用户名，密码以及验证码。
		* 如果用户名和密码输入有误，跳转登录页面，提示:用户名或密码错误
		* 如果验证码输入有误，跳转登录页面，提示：验证码错误
		* 如果全部输入正确，则跳转到主页success.jsp，显示：用户名,欢迎您

### 2、分析
![image](Java_EE的会话技术Cookie和Session/4.png)	

### 3、代码实现
[已上传到GitHub上](https://github.com/AllenXueKY/JavaWeb/tree/master/day09_cookie%26session)
（*备注：此项目中包括本篇Blog中的所有案例以及应用*）

### 4、效果展示
* 验证码错误时：
![image](Java_EE的会话技术Cookie和Session/5.png)
* 用户名或密码错误时：
![image](Java_EE的会话技术Cookie和Session/6.png)
* 用户名、密码和验证码都正确时：
![image](Java_EE的会话技术Cookie和Session/7.png)