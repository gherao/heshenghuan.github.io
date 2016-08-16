---
title: Leetcode刷题记录
date: 2016-08-16 14:43:54
tags: 刷题
categories: 学习
---

# LeetCode 刷题记录

> 争取每日一题~

## 2015.8~2016.8 期间未做笔记的题目

共71题，具体可查看github repo: [Leetcode-exercise](https://github.com/heshenghuan/leetcode-exercise)

## 2016.8.16

### 10. Regular Expression Matching

> 链接：https://leetcode.com/problems/regular-expression-matching/
>
> 难度：Hard
>
> 标签：Dynamic Programming, Backtracking, String

题目的描述很简单，就是匹配目标正则表达式。这里的要求也较为简单，只需“Implement regular expression matching with `'.'` and `'*'`. ”，且题目要求中有如下语句：

> `'.'` Matches any single character.
> `'*'` Matches zero or more of the preceding element.
>
> The matching should cover the **entire** input string (not partial).

说明要求全局匹配。最初看到这个题目时脑海里还是只有自动机一个思路，但发现题目要求的比较简单，使用自动机来解题似乎是小题大做了。之后发现题目标签中给出了动态规划的tag，仔细思考后发现确实是最便捷的方法了。

来看一下这题的思路:

令dp[i]\[j]表示s[0...i-1]与p[0...j-1]​是否匹配，则根据pattern字符串的字符可分为以下两种情况

1. if p[j-1] != '*'

   dp[i]\[j] = dp[i-1]\[j-1] && (s[i-1] == p[j-1] || '.' == p[j-1])

2. if p[j-1] == '*'

   当以下两个条件任一满足时，dp[i]\[j]为true：

   1. “x*”只重复了0次，匹配一个空字符，dp[i]\[j] = dp[i]\[j-2]
   2. "x*"至少重复了1次，匹配“x\*x”，于是dp[i]\[j] = (s[i-1] == x) && dp[i-1]\[j]

3. '.'匹配任何单个字符

使用dp这个二维数组来记录匹配时的状态，并根据之前的状态做转移。i和j可以想象成指在源串和模式串上的两个指针，当i, j=0时表示空串。

于是有任何dp[i]\[0] = false，而dp[0]\[j]的情况会复杂一点，因为当第一个字符后就有*时空串也能匹配，所以dp[0]\[j] = j>1 && p[j-1] == '\*' && dp[0]\[j-2]（这里的j>1是因为p的第一个字符必不为\*）。

之后的过程就是用一个二重循环进行遍历，更新规则如上即可，最后dp[m]\[n]的值就是答案。