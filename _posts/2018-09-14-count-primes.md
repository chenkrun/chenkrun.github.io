---
layout: post
title: LeetCode204（Count Primes）
date: 2018-09-14
categories: Mathematics
tag: algorithm
excerpt: 本文介绍LeetCode第204题“计算素数”以及一些关于“素数”的算法
---

# 前言

Count the number of prime numbers less than a non-negative number, n.

```
Input: 10
Output: 4
Explanation: There are 4 prime numbers less than 10, they are 2, 3, 5, 7.
```

# 分析

1. 最先想到的方法，是逐个对n以内的数字进行素性判断，最后对满足素性的数字进行统计。但是，当n非常大时，会非常耗时；
2. 埃拉托斯特尼筛法（参考**维基百科**）。给出要筛数值的范围n，找出√n以内的素数，把素数留下，把素数的倍数剔除掉。

埃拉托斯特尼筛法，可以用以下的伪代码表示：

```
Input: an integer n > 1
 
Let A be an array of Boolean values, indexed by integers 2 to n,
initially all set to true.
 
 for i = 2, 3, 4, ..., not exceeding √n:
  if A[i] is true:
    for j = i2, i2+i, i2+2i, i2+3i, ..., not exceeding n :
      A[j] := false
 
Output: all i such that A[i] is true.
```

选择√n的原因是，若n为合数，不为素数，那么一定由两个自然数相乘得到。一个大于或等于√n，一个小于或等于√n。

# Python实现示例

```
class Solution(object):
    def countPrimes(self, n):
        """
        :type n: int
        :rtype: int
        """
        if n<=2:
            return 0
        prime_list = [1]*n
        prime_list[0] = 0
        prime_list[1] = 0
        for i in range(2, int(n**0.5)+1):
            if prime_list[i] == 1:
                prime_list[i*i:n:i] = [0]*len(prime_list[i*i:n:i])
        return sum(prime_list)
```

# 与素数相关的其他算法

1.费马素性检验

费马素性检验利用随机化算法判断一个数是合数还是“可能”是素数。其概念如下：

```
“费马小定理”：
如果n是素数，1<=a<=n-1，那么如果a^(n-1)和1除以n的余数相等（同余定理），则n有可能为素数，否则n是合数。

若a^(n-1)和1除以n的余数相等，但n是合数，则a被称为费马骗子数（Fermat liars）或卡米歇尔数；
若a^(n-1)和1除以n的余数不想等，那么a就是费马证人数（Fermat witness）。
```

正是由于存在卡米歇尔数，令费马素性检验无法成为经常实际应用的素性检验。但是，卡米歇尔数很稀有，所以也有一些应用场景，如加密程序PGP。

2.米勒-拉宾素性检验

米勒-拉宾素性检验也是一种素数判定法则，利用随机化算法判断一个数是合数还是“可能”是素数。其概念如下：

```
假设n是一个奇素数，且n>2。于是n-1是一个偶数，可以被表示为2^s*d的形式，s和d都是正整数且d是奇数。

对于任意0<=r<=s-1，若均满足：
1.a^d和1除以n的余数不相等；
2.a^(2^r*d)和-1除以n的余数不相等。

则n不是素数，a称为n是合数的一个凭证。否则，a可能是一个证明n是素数的“强伪证”。对于测试一个大数是否是素数，常见的做法是随机选取基数a。
```

米勒-拉宾素性检验，可以用以下伪代码表示：

```
write n − 1 as 2r·d with d odd by factoring powers of 2 from n − 1
WitnessLoop: repeat k times:
    pick a random integer a in the range [2, n − 2]
    x ← ad mod n
    if x = 1 or x = n − 1 then
        continue WitnessLoop
    repeat r − 1 times:
    x ← x2 mod n
    if x = 1 then
        return composite
    if x = n − 1 then
        continue WitnessLoop
    return composite
return probably prime
```

Python实现实例：

```
待完成
```