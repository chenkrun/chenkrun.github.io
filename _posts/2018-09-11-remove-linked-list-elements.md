---
layout: post
title: LeetCode203（Remove Linked List Elements）
date: 2018-09-11
categories: Mathematics
tag: Linked List
excerpt: 本文介绍LeetCode第203题“去除链表中的元素”
---

# 前言

Remove all elements from a linked list of integers that have value val.

```
Input:  1->2->6->3->4->5->6, val = 6
Output: 1->2->3->4->5
```

# 分析

这个问题的关键点在于，当遍历链表过程中发现某个节点是需要删除的节点时，已经晚了。因为，不能再回到该节点前一个节点了。有个技巧，就是在原链表头节点前加入一个节点，从头节点前的节点开始，与头节点一起遍历链表。

# Python实现示例

```
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def removeElements(self, head, val):
        """
        :type head: ListNode
        :type val: int
        :rtype: ListNode
        """
        tmp = ListNode(0)
        tmp.next = head
        pre = tmp
        cur = head
        while cur is not None:
            if cur.val == val:
                pre.next = pre.next.next
            else:
                pre = pre.next
            cur = cur.next
        return tmp.next
```