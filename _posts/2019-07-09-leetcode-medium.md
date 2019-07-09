---
layout: post
title: LeetCode专题 - Medium题集合
tags: [Leetcode, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 50. Pow(x, n)

实现 pow(x, n) ，即计算 x 的 n 次幂函数。

```
示例 1:

输入: 2.00000, 10
输出: 1024.00000

示例 2:

输入: 2.10000, 3
输出: 9.26100

示例 3:

输入: 2.00000, -2
输出: 0.25000
解释: 2-2 = 1/22 = 1/4 = 0.25
```

说明:
    -100.0 < x < 100.0
    n 是 32 位有符号整数，其数值范围是 [−231, 231 − 1] 。

{% highlight c++ linenos %}
class Solution {
public:
    double myPow(double x, int n) {
        //corner case
        if(x == 1){
            return x;   
        }else if(x == -1){
            return n & 0x01 ? x : -x;
        }else if(n == 0){
            return 1;
        }else if(n == 1){
            return x;
        }else if(n == INT_MIN){
            return 0.0; //-INT_MIN will be out of range
        }
        
        bool sign = n > 0 ? false : true;
        n = abs(n);
        //2^6 -> calc 2^3 first
        double res = myPow(x, n >> 1);
        //2^3 * 2^3
        res *= res;
        if(n & 0x1 == 1){
            // 2^7 = 2^3 * 2^3 * 2
            res *= x;
        }
        
        if(sign && res != 0){
            return 1/res;
        }else{
            return res;
        }
    }
};
{% endhighlight %}
测试一下，
```
执行结果：
通过
执行用时 :4 ms, 在所有 C++ 提交中击败了90.99% 的用户
内存消耗 :8.3 MB, 在所有 C++ 提交中击败了81.51%的用户
```

{% highlight c++ linenos %}
{% endhighlight %}