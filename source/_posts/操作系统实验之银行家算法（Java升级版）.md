---
title: 操作系统实验之银行家算法（Java升级版）
date: 2020-01-15 13:21:53
tags: [操作系统,Java_SE]
category: 操作系统
---
## 前言
***这次较上一篇文章更加透彻地展示了银行家算法，分析步骤和上一篇一样，直接上代码***

## 代码实现
Processes.class
```
/**
 * 进程类，包含多个进程的资源情况以及系统资源的情况
 */

import java.util.ArrayList;
import java.util.Scanner;

public class Processes {
    private String[] resourceName;    //系统资源名称
    private boolean[] isDel;          //是否已排除
    private Integer[][] maxNum;       //Pi的需求资源数量（最大需求资源数量）
    private Integer[][] allocation;   //已分配给该进程的资源
    private Integer[][] needNum;      //还需要的资源数量
    private Integer AVAILABLE_COUNT_Y;//资源的种类数量
    private Integer PROCESSES_COUNT;  //进程的个数
    private ArrayList<String> safe;    //安全序列

    public ArrayList<String> getSafe() {
        return safe;
    }

    public Processes() {
        Init();
    }

    public boolean getIsDel(int i) {
        return isDel[i];
    }

    public Integer getAVAILABLE_COUNT_Y() {
        return AVAILABLE_COUNT_Y;
    }

    public Integer getPROCESSES_COUNT() {
        return PROCESSES_COUNT;
    }

    public String getResourceName(int j) {
        return resourceName[j];
    }

    public Integer getMaxNum(int i,int j) {
        return maxNum[i][j];
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
            System.out.println("请输入进程P" + i + "对" + AVAILABLE_COUNT_Y + "类资源的最大需求：");
            for (int j = 0; j < AVAILABLE_COUNT_Y; j++) {
                System.out.print(resourceName[j] + "：");
                maxNum[i][j] = input.nextInt();
            }

            System.out.println("请输入进程P" + i + "已申请到的各类资源数：");
            for (int j = 0; j < AVAILABLE_COUNT_Y; j++) {
                System.out.print(resourceName[j] + "：");
                allocation[i][j] = input.nextInt();
            }

            for (int j = 0; j < AVAILABLE_COUNT_Y; j++) {
                needNum[i][j] = maxNum[i][j] - allocation[i][j];
            }
        }
        isDel = new boolean[PROCESSES_COUNT];
        for (int i = 0; i < PROCESSES_COUNT; i++) {
            isDel[i] = false;
        }
        safe = new ArrayList<>();
    }

    public void Del(int i){
        isDel[i] = true;
        safe.add("P" + i);
    }
}
```
Banker.class
```
/**
 * 银行家算法
 */

import java.util.ArrayList;
import java.util.Scanner;

public class Banker {
    private Integer[][] request;  //进程的请求向量
    private Integer[] num;        //资源的总数量
    private Integer[] available;  //可利用资源向量
    private int num1;             //进程编号num

    /**
     * 设置请求资源量Request
     * @param processes
     */
    public void setRequest(Processes processes) {
        Scanner in = new Scanner(System.in);
        System.out.println("请输入请求资源的进程编号：");
        num1 = in.nextInt();//设置全局变量进程编号num
        System.out.println("请输入请求各资源的数量：");
        for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
            request[num1][j] = in.nextInt();
        }
        System.out.println("即进程P" + num1 + "对各资源请求Request：(" + request[num1][0] + "," + request[num1][1] + "," + request[num1][2] + ").");
        BankBegin(processes);
    }

    /**
     * 银行家算法
     */
    public Processes Bank() {
        Processes processes = new Processes();
        Scanner input = new Scanner(System.in);
        System.out.println("请输入各类资源的数量：");
        num = new Integer[processes.getAVAILABLE_COUNT_Y()];
        for (int i = 0; i < processes.getAVAILABLE_COUNT_Y(); i++) {
            System.out.print(processes.getResourceName(i) + "：");
            num[i] = input.nextInt();
        }
        request = new Integer[processes.getPROCESSES_COUNT()][processes.getAVAILABLE_COUNT_Y()];
        available = new Integer[processes.getAVAILABLE_COUNT_Y()];
        //给系统可利用资源向量赋值
        available = num;
        for (int i = 0; i < processes.getPROCESSES_COUNT(); i++) {
            for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                available[j] = available[j] - processes.getAllocation(i, j);
            }
        }
        System.out.println("T0时刻的安全性：");
        printSystemVariable(processes);
        if (Safity(processes)){
            System.out.print("此时存在一个安全序列");
            System.out.println("故当前可分配！");
            //给请求向量赋值
            setRequest(processes);

        }else {
            System.out.println("当前系统处于不安全状态，故不存在安全序列。");
            System.exit(0);
        }

        return processes;
    }

    /**
     * 开始执行算法
     * @param processes
     */
    public void BankBegin(Processes processes) {
        //判断是否能都满足请求条件
        boolean T = true;
        int trueCount = 0;
        for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
            if (request[num1][j] <= processes.getNeedNum(num1, j)) {//判断Request是否小于Need
                if (request[num1][j] <= available[j]) {//判断Request是否小于Alloction
                    trueCount++;
                    if (trueCount == processes.getAVAILABLE_COUNT_Y()) {
                        for (int i = 0; i < processes.getAVAILABLE_COUNT_Y(); i++) {
                            available[i] = available[i] - request[num1][i];
                            processes.setAllocation(num1, i, (processes.getAllocation(num1, i) + request[num1][i]));
                            processes.setNeedNum(num1, i, (processes.getNeedNum(num1, i) - request[num1][i]));
                        }
                        break;
                    }
                } else {
                    System.out.println("当前没有足够的资源可分配，进程P" + num1 + "需等待。");
                    T = false;
                }
            } else {
                System.out.println("进程P" + num1 + "请求已经超出最大需求量Need.");
                T = false;
            }
        }
        if (T == true) {
            printSystemVariable(processes);
            System.out.println("预分配完成，进入安全性算法：");
            if (Safity(processes)){
                System.out.print("此时存在一个安全序列");
                System.out.print("，");
                System.out.println("故当前可分配！");
            }else {
                System.out.println("当前系统处于不安全状态，故不存在安全序列。");
            }
        }
    }

    /**
     * 安全性算法
     */
    public boolean Safity(Processes processes) {
        Integer[] work = new Integer[processes.getAVAILABLE_COUNT_Y()];     //工作向量Work
        for (int i = 0; i < processes.getAVAILABLE_COUNT_Y(); i++) {
            work[i] = available[i];
        }
        String[] finish = new String[processes.getPROCESSES_COUNT()];       //工作向量Finish
        for (int j = 0; j < finish.length; j++) {
            finish[j] = "false";
        }

        for (int i = 0; i < processes.getPROCESSES_COUNT(); i++) {
            ArrayList<String> isFind = new ArrayList<>();
            //如果进程没有被排除，则进行判断
            if (!(processes.getIsDel(i))) {
                //判断第二步的条件
                if (finish[i].compareTo("false") == 0) {
                    for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                        if (processes.getNeedNum(i, j) <= work[j]) {
                            isFind.add("true");
                        }
                    }
                }
            }
            //Pi所需资源均满足第二步条件时，顺利执行
            if (isFind.size() == processes.getAVAILABLE_COUNT_Y()) {
                for (int k = 0; k < processes.getAVAILABLE_COUNT_Y(); k++) {
                    work[k] = work[k] + processes.getAllocation(i, k);
                    finish[i] = "true";
                }
                processes.Del(i);
            }

        }

        return true;
    }

    /**
     * 打印资源数据
     */
    public void printSystemVariable(Processes processes) {
        System.out.println("此时资源分配量如下：");
        System.out.println("进程  " + "   Max   " + "   Alloction " + "    Need  " + "     Available ");
        for (int i = 0; i < processes.getPROCESSES_COUNT(); i++) {
            System.out.print("P" + i + "     ");
            for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                System.out.print(processes.getMaxNum(i, j) + "  ");
            }
            System.out.print("|  ");
            for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                System.out.print(processes.getAllocation(i, j) + "  ");
            }
            System.out.print("|  ");
            for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                System.out.print(processes.getNeedNum(i, j) + "  ");
            }
            System.out.print("|  ");
            if (i == 0) {
                for (int j = 0; j < processes.getAVAILABLE_COUNT_Y(); j++) {
                    System.out.print(available[j] + "  ");
                }
            }
            System.out.println();
        }
    }


}
```
BankerTest.class
```
/**
 * 银行家算法测试类
 */

import java.util.Scanner;

public class BankerTest {
    public static void main(String[] args) {
        //是否继续请求
        boolean Choose = true;
        String string;
        Scanner in = new Scanner(System.in);
        Banker banker = new Banker();
        Processes processes = banker.Bank();
        while (Choose == true) {
            banker.setRequest(processes);
            System.out.println("您是否还要进行请求：y/n?");
            string = in.nextLine();
            if (string.endsWith("n")) {
                Choose = false;
            }
        }
    }
}
```