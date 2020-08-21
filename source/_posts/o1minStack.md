---
title: 时间复杂度O(1)获取最小值的栈
date: 2019-04-21 12:00:04
tags: [JAVA,数据结构]
categories: 数据结构
---

​		这个是之前遇到过的一个问题,做个记录。

​		要求:实现一个栈，带有出栈，入栈，取最小元素三个方法。要保证这三个方法的时间复杂度都是O（1）

#### 代码

```
public class DataStack<T extends Number> {

    Stack<T> data;
    Stack<T> minData;

    DataStack(){
        this.data=new Stack<T>();
        this.minData=new Stack<T>();
    }

    /**
     * 1.添加
     * 数据栈正常加入
     * 如果比最小栈栈顶的大则忽略，小则压入最小栈
     * @param e
     * @return
     */
    void push (T e){
        if(minData.empty())
            minData.push(e);
        else if(minData.peek().intValue()>=e.intValue())
            minData.push(e);
        data.push(e);
    }

    /**
     * 1、弹出栈顶的
     * 数据栈正常出栈
     * 如果比最小栈栈顶的大则忽略，小或等于则最小栈出栈
     * @param
     * @return
     */
    T pop(){
        if(minData.empty())
            return null;
        else if (minData.peek().intValue()==data.peek().intValue())
            minData.pop();
        return data.pop();
    }

    /**
     * 获取最小值
     * @return
     */
    T getMin(){
        return minData.empty()?null: minData.peek();
    }
}
```

#### 效果

```
DataStack stack = new DataStack<Integer>();
stack.push(3);
System.out.println(stack.getMin());
stack.push(6);
System.out.println(stack.getMin());
stack.push(8);
System.out.println(stack.getMin());
stack.push(1);
System.out.println(stack.getMin());
stack.push(2);
System.out.println(stack.getMin());
System.out.println("=================");
stack.pop();
System.out.println(stack.getMin());

stack.pop();
System.out.println(stack.getMin());

stack.pop();
System.out.println(stack.getMin());

stack.pop();
System.out.println(stack.getMin());
```

运行结果:

![](o1minStack\minstack.png)