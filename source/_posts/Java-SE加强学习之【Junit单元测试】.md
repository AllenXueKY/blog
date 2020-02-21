---
title: Java_SE加强学习之【Junit单元测试】
date: 2020-02-22 00:04:51
tags: [Java_SE]
category: Java_SE
---
## Junit单元测试
### 1、测试分类
* 黑盒测试：不需要写代码，给输入值，看程序是否能够输出期望的值。
* **白盒测试：需要写代码的。关注程序具体的执行流程**。
![!mage](Java_SE加强学习之【Junit单元测试】/1.png)
### 2、Junit使用：白盒测试
#### 步骤：
* 定义一个测试类(测试用例)
	* 建议：
		* 测试类名：被测试的类名Test		例：`CalculatorTest`
		* 包名：`xxx.xxx.xx.test`		例：cn.itcast.test

* 定义测试方法：可以独立运行
	* 建议：
		* 方法名：test测试的方法名`testAdd() ` 
		* 返回值：`void`
		* 参数列表：空参

* 给方法加`@Test`
* 导入junit依赖环境
	* 判定结果：
		* **红色：失败**
		* **绿色：成功**
		* 一般我们会使用断言操作来处理结果
			* `Assert.assertEquals`(期望的结果,运算的结果);
* 实例代码：
```
public class CalculatorTest {
    /**
     * 初始化方法
     * 用于资源申请，所有测试方法在执行之前都会先执行该方法
     */
    @Before
    public void init(){
        System.out.println("init...");
    }

    /**
     * 释放资源方法：
     *    在所有测试方法执行完后，都会自动执行方法
     */
    @After
    public void close(){
        System.out.println("close...");
    }
    /**
     * 测试add方法
     */
    @Test
    public void testAdd(){
//        System.out.println("我被执行了");
        //1、创建对象
        System.out.println("testAdd...");
        Calculator c = new Calculator();
        //2、调用add方法
        int result = c.add(1,2);
        //System.out.println(result);

        //3、断言  我断言这个结果是3
        Assert.assertEquals(3,result);
    }
    @Test
    public void testSub(){
        Calculator c = new Calculator();
        int result = c.sub(1,2);
        System.out.println("testSub...");
        Assert.assertEquals(-1,result);
    }
}

```
### 3、补充
* `@Before`:
	* 修饰的方法会在测试方法之前被自动执行
* `@After`:
	* 修饰的方法会在测试方法执行之后自动被执行
