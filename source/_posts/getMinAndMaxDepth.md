---
title: 二叉树的最小深度和最大深度
date: 2019-04-24 21:38:53
tags: [JAVA,数据结构,二叉树]
categories: 数据结构
---

&#160;&#160;&#160;&#160;二叉树的最大深度为根节点到最远叶节点路径上的节点数，最小深度为根节点到最近叶节点路径上的节点数。这里使用递归的办法。

&#160;&#160;&#160;&#160;求最大值，对左右节点分别递归,每一层取下一层较大深度，一层加1，最底层加0。

```
/**
 * 求二叉树的最大深度
 *
 * @param root
 * @return
 */
public static int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int m = maxDepth(root.left);
    int n = maxDepth(root.right);
    return (m > n ? m : n) + 1;
}
```

&#160;&#160;&#160;&#160;求最小值有点不一样，左节点空的递归右节点，右节点空的递归左节点，每一层取下层较小深度，一层加1，最底层加0。

```
/**
 * 求二叉树的最小深度
 *
 * @param root
 * @return
 */
public static int minDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    if (root.getLeft() == null) {
        return minDepth(root.getRight()) + 1;
    }
    if (root.getRight() == null) {
        return minDepth(root.getLeft()) + 1;
    }
    int m = minDepth(root.left);
    int n = minDepth(root.right);
    return (m < n ? m : n) + 1;

}
```