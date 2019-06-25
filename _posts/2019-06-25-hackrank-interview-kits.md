---
layout: post
title: Hankrank题解集合
tags: [Hankrank, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# Sock Merchant (Scores 15)

[原题](https://www.hackerrank.com/challenges/sock-merchant/problem?h_l=interview&playlist_slugs%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D=warmup)

题目大意：一个长为N的数组，表示N条颜色不同的袜子，要求这一堆袜子可以找出几双。

解题思路：记录不同颜色袜子的数目。

{% highlight c++ linenos %}
int sockMerchant(int n, vector<int> ar) {
    unordered_map<int, int> freq;
    for(auto & a : ar){
        freq[a]++;
    }

    int sum = 0;
    for(auto & f : freq){
        sum += f.second/2;
    }
    return sum;
}
{% endhighlight %}
测试一下，
```
Success, 6 cases passed.
```

{% highlight c++ linenos %}
{% endhighlight %}