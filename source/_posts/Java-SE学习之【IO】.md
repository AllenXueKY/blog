---
title: Java_SE学习之【IO】
date: 2019-10-14 15:45:55
tags: [Java_SE]
category: Java_SE
---
## 一、文件
* 传统中的文件：中央红头文件、销售许可书、投标书、毕业论文等
* 计算机中的文件：相关记录或放在一起的数据集合

### 1、File类
* java.io.File
    * 可以用来处理文件目录问题
![image](Java_SE学习之【IO】/0.png)

下面通过一段代码来进一步理解：
```
import java.io.File;
import java.io.FileFilter;
import java.io.FilenameFilter;
import java.io.IOException;
import java.io.InputStream;
import java.io.Writer;
import java.util.Date;

import javax.swing.JFileChooser;

/**
 * 演示File类的基本使用及常用方法
 * File类并不包含对文件的读写操作
 */
public class FileDemo {

	public static void main(String[] args) throws IOException {
		//建议：不要直接使用绝对文件名。如果使用了像c:\\test.java之类的文件名，在Windows上正常运行，但不能在其他平台上运行
		//应该使用与当前目录相关的文件名
		JFileChooser fileChooser = new JFileChooser(new File("."));	//设置文件对话框打开时的根目录
		//让FileChooser可以选择文件夹
		fileChooser.setFileSelectionMode(JFileChooser.FILES_AND_DIRECTORIES);
		fileChooser.setFileFilter(new MyFileFilter());
		fileChooser.showOpenDialog(null);	//显示出选择文件的对话框(null表示无父窗体)
		//获得用户选择的文件 建议使用时判断file是否为null，如果为null，表示用户点了取消按钮
		File file = fileChooser.getSelectedFile();	//获得用户选择的文件
		if(null == file){
			System.out.println("用户未选择任何文件，系统强制退出！");
			System.exit(0);
		}
		System.out.println("文件/文件夹是否存在：" + file.exists());
		System.out.println("是否是一个文件：" + file.isFile());
		System.out.println("是否是一个文件夹：" + file.isDirectory());
		System.out.println("文件/文件夹名称：" + file.getName());
		System.out.println("文件/文件夹的绝对路径：" + file.getAbsolutePath());
		System.out.println("文件/文件夹的最后修改时间：" + new Date(file.lastModified()).toLocaleString());
		System.out.println("文件/文件夹所占空间大小：" + file.length() / 1024 + "KB");
		System.out.println("是否可读：" + file.canRead());
		System.out.println("是否可写：" + file.canWrite());
		System.out.println("是否隐藏：" + file.isHidden());
		
		
		//列出当前目录下的所有文件/文件夹
		if(file.isDirectory()){
			System.out.println(file.getAbsolutePath() + "路径下的所有文件及文件夹：");
			String[] fileNames = file.list(new DirFilter());
			for (int i = 0; i < fileNames.length; i++) {
				System.out.println(fileNames[i]);
			}
		}
		

	}
	
	static class MyFileFilter extends javax.swing.filechooser.FileFilter{

		@Override
		public boolean accept(File f) {
			if(f.getName().endsWith("wav")){
				return true;
			}
			return false;
		}

		@Override
		public String getDescription() {
			// TODO Auto-generated method stub
			return "*.*, *.wav";
		}
	}
	//补充：选学
	/**
	 * 我们自定义的文件名过滤器类，必须实现FilenameFileter接口
	 */
	static class DirFilter implements FilenameFilter{

		@Override
		public boolean accept(File dir, String name) {
			//假设我们要求只显示git为后缀的文件，过滤掉其他后缀
			if(name.endsWith("wav")){
				return true;
			}
			return false;
		}
		
	}
}

```
### 2、文件中的数据操作
* 文件中的数据操作
    * 就是通过计算机的输入/输出设备完成
* 在Java中将数据的输入/输出抽象为流（stream）
    * 数据收集时：有来源地
    * 数据使用时：有目的地

## 二、字节流
* 应用程序中如何使用
    * 应用程序运行在内存中

### 1、Java中流的分类
* 在Java中的操作文件主要有两大类：字节流、字符流。两类都分为输入和输出操作
* 在字节流中输入数据使用的是InputStream，输出数据主要使用OutputStream
    * InputStream和OutputStream是为字节流设计的，主要是用来处理字节或二进制对象
* 在字符流中输入数据主要使用Reader完成，输出数据主要使用Writer完成
    * Reader和Writer是为字符流（一个字符占2个字节）设计的，主要用来处理字符或字符串
    * 对多国语言支持比较好，如果是音频、视频、图片等建议使用字节流
* *注意：磁盘上保存的文件是二进制字节数据，字符流中多了对编码的处理*

### 2、字节流的架构
* InputStream输入流：
![image](Java_SE学习之【IO】/1.png)
* OutputStream输出流：
![image](Java_SE学习之【IO】/2.png)
* 其他常用的输入流：
![image](Java_SE学习之【IO】/3.png)

### 3、InputStream和OutputStream的常用方法
![image](Java_SE学习之【IO】/4.png)
### 4、演示文件输入、输出流的基本用法
```
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.util.Scanner;

/**
 * 演示文件输入、输出流的基本用法
 * 注意：目前的讀寫方式還比較原始，建議在這裡熟悉InputStream/OutputStream的一些重用方法即可
 * 後續會有一些高階的類對讀寫方法進行升級和擴展
 */
public class FileStreamDemo {
	private static final String FilePath = "src/com/javabase/ioStream/FileDemo.java";
	public static void main(String[] args) throws IOException {
		
//		//ReadFile();
		writeFile();
	}
	
	public static void writeFile() throws IOException{
		//注意：此文件默认不存在
		final String FilePath1 = "src/com/javabase/ioStream/FileDemo1.java";
		OutputStream outStream = new FileOutputStream(FilePath1, false);//第二个参数：是否追加写入
		String content = "package com.javabase.ioStream;\n";
		content += "//本类由代码自动生成\n";
		content += "public class FileDemo1{\n";
		content += "\tpublic static void main(String[] args){\n";
		content += "\t\tSystem.out.println(\"Hello World!\");\n\t}\n}";
		outStream.write(content.getBytes());//核心语句：将字符串转换成字节数组，写入到文件中
		//写入完毕后一定记得关闭打开的资源
		outStream.close();
		System.out.println("内容已成功写入到文件中，请检查：");
		System.out.println(FilePath1);
	}
	
	public static void ReadFile() throws IOException{
		File file = new File(FilePath);//省略判断
		InputStream inputStream = new FileInputStream(file);
		//inputStream.available() 获取输入流可以读取的文件大小（字节）
		//读取文件的基本操作 - 相对比较固定
		byte[] bytes = new byte[20000];
		inputStream.read(bytes);
//		int count = 0;
//		while((bytes[count] = (byte)inputStream.read()) != -1){
//			count++;
//		}
		String content = new String(bytes);	//将读取出的字节数组转换成字符串，以便打印
		System.out.println(content);
		inputStream.close();
	}

}
```
### 5、BufferedStream
* 带缓冲的流，就是加强版的Stream，直接上代码（引用老九君的例子）：
```

import java.io.BufferedInputStream;
import java.io.BufferedOutputStream;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

/**
 * 使用带缓冲的流增加读写效率
 */
public class BufferedStreamDemo {
	/**
	 * 注意：本文件路径是在老九君的电脑中
	 * 如果是本地测试，一定记得将文件路径修改成自己电脑中的路径！
	 */
	private static final String FilePath1 = "C:/Users/窖头/Videos/Recordings/Java直播之皇帝选妃项目配套录音.mp3";
	private static final String FilePath2 = "C:/Users/窖头/Videos/Recordings/已复制文件.mp3";
	
	
	public static void main(String[] args) throws IOException {
		//使用缓冲流装饰系统的输入流对象
//		BufferedInputStream inStream = new BufferedInputStream(System.in);
//		byte[] bytes = new byte[200];
//		System.out.println("请输入字符串：");
//		inStream.read(bytes);
//		System.out.println("用户输入的字符串是：" + new String(bytes));
		
		long time = CopyFileByBufferedStream();
		System.out.println("拷贝用时：" + time + "毫秒");
		
		//小作业：请各位小伙伴自行使用FileInputStream实现文件的复制
		//通过复制所用的时间，对比使用缓存的好处
		//通过设置不同的缓存大小，体会效率的区别
	}
	/**
	 * 使用缓存流拷贝文件
	 * @return 拷贝过程的用时（毫秒）
	 */
	private static long CopyFileByBufferedStream(){
		File file = new File(FilePath1);//要复制的文件对象
		if(!file.exists()){
			System.out.println("文件不存在，复制失败！");
			return 0;
		}
		long starTime = System.currentTimeMillis();
		InputStream inStream = null;
		BufferedInputStream bInStream = null;
		OutputStream outStream = null;
		BufferedOutputStream bOutStream = null;
		try {
			inStream = new FileInputStream(file);
			bInStream = new BufferedInputStream(inStream);
			outStream = new FileOutputStream(FilePath2);
			bOutStream = new BufferedOutputStream(outStream);
			int i = -1;//读取出的临时变量
			while((i = bInStream.read()) != -1){
				bOutStream.write(i);
				//注意：读取一个字节，就写入到了缓存中！需要在最后，将缓存中的内容写入到文件中！.
				//即，需要清空缓冲区，将缓冲区的内容写到文件中 - flush()
			}
			System.out.println("文件复制成功！");
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				//bOutStream.flush();
				bOutStream.close();//可以直接调用close方法，内部已经帮我们自动调用了flush方法
				outStream.close();
				bInStream.close();
				inStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		long endTime = System.currentTimeMillis();
		return endTime - starTime;
	}
	
		
//	static final String FilePath1 = "src/com/xuetang9/javabase/chapter13/FileStreamDemo.java";
//	static final String FilePath2 = "src/com/xuetang9/javabase/chapter13/BufferTest.txt";
	
//	public static void main(String[] args) {
//		FileInputStream fInputStream = null;
//		BufferedInputStream bInputStream = null;
//		try {
//			fInputStream = new FileInputStream(FilePath1);
//			bInputStream = new BufferedInputStream(fInputStream);
//			byte[] bytes = new byte[bInputStream.available()]; 
//			bInputStream.read(bytes);
//			System.out.println(new String(bytes));
//		} catch (FileNotFoundException e) {
//			e.printStackTrace();
//		} catch (IOException e) {
//			e.printStackTrace();
//		} finally {
//			try {
//				bInputStream.close();
//				fInputStream.close();
//			} catch (IOException e) {
//				e.printStackTrace();
//			}
//		}
//		
//		BufferedOutputStream bOutStream = null;
//		try {
//			bOutStream = new BufferedOutputStream(new FileOutputStream(FilePath2), 10240);
//			bOutStream.write("今天天气很好！".getBytes());
//		} catch (FileNotFoundException e) {
//			e.printStackTrace();
//		} catch (IOException e) {
//			e.printStackTrace();
//		} finally {
//			try {
//				bOutStream.close();
//			} catch (IOException e) {
//				e.printStackTrace();
//			}
//		}
//		
//
//	}

}
```
## 三、装饰器
*仅理论框架，后面会出详细的代码讲解*
![image](Java_SE学习之【IO】/5.png)

装饰器有一个固定的写法：
```
InputStream inputSteam = new FileInputStream("");
//使用带缓存的流对象装饰输入流对象
BufferedInputStream bInStream = new BufferedInputStream(inputSteam);
```
可以扩展出好几种装饰器，用法类似
## 四、字符流
### 1、字符流的架构
![image](Java_SE学习之【IO】/6.png)
### 2、使用字符流读写文件
* **读取文件**
    * FileReader
    * BufferedReader
* **写入文件**
    * FileWriter
    * BufferedWriter

### 3、固定的读取格式
```
BufferedReader bReader = new BufferedReader(new FileReader(FilePath1));
//固定的读取格式：
String line = null;
while((line = bReader.readLine()) != null){
	System.out.println(line);
}
```
### 4、例子
```
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

/**
 * 使用字符流对文件进行基本的读写操作
 */
public class ReaderAndWriterDemo {
	private static final String FilePath1 = "src/com/javabase/ioStream/FileDemo1.java";
	private static final String FilePath2 = "src/com/javabase/ioStream/FileDemo1.java";
	
	public static void main(String[] args) {
		try {
			BufferedReader bReader = new BufferedReader(new FileReader(FilePath1));
			//固定的读取格式：
			String line = null;
			while((line = bReader.readLine()) != null){
				System.out.println(line);
			}
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
		//要写入的字符串
		String content = "package com.xuetang9.javabase.chapter13;\n";
		content += "//本类由代码自动生成1111111111\n";
		content += "public class FileDemo1{\n";
		content += "\tpublic static void main(String[] args){\n";
		content += "\t\tSystem.out.println(\"Hello World!\");\n\t}\n}";
		try(BufferedWriter bWriter = new BufferedWriter(new FileWriter(FilePath2))) {
			//使用了jdk7以后新增的自动关闭资源的写法
			//使用前提：资源类必须实现Closeable接口
			bWriter.write(content);	//将字符串写入到缓冲中
		} catch (IOException e) {
			e.printStackTrace();
		} catch (Exception e) {
			//注意：由于bWriter.close会抛出IOException，这里的异常就被新结构抑制了
			//以下的代码用来查看被抑制的异常信息
			if(e.getSuppressed() != null){
				Throwable[] throwables = e.getSuppressed();
				for(Throwable t : throwables){
					System.err.println(t.getMessage() + "cased by " + t.getClass().getSimpleName());
				}
			}
		}

	}

}
```
### 5、PrintWriter和PrintStream
 * **printWriter**用来处理**字符流**，一次写入**2个字节**；**PrintStream**用来处理***字节流**，一次写入**1个字节**
 * 建议：在处理文本文件时，使用**PrintWriter**
 
上代码（老九君赞助）：
```
import java.io.File;
import java.io.FileNotFoundException;
import java.io.PrintWriter;
import java.util.Scanner;

public class PrintWriterDemo {
	static final String FilePath = "src/com/xuetang9/javabase/chapter13/FileDemo1.java";
	public static void main(String[] args) {
		
		PrintWriter writer1 = new PrintWriter(System.out);
		writer1.println("使用PrintWriter打印出的内容！");
		writer1.flush();
		writer1.close();
//		//补充：关于Java中的分隔符  windows - \r\n   
//		String separator = System.getProperty("line.separator");//获得系统默认的分隔符 - 固定写法
//		String content = "package com.xuetang9.javabase.chapter13;" + separator;
//		content += "public class FileDemo1{" + separator;
//		content += "\tpublic static void main(String[] args){" + separator;
//		content += "\t\tSystem.out.println(\"HelloWorld!\");" + separator;
//		content += "\t}" + separator;
//		content += "}";
//		
//		
//		
//		try(PrintWriter writer = new PrintWriter(new File(FilePath))){
//			writer.write(content);
//			System.out.println("写入成功！");
//		} catch (FileNotFoundException e) {
//			e.printStackTrace();
//		}
//		
//		try(Scanner input = new Scanner(new File(FilePath))){
//			StringBuffer value = new StringBuffer();
//			while(input.hasNextLine()){
//				value.append(input.nextLine());
//				value.append(separator);
//			}
//			System.out.println("以下是从文件中读取出的内容：");
//			System.out.println(value.toString());
//		} catch (FileNotFoundException e) {
//			e.printStackTrace();
//		}

	}

}
```
## 五、序列化和反序列化
* 对象的输入和输出
    * **ObjectInputStream** & **ObjectOutputStream**可以读/写可序列化的对象
    * 序列化的作用就是把对象以**二进制**写入文件（Sava）
    * 反序列化的作用时把**二进制文件转化为对象**（Load）
* 什么情况下需要序列化
    * 当想把内存中的对象状态保存到一个文件中或者数据库中的时候
    * 当想用套接字在网络上传送对象的时候
    * 当想通过RMI（远程方法调用）传输对象时
* **注意：**\
1、序列化时，只对对象的状态要进行保存，而不管对象的方法\
2、当一个父类实现序列化，子类自动实现序列化，不需要显示实现Serializable接口\
3、当一个对象的实例变量引用其他对象，序列化该对象时也把引用对象进行序列化

## 六、随机访问文件
* **java.io.RandomAccessFile**
    * 使用顺序流打开的文件称作**顺序访问文件**，顺序访问文件的内容**不能更新**
    * **RandomAccessFile**允许在文件的**任意位置上**进行读写（随机访问文件）
![image](Java_SE学习之【IO】/7.png)

***ps：最后两点没有给出代码的原因。上面给的代码太多了，不方便阅读，如果需要请联系我***