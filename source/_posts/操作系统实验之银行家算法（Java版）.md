---
title: 操作系统实验之银行家算法（Java版）
date: 2019-11-17 21:51:34
tags: [操作系统,Java_SE]
category: 操作系统
---
## 一、实验内容
银行家算法的实现。
## 二、实验目的
银行家算法是一种最有代表性的避免死锁的算法。在避免死锁方法中允许进程动态地申请资源，但系统在进行资源分配之前，应先计算此次分配资源的安全性，若分配不会导致系统进入不安全状态，则分配，否则等待。通过编写一个模拟动态资源分配的银行家算法程序，帮助学生进一步深入理解死锁、产生死锁的必要条件、安全状态等重要概念，并掌握避免死锁的具体实施方法。
## 三、实验原理
### 3.1、银行家算法中的数据结构 
#### 1）可利用资源向量Available
是个含有m个元素的数组，其中的每一个元素代表一类可利用的资源数目。如果Available［j］=K，则表示系统中现有Rj类资源K个。 
#### 2）最大需求矩阵Max
这是一个n×m的矩阵，它定义了系统中n个进程中的每一个进程对m类资源的最大需求。如果Max［i,j］=K，则表示进程i需要Rj类资源的最大数目为K。
#### 3）分配矩阵Allocation
这也是一个n×m的矩阵，它定义了系统中每一类资源当前已分配给每一进程的资源数。如果Allocation［i,j］=K，则表示进程i当前已分得Rj类资源的数目为K。
#### 4）需求矩阵Need。
这也是一个n×m的矩阵，用以表示每一个进程尚需的各类资源数。如果Need［i,j］=K，则表示进程i还需要Rj类资源K个，方能完成其任务。  
Need［i,j］=Max［i,j］-Allocation［i,j］ 
### 3.2、银行家算法
***设Requesti是进程Pi的请求向量，如果Requesti［j］=K，表示进程Pi需要K个Rj类型的资源。当Pi发出资源请求后，系统按下述步骤进行检查：***

* (1)如果Requesti［j］≤Need［i,j］，便转向步骤(2)；否则认为出错，因为它所需要的资源数已超过它所宣布最大值。
* (2)如果Requesti［j］≤Available［j］，便转向步骤(3)；否则，表示尚无足够资源，Pi须等待。 
* (3)系统试探着把资源分配给进程Pi，并修改下面数据结构中的数值：
```
Available［j］=Available［j］-Requesti［j］;

Allocation［i,j］=Allocation［i,j］+Requesti［j］; 

Need［i,j］=Need［i,j］-Requesti［j］;
```
*系统执行安全性算法，检查此次资源分配后，系统是否处于安全状态。若安全，才正式将资源分配给进程Pi，以完成本次分配；否则，将本次的试探分配作废，恢复原来的资源分配状态，让进程Pi等待。*
### 3.3、安全性算法
#### 1）设置两个向量：
**工作向量Work:** 它表示系统可提供给进程继续运行所需的各类资源数目，它含有m个元素，在执行安全算法开始时，Work=Available;

**工作向量Finish:** 它表示系统是否有足够的资源分配给进程，使之运行完成。开始时先做Finish［i］=false; 当有足够资源分配给进程时， 再令Finish［i］=true。 
#### 2）从进程集合中找到一个能满足下述条件的进程：  
```      
Finish［i］=false; 
Need［i,j］≤Work［j］；
```
若找到，执行 (3)，否则，执行 (4)
#### 3）当进程Pi获得资源后，可顺利执行，直至完成，并释放出分配给它的资源，故应执行：
```
Work［j］= Work［i］+Allocation［i,j］;
Finish［i］= true;
go to step 2; 
```
#### 4）如果所有进程的Finish［i］=true都满足， 则表示系统处于安全状态；否则，系统处于不安全状态
## 四、代码实现
### 1、创建进程类（多个进程）
```
import java.util.Scanner;

public class Processes {
    private String[] resourceName;    //系统资源名称
    private Integer[][] maxNum;       //Pi的需求资源数量（最大需求资源数量）
    private Integer[][] allocation;   //已分配给该进程的资源
    private Integer[][] needNum;      //还需要的资源数量
    private Integer AVAILABLE_COUNT_Y;//资源的种类数量
    private Integer PROCESSES_COUNT;  //进程的个数
    public Processes() {
        Init();
    }

    public Integer getAVAILABLE_COUNT_Y() {
        return AVAILABLE_COUNT_Y;
    }

    public void setAVAILABLE_COUNT_Y(Integer AVAILABLE_COUNT_Y) {
        this.AVAILABLE_COUNT_Y = AVAILABLE_COUNT_Y;
    }

    public Integer getPROCESSES_COUNT() {
        return PROCESSES_COUNT;
    }

    public void setPROCESSES_COUNT(Integer PROCESSES_COUNT) {
        this.PROCESSES_COUNT = PROCESSES_COUNT;
    }

    public String getResourceName(int j) {
        return resourceName[j];
    }

    public void setResourceName(String[] resourceName) {
        this.resourceName = resourceName;
    }

    public Integer[][] getMaxNum() {
        return maxNum;
    }

    public void setMaxNum(Integer[][] maxNum) {
        this.maxNum = maxNum;
    }

    public Integer getAllocation(int i,int j) {
        return allocation[i][j];
    }

    public void setAllocation(int i,int j,Integer allo) {
        allocation[i][j] = allo;
    }

    public Integer getNeedNum(int i,int j) {
        return needNum[i][j];
    }

    public void setNeedNum(int i,int j,Integer need) {
        needNum[i][j] = need;
    }

    /**
     * 初始化
     */
    public void Init() {
        System.out.print("请输入资源的种类数量：");
        Scanner input = new Scanner(System.in);
        AVAILABLE_COUNT_Y = input.nextInt();
        resourceName = new String[AVAILABLE_COUNT_Y];
        System.out.println("请输入各类资源的名称：");
        for (int i = 0; i < AVAILABLE_COUNT_Y; i++) {
            System.out.print("资源" + (i+1) + "的名称：");
            resourceName[i] = input.next();
        }

        System.out.println("请输入进程的个数：");
        PROCESSES_COUNT = input.nextInt();
        maxNum = new Integer[PROCESSES_COUNT][AVAILABLE_COUNT_Y];
        allocation = new Integer[PROCESSES_COUNT][AVAILABLE_COUNT_Y];
        needNum = new Integer[PROCESSES_COUNT][AVAILABLE_COUNT_Y];
        for (int i = 0; i < PROCESSES_COUNT; i++) {
            System.out.println("请输入进程" + (i+1) + "对" + AVAILABLE_COUNT_Y + "类资源的最大需求：");
            for (int j = 0; j < AVAILABLE_COUNT_Y; j++) {
                System.out.print(resourceName[j] + "：");
                maxNum[i][j] = input.nextInt();
            }

            System.out.println("请输入进程" + (i+1) + "已申请到的各类资源数：");
            for (int j = 0; j < AVAILABLE_COUNT_Y; j++) {
                System.out.print(resourceName[j] + "：");
                allocation[i][j] = input.nextInt();
            }

            for (int j = 0; j < AVAILABLE_COUNT_Y; j++) {
                needNum[i][j] = maxNum[i][j] - allocation[i][j];
            }

        }
    }
}
```
### 2、创建银行家类
```
import java.util.Scanner;

public class Banker {
    private Integer[][] request;  //进程的请求向量
    private Integer[] available;  //可利用资源向量
    /**
     * 银行家算法
     */
    public void Bank(){
        Processes processes = new Processes();
        Scanner input = new Scanner(System.in);
        request = new Integer[processes.getPROCESSES_COUNT()][processes.getAVAILABLE_COUNT_Y()];
        available = new Integer[processes.getAVAILABLE_COUNT_Y()];
        //给请求向量赋值
        System.out.println("请输入各进程对各类资源分别的请求量：");
        for (int i = 0; i < processes.getPROCESSES_COUNT(); i++) {
            System.out.println("进程" + (i+1));
            for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                System.out.print(processes.getResourceName(j) + "：");
                request[i][j] = input.nextInt();
            }
        }
        //给系统可利用资源向量赋值
        System.out.println("请输入各类可利用的资源数目：");
        for (int i = 0; i < processes.getAVAILABLE_COUNT_Y(); i++) {
            System.out.print(processes.getResourceName(i) + "：");
            available[i] = input.nextInt();
        }
        //判断资源是否能都满足请求
        for (int i = 0; i < processes.getPROCESSES_COUNT(); i++) {
            System.out.println("进程" + (i+1) + "开始进行请求。。。");
            for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                if (request[i][j] <= processes.getNeedNum(i,j)){
                    if (request[i][j] <= available[j]){

                        System.out.println("请求暂时成功，正在进行安全性检查。。。。。。");
                        //请求暂时成功进行安全性检查


                        available[j] = available[j] - request[i][j];
                        processes.setAllocation(i,j,processes.getAllocation(i,j) + request[i][j]);
                        processes.setNeedNum(i,j,processes.getNeedNum(i,j) - request[i][j]);
                        System.out.println("P" + (i+1) + "分配成功！");
                        break;
                    }else {
                        System.err.println("尚无足够的资源，P" + (i+1) + "需等待");
                        continue;
                    }
                }else {
                    System.err.println("请求出错，原因：P" + (i+1) + "进程所需要" + processes.getResourceName(j) + "类资源数已超过它所宣布最大值");
                    continue;
                }
            }
            System.out.println();
        }
        Safity(processes);
    }

    /**
     * 安全性算法
     */
    public void Safity(Processes processes){
        Integer[] work = new Integer[processes.getAVAILABLE_COUNT_Y()];     //工作向量Work
        work = available;
        String[] finish = new String[processes.getPROCESSES_COUNT()];   //工作向量Finish
        for (int j = 0; j < finish.length; j++) {
            finish[j] = "false";
        }
        boolean flag = true;    //是否运行完成标志
        for (int i = 0; i < processes.getPROCESSES_COUNT(); i++) {
            for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                if (finish[i].compareTo("false") == 0  &&  processes.getNeedNum(i,j) <= work[j]){
                    work[j] = work[j] + processes.getAllocation(i,j);
                    finish[i] = "true";

                }else {

                    for (int k = 0; k < processes.getPROCESSES_COUNT(); k++) {
                        if (finish[k].compareTo("true") != 0){
                            flag = false;
                        }
                    }
                }
            }
        }

        if (flag)
            System.out.println("系统处于安全状态");
        else
            System.out.println("系统处于不安全状态");
    }
}
```
### 3、创建测试类，调用银行家算法
```
public class BankerTest {
    public static void main(String[] args) {
        Banker banker = new Banker();
        banker.Bank();
    }
}

```