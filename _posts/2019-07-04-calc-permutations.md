---
layout: post
title: 求字符串的所有全排列
tags: [C/C++, Algorithm, Data Structures]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

问题是，求一个字符串中字符的所有排列情况。
```
例如，字符串"abc"，用'a'，‘b’和'c'能排列的所有字符串
    abc, acb, bac, cab 和 cba
```
下面有几种代表性的解法，

# 递归（深度优先搜索）

从集合中依次选出一个元素，作为排列的第一个元素，递归对剩下的元素进行全排列。
```
固定a，对bc进行排列，得到abc，acb
固定b，得到bac，bca
...
```

{% highlight c++ linenos %}
void calcAllPermutation(string &s, int from, int to, vector<string> &ans){
    if(to <= 1){ //invalid pos
        return ;
    }else if(from == to){ //valid string found
        ans.push_back(s.substr(0, to + 1));
    }else{
        //permutation for the rest string
        for(int j = from; j <= to; j++){
            swap(s[j], s[from]);
            calcAllPermutation(s, from + 1, to, ans);
            swap(s[j], s[from]);
        }
    }
}

void calcAllPermutationTest(){
    string s = "abc";
    vector<string> ans;
    calcAllPermutation(s, 0, s.size()-1, ans);
    print(ans);
}
{% endhighlight %}
测试一下，
```
abc acb bac bca cba cab 
```

# 字典序排列

对于字符，可以定义字典顺序。所以给定两个字符串，逐个字符进行比较，先出现较小字符的串顺序小，字符一直相等，则较短的串顺序小。
```
对于1~5组成的组合，
    起点：12345
    终点：54321

从起点出发，每次都生成字典序正好比当前排列大的下一个排列
```

为了使得下一个排列(A)y(B)尽可能小，需要，
    * A尽可能长
    * y尽可能小
    * B中的字符由大到小排列

找下一个排列可以用std::next_permutation()
{% highlight c++ linenos %}
void calcAllPermutation2(string &s, vector<string> &ans){
    do{
        ans.push_back(s);
    }while(next_permutation(s.begin(), s.end()));
    return;
}
{% endhighlight %}
测试一下
```
abc acb bac bca cab cba 
```

也可以自己写一个，
{% highlight c++ linenos %}
bool calcAllPermutation3Helper(string &s, int num){
    int i = 0;
    //find the pos i of last ascending element
    for(i = num - 2; i >= 0 && s[i] >= s[i+1]; i--){}
    if(i < 0){
        return false;
    }

    int k = 0;
    //find the last pos k greater than s[i]
    for(k = num - 1; k > i && s[k] <= s[i]; k--){}
    swap(s[i], s[k]);
    reverse(s.begin() + i + 1, s.begin() + num); //revers the substr after i
    return true;
}

void calcAllPermutation3(string &s, vector<string> &ans){
    do{
        ans.push_back(s);
    }while(calcAllPermutation3Helper(s, s.size()));
}
{% endhighlight %}
测试一下，
```
abc acb bac bca cab cba 
```

