---
layout: post
title: 常用编程技巧篇
tags: [C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 读写文件

读写文件的过程
{% highlight c++ linenos %}
#include <fstream>
void handlingFiles(){
    fstream file;
    char text[256];
    file.open("example.txt", ios::out | ios::in);
    cout << "write text to file" << endl;
    cin.getline(text, sizeof(text));
    file << text << endl; //write
    file >> text; //read
    cout << text << endl;
    file.close();
}
{% endhighlight %}


# 分解字符串

C/C++中缺乏按格式分解字符串的方法，我们常常需要自己实现它。这里列举一种我知道的最简便的方法。

{% highlight c++ linenos %}
#include <sstream>
vector<string> tokenize(const string &s, const char &delimiter){
    vector<string> tokens;
    stringstream ss(s); //convert string to stream
    string tmp;
    //get lines from stream
    while(getline(ss, tmp, delimiter)){
        if(!tmp.empty())
            tokens.push_back(tmp); //save tokens
    }
    return move(tokens);
}
{% endhighlight %}

写一个驱动程序测试一下，
{% highlight c++ linenos %}
void tokenizeDemo(){
    string s = "hello world \n hello paopao \n bye bye \n";
    auto lines = tokenize(s, '\n'); //tokenize by line
    for(auto & line : lines){ 
        auto items = tokenize(line, ' '); //by space
        for(auto & item : items){
            cout << "|"<< item <<"|"<< endl;
        }
    }
}
{% endhighlight %}
看一下测试结果,
```
|hello|
|world|
|hello|
|paopao|
|bye|
|bye|
```

{% highlight c++ linenos %}

{% endhighlight %}