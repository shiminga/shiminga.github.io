---
title: 数据结构-单链表反转，取中间值
date: 2019-04-19 23:45:31
tags: [JAVA,数据结构]
categories: 数据结构
---

​		在Java中，通过节点之间的引用可以实现单向链表结构。由于每个节点只知道自己下一个节点，其在内存中是不连续的，因而进行**遍历时效率不高**，但是**插入和删除时效率较高**。

##### 单链表反转

​		由于节点间的引用关系，只需要传入头结点。找到原来的尾节点作为新的头结点，通过递归将原来的引用倒置，最后返回新的头结点即可。

*****

```
/**
 * 链表反转
 * @param head
 * @return
 */
public static Node reverse(Node head) {
    if (head.next == null)//尾节点需要作为新的Head
        return head;
    Node temp = head.next;//需要暂时存储原先节点的下一节点然后才能修改，不然就丢失了
    Node newHead = reverse(head.next);//递归
    temp.next = head;//将引用倒置
    head.next = null;
    return newHead;
}
```

*****

##### 取单链表中间值

​		最常用的方式是通过快慢指针，快的速度是慢的两倍，快的到达尾部慢的就走了一半。

*****

```
/**
 * 求取链表中间值
 * @param head
 * @return
 */
public static Node searchMid(Node head) {
    //一倍速，两倍速  快的到达尾部，慢的就在中间
    Node slow=head;
    Node fast = head;
    while(fast.next!=null){
        if(fast.next.next!=null){
            slow=slow.next;
            fast=fast.next.next;
        }else{
            fast=fast.next;
            slow=slow.next;
        }
    }
    return slow;
}
```

​		链表这种结构还是比较常见的，像LinkedList这种，熟悉常见的几种数据结构能够帮助理解java中的工具类。

