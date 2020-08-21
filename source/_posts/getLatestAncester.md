---
title: 二叉树两个节点最近祖先
date: 2019-04-24 22:00:23
tags: [JAVA,数据结构,二叉树]
categories: 数据结构
---

&#160;&#160;&#160;&#160;已知二叉树的两个节点，要求找到二者最近的祖先。

&#160;&#160;&#160;&#160;先遍历其中一个节点，保存它到根节点的所有节点，再看另外一个节点的祖先路径与之重叠部分。

```
/**
 * 寻找两个节点最近的祖先
 *
 * @param node1
 * @param node2
 * @return
 */
public static TreeNode searchNearestAncester(TreeNode node1, TreeNode node2) {
    List list = new ArrayList();
    list.add(node1);
    if (node1.parent != null) {
        while (node1.parent != null) {
            list.add(node1.parent);
            node1 = node1.parent;
        }
    }
    //
    if (list.indexOf(node2) != -1)
        return node2;
    if (node2!= null) {
        while (node2.parent!= null) {
            node2 = node2.parent;
            if (list.indexOf(node2) != -1)
                return node2;
        }
    }
    return null;
}
```