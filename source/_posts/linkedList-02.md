---
title: 数据结构-合并两个有序单链表
date: 2019-04-20 00:39:13
tags: [JAVA,数据结构]
categories: 数据结构
---

​		这里采用的是非递归方式,假设两个链表都是递增的。首先找出新的头部，头部所在链表取下一个节点和另外一条链表第一个节点比较，新头部指向较小的，然后又取较小的所在链表的下一个节点和前一次的较大的比较，就这样每一次移一个节点直至有一条链表结束。代码如下:

**********

```
/**
 * 合并两个有序链表
 * @param l1
 * @param l2
 * @return
 */
public static Node mergeLinkedList(Node l1,Node l2) {
    //设两个链表都是从头到尾递增
    Node head = null;
    if (l1 == null) {
        return l2;
    }
    if (l2 == null) {
        return l1;
    }
    if (l1.data <= l2.data) {//如果l1节点的值小于等于l2节点的值，由于这两个链表是有序的，所以合并后最小的节点(head节点)就是它们两者中的小者
        head = l1;
        l1 = l1.next;//后移，用于继续比较选出接下来最小的节点
    } else {
        head = l2;
        l2 = l2.next;
    }
    Node temp = head;
    //接着比较两个链表，对两个链表中的最小的节点进行比较选出最小的，也就是合并后的第二小的节点，循环知道有一个链表为空
    while (l1 != null && l2 != null) {
        if (l1.data <= l2.data) {
            temp.next = l1;
            l1 = l1.next;
        } else {
            temp.next = l2;
            l2 = l2.next;
        }
        temp = temp.next;
    }
    if (l1 == null) {
        temp.next = l2;
    }
    if (l2 == null) {
        temp.next = l1;
    }
    return head;
}
```

​		测试数据:

**********

```
Node n1 = new Node(3);
Node n2 = new Node(5);
Node n3 = new Node(7);
Node n4 = new Node(8);
Node n5 = new Node(9);
Node n6 = new Node(10);
Node n7 = new Node(13);
Node n8 = new Node(14);
n1.setNext(n2);
n2.setNext(n3);
n3.setNext(n4);
n4.setNext(n5);
n5.setNext(n6);
n6.setNext(n7);
n7.setNext(n8);

Node n9 = new Node(1);
Node n10 = new Node(2);
Node n11 = new Node(20);
n9.setNext(n10);
n10.setNext(n11);

System.out.println(n1);
System.out.println(n9);
System.out.println(Node.mergeLinkedList(n1,n9));
```

输出结果:

![](linkedList-02\hebing.png)