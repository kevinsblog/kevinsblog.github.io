---
layout: post
title: LeetCode专题-字符串应用
---

## 392. Is Subsequence
Medium

Given a string s and a string t, check if s is subsequence of t.

You may assume that there is only lower case English letters in both s and t. t is potentially a very long (length ~= 500,000) string, and s is a short string (<=100).

A subsequence of a string is a new string which is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, "ace" is a subsequence of "abcde" while "aec" is not).

Example 1:
s = "abc", t = "ahbgdc"

Return true. 

题目大意：给两个字符串s和t，判断s是不是t的子串。s是t的子串意味着可以通过删除一些字符t中的字符得到s。

解题方法：维护两个指针，分别指向s和t，遇到相同的字符则推进指针，若s中所有的字符都可以在t中找到，s是t的子串。

```python
class Solution(object):
    def isSubsequence(self, s, t):
        """
        :type s: str
        :type t: str
        :rtype: bool
        """
        i, j = 0, 0
        while i < len(s) and j < len(t):
            if s[i] == t[j]:
                i += 1
            j += 1

        return i == len(s) #True if all characters in s have been found in t  
```
测试一下
```
Success
[Details]
Runtime: 240 ms, faster than 20.36% of Python online submissions for Is Subsequence.
Memory Usage: 19 MB, less than 63.56% of Python online submissions for Is Subsequence.
```

## 394. Decode String
Medium

Given an encoded string, return it's decoded string.

The encoding rule is: k[encoded_string], where the encoded_string inside the square brackets is being repeated exactly k times. Note that k is guaranteed to be a positive integer.

You may assume that the input string is always valid; No extra white spaces, square brackets are well-formed, etc.

Furthermore, you may assume that the original data does not contain any digits and that digits are only for those repeat numbers, k. For example, there won't be input like 3a or 2[4].

Examples:

s = "3[a]2[bc]", return "aaabcbc".
s = "3[a2[c]]", return "accaccacc".
s = "2[abc]3[cd]ef", return "abcabccdcdcdef".

题目大意：给一个字符串的缩写，解析它得到全称。

解题思路：顺序解析缩写字符串，从'['开始，到']'结束。

```python
class Solution(object):
    def decodeString(self, s):
        """
        :type s: str
        :rtype: str
        """
        idx = 0
        (ans, idx) = self.decodeStringHelper(s, idx)
        return ans        
    
    def decodeStringHelper(self, s, idx):
        ans = ''
        # continue searching if reach end of string or ']'
        while idx < len(s) and s[idx] != ']':
            if s[idx].isdigit():
                repeats = 0
                # occur repeats num
                while idx < len(s) and s[idx].isdigit():
                    repeats = repeats*10 + int(s[idx])
                    idx += 1

                idx += 1 # skip '['
                # handle the string that needs to repeats
                (temp, idx) = self.decodeStringHelper(s, idx)
                idx += 1 # skip ']'
                ans += temp*repeats
            else:
                ans += s[idx]
                idx += 1

        return (ans, idx)    
```
测试一下
```
Success
[Details]
Runtime: 16 ms, faster than 93.20% of Python online submissions for Decode String.
Memory Usage: 11.9 MB, less than 9.04% of Python online submissions for Decode String.
```
