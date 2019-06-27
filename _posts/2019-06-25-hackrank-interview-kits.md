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

[原题链接](https://www.hackerrank.com/challenges/sock-merchant/problem?h_l=interview&playlist_slugs%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D=warmup)

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

# Counting Valleys (Scores 15)

[原题链接](https://www.hackerrank.com/challenges/counting-valleys/problem?h_l=interview&playlist_slugs%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D=warmup)

题目大意：一个旅行者记录了沿途的地势，或爬坡或下坡。定义山谷为低于海平面的一段，要求求出旅行者经历过多少个山谷。

解题思路：计算海拔小于0的连续段有多少个。

{% highlight c++ linenos %}
int countingValleys(int n, string s) {
    vector<int> altitude(s.size()+1, 0);
    for(int i = 0; i < s.size(); i++){
        altitude[i+1] = altitude[i] + (s[i] == 'U' ? 1 : -1);
    }
    int ans = 0;
    for(int i = 0; i < altitude.size(); i++){
        //introudce new valley is altitude is below sea level
        if(i>0 && altitude[i-1] >= 0 && altitude[i] < 0){
            ans++;
        }
    }

    //if the hiker can not reach above sea, the last valley is not valid
    if(altitude.back() < 0){
        ans--;
    }

    return ans;
}
{% endhighlight %}
测试一下，
```
Success for all 21 Test Case
```

# Jumping on the Clouds (Scores 20)

[原题链接](https://www.hackerrank.com/challenges/jumping-on-the-clouds/problem?h_l=interview&playlist_slugs%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D=warmup)

题目大意：人要在一些云之间跳跃，标注为1的云不能停留，人可以往前跳一步或者两步，要求跳到终点需要多少步。

解题思路：用动态规划，记录跳到第i朵云需要的最少步数。

{% highlight c++ linenos %}
int jumpingOnClouds(vector<int> c) {
    vector<int> dp(c.size(), 0); //min steps needs to jump on i
    dp[0] = 0;
    for(int i = 1; i < c.size(); i++){
        if(c[i] == 1){
            dp[i] = INT_MAX; //impossible to jump to i
        }else{
            int jump2 = i >= 2 ? dp[i-2] : INT_MAX;
            dp[i] = min(jump2, dp[i-1]) + 1; //jump by 1 step or 2 steps
        }
    }
    return dp.back();
}
{% endhighlight %}
测试一下，
```
Success to pass all 6 cases
```

# Repeated String (Scores 20)

[原题链接](https://www.hackerrank.com/challenges/repeated-string/problem?h_l=interview&playlist_slugs%5B%5D=interview-preparation-kit&playlist_slugs%5B%5D=warmup)

题目大意：给一个字符串，用它不断重复循环形成一个长的字符串，截取这个长字符串的一部分，要求求出字符串第一个字符被重复的总次数。

解题思路：利用/和%的技巧。

{% highlight c++ linenos %}
long repeatedString(string s, long n) {
    vector<int> freq; //count the occurrance of first letter at certain index
    int occ = 0;
    for(auto & c : s){
        if(c == *s.begin()){
            freq.push_back(++occ); //occurrance plus one
        }else{
            freq.push_back(occ);
        }
    }

    int times = n / s.size(),
        remain = n % s.size();
    //mind the priority of ? :, the closure must be there, otherwise,
    //  compiler would interpert like (a + b) ? xx ? xxx;
    return times*occ + (remain > 0 ? freq[remain] : 0 );
}
{% endhighlight %}
测试一下，
```
Fail, pass 5 of 23 cases.
```


{% highlight c++ linenos %}

{% endhighlight %}