---
layout: post
title: LeetCode198（House Robber）
date: 2018-09-10
categories: Mathematics
tag: dynamic programming
excerpt: 本文介绍LeetCode第198题“房屋强盗”
---

# 前言

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

```
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.

Input: [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and rob house 5 (money = 1).
             Total amount you can rob = 2 + 9 + 1 = 12.
```

# 分析

类似于动态规划的思路，对于第i间房间，强盗有两种选择：
1. 抢这间房间，则赃物总数将为前i-2间房间的赃物总数，加上这件房间的赃物；
2. 不抢这间房间，则赃物总数为前i-1间房间的赃物总数。

因此，赃物总数的递推公式为：
1. dp[0] = nums[0];
2. dp[1] = max(nums[0], nums[1]);
3. dp[i] = max(dp[i-2]+nums[i], dp[i-1])

# Python实现示例

```
class Solution(object):
    def rob(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        length = len(nums)
        if length == 0:
            return 0
        if length == 1:
            return nums[0]
        dp = [nums[0], max(nums[0], nums[1])]
        for i in range(2, length):
            dp.append(max(dp[i-1], dp[i-2]+nums[i]))
        return dp[-1]
```