---
title: Mybatis学习day02
date: 2019-10-31 19:16:16
tags: [Java_EE,Mybatis]
category: Java_EE
---
## 一、内容简介
* 1、**回顾mybatis自定义和环境搭建+完善自定义Mybatis的注解开发**
* 2、**Mybatis基于代理Dao的CRUD操作**	

**重点内容：**

* 3、**CRUD中可能遇到的问题：参数的传递以及返回值的封装**
* 4、介绍Mybatis基于传统dao方式的使用（自己编写dao的实现类）

*了解的内容：*
* 5、mybatis主配置文件中的常用配置：
properties标签 ，typeAliases标签	---解释Integer的写法

mappers标签的子标签：package
* OGNL表达式：
    
    Object Graphic Navigation Language
    
    对象	图  	导航	   语言
	
*它是通过对象的取值方法来获取数据。在写法上把get给省略了。*

*比如：我们获取用户的名称
	类中的写法：user.getUsername();
	OGNL表达式写法：user.username
mybatis中为什么能直接写username,而不用user.呢？
	因为在parameterType中已经提供了属性所属的类，所以此时不需要写对象名*
## 二、回顾
### 2.1 自定义流程再分析
![image](Mybatis学习day02/1.png)
### 2.2 mybatis 环境搭建步骤 
* 第一步：创建 maven 工程 
* 第二步：导入坐标 
* 第三步：编写必要代码（实体类和持久层接口） 
* 第四步：编写 SqlMapConfig.xml 
* 第五步：编写映射配置文件 第六步：编写测试类 

## 三、基于代理 Dao 实现 CRUD 操作 
**使用要求**： 
* 1、持久层接口和持久层接口的映射配置必须在相同的包下  
* 2、持久层映射配置中 mapper 标签的 namespace 属性取值必须是持久层接口的全限定类名  
* 3、SQL 语句的配置标签<select>,<insert>,<delete>,<update>的 id 属性必须和持久层接口的 方法名相同

### 3.1 根据 ID 查询 
#### 3.1.1 在持久层接口中添加 findById 方法  
```
 /**   
   * 根据 id 查询   
   * @param userId 
   * @return 
   */  
   User findById(Integer userId);  
 ```
#### 3.1.2 在用户的映射配置文件中配置
```
<!-- 根据 id 查询 --> 
<select id="findById" resultType="com.allen.domain.User" parameterType="int">
    select * from user where id = #{uid}
</select> 
/*
细节：  resultType属性：用于指定结果集的类型。  
        parameterType属性：用于指定传入参数的类型。
        sql语句中使用#{}字符：它代表占位符，相当于原来 jdbc 部分所学的?，都是用于执行语句时替换实际的数据。
        具体的数据是由#{}里面的内容决定的。
        #{}中内容的写法:由于数据类型是基本类型，所以此处可以随意写。*/
```
#### 3.1.3 在测试类添加测试 
```
/** 
 *  
 * <p>Title: MybastisCRUDTest</p> 
 * <p>Description: 测试 mybatis 的 crud 操作</p> 
 */ 
 public class MybastisCRUDTest { 
    private InputStream in ;
    private SqlSessionFactory factory;
    private SqlSession session; 
    private IUserDao userDao;
    
    @Test  public void testFindOne() {
        //6.执行操作  
        User user = userDao.findById(41); 
        System.out.println(user);
    }   
     @Before//在测试方法执行之前执行
     public void init()throws Exception {
         //1.读取配置文件 
         in = Resources.getResourceAsStream("SqlMapConfig.xml");  
         //2.创建构建者对象   
         SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();  
         //3.创建 SqlSession 工厂对象 
         factory = builder.build(in); 
         //4.创建 SqlSession 对象   
         session=factory.openSession(); 
         //5.创建 Dao 的代理对象   
         userDao=session.getMapper(IUserDao.class);  
         
     }   
     @After//在测试方法执行完成之后执行  
     public void destroy() throws Exception{
        session.commit(); 
        //7.释放资源 
        session.close();
        in.close();
    } 

 } 
```
### 3.2 保存操作 
#### 3.2.1 在持久层接口中添加新增方法
```
/** 
  * 保存用户
  * @param user 
  * @return 影响数据库记录的行数 
  */ 
  int saveUser(User user); 
```
#### 3.2.2 在用户的映射配置文件中配置
```
<!-- 保存用户-->
<insert id="saveUser" parameterType="com.itheima.domain.User"> 
    insert into user(username,birthday,sex,address) values(#{username},#{birthday},#{sex},#{address}) 
</insert> 
```
**细节**：  
* **parameterType 属性**： 
代表参数的类型，因为我们要传入的是一个类的对象，所以类型就写类的全名称。
* **sql 语句中使用#{}字符**：  它代表占位符，相当于原来 jdbc 部分所学的?，都是用于执行语句时替换实际的数据。<br/>
具体的数据是由#{}里面的内容决定的。
* **#{}中内容的写法**： 
由于我们保存方法的参数是 一个 User 对象，此处要写 User 对象中的属性名称。
它用的是 ognl 表达式。 
* **ognl 表达式：** 
它是 apache 提供的一种表达式语言，全称是：   **Object Graphic Navigation Language  对象图导航语言**<br/>
它是按照一定的语法格式来获取数据的。   语法格式就是使用#{对象.对象}的方式<br/> 
#{user.username}它会先去找 user 对象，然后在 user 对象中找到 username 属性，并调用 getUsername()方法把值取出来。但是我们在 parameterType 属性上指定了实体类名称，所以可以省略 user. 而直接写username。
#### 3.2.3 添加测试类中的测试方法 
```
@Test 
public void testSave(){ 
    User user = new User(); 
    user.setUsername("modify User property"); 
    user.setAddress("北京市顺义区");
    user.setSex("男"); 
    user.setBirthday(new Date());
    System.out.println("保存操作之前："+user); 
    //5.执行保存方法 
    userDao.saveUser(user); 
     System.out.println("保存操作之后："+user); 
} 
```

*打开 Mysql 数据库发现并没有添加任何记录，原因是什么？ 这一点和 jdbc 是一样的，我们在实现增删改时一定要去控制事务的提交，那么在 mybatis 中如何控制事务 提交呢？ 可以使用:session.commit();来实现事务提交*。 

加入事务提交后的代码如下：
```
@After//在测试方法执行完成之后执行
public void destroy() throws Exception{  
    session.commit();
    //7.释放资源
    session.close();
    in.close();
}
```
#### 3.2.4 问题扩展：新增用户 id 的返回值 
新增用户后，同时还要返回当前新增用户的 id 值，因为 id 是由数据库的自动增长来实现的，所以就相 当于我们要在新增后将自动增长 auto_increment 的值返回。 
```
<insert id="saveUser" parameterType="USER">
    <!-- 配置保存时获取插入的 id --> 
    <selectKey keyColumn="id" keyProperty="id" resultType="int"> 
        select last_insert_id(); 
    </selectKey> 
    insert into user(username,birthday,sex,address)  
    values(#{username},#{birthday},#{sex},#{address}) 
</insert> 
```
### 3.3用户更新
#### 3.3.1 在持久层接口中添加更新方法
```
/** 
 * 更新用户
 * @param user 
 * @return 影响数据库记录的行数
 */ 
 int updateUser(User user); 
```
#### 3.3.2 在用户的映射配置文件中配置
```
<!-- 更新用户 --> 
<update id="updateUser" parameterType="com.itheima.domain.User"> 
    update user set username=#{username},birthday=#{birthday},sex=#{sex},
    address=#{address} where id=#{id}
</update>
```
#### 3.3.3 加入更新的测试方法 
```
@Test
public void testUpdateUser()throws Exception{  
    //1.根据 id 查询  
    User user = userDao.findById(52);
    //2.更新操作 
    user.setAddress("北京市顺义区");
    int res = userDao.updateUser(user); 
    System.out.println(res);
}
```
### 3.4 用户删除
#### 3.4.1 在持久层接口中添加删除方法 
```
 /** 
  * 根据 id 删除用户
  * @param userId
  * @return  
  */ 
  int deleteUser(Integer userId); 
```
#### 3.4.2 在用户的映射配置文件中配置
```
<!-- 删除用户 --> 
<delete id="deleteUser" parameterType="java.lang.Integer">
    delete from user where id = #{uid}
</delete> 
```
#### 3.4.3 加入删除的测试方法 
```
@Test
public void testDeleteUser() throws Exception { 
    //6.执行操作
    int res = userDao.deleteUser(52);
    System.out.println(res);
} 
```
## 四、总结
上面的都是CRUD的基本操作，已经写得头皮发麻，其它内容代码以及资料，如下：

*链接：https://pan.baidu.com/s/1ykcwagvtnCX43NlCWVxXwQ 
提取码：erey 
复制这段内容后打开百度网盘手机App，操作更方便哦*