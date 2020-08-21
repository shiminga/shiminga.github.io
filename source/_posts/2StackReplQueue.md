---
title: 两个栈实现一个队列
date: 2019-04-21 11:40:56
tags: [JAVA,数据结构]
categories: 数据结构
---

​		本文使用栈的基本操作实现队列的效果,由于栈的LIFO,两个栈逆逆得顺就能FIFO

#### 代码

```
public class MyQueue<T extends Number> {
    Stack<T> data;
    Stack<T> tempData;

    MyQueue(){
        this.data=new Stack<T>();
        this.tempData=new Stack<T>();
    }

    void add(T e){
        data.push(e);
    }

    /**
     * 两个栈实现对队列的消费操作
     */
    T remove(){
        //放入临时栈
        while (!data.empty())
            tempData.push(data.pop());
        //去掉栈顶元素
        T t= tempData.pop();
        //从临时栈倒回去
        while (!tempData.empty())
            data.push(tempData.pop());
        return t;
    }
}
```

#### 效果

```
MyQueue queue = new MyQueue();
queue.add(3);
queue.add(4);
queue.add(5);
queue.add(6);
queue.add(7);

System.out.println(queue.remove());
System.out.println(queue.remove());
System.out.println(queue.remove());
System.out.println(queue.remove());
System.out.println(queue.remove());
```

运行结果:

![](2StackReplQueue\myqueue.png)

