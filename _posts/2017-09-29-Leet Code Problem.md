---
title:  Leet Code Problem
date:   2017-09-29
categories: [Data Struct, LeetCode]
tags: [Data Struct]
---

## Leet Code Problem

412.Fizz Buzz

Write a program that outputs the string representation of numbers from 1 to n.

But for multiples of three it should output “Fizz” instead of the number and for the multiples of five output “Buzz”.  
For numbers which are multiples of both three and five output “FizzBuzz”.

Example:

n = 15,

Return:[ “1”, “2”, “Fizz”, “4”, “Buzz”, “Fizz”, “7”, “8”, “Fizz”, “Buzz”, “11”, “Fizz”, “13”, “14”, “FizzBuzz”]

    class Solution(object):
        def fizzBuzz(self, n):
            res = []
            for i in range(1,n+1):
                if (i % 3 == 0 ) and (i % 5 == 0 ):
                    res.append('FizzBuzz')
                elif (i % 3 == 0 ):
                    res.append('Fizz')
                elif (i % 5 == 0 ):
                    res.append('Buzz')
                else :
                    res.append(str(i))
    
            return  res



* * *

88.Merga Sorted Array

Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

Note:You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2.

The number of elements initialized in nums1 and nums2 are m and n respectively.

    class Solution(object):
        def merge(self, nums1, m, nums2, n):
            while m > 0 and n > 0:
                if nums1[m-1] <= nums2[n-1]: 
                	nums1[m+n-1] = nums2[n-1] 
                	n-=1 
                else: 
                	m-=1 
                
                if n> 0:
                nums1[:n] = nums2[:n]



* * *

41. First Missing Positive

Given an unsorted integer array, find the first missing positive integer.

For example,

Given [1,2,0] return 3,

and [3,4,-1,1] return 2\.

Your algorithm should run in O(n) time and uses constant space.

    class Solution(object):
        def firstMissingPositive(self, nums):
            """
            :type nums: List[int]
            :rtype: int
            """
    
            for i in range(len(nums)):
                if i + 1 not in nums:
                    return  i + 1
    
            return len(nums) + 1



```
class Solution(object):
    def firstMissingPositive(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        i = 0

        while i < len(nums):
            if nums[i] != i + 1 and nums[i] < len(nums) and nums[i] > 0 and nums[i] != nums[nums[i] - 1]:
                temp = nums[i]
                nums[i] = nums[temp - 1]
                nums[temp - 1] = temp
            else:
                i += 1

        i = 0
        for i in range(len(nums)):
            if nums[i] != i + 1:
                return i + 1

        return len(nums) + 1
```



* * *

206. Reverse Linked List

Reverse a singly linked list.

Hint:A linked list can be reversed either iteratively or recursively. Could you implement both?

Note: 

找出Node和Node.next,

定义个new next为None,

对Node和Node.next

重定向init : new next = None

* * *

old node <- node

node <- node.next

old node.next -> new next

new next = old node

    class Solution(object):
        def reverseList(self, head):
            """
            :type head: ListNode
            :rtype: ListNode
            """
    
            if not head: return None
            if not head.next: return head
            nex = head.next
            reversenode = self.reverseList(nex)
            nex.next = head
            head.next = None
            return reversenode



```
class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        newNext = None
        while head:
            old = head
            head = head.next
            old.next = newNext
            newNext = old

        return newNext
```



* * *

24. Swap Nodes in Pairs

Given a linked list, swap every two adjacent nodes and return its head.

For example,

Given 1->2->3->4, you should return the list as 2->1->4->3.

Your algorithm should use only constant space.

You may not modify the values in the list, only nodes itself can be changed.

Note: 等号左边为指针，等号右边为内存

    # Definition for singly-linked list.
    # class ListNode(object):
    #     def __init__(self, x):
    #         self.val = x
    #         self.next = None
    
    class Solution(object):
        def swapPairs(self, head):
            """
            :type head: ListNode
            :rtype: ListNode
            """
            if not head or not head.next: return head
            pre = head.next
            head.next = self.swapPairs(head.next.next)
            pre.next = head
            return pre



* * *

最短路径

Dijkstra’s algorithm

    1  function Dijkstra(Graph, source):
    2      dist[source] ← 0                          // 初始化源点到自己的距离为0
    3
    4      create vertex set Q                       // 创建顶点集合Q
    5
    6      for each vertex v in Graph:               // 循环图中所有节点
    7          if v ≠ source                         // 排除源点
    8              dist[v] ← INFINITY                // 源点到任意v节点距离为无限大
    9              prev[v] ← UNDEFINED               // 任意v节点的前一节点为未定义
    10         Q.add_with_priority(v, dist[v])       // 将有所节点加入集合Q
    11
    12
    13
    14     while Q is not empty:                        // 循环集合Q
    15         u ← Q.extract_min()                      // 取出最小距离的节点 源点距离为0 从源点开始
    16         for each neighbor v of u:                // 循环 u 的所有临节点 v
    17             alt ← dist[u] + length(u, v)         // (source -> u 的距离) +（u -> v节点的距离） 
    18             if alt < dist[v]                     // 两重比较：1.从上一层扩散到下一层时，比较之前定义未知节点的无限大距																											// 离，从而进入下一层 2.进入下一层后反复比较 u 到所有它的临节点 v 的																										// 距离，哪个最小 
    19                 dist[v] ← alt                    // 更新源点到节点v的距离
    20                 prev[v] ← u                      // 将u定义为v的最短路径中的前一节点
    21                 Q.decrease_priority(v, alt)      // 降低v在Q集合中的优先级
    22
    23     return dist[], prev[]

98\. Validate Binary Search Tree

Given a binary tree, determine if it is a valid binary search tree (BST).

Assume a BST is defined as follows:

The left subtree of a node contains only nodes with keys less than the node’s key.

The right subtree of a node contains only nodes with keys greater than the node’s key.

Both the left and right subtrees must also be binary search trees.

Example 1: 2 / \ 1 3

Binary tree [2,1,3], return true.

Example 2: 1 / \ 2 3

Binary tree [1,2,3], return false.

    Definition for a binary tree node.
    class TreeNode(object):
        def __init__(self, x):
            self.val = x
            self.left = None
            self.right = None
    
    class Solution(object):
        def isValidBST(self, root):
            return self.isvalidBst(root, min = float('-inf'), max = float('inf'))


​    
        def isvalidBst(self,root, min, max):
            if not root:
                return True  
            if root.val >= max or root.val <= min:
            	return false
            	
            self.isvalidbst(root.left,min,root.val) and self.isvalidbst(root.right,root.val, max)



---

125. Valid Palindrome

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

For example,

“A man, a plan, a canal: Panama” is a palindrome.

“race a car” is not a palindrome.

Note:

Have you consider that the string might be empty? This is a good question to ask during an interview.

For the purpose of this problem, we define empty string as valid palindrome.

判断是否字符串是否为回文

回文：句子或者单词颠倒是相同的就为回文

    class Solution(object):
        def isPalindrome(self, s):
    
            newS = []
    
            s = s.lower()
            for c in s:
                if c.isalnum():
                    newS.append(c)
    
            return  newS == newS[::-1]
    
    class Solution(object):
        def isPalindrome(self, s):ol
            """
            s = s.lower()
            leftStep = 0
            rightStep = len(s) - 1
    
            while leftStep < rightStep:
                while leftStep < rightStep and not s[leftStep].isalnum():
                    leftStep += 1
                while leftStep < rightStep and not s[rightStep].isalnum():
                    rightStep -= 1
                if s[leftStep] != s[rightStep]:
                    return False
                else:
                    leftStep += 1
                    rightStep -= 1
            return True



---

20. Valid Parentheses

Given a string containing just the characters ‘(‘, ‘)’, ‘{‘, ‘}’, ‘[‘ and ‘]’, determine if the input string is valid.

The brackets must close in the correct order, “()” and “()[]{}” are all valid but “(]” and “([)]” are not.

    class Solution(object):
        def isValid(self, s):
            stack = []
    
            for c in s:
                if c == '(' or c == '[' or c == '{':
                    stack.append(c)
                elif stack == [] \
                        or c == ')' and stack.pop() != '(' \
                        or c == ']' and stack.pop() != '[' \
                        or c == '}' and stack.pop() != '{':
                    return False
            return stack == []



---

32. Longest Valid Parentheses

Given a string containing just the characters ‘(‘ and ‘)’, find the length of the longest valid (well-formed) parentheses substring.

For “(()”, the longest valid parentheses substring is “()”, which has length = 2\.

Another example is “)()())”, where the longest valid parentheses substring is “()()”, which has length = 4\.

    class Solution(object):
        def longestValidParentheses(self, s):
    
            stack = []
            start = 0
            longest = 0
    
            if len(s) == 0 or not s:
                return 0
    
            for i ,c in enumerate(s):
                if c == '(':
                    stack.append(i)
                else:
                    if stack == []:        #如果字符不为“左括号”并且栈内无元素（当前值为右括号）
                        start = i + 1      #将起始索引设置为当前右括号下一位
                    else:
                        stack.pop()        #当前字符为右括号，栈内有元素（左括号），将栈内元素pop出来
                        # 如果栈pop出元素后为空栈，将当前字符索引减去匹配的左括号起始索引 然后元素个数并且加一
                        # 不为空，当前字符索引减去栈内元素的索引
                        longest = max(longest,i - start + 1) if stack == [] else max(longest,i - stack[-1])    
            return longest
    
    class Solution(object):
        def longestValidParentheses(self, s):
    
            stack = []
            nums = []
    
            for i in range(0, len(s)):
                nums.append(0)
    
            for i, c in enumerate(s):
                if c == '(':
                    stack.append(i)
                elif c == ')' and stack != []:
                    nums[i] = nums[stack[-1]] = 1
                    stack.pop()
    
            longest = temp = 0
            for i, c in enumerate(s):
                if nums[i]:
                    temp += 1
                    longest = max(longest, temp)
                else:
                    temp = 0
    
            return longest

141.Linked list Cycle

Given a linked list, determine if it has a cycle in it.

Follow up:  
Can you solve it without using extra space?

    class Solution(object):
        def hasCycle(self, head):
    
            if not head: return False
    
            slow = head
            fast = head
    
            while fast.next and fast.next.next:
                slow = slow.next
                fast = fast.next.next
                if fast is slow:
                    return True
            return False



---

141.Linked list Cycle II

Given a linked list, return the node where the cycle begins. If there is no cycle, return null.

Note: Do not modify the linked list.

Follow up:

Can you solve it without using extra space?

    class Solution(object):
        def detectCycle(self, head):
    
            if not head: return None
    
            slow = head
            fast = head
    
            while fast.next and fast.next.next:
                slow = slow.next
                fast = fast.next.next
                if fast is slow:
                    while head is not slow:
                        slow = slow.next
                        head = head.next
                    return head
    
            return None



---

FindNumber

在二维数组中查找一个数字是否存在

    class Solution:
        def Find(self, target, array):
    
            rowIndex = 0
            lineIndex = len(array[0]) - 1
    
            while lineIndex >= 0 and \
                            rowIndex <= len(array) - 1: if target="=" array[rowindex][lineindex]: return true elif> array[rowIndex][lineIndex]:
                    rowIndex += 1
                elif target < array[rowIndex][lineIndex]:
                    lineIndex -= 1
            return False



---

RebuildTree

给予你两个前序遍历和中序遍历后的数组，重新生成树

    class TreeNode:
        def __init__(self, x):
            self.val = x
            self.left = None
            self.right = None
    
    class Solution:
        def reConstructBinaryTree(self, pre, tin):
    
            if not pre and not tin:
                return None
            root = TreeNode(pre[0])
    
            #根节点在中序遍历数组中的索引位置
            root_pos_in_order = tin.index(root.val)
    
            # <:左子树元素>:右子树元素
            # 前序遍历
            # [root,<,<,<,<,<,>,>,>,>]
            # 中序遍历
            # [<,<,<,<,<,root,>,>,>,>]
    
            #左子树节点个数  在中序遍历后的数组中，左子树节点元素在根节点的左侧
            # 因此左子树的节点个数为 中序遍历后数组中根节点的索引下标值
            #  1 2 3 4 5 6    7 8 9 10 节点个数
            #  0 1 2 3 4 5    6 7 8 9  数组下标索引
            left_length = root_pos_in_order
    
            right_length = len(tin) - (left_length + 1) #(左子树节点个数 + 根节点)
    
            # pre[1:root_pos_in_order] 为前序遍历结果中 当前节点的左子树范围
            # tin[:root_pos_in_order - 1] 为中序遍历结果中 当前节点的左子树范围
            #  为确定下一层 节点 区分其 左 右 子树范围提供索引 而传递使用
            if left_length > 0:
                root.left = self.reConstructBinaryTree(pre[1:root_pos_in_order + 1],tin[:root_pos_in_order])
    
            # pre[root_pos_in_order + 1:] 为前序遍历结果中 当前点的右子树范围
            # tin[:root_pos_in_order - 1] 为中序遍历结果中 当前节点的右子树范围
            #  为确定下一层 节点 区分其 左 右 子树范围提供索引 而传递使用
            if right_length > 0:
                root.right = self.reConstructBinaryTree(pre[root_pos_in_order + 1:],tin[root_pos_in_order + 1:])
    
            return root

