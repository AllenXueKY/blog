---
title: Java_EE的动态网页技术JSP
date: 2020-02-25 21:18:48
tags: [JavaWeb]
category: JavaWeb
---
## 一、JSP的入门学习
### 1、概念
* **Java Server Pages： java服务器端页面**
	* 可以理解为：**一个特殊的页面**，其中既可以指定定义**html标签**，又可以定义**java代码**
	* 用于**简化**书写！！！

### 2、原理
* **JSP本质上就是一个Servlet**
![image](Java_EE的动态网页技术JSP/1.png)

### 3、JSP的脚本：JSP定义Java代码的方式
* `<%  代码 %>`：**定义的java代码，在service方法中。service方法中可以定义什么，该脚本中就可以定义什么。**
* `<%! 代码 %>`：**定义的java代码，在jsp转换后的java类的成员位置。**
* `<%= 代码 %>`：**定义的java代码，会输出到页面上。输出语句中可以定义什么，该脚本中就可以定义什么。**


### 4、JSP的内置对象
* **内置对象：在jsp页面中不需要获取和创建，可以直接使用的对象**
* jsp一共有**9个内置对象**。
* 先入门学习3个：
	* **request**
	* **response**
	* **out**：字符输出流对象。可以将数据输出到页面上。和`response.getWriter()`类似。
		* `response.getWriter()`和`out.write()`的**区别**：
			* 在tomcat服务器真正给客户端做出响应之前，会先找response缓冲区数据，再找out缓冲区数据。
			* `response.getWriter()`数据输出永远在`out.write()`之前

### 5. 案例:改造Cookie案例
* Cookie案例：记录上一次访问时间
* Cookie实现的代码和分析请访问[Java_EE的会话技术Cookie&Session](https://blog.csdn.net/qq_34504626/article/details/104493207)
* 使用jsp实现，代码如下：
```
<%@ page import="java.net.URLDecoder" %>
<%@ page import="java.util.Date" %>
<%@ page import="java.text.SimpleDateFormat" %>
<%@ page import="java.net.URLEncoder" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>allen</title>
</head>
<body>

<%

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
%>
            <h1>欢迎回来，您上次访问的时间为：<%=value%></h1>

<%

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
%>

        <h1>您好，欢迎您首次访问</h1>
<%
    }

%>

</body>
</html>
```

## 二、JSP进阶学习
### 1、指令
* 作用：**用于配置JSP页面，导入资源文件**
* 格式：
```
<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 ... %>
```
* **分类**：
	* **page**：**配置JSP页面的**
		* **contentType**：等同于`response.setContentType()`
			* 设置响应体的**mime类型以及字符集**
			* 设置当前**jsp页面的编码**（*只能是高级的IDE才能生效，如果使用低级工具，则需要设置pageEncoding属性设置当前页面的字符集*）
		* **import：导包**
		* **errorPage**：当前页面发生异常后，会自动跳转到指定的错误页面
		* **isErrorPage**：标识当前也是是否是错误页面。
			* true：是，可以使用内置对象exception
			* false：否。**默认值**。不可以使用内置对象exception

	* **include**：页面包含的。导入页面的资源文件
		* `<%@include file="top.jsp"%>`
	* **taglib**：导入资源

        	<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
        	<%--prefix：前缀，自定义的--%>


### 2、注释
* html注释：
	* `<!-- -->`:只能注释html代码片段
* jsp注释：推荐使用
	* `<%-- --%>`：可以注释所有


### 3、内置对象
* **在jsp页面中不需要创建，直接使用的对象**
* 一共有9个：
![image](Java_EE的动态网页技术JSP/2.png)