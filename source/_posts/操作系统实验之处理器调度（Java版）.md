---
title: 操作系统实验之处理器调度（Java版）
date: 2019-11-17 21:26:27
tags: [操作系统,Java_SE]
category: 操作系统
---
## 声明
***代码虽Low*，*内容却很实在，思路很清晰，能够实现操作系统处理器调度算法的简单模拟。如果有不对的地方望指正，在下方评论或者私信我均可***
## 实验具体详情
### 一、实验内容
按优先数调度算法实现处理器调度。
### 二、实验目的
在采用多道程序设计的系统中，往往有若干个进程同时处于就绪状态。当就绪进程个数大于处理器数时，就必须依照某种策略来决定哪些进程优先占用处理器。本实验模拟在单处理器情况下的处理器调度，帮助学生加深了解处理器调度的工作。
### 三、实验原理
设计一个按优先数调度算法实现处理器调度的程序。

 (1) 假定系统有五个进程，每一个进程用一个进程控制块PCB来代表，进程控制块的格式为：
![image](操作系统实验验之处理器调度（Java版）/1.png)

其中，进程名——作为进程的标识，假设五个进程的进程名分别为P1，P2，P3，P4，P5。
- 指针——按优先数的大小把五个进程连成队列，用指针指出下一个进程的进程控制块的首地址，最后一个进程中的指针为“0”。
- 要求运行时间——假设进程需要运行的单位时间数。
- 优先数——赋予进程的优先数，调度时总是选取优先数大的进程先执行。
- 状态——可假设有两种状态，“就绪”状态和“结束”状态。五个进程的初始状态都为“就绪”，用“R”表示，当一个进程运行结束后，它的状态为“结束”，用“E”表示。

(2) 在每次运行你所设计的处理器调度程序之前，为每个进程任意确定它的“优先数”和“要求运行时间”。
(3) 为了调度方便，把五个进程按给定的优先数从大到小连成队列。用一单元指出队首进程，用指针指出队列的连接情况。例：
![image](操作系统实验验之处理器调度（Java版）/2.png)

(4) 处理器调度总是选队首进程运行。采用动态改变优先数的办法，进程每运行一次优先数就减“1”。由于本实验是模拟处理器调度，所以，对被选中的进程并不实际的启动运行，而是执行：**优先数-1** 、**要求运行时间-1**来模拟进程的一次运行。

*提醒注意的是：在实际的系统中，当一个进程被选中运行时，必须恢复进程的现场，让它占有处理器运行，直到出现等待事件或运行结束。在这里省去了这些工作。*

(5) 进程运行一次后，若要求运行时间¹0，则再将它加入队列（按优先数大小插入，且置队首标志）；若要求运行时间=0，则把它的状态修改成“结束”（E），且退出队列。

(6) 若“就绪”状态的进程队列不为空，则重复上面（4）和（5）的步骤，直到所有进程都成为“结束”状态。

(7) 在所设计的程序中应有显示或打印语句，能显示或打印每次被选中进程的进程名以及运行一次后进程队列的变化。

(8) 为五个进程任意确定一组“优先数”和“要求运行时间”，启动所设计的处理器调度程序，显示或打印逐次被选中进程的进程名以及进程控制块的动态变化过程。

## 三、代码实现
### 1、创建一个ProcessPCB类（进程PCB类）
```
import java.util.Random;

public class ProcessPCB {
    private String processName;        //进程名
    private Integer requairRunningTime; //要求时间
    private Integer fastNumber;        //优先数
    private Character status;             //状态

    public ProcessPCB(String processName) {
        Random random = new Random();
        this.fastNumber = random.nextInt(31) + 1;
        this.requairRunningTime = random.nextInt(10) + 1;
        this.processName = processName;
        this.status = 'R';
    }

    public String getProcessName() {
        return processName;
    }

    public void setProcessName(String processName) {
        this.processName = processName;
    }

    public Integer getRequairRunningTime() {
        return requairRunningTime;
    }

    public void setRequairRunningTime(Integer requairRunningTime) {
        this.requairRunningTime = requairRunningTime;
    }

    public Integer getFastNumber() {
        return fastNumber;
    }

    public void setFastNumber(Integer fastNumber) {
        this.fastNumber = fastNumber;
    }

    public Character getStatus() {
        return status;
    }

    public void setStatus(Character status) {
        this.status = status;
    }

    public void Start(){
        this.fastNumber = fastNumber - 1;
        this.requairRunningTime = requairRunningTime - 1;
    }

    public void Ending(){
        this.status = 'E';
    }
    @Override
    public String toString() {
        return "PCB" + processName.substring(1) + "{" +
                "Name='" + processName + '\'' +
                ", Time=" + requairRunningTime +
                ", Number=" + fastNumber +
                ", Status=" + status +
                '}';
    }
}
```
### 2、进行模拟
```
import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;

public class ProcessDemo {
    static class CompareTime implements Comparator<ProcessPCB>{
        @Override
        public int compare(ProcessPCB p1, ProcessPCB p2) {
            return p2.getFastNumber().compareTo(p1.getFastNumber());
        }
    }
    public static void main(String[] args) {
        ProcessPCB p1 = new ProcessPCB("P1");
        ProcessPCB p2 = new ProcessPCB("P2");
        ProcessPCB p3 = new ProcessPCB("P3");
        ProcessPCB p4 = new ProcessPCB("P4");
        ProcessPCB p5 = new ProcessPCB("P5");
        ArrayList<ProcessPCB> pointerR = new ArrayList<ProcessPCB>();
        ArrayList<ProcessPCB> pointerE = new ArrayList<ProcessPCB>();
        pointerR.add(p1);
        pointerR.add(p2);
        pointerR.add(p3);
        pointerR.add(p4);
        pointerR.add(p5);
        CompareTime cT = new CompareTime();
        int count = 0;//进程运行次数
        System.out.println("运行之前：");
        for (ProcessPCB p:pointerR) {
            System.out.println(p.toString());
        }
        System.out.println();
        System.out.println("开始运行。。。。。。");
        while (pointerR.size() != 0){
            count++;
            for (int j = 0; j < pointerR.size()+1; j++) {
                Collections.sort(pointerR, cT);
            }
            ProcessPCB pFirtst = pointerR.get(0);
            //1、选择优先级高的进程进行运行
            System.out.println("第" + count + "次被选中的进程名为：" + pFirtst.getProcessName());

            for (ProcessPCB p:pointerR) {
                System.out.println(p.toString());
            }
            pointerR.get(0).Start();

            if (pointerR.get(0).getRequairRunningTime() != 0) {
                //2、运行一次后，若要求运行时间不等于零，则再将它加入到队列中（按优先数大小插入）

            }else {
                //3、要求运行时间等于零，则修改状态为“E”，且退出队列
                pointerR.get(0).Ending();
                pointerR.remove(0);
                pointerE.add(pFirtst);
            }

        }
        System.out.println();
        System.out.println("所有进程运行结束。。。。。。。");
        System.out.println("所有进程状态如下：");
        for (ProcessPCB p:pointerE) {
            System.out.println(p.toString());
        }
    }
}
```