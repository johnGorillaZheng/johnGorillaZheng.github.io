---
title: JS排序算法
date: 2019-02-28 16:54:57
tags:
---

这篇帖子主要是为了总结最常见的排序算法以及应付以后的面试，防止面试突然脑袋 NG。将包括冒泡，插入，快速，归并等常见排序。代码为主，文字仅负责点拨。排序结果为按照升序排序。

<!--more-->

## 初级排序

## 冒泡排序

提示：
时间复杂度：$O(n)$ ~ $O(n^2)$
空间复杂度：$O(1)$

```js
function bubbleSort(nums) {
    let length = nums.length;
    for (let i = 0; i < length - 1; i++) {
        for (let j = 0; j < length - 1 - i; j++) {
            if (nums[j] > nums[j + 1]) {
                let tmp = nums[j];
                nums[j] = nums[j + 1];
                nums[j + 1] = tmp;
            }
        }
    }
    return nums;
}
```

## 选择排序

提示：每次选最小值与第一个数进行交换
时间复杂度：$O(n)$ ~ $O(n^2)$
空间复杂度：$O(1)$

```js
function selectionSort(nums) {
    let length = nums.length;
    for (let i = 0; i < length; i++) {
        let minIndex = i;
        for (let j = i; j < length; j++) {
            if (nums[j] < nums[minIndex]) {
                minIndex = j;
            }
        }
        let tmp = nums[i];
        nums[i] = nums[minIndex];
        nums[minIndex] = tmp;
    }
    return nums;
}
```

## 插入排序

提示：将第一个数视为已排好的数组，然后将大于插入对象的数全部像右平移，进行插入
时间复杂度：$O(n)$ ~ $O(n^2)$
空间复杂度：$O(1)$

```js
function insertionSort(nums) {
    for (let i = 1; i < nums.length; i++) {
        let key = nums[i];
        let j = i - 1;
        while (j >= 0 && nums[j] > key) {
            nums[j + 1] = nums[j];
            j--;
        }
        nums[j + 1] = key;
    }
    return nums;
}
```

## 高级排序

## 希尔排序

提示：分治思想，通过设置增量，将其变成若干组插入排序，
时间复杂度：$O(n^{1.3})$ ~ $O(\log_2n)$ ~ $O(n^2)$,
空间复杂度：$O(1)$

```js
function shellSort(nums) {
    if (nums.length <= 1) {
        return nums;
    }

    for (let d = Math.ceil(nums.length / 2); d >= 1; d = Math.ceil(d / 2)) {
        for (let i = 0; i < nums.length - d; i++) {
            for (let j = i; j < nums.length; j = j + d) {
                if (nums[j] > nums[j+d]) {
                    let tmp = nums[j];
                    nums[j] = nums[j+d];
                    nums[j+d] = tmp;
                }
            }
        }
    }
    return nums
}
```

## 快速排序

提示：分治思想
时间复杂度：$O(log_2n)$ ~ $O(n^2)$,
空间复杂度：$O(log_2n)$ ~ $O(n)$

```js
function quickSort(nums) {
    if (nums.length == 0) {
        return [];
    }

    let small = [];
    let great = [];
    let pivot = nums[0];

    for (let i = 1; i < nums.length; i++) {
        if (nums[i] < pivot) {
            small.push(nums[i]);
        } else {
            great.push(nums[i]);
        }
    }
    return quickSort(small).concat(pivot, quickSort(great));
}
```

归并排序

提示：将数据集分解为一组只有一个元素的 数组，然后创建一组左右子数组将他们合并起来。
时间复杂度：$O(log_2n)$
空间复杂度：$O(n)$

```js
function mergeSort(nums) {
    if (nums.length < 2) {
        return nums;
    }
    let middleIndex = parseInt(nums.length / 2);
    let left = nums.slice(0, middle);
    let right = nums.slice(middle);
    return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right) {
    let result = [];
    for (let i = 0, j = 0; i < left.length && j < right.length;) {
        if (left[i] > right[j]) {
            result.push(right[j]);
            j++;
        } else {
            result.push(right[i]);
            i++;
        }
    }

    while (i < left.length) {
        result.push(left[i]);
        i++;
    }

    while (j < right.length) {
        result.push(right[j]);
        j++;
    }

    return result;
}
```
