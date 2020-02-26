---
title: MySQL8.0连接配置（Java版）
date: 2020-02-26 22:20:56
tags: [JavaWeb]
category: JavaWeb
---
## 一、JDBC中连接配置
```
Class.forName("com.mysql.cj.jdbc.Driver");
Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db_name?useSSL=false&serverTimezone=UTC","root","DatabasePassword");
/*将db_name改为要连接的数据库名；DatabasePassword是连接数据库的密码*/
```

## 二、使用连接池相关技术配置文件
```
driverClassName=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/jdbc?useSSL=false&serverTimezone=UTC
username=root
password=991104
# 初始化连接数量
initialSize=5
# 最大连接数
maxActive=10
# 最大等待时间
maxWait=3000
```
![image](MySQL8.0连接配置（Java版）/1.png)