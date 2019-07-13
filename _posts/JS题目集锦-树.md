---
title: JS题目集锦-树
date: 2018-09-08 10:12:30
tags:
---

这里会记录我在 leetcode 刷题中“树“这一块做的题的记录以及想法。会不定期更新，速度取决于做题的速度以及心情。
<!--more-->

其中，所有树均用以下定义：

```js
//Definition for a binary tree node.
function TreeNode(val) {
    this.val = val;
    this.left = this.right = null;
}
```

# 题目合集

## 反转二叉树 题号：226

说明：这里运用到了分治的思想，通过递归的方法，逐步对每个节点以及其左右子树进行左右置换。

```js
function invert(node) {
    if (node == null) {
        return node;
    }
    let temp = node.left;
    node.left = node.right;
    node.right = temp;

    if (node.left != null) {
        invert(node.left);
    }
    if (node.right != null) {
        invert(node.right);
    }
}
```

## 判断是否是同一树 题号：617

说明： 这里同样运用到了分治的思想，通过递归  检查每个节点的左右子树，从而完成全部节点的比较。两个节点相同时，比较其子节点； 一旦出现了空值的情况 ，即到达递归的临界点，对当前状态进行判断并返回一个真值。

```js
function isSameTree(p, q) {
    if (p !== null && q !== null) {
        if (p.val != q.val) {
            return false;
        }
    }
    if (p == null && q == null) {
        return true;
    }
    if ((p !== null && q === null) || (p === null && q !== null)) {
        return false;
    }
    return (
        isSameTree(
            p.left === null ? null : p.left,
            q.left === null ? null : q.left
        ) &&
        isSameTree(
            p.right === null ? null : p.right,
            q.right === null ? null : q.right
        )
    );
}
```

## 判断一棵树是否对称 题号：101

说明：上面一题的变形

```js
function isSymmetric(root) {
    if (root == null) {
        return true;
    }
    return subLeafSymmetric(root.left, root.right);
}

function subLeafSymmetric(p, q) {
    if (p !== null && q !== null) {
        if (p.val != q.val) {
            return false;
        }
    }
    if (p == null && q == null) {
        return true;
    }
    if ((p !== null && q === null) || (p === null && q !== null)) {
        return false;
    }
    return (
        subLeafSymmetric(
            p.left === null ? null : p.left,
            q.right === null ? null : q.right
        ) &&
        subLeafSymmetric(
            p.right === null ? null : p.right,
            q.left === null ? null : q.left
        )
    );
}
```

## 合并两个树（每个相同位置节点值相加）题号：100

说明：还是通过递归的方法，将两个树的每个相对应位置的节点进行相加。

```js
function mergeTrees(t1, t2) {
    if (null !== t1 || null !== t2) {
        let tmp = new TreeNode(0);
        if (t1 != null) {
            tmp.val = t1.val;
        }
        if (t2 != null) {
            tmp.val += t2.val;
        }
        tmp.left = mergeTrees(
            t1 != null ? t1.left : null,
            t2 != null ? t2.left : null
        );
        tmp.right = mergeTrees(
            t1 != null ? t1.right : null,
            t2 != null ? t2.right : null
        );
        return tmp;
    } else {
        return null;
    }
}
```

## 构建字符串 题号：606

```js
function tree2str(t) {
    let result = "";
    if (t == null) {
        return "";
    }
    result = t.val.toString();

    if (t.right != null) {
        return (
            result +
            "(" +
            tree2str(t.left) +
            ")" +
            "(" +
            tree2str(t.right) +
            ")"
        );
    }
    if (t.left == null && t.right == null) {
        return result;
    }
    if (t.left != null && t.right == null) {
        return result + "(" + tree2str(t.left) + ")";
    }
}
```

## 二叉搜索树中查找值 题号：700

```js
function searchBST(root, val) {
    if (root == null) {
        return [];
    }
    if (root.val == val) {
        return root;
    }
    if (root.val > val) {
        return searchBST(root.left, val);
    }
    if (root.val < val) {
        return searchBST(root.right, val);
    }
}
```

## 计算二叉树最小深度 题号：111

```js
function minDepth(root) {
    if (!root) return 0;
    if (!root.left && !root.right) return 1;

    let result = 1;
    let left = result + minDepth(root.left);
    let right = result + minDepth(root.right);

    if (left == 1 && right != 1) return right;
    if (left != 1 && right == 1) return left;
    return right < left ? right : left;
}
```

## 计算二叉树最大深度 题号：104

```js
function maxDepth(root) {
    if (!root) return 0;
    if (!root.left && !root.right) return 1;

    let result = 1;
    let left = result + maxDepth(root.left);
    let right = result + maxDepth(root.right);

    return right > left ? right : left;
}
```

## 检查二叉树所有节点数值是否相同 题号：965

```js
function isUnivalTree(root) {
    if (!root) return true

    if (root.left && root.right) {
        if ((root.left.val == root.right.val) && (root.left.val == root.val)) {
            return isUnivalTree(root.left) && isUnivalTree(root.right)
        } else {
            return false
        }
    }

    if (!root.left && !root.right) {
        return true
    }

    if (!root.left && root.right) {
        if (root.right.val == root.val) {
            return isUnivalTree(root.right)
        } else {
            return false
        }

    }

    if (root.left && !root.right) {
        if (root.left.val == root.val) {
            return isUnivalTree(root.left)
        } else {
            return false
        }
    }

};
```

## 最大二叉树 题号：654

说明：个人觉得这道题和快速排序所用到的分治思想简直如出一辙。先在数组中找到最大值，记作根节点。然后以此跟节点作为划分，递归算出左右子数组的最大数值，记作左右子节点，再各自进行划分，直到叶子节点。

```js
var constructMaximumBinaryTree = function(nums) {
    if (nums.length < 1) {
        return null
    }
    let max = getMax(nums)
    let maxIndex = getIndex(max, nums)
    let result = new TreeNode(max)

    if (maxIndex == 0) {
        result.left = null
        result.right = constructMaximumBinaryTree(nums.slice(1))
    } else if (maxIndex == nums.length - 1) {
        result.right = null
        result.left = constructMaximumBinaryTree(nums.slice(0, nums.length-1))
    } else {
        result.left  = constructMaximumBinaryTree(nums.slice(0, maxIndex))
        result.right = constructMaximumBinaryTree(nums.slice(maxIndex+1, nums.length))
    }

    return result
};

var getMax = function(nums) {
    let max = 0;

    for(let i = 0; i < nums.length; i++) {
        if(nums[i] > max) {
            max = nums[i]
        }
    }
    return max
}

var getIndex = function(num, arr) {
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] == num) {
            return i
        }
    }
}
```
