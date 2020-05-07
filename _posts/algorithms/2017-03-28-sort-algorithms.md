---
title: 常见排序算法
date: 2017-03-28 12:16:23
tags: 
    - sort
    - Algorithms
---

![常见排序算法]({{ site.url }}/assets/imgs/algorithms/sort.jpg/)

<!-- more -->

## 插入排序
### 直接插入排序
时间复杂度: 平均`O(n^2)`，最好`O(n)`，最坏`O(n^2)`
空间复杂度`O(1)`
稳定

对于含`n`个元素的数组`arr`
- 将数组视为有序与无序两部分，`arr[0~i-1]`为有序部分，`arr[i~n-1]`为无序部分
- 外层循环初始值为`i=1`，即`arr[0]`为有序部分
- 从`i=1`开始，依次向后遍历
- 对每一个`arr[i]`，依次向前遍历
- 使得`j = i`，比较`arr[j-1]`与`arr[j]`大小，在`arr[j-1] > arr[j]`时，交换两者的值，继续向前冒泡式遍历。注意继续此层循环的条件是`j-1>=0 && arr[j-1]>arr[j]`

```java
public static void insertionSort(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        for (int j = i; j-1 >= 0 && arr[j-1] > arr[j]; j--) {
            int tem = arr[j];
            arr[j] = arr[j-1];
            arr[j-1] = tem;
        }
    }
}
```
---
### 希尔排序（缩小增量排序）
时间复杂度: 平均`O(n^1.3)`，最好`O(n)`，最坏`O(n^2)`
空间复杂度`O(1)`
不稳定

- 与直接选择排序不同的是，希尔排序是按照一个增量将数组分为几部分，将各部分分别进行直接选择排序，最后进行一次全体的直接插入排序
- 比如，对于`int[] arr = {49, 38, 65, 97, 76, 13, 27, 49, 55, 4}`
设置增量为`int[] gaps = {5, 3, 1}`
第一次排序：将`arr`分为`{49,13}`,`{38,27}`,`{65,49}`,`{97,55}`,`{76,4}`
结果：`{13,27,49,55,4,49,38,65,97,76}`
第二次排序：分为`{13,55,38,76}`,`{27,4,65}`,`{49,49,97}`
结果：`{13,4,49,38,27,49,55,65,97,76}`
第三次进行全体直接插入排序
结果为`{4 13 27 38 49 49 55 65 76 97}`
- 值较小的元素会在前几轮排序中尽量前移，值较大的后移，这样减少了比较和移动的次数，最后一次排序的时候就只需要做少量的比较和移动了

```java
private static void shellSort(int[] arr, int[] gaps) {
    for (int i = 0; i < gaps.length; i++) {
        shellInsert(arr, gaps[i]);
    }
}

private static void shellInsert(int[] arr, int gap) {
    for (int i = gap; i < arr.length; i++) {
        if (arr[i] < arr[i - gap]) {
            for (int j = i; j - gap >= 0 && arr[j - gap] > arr[j]; j -= gap) {
                int tem = arr[j];
                arr[j] = arr[j - gap];
                arr[j - gap] = tem;
            }
        }
    }
}
```
---
## 交换排序
### 冒泡排序
时间复杂度: 平均`O(n^2)`，最好`O(n)`，最坏`O(n^2)`
空间复杂度`O(1)`
稳定

- 原理：每次遍历都将最大的元素往数组尾部交换，当前一次遍历没有进行任何交换的时候，说明数组已经排序好了
- 优化：记录每一次遍历最后交换的位置，则下一次遍历进行到该位置的时候就不用继续向后遍历，因为后面已经排序好了

```java 
private static void bubbleSort(int[] arr) {
    boolean flag = true;
    int k = arr.length - 1;
    while (flag) {
        flag = false;
        for (int i = 0; i < k; i++) {
            if (arr[i] > arr[i + 1]) {
                int tem = arr[i];
                arr[i] = arr[i + 1];
                arr[i + 1] = tem;
                flag = true;
            }
        }
        k--;
    }
}

private static void bubbleSort1(int[] arr) {
    int flag = arr.length - 1;
    while (flag > 0) {
        int k = flag;
        flag = 0;
        for (int i = 0; i < k; i++) {
            if (arr[i] > arr[i + 1]) {
                int tem = arr[i];
                arr[i] = arr[i + 1];
                arr[i + 1] = tem;
                flag = i + 1;
            }
        }
    }
}
```
---
### 快速排序
时间复杂度: 平均`O(nlogn)`，最好`O(nlogn)`，最坏`O(n^2)`
空间复杂度`O(logn)`_要为递归栈提供空间
不稳定

- 选定数组头为哨兵，将数组划分为小于哨兵或大于哨兵两段
- 从两头向中间扫描数组，对于数组末端的，将小于哨兵的移动到数组左端。反之，将数组左端大于哨兵的移动到数组右端
- 注意的是，开始的时候将哨兵取出，所以数组头这个位置就可以被覆盖。所以先从右端开始扫描，找到小于哨兵的那个index假设为`j`，就将`j`移动到数组头，此时`arr[j]`就可以被覆盖。再从左端开始扫描，找到第一个大于哨兵的index为`i`，就可以将`arr[i]`移动到`arr[j]`。以此类推
- 在从两头到中间的扫描过程中，终会出现`i==j`的情况，此时说明已经扫描完毕，现在`i`的位置就是哨兵应该放入的位置

Java实现：
```java 
private static void quickSort(int[] arr) {
    qSort(arr, 0, arr.length - 1);
}

private static void qSort(int[] arr, int low, int high) {
    int i = low;
    int j = high;
    if (i < j) {
        int flag = arr[i];                      // 用字表的第一项作为哨兵
        while (i < j) {                         // 从表的两侧向中间扫描
            while (i < j && arr[j] > flag) {    // 将比哨兵小的项移到左边
                j--;
            }
            arr[i] = arr[j];                    // 因为arr[i]已经保存在flag中了，或者已经移动到右边了

            while (i < j && arr[i] < flag) {    // 将比哨兵大的移动到右边
                i++;
            }
            arr[j] = arr[i];                    // arr[j]已经移动到左边了
        }
        arr[i] = flag;                          // 此时i==j，为中间空余位置

        qSort(arr, low, i - 1);
        qSort(arr, i + 1, high);
    }
}
```
JavaScript实现：
```js
function quickSort(array) {
    qSort(array, 0, array.length - 1);
}

function qSort(array, low, high) {
    var i = low;
    var j = high;
    if (i < j) {
        var flag = array[i];
        while (i < j) {
            while (i < j && arr[j] >= flag) {
                j--;
            }
            array[i] = array[j];

            while (i < j && arr[i] <= flag) {
                i++;
            }
            array[j] = array[i];
        }
        array[i] = flag;

        qSort(arr, low, i - 1);
        qSort(arr, i + 1, high);
    }
}
```
---
## 选择排序
### 简单选择排序
时间复杂度: 平均`O(n^2)`，最好`O(n^2)`，最坏`O(n^2)`
空间复杂度`O(1)`
不稳定

- 对数组的每一个位置`i`，每一次从包括当前位置的后面所有位置中选择最小的值的位置`j`，将`i`与`j`的值互换
- 不稳定。譬如对于数组`{6, 7, 6, 2, 10}`，第一个`6`会与`2`交换位置，结果就是改变了两个`6`的原始顺序

```java
private static void selectSort(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
        int flag = i;
        for (int j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[flag]) {
                flag = j;
            }
        }
        if (flag != i) {
            int tem = arr[i];
            arr[i] = arr[flag];
            arr[flag] = tem;
        }
    }
}
```
---
### 堆排序
时间复杂度: 平均`O(nlogn)`，最好`O(nlogn)`，最坏`O(nlogn)`
空间复杂度`O(1)`
不稳定

- 堆的定义为：n个元素组成的序列`{k1, k2, k3, … ,kn}`，当且仅当满足：
> k<sub>i</sub> <= k<sub>2i</sub> 且 k<sub>i </sub><= k<sub>2i+1</sub> `或 ` k<sub>i</sub> >= k<sub>2i</sub> 且 k<sub>i </sub>>= k<sub>2i+1</sub> 
其中`i = 1, 2, 3, ..., floor(n/2) `

  即完全二叉树中所有非终端节点的值均不大于（或不小于）其左右子节点的值
- 将数组调整为最大堆
- 将堆顶元素与数组尾部元素互换，然后重新调整

```java
private static void heapSort(int[] arr) {
    // 将原数组调整为最大堆
    for (int i = (arr.length - 1) / 2; i >= 0; i--) {
        heapAdjust(arr, i, arr.length);
    }

    // 将堆顶的元素和未排序的最后一个元素互换，然后重新调整为最大堆
    for (int i = arr.length - 1; i > 0; i--) {
        int tem = arr[0];
        arr[0] = arr[i];
        arr[i] = tem;
        heapAdjust(arr, 0, i);
    }
}

private static void heapAdjust(int[] arr, int s, int len) {
    int tem = arr[s];       // 保存堆顶元素
    int j = s * 2 + 1;      // 设置j为左儿子的位置
    while (j < len) {       // index必须小于堆的长度
        // 从左右孩子中选最大的
        if ((j + 1) < len && arr[j] < arr[j + 1]) {
            j++;
        }
        // 是否为该插入的位置
        if (arr[j] < tem) {
            break;
        }
        arr[s] = arr[j];        //将较大的值向上移动
        s = j;                  //指向刚刚移动前的位置
        j = 2 * j + 1;              //进入下一层
    }
    arr[s] = tem;       // 插入
}
```

---
## 归并排序
时间复杂度: 平均`O(nlogn)`，最好`O(nlogn)`，最坏`O(nlogn)`
空间复杂度`O(n)`
稳定

- 归并排序是一个分而治之的思想
- 原序列长度为`n`，可以将其持续划分为`n`个有序的子序列，每个子序列长度为1。将子序列两两合并，就有`ceil(n/2)`个长度为1或2的有序子序列。这样持续归并，直到得到一个长度为`n`的序列，即排序完成
- 例如对于序列`{49, 38, 65, 97, 76, 13, 27}`
划分为`{49, 38, 65, 97}`和`{76, 13, 27}`
再划分：`{49, 38}, {65, 97}, {76, 13}, {27}`
再划分为长度为1的
归并：`{38, 49}, {65, 97}, {13, 76}, {27}`
再归并：`{38, 49, 65, 97}, {13, 27, 76}`
再归并：`{13, 27, 38, 49, 65, 76, 97}`

```java
private static void mergeSort(int[] arr) {
    mSort(arr, 0, arr.length - 1);
}

private static void mSort(int[] arr, int start, int end) {
    // 归并长度为1，直接结束
    if (start == end) {
        return;
    }

    int m = (start + end) / 2;      // 中间点
    mSort(arr, start, m);           // 递归归并前段
    mSort(arr, m + 1, end);   // 递归归并后段
    merge(arr, start, m, end);      // 将两段进行归并
}

private static void merge(int[] arr, int start, int m, int end) {
    int size1 = m - start + 1;          // 前段的长度
    int size2 = end - m;                // 后段的长度
    int[] arr1 = new int[size1];
    int[] arr2 = new int[size2];

    // 将原来数组的值复制入两段新数组
    System.arraycopy(arr, start, arr1, 0, size1);
    System.arraycopy(arr, m + 1, arr2, 0, size2);

    int i = 0, j = 0, k = start;                // i为前段flag，j为后段flag，k为原数组flag
    while (i < size1 && j < size2) {
        if (arr1[i] < arr2[j]) {
            arr[k++] = arr1[i++];
        } else {
            arr[k++] = arr2[j++];
        }
    }

    // 将剩余的部分复制进原数组
    while (i < size1) {
        arr[k++] = arr1[i++];
    }
    while (j < size2) {
        arr[k++] = arr2[j++];
    }
}
```

