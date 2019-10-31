---
title: Mybatis学习day01
date: 2019-10-31 19:01:51
tags: [Java_EE,Mybatis]
category: Java_EE
---
## 一、前言
Mybatis的学习，我是通过哔哩哔哩网站上的视频，黑马程序员的，个人觉得很不错，推荐给大家，讲解思路清晰，内容充实，总共四天，让我能够全面的了解Mybatis的常用操作及应用。
<br/>*链接如下：https://www.bilibili.com/video/av47952553/?p=1*<br>

## 二、Mybatis框架简介
**mybatis**是一个优秀的基于java的持久层框架，它内部封装了**jdbc**，使开发者只需要关注**sql语句**本身，
而不需要花费精力去处理加载驱动、创建连接、创建 statement 等繁杂的过程。

**mybatis**通过**xml 或注解的方式**将要执行的各种 statement 配置起来，并通过 java 对象和 statement 中
sql 的动态参数进行映射生成最终执行的 sql语句，最后由 mybatis 框架执行 sql 并将结果映射为 java对象并返回。
## 三、jdbc的回顾
```
public static void main(String[] args) { 
    Connection connection = null; 
    PreparedStatement preparedStatement = null; ResultSet resultSet = null;

    try {
        //加载数据库驱动
        Class.forName("com.mysql.jdbc.Driver");
        //通过驱动管理类获取数据库链接
        connection=DriverManager.getConnection("jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8","root", "root");
        //定义 sql 语句 ?表示占位符
        String sql = "select * from user where username = ?";

        //获取预处理 statement
        preparedStatement = connection.prepareStatement(sql);
        //设置参数，第一个参数为 sql 语句中参数的序号（从 1 开始），第二个参数为设置的参数值
        preparedStatement.setString(1, "王五");
        //向数据库发出 sql 执行查询，查询出结果集
        resultSet =	preparedStatement.executeQuery();
        //遍历查询结果集
        while(resultSet.next()){
            System.out.println(resultSet.getString("id")+" "+resultSet.getString("username"));
        }
    } catch (Exception e) { 
        e.printStackTrace();
    }finally{
        //释放资源
        if(resultSet!=null){
            try {       
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(preparedStatement!=null){
            try {
                preparedStatement.close();
            } catch (SQLException e) {     
                e.printStackTrace();
            }
        }
        if(connection!=null){
            try {
                connection.close();
            } catch (SQLException e) {
                 e.printStackTrace();
            }
        }
    }
}

/*上边使用 jdbc 的原始方法（未经封装）实现了查询数据库表记录的操作。*/
```
### jdbc 问题分析

* 数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库链接池可解决此问题。

* Sql 语句在代码中硬编码，造成代码不易维护，实际应用 sql 变化的可能较大，sql 变动需要改变 java代码。

* 使用 preparedStatement向占有位符号传参数存在硬编码，因为 sql 语句的where条件不一定，可能多也可能少，修改 sql 还要修改代码，系统不易维护。

* 对结果集解析存在硬编码（查询列名），sql变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成 pojo 对象解析比较方便。

## 四、Mybatis快速入门
### 1、什么是框架？
**它是我们软件开发中的一套解决方案，不同的框架解决**的是不同的问题。**<br/>
使用框架的好处：**
	框架封装了很多的细节，使开发者可以使用极简的方式实现功能。大大提高开发效率。
### 2、三层架构
**表现层**：
	是用于展示数据的<br/>
**业务层**：
	是处理业务需求<br/>
**持久层**：
	是和数据库交互的
### 3、持久层技术解决方案
**JDBC技术**：
	Connection
	PreparedStatement
	ResultSet<br/>
**Spring的JdbcTemplate：**
	Spring中对jdbc的简单封装<br/>
**Apache的DBUtils：**
	它和Spring的JdbcTemplate很像，也是对Jdbc的简单封装<br/>

*以上这些都不是框架<br/>
	JDBC是规范
	Spring的JdbcTemplate和Apache的DBUtils都只是工具类*

### 4、mybatis的概述
**mybatis是一个持久层框架，用java编写的**。<br/>
它封装了jdbc操作的很多细节，使开发者只需要关注sql语句本身，而无需关注注册驱动，创建连接等繁杂过程
它使用了ORM思想实现了结果集的封装。

**ORM：**
	**Object Relational Mappging 对象关系映射**<br/>
*简单的说：
	就是把数据库表和实体类及实体类的属性对应起来
	让我们可以操作实体类就实现操作数据库表。*
	
	user			User
	id			    userId
	user_name		userName
		
**实体类中的属性和数据库表的字段名称保持一致。**

	user			User
	id			    id
	user_name		user_name
### 5、mybatis的入门
**mybatis的环境搭建**
- 第一步：创建maven工程并导入坐标
- 第二步：创建实体类和dao的接口
- 第三步：创建Mybatis的主配置文件SqlMapConifg.xml
- 第四步：创建映射配置文件IUserDao.xml


**环境搭建的注意事项：**

 **第一**：创建**IUserDao.xml** 和 **IUserDao.java**时名称是为了和我们之前的知识保持一致。

在Mybatis中它把**持久层的操作接口名称和映射文件**也叫做**Mapper**
所以,IUserDao 和 IUserMapper是一样的

**第二**：在idea中创建目录的时候，它和包是不一样的
- 	包在创建时：com.itheima.dao它是三级结构
- 	目录在创建时：com.itheima.dao是一级目录

**第三**：**mybatis的映射配置文件位置必须和dao接口的包结构相同**

**第四**：映射配置文件的mapper标签namespace属性的取值必须是dao接口的全限定类名

**第五**：映射配置文件的操作配置（select），id属性的取值必须是dao接口的方法名

***当我们遵从了第三，四，五点之后，我们在开发中就无须再写dao的实现类***。

**mybatis的入门案例**
- 	第一步：读取配置文件
- 	第二步：创建SqlSessionFactory工厂
- 	第三步：创建SqlSession
- 	第四步：创建Dao接口的代理对象
- 	第五步：执行dao中的方法
- 	第六步：释放资源

*注意事项：*
- 	*不要忘记在映射配置中告知mybatis要封装到哪个实体类中*
- 	*配置的方式：指定实体类的全限定类名*

***mybatis基于注解的入门案例：(了解，后面几天的学习会有专门的讲解)**<br/>
把IUserDao.xml移除，在dao接口的方法上使用@Select注解，并且指定SQL语句<br/>
同时需要在SqlMapConfig.xml中的mapper配置时，使用class属性指定dao接口的全限定类名。*<br/>

**明确：
	我们在实际开发中，都是越简便越好，所以都是采用不写dao实现类的方式。
	不管使用XML还是注解配置。
	但是Mybatis它是支持写dao实现类的。**

### 6、自定义Mybatis的分析：（了解，需大致了解其内部实现）
#### mybatis在使用代理dao的方式实现增删改查时做什么事呢？
**只有两件事**：<br/>
**第一：创建代理对象**<br/>
**第二：在代理对象中调用selectList**
	
#### 自定义mybatis能通过入门案例看到类
class Resources<br/>
class SqlSessionFactoryBuilder<br/>
interface SqlSessionFactory<br/>
interface SqlSession
## 五、day01代码以及资料（百度云盘）
由于代码较多，不方便展示，下面的资料中所有老师上课讲解的东西都有，最有效的学习方式是在听课的同时，能够自己进行同步操作，即能加深印象又能学会和理解

*链接：https://pan.baidu.com/s/1yE5o757PRiESId4vUDMLKg 
提取码：2ekq 
复制这段内容后打开百度网盘手机App，操作更方便哦*




