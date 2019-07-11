---
title: JS题目集锦-链表
date: 2018-09-14 17:56:32
tags:
---

这里会记录我在 leetcode 刷题中“链表“这一块做的题的记录以及想法。会不定期更新，速度取决于做题的速度以及心情。一些题一开始是用 java 写的，这里会统一换成 js
<!--more-->

其中所有链表均为以下定义

```js
function ListNode(val) {
    this.val = val;
    this.next = null;
}
```

# 题目合集

## 判断是否是回文 col：234

```js
function isPalindrome(head) {
    if (head === null || head.next === null) return true;

    var compare_1 = [];
    var compare_2 = [];

    while (head !== null) {
        compare_1.push(head.val);
        compare_2.unshift(head.val);
        head = head.next;
    }

    for (var i = 0; i < compare_1.length; i++) {
        if (compare_1[i] !== compare_2[i]) {
            return false;
        }
    }
    return true;
}
```

## 链表反转 col：206

```js
function reverseList(head) {
    if (head == null) {
        return head;
    }
    let current = head;
    let previous = null;
    while (head != null) {
        current = head;
        head = head.next;
        current.next = previous;
        previous = current;
    }
    return current;
}
```

## 链表按给定数 k 向右旋转 k 次 col：61

```js
function rotateRight(head, k) {
    if (!head || k == 0 || !head.next) {
        return head;
    }
    let length = 0;
    let head_for_count = head;
    while (head_for_count != null) {
        length++;
        head_for_count = head_for_count.next;
    }

    let sliceIndex = length - (k % length);
    let sliceNode = head;

    for (let i = 1; i < sliceIndex; i++) {
        sliceNode = sliceNode.next;
    }
    if (!sliceNode.next) {
        return head;
    } else {
        let resultNode = sliceNode.next;
        let endNode = resultNode;
        while (endNode.next) {
            endNode = endNode.next;
        }
        endNode.next = head;
        sliceNode.next = null;
        return resultNode;
    }
}
```

## 在已排号序的单链表中去除重复项 col：83

```js
function deleteDuplicates(head) {
    if (head == null) return head;
    let current = head;
    while (current.next != null) {
        if (current.next.val == current.val) {
            current.next = current.next.next;
        } else {
            current = current.next;
        }
    }
    return head;
}
```

## 判断是否有环 col：141

```js
function hasCycle(head) {
    if (head == null || head.next == null) return false;
    ListNode walker = head;
    ListNode runner = head;
    while (runner.next != null && runner.next.next != null) {
        walker = walker.next;
        runner = runner.next.next;
        if (runner == walker) return true;
    }
    return false;
}
```

## 两个链表相加 col：2

```js
function addTwoNumbers(l1, l2) {
    let result = new ListNode(0);
    let current = result;
    let sum = 0;
    let mid = 0;
    while (l1 != null || l2 != null) {
        sum += mid;
        if (l1 != null) {
            sum += l1.val;
            l1 = l1.next;
        }
        if (l2 != null) {
            sum += l2.val;
            l2 = l2.next;
        }
        mid = sum >= 10 ? 1 : 0;
        current.val = sum % 10;
        if (l1 != null || l2 != null) {
            current.next = new ListNode(0);
        }
        if (l1 == null && l2 == null && mid == 1) {
            current.next = new ListNode(1);
        }
        current = current.next;
        sum = 0;
    }
    return result;
}
```

## Next Greater Node In Linked List col: 1019

```js
function nextLargerNodes (head) {
    let count = 0
    let current = head
    let result = []

    while (current) {
        let tmpPointer = current
        let tmp = 0

        while (tmpPointer) {
            if (tmpPointer.val > current.val){
                tmp = tmpPointer.val
                break
            }
            tmpPointer = tmpPointer.next
        }
        result.push(tmp)
        current = current.next
    }
    return result
}
```

## Remove Duplicates from Sorted List II col:82

```js
function deleteDuplicates(head) {

    if (!head || !head.next) return head

    if (head) {
        let pointer = head
        while (pointer.next) {
            if (pointer.next.val == pointer.val) {
                pointer.next.remove = true
                pointer.remove = true
            }
            pointer = pointer.next
        }
        let pointer2 = head
        while (pointer2 && pointer2.next) {
            if (pointer2.next.remove) {
                let temp = pointer2.next
                while(temp && temp.remove) {
                    temp = temp.next
                }
                pointer2.next = temp
            }
            pointer2 = pointer2.next
        }
    }

    return head.remove ? head.next : head
}
```
