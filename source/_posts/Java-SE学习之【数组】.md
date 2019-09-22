---
title: Java_SE学习之【数组】
date: 2019-09-21 14:14:45
tags: [Java_SE]
category: Java_SE
---
## 一、前言
先分享一下，这一周学习**Java**的感受吧。学习**Java**和之前学习C语言和C++有一些不同，刚开始学习时很难接受，也很难上手。     

和C/C++比较的话，语法上稍微能简洁一点，但是运行速度却是天壤之别，而且当了解到**Java**没有指针时，瞬间觉得这语言没了灵魂，但是通过这一周的学习发现，**Java**其实也有一些值得去学习的地方，有一些内置的方法能够快速高效地达到预期的结果。所以说任何一门编程语言之所以存在，都会有一些值得去学习的地方，在学习的道路中，不能存在所谓的”鄙视链“，要以平和的心态对待每一门语言，这样才能使自己更加强大。

下面说说，**Java**中**数组**是如何进行**定义、初始化、动态赋值、排序、查找、删除和插入**的。

## 二、数组的定义
在**Java**中数组的定义与C语言大不相同。

```
/*C语言中数组的定义*/
int array[10];
//类型说明符 数组名[常量表达式];

/*Java中数组的定义*/
int[] array = new int[10];
//类型说明符[] 数组名 = new 类型说明符[常量表达式]

/*也可以这样写*/
int[] array;
array = new int[10];
```
通过对第二种方法的分析来了解**数组**在创建过程中内存的分配情况。第一行代码声明了一个变量array，该变量的类型为int[]，即一个int类型的数组，但是变量array只占用一块内存单元，并没有被分配初始值。第二行代码创建了一个数组，将数组的地址赋值给变量array，在程序运行期间可以使用**变量array**来引用数组。
## 三、一维数组的初始化
先通过几个例子感受一下：

```
//错误：声明并初始化数组时，不需要指定数组的大小
int years[6] = {2012,2013,2014,2015,2016,2017};

//错误：声明并初始化数组时，不需要指定数组的大小
int[12] months ={1,3,5,7,8,10,12}; 

//正确：元素个数为2（days.length == 2）
int days[] = {1,15};

//语法正确：但不能为数组元素赋值
int array[] = {};
```
通过上面的例子发现，Java事儿还是挺多的。
静态初始化有两种方式。
  
   1.类型[] 数组名 = new 类型[]{元素,元素,元素,...}；
   
   2.类型[] 数组名 = {元素,元素,元素,...}；

上面的两种方式都可以实现数组的静态初始化，通常采用第二种方式。**需要注意的是：1、创建数组后就不能再修改数组的大小。    2、基本数据类型数组的默认值为0。  3、char型数组元素的默认值为\u0000**


```
public class blogTest {

	public static void main(String[] args) {
		int[] array = new int[2];
		System.out.println("int型数组中的元素有:");
		for (int i = 0; i < array.length; i++) {
			System.out.println(array[i]);
		}
		char[] arrayc = new char[2];
		System.out.println("char型数组中的元素有:");
		for (int i = 0; i < arrayc.length; i++) {
			System.out.println(arrayc[i]);
		}
	}
}
/*
运行结果：
int型数组中的元素有:
0
0
char型数组中的元素有:


*/
//控制台输出的是空格，\u0000 表示的是Unicode值
```
## 三、一维数组的动态赋值
大概的思想和C语言是类似的，都是先定义一个空的数组再通过**for循环**来对数组进行赋值，下面来看一个C语言中很熟悉的斐波那契额数列的例子，这样会更快地理解动态赋值。

```
public class HelloWorld {
	public static void main(String[] args) {
		int[] nums= new int[20];
		int sum=0;
		double avg;
		nums[0]=1;
		nums[1]=1;
		for (int i = 2; i < nums.length; i++) {
			nums[i]=nums[i-1]+nums[i-2];
		}
		System.out.println("打印结果");
		for (int i = 0; i < nums.length; i++) {
			System.out.print(nums[i]+ ", ");
			sum+=nums[i];
		}
		System.out.println();
		System.out.println(sum/nums.length/1.0);
	}
}

/*
运行结果：
打印结果
1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233, 377, 610, 987, 1597, 2584, 4181, 6765, 
885.0
*/
```
## 四、数组的排序
常见的几种排序：冒泡排序、选择排序、快速排序
### 1、冒泡排序
冒泡排序算法的原理如下：

**a**.  比较相邻的元素。如果第一个比第二个大，就交换他们两个。

**b**.  对每一对相邻元素做同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。

**c**.   针对所有的元素重复以上的步骤，除了最后一个。

**d**.   持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

```
//结果升序
public class blogTest {
	public static void main(String[] args) {
	    int[] arr = {78,89,67,98,90,56,88};
	    for(int i=0; i<arr.length; i++){
	        for(int j=i; j<arr.length; j++){
	            if(arr[i]>arr[j]){
	                int temp = arr[j];
	                arr[j]  = arr[i];
	                arr[i] = temp;
	            }
	        }
	    }
	    for (int i = 0; i < arr.length; i++) {
			System.out.print(arr[i]+"\t");
		}
	}
}

/*
运行结果：
56	67	78	88	89	90	98	
*/
```
需要进行降序的冒泡排序时，将上述代码中第二个for循环中的if语句的条件 `arr[i]>arr[j] ` 改为`arr[i]arr[j]`即可。
### 2、选择排序
**简单选择排序是最简单直观的一种算法**，每一趟从待排序的数据元素中选择最小（或最大）的一个元素作为首元素，直到所有元素排完为止，简单选择排序是不稳定排序。
```
public class blogTest {
	public static void main(String[] args) {
		int[] arr = {52,63,14,59,68,35,8,67,45,99};
		System.out.println("原数组：");
		for (int i : arr) {
			System.out.print(i+" ");
		}
		System.out.println();
		for(int i = 0; i < arr.length-1; i++){
			int min = i;
			for(int j = i+1; j <arr.length-1;j++){
				if(arr[j]<arr[min]){
					min = j;
				}
			}
			if(min!=i){
				int temp = arr[i];
				arr[i] = arr[min];
				arr[min] = temp;
			}
		}
		System.out.println("排序后：");
		for (int i : arr) {
			System.out.print(i+" ");
		}
	}
}

/*
运行结果：
原数组：
52 63 14 59 68 35 8 67 45 99 
排序后：
8 14 35 45 52 59 63 67 68 99 
*/
```
上面的代码中有些for循环的条件为`int i : arr`

等价为 `int i ; i < arr.length() ; i++`
### 3、sort方法之快速排序
**快速排序基本思想：**
通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

Java中有自带的类库可以直接实现快速排序：
```
import java.util.Arrays;
public class blogTest {
	public static void main(String[] args) {
		int[] arr = {52,63,14,59,68,35,8,67,45,99};
		System.out.println("原数组：");
		for (int i : arr) {
			System.out.print(i+" ");
		}
		System.out.println();
		Arrays.sort(arr);
		System.out.println("排序后：");
		for (int i : arr) {
			System.out.print(i+" ");
		}
	}
}

/*
运行结果：
原数组：
52 63 14 59 68 35 8 67 45 99 
排序后：
8 14 35 45 52 59 63 67 68 99 
*/
```

需要进行逆序时，在引用sort方法后，加上以下代码调换数组元素的顺序即可。
```
for (int i = 0; i < arr.length / 2; i++) {
			int temp = arr[i];
			arr[i] = arr[arr.length-i-1];
			arr[arr.length-i-1]=temp;
		}
```
## 五、数组的二分查找法
**基本原理：**

首先将要查找的元素（key）与数组的中间元素比较

**1**、如果key小于中间元素，只需要在数组的前一半元素中继续查找

**2**、如果key和中间元素相等，匹配成功，查找结束

**3**、如果key大于中间元素，只需要在数组的后一半元素中继续查找key


```
import java.util.Scanner;
public class blogTest {
	public static void main(String[] args) {
		int[] arr = {1,2,3,4,5,6};
		System.out.print("请输入要查找的值:");
		Scanner input = new Scanner(System.in);
		int key = input.nextInt();
		boolean isFind = false;
		int low = 0;
		int high = arr.length - 1;
		while (low <= high) {
			int mid = low + (high - low) / 2;
		    if (arr[mid] > key){
		    	high = mid - 1;
		    }else if(arr[mid] < key){
		        low = mid + 1;
		    }else{
		        System.out.println("找到了！下标为"+mid);
		        isFind = true;
		        break;
		    }
		}
		if (!isFind) {
	    	System.out.println("抱歉，未找到!");
		}	
	}
}

/*
运行结果：
请输入要查找的值:10
抱歉，未找到!

请输入要查找的值:5
找到了！下标为4
*/
```
## 六、数组的删除和插入
### 1、删除
**删除之前操作的步骤**：先排序然后查找要删除的元素的下标

**删除操作的步骤：**

**a**、找到要删除元素的下标

**b**、从要删除的元素开始，后面一个元素赋值给前面一个元素——覆盖

**c**、数组总长度-1

具体代码实现如下：
```
import java.util.Scanner;
public class blogTest {
	public static void main(String[] args) {
		int[] arr = {1,2,3,4,5,6}; //这里直接给的是一个有序的数组，如果改变为无序，加入sort方法排序即可
		System.out.print("请输入要删除的值:");
		Scanner input = new Scanner(System.in);
		int key = input.nextInt();
		int deleteIndex = -1;	//要删除元素的下标
		int count = arr.length; //当前数组元素的总个数
		boolean isFind = false;
		int low = 0;
		int high = arr.length - 1;
		while (low <= high) {
			int mid = low + (high - low) / 2;
		    if (arr[mid] > key){
		    	high = mid - 1;
		    }else if(arr[mid] < key){
		        low = mid + 1;
		    }else{
		        System.out.println("找到了！下标为"+mid);
		        isFind = true;
		        deleteIndex = mid;
		        break;
		    }
		}
		if (!isFind || deleteIndex == -1) {
	    	System.out.println("抱歉，未找到!");
	    	System.exit(0);
		}
		count--;
		for (int i = deleteIndex; i < count; i++) {
			arr[i]=arr[i+1];
		}
		System.out.print("已删除！剩余数组元素为：");
		for (int i = 0; i < count; i++) {
			System.err.print(arr[i]+"\t");
		}
	}
}

/*
运行结果：
请输入要删除的值:5
找到了！下标为4
已删除！剩余数组元素为：1	2	3	4	6	

请输入要删除的值:10
抱歉，未找到!
*/
```
其实并没有删除，只不过是将要删除的数后的元素重新进行赋值，在遍历数组时，将数组最后一个元素不进行遍历。因为在Java中数组的空间和总大小是不能改变的，在C语言和C++中也是一样。
### 2、插入
**插入操作的步骤：**

**a**、用**copyOf**方法复制原来的数组**arr**得到比原来数组长度大1新数组**arr1**

**b**、找到要插入元素的下标（找到第一个大于要插入元素的下标）

**c**、从最后一个元素开始**arr1[i] = arr[i-1]**  直到**index**结束

**d**、在**index**处赋值

具体代码实现如下：

```
import java.util.Arrays;
import java.util.Scanner;
public class blogTest {
	public static void main(String[] args) {
      Scanner input = new Scanner(System.in);
      int[] arr = { 99, 85, 82, 63, 60 };
      Arrays.sort(arr);
      int[] arr1 = Arrays.copyOf(arr, arr.length + 1);//用copyOf方法复制原来的数组得到比原来数组长度大1新数组arr1
      int index = 0;
      System.out.print("请输入要插入的数：");
      int a = input.nextInt();
      for (int i = 0; i < arr1.length; i++) {
         if (arr[i] > a) {
            index = i;
            break;
         }
      }
      for (int i = arr1.length - 1; i > index; i--) {
         arr1[i] = arr[i - 1];
      }
      arr1[index] = a;
      System.out.print("插入后的数下标是：" + index);
      System.out.println();
      System.out.print("插入后的数组元素为：");
      for (int i : arr1) {
         System.out.print(i + "\t");
      }
	}
}

/*
运行结果：
请输入要插入的数：80
插入后的数下标是：2
插入后的数组元素为：60	63	80	82	85	99	
*/
```
也可以将要插入的数直接赋值给新的数组arr1的最后一个元素，然后进行排序即可。
## 七、浅淡二维数组
二维数组可以看作是由N个一维数组所组成的数组，在二维数组的相关操作中，多采用嵌套的for循环进行，所以最基础的东西还是一维数组的这些操作，掌握了这些操作，二维数组或者多维数组都能够自己分析。
## 八、小结
### *写了一下午，已经有点崩溃了*

写之前的样子：

![image](Java-SE学习之【数组】/0.jpg) 


写完以后的样子：


![image](Java-SE学习之【数组】/1.jpg) 