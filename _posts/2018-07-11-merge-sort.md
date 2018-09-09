---
layout: post
title: 归并排序
date: 2018-07-11
categories: Mathematics
tag: algorithm
excerpt: 本文介绍归并排序算法及其复杂度
---

# 前言

归并操作（merge），也叫归并算法，指的是将两个已经排序的序列合并成一个序列的操作。归并排序算法依赖归并操作。

# 复杂度

归并排序分为拆分和合并两个过程。
1. 拆分
```
第0次        8 7 6 5 4 3 2 1
第1次       8 7 6 5 | 4 3 2 1
第2次     8 7 | 6 5 | 4 3 | 2 1
第3次  8 | 7 | 6 | 5 | 4 | 3 | 2 | 1
```
拆分过程的复杂度主要集中于调用拆分函数的次数。以上面的例子来说，第1次拆分1次，第2次拆分2次，第3次拆分4次，...
拆分次数总和为：S = 1+2+4+...，是一个首项为1，公比为2，项数为log2n的等比数列求和。因此，总和为n-1，具有O(n)时间复杂度。
2. 合并
```
第0次  8 | 7 | 6 | 5 | 4 | 3 | 2 | 1
第1次      7 8 | 5 6 | 3 4 | 1 2
第2次        5 6 7 8 | 1 2 3 4
第3次         1 2 3 4 5 6 7 8
```
合并过程是将两个子部分的每个数进行比较，因此合并过程中每层要比较n次，总层数为log2n。所以，合并过程具有O(nlogn)的时间复杂度。
3. 总结
对于整个归并排序来说，总时间复杂度为O(n+nlogn)-->O(nlogn)。并且，最坏情况和最优情况的时间复杂度是相同的。其次，空间复杂度也是固定的，为O(n)。

# Python实现示例
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-

def merge_sort(nums):
    length = len(nums)
    if length <= 1:
        return nums
    mid = length//2
    f_list = merge_sort(nums[:mid])
    s_list = merge_sort(nums[mid:])

    f = 0
    s = 0
    r_list = []
    while f < mid and s < mid:
        if f_list[f] <= s_list[s]:
            r_list.append(f_list[f])
            f += 1
        else:
            r_list.append(s_list[s])
            s += 1
    
    if f == mid:
        r_list.extend(s_list[s:])
    elif s == mid:
        r_list.extend(f_list[f:])

    return r_list

if __name__ == "__main__":
    nums = [6, 5, 3, 1, 8, 7, 2, 4]
    return_nums = merge_sort(nums)

    print return_nums
```

参考：https://zh.wikipedia.org/wiki/归并排序