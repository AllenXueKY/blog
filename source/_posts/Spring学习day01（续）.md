---
title: Spring学习day01（续）
date: 2020-02-13 18:24:41
tags: [Java_EE,Spring]
category: Java_EE
---
##### 4.3.3.5 注入集合属性 
顾名思义，就是给类中的集合成员传值，它用的也是set方法注入的方式，只不过变量的数据类型都是集合。 我们这里介绍注入数组，List,Set,Map,Properties。具体代码如下： 
```
public class AccountServiceImpl implements IAccountService { 
    private String[] myStrs;  
    private List<String> myList;  
    private Set<String> mySet;  
    private Map<String,String> myMap;
    private Properties myProps;   
    
    public void setMyStrs(String[] myStrs) {  
        this.myStrs = myStrs; 
    }
    public void setMyList(List<String> myList) {
        this.myList = myList;
    }
    public void setMySet(Set<String> mySet) { 
        this.mySet = mySet;  
    }  
    public void setMyMap(Map<String, String> myMap) {
        this.myMap = myMap;
    }
    public void setMyProps(Properties myProps) {
        this.myProps = myProps;
    } 
 
    @Override 
    public void saveAccount() {   
        System.out.println(Arrays.toString(myStrs));   
        System.out.println(myList);   
        System.out.println(mySet);
        System.out.println(myMap);
        System.out.println(myProps);  
    } 
} 

<!-- 注入集合数据    
    List 结构的：
        array,list,set 
    Map 结构的           
        map,entry,props,prop 
--> 
<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"> 
    <!-- 在注入集合数据时，只要结构相同，标签可以互换 -->
    <!-- 给数组注入数据 -->  
    <property name="myStrs"> 
        <set>        
            <value>AAA</value>    
            <value>BBB</value>    
            <value>CCC</value>   
        </set> 
    </property> 
    <!-- 注入 list 集合数据 -->  
    <property name="myList">   
        <array>    
            <value>AAA</value>    
            <value>BBB</value>    
            <value>CCC</value>   
        </array>
    </property> 
    <!-- 注入 set 集合数据 -->  
    <property name="mySet"> 
        <list>    
            <value>AAA</value>    
            <value>BBB</value>    
            <value>CCC</value>   
        </list>
    </property> 
    <!-- 注入 Map 数据 -->  
    <property name="myMap">       
        <props> 
            <prop key="testA">aaa</prop>
            <prop key="testB">bbb</prop>
        </props> 
    </property> 
    <!-- 注入 properties 数据 --> 
    <property name="myProps"> 
        <map>
            <entry key="testA" value="aaa"></entry> 
            <entry key="testB">     
                <value>bbb</value> 
            </entry>
        </map>  
    </property> 
</bean>
```
## 五、day01代码以及资料（百度云盘）
下面的资料中所有老师上课讲解的东西都有，最有效的学习方式是在听课的同时，能够自己进行同步操作，即能加深印象又能学会和理解

*链接：https://pan.baidu.com/s/10LkCVekV03iNHtHihXD00g 
提取码：p8wv 
复制这段内容后打开百度网盘手机App，操作更方便哦*

