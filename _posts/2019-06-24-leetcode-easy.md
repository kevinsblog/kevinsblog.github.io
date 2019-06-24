---
layout: post
title: LeetCode专题 - Easy题集合
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

#709. To Lower Case

Easy （字符串题）

Implement function ToLowerCase() that has a string parameter str, and returns the same string in lowercase.

```
Example 1:

Input: "Hello"
Output: "hello"

Example 2:

Input: "here"
Output: "here"

Example 3:

Input: "LOVELY"
Output: "lovely"
```

题目大意：设计一个函数，把字符串中的大写字符改成小写。

解题思路：利用std::transform对字符串区间元素进行修改，设计一个lamda表达式作为算子。

{% highlight c++ linenos %}
class Solution {
public:
    string toLowerCase(string str) {
        transform(str.begin(), str.end(), str.begin() ,[](unsigned char c)                  -> unsigned char { return std::tolower(c); });
        return move(str);
    }
};
{% endhighlight %}
```
Success
Details
Runtime: 4 ms, faster than 77.96% of C++ online submissions for To Lower Case.
Memory Usage: 8.2 MB, less than 34.85% of C++ online submissions for To Lower Case.
```