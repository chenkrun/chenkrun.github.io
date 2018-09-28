---
layout: post
title: LeetCode206（Reverse Linked List）
date: 2018-09-28
categories: Mathematics
tag: Linked List
excerpt: 本文介绍LeetCode第206题“反转链表”
---

## 一、前言

Reverse a singly linked list.

```
Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL
```

## 二、分析

题目要求分别用迭代法和递归法来实现“反转链表”，我们用图形来理解这两种方法。

- 循环法

![reverse_linked_list_iteratively.bmp](/images/reverse_linked_list_iteratively.bmp)

- 递归法

![reverse_linked_list_recursively.bmp](/images/reverse_linked_list_recursively.bmp)

## 三、Python实现示例

- 循环法

```
class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head or not head.next:
            return head
        new = None
        while head:
            next = head.next
            head.next = new
            new = head
            head = next
        return new
```

- 递归法

```
class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head or not head.next:
            return head
        new = self.reverseList(head.next)
        head.next.next = head
        head.next = None
        
        return new
```

- 测试

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-

class ListNode(object):
    def __init__(self, x):
        self.val = x
        self.next = None


class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        if not head or not head.next:
            return head
        new = None
        while head:
            next = head.next
            head.next = new
            new = head
            head = next
        return new


if __name__ == "__main__":
    four = ListNode(4)

    three = ListNode(3)
    three.next = four

    two = ListNode(2)
    two.next = three

    one = ListNode(1)
    one.next = two

    head = ListNode(0)
    head.next = one

    new = Solution().reverseList(head)
    while new:
        print new.val
        new = new.next
```

