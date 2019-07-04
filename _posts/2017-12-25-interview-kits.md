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

# Memcpy的实现

不调用C/C++库函数，实现memcpy。

{% highlight c++ linenos %}
void * memcpy(void *dst, void *src, size_t cnt){
    assert(dst != nullptr && src != nullptr);
    unsigned char *pdst = (unsigned char *)dst;
    const unsigned char *psrc = (const unsigned char *)src;

    //make sure no overlap between two memory region
    assert(!(psrc <= pdst && pdst < psrc + cnt) && 
            !(pdst <= psrc && psrc < pdst + cnt));

    while(cnt--){
        *pdst++ = *psrc++;
    }

    return dst;
}

void memcpyTest(){
    //copy str from src to dst and print out
    char src[256] = "hello my friend", dst[256];
    cout << (char *)memcpy(dst, src, strlen(src)+1)<<endl;
}
{% endhighlight %}
测试一下
```
hello my friend
```

# Memove的实现

memove实现的是剪切粘贴的功能，允许内存存在重叠区，可能覆盖原数据。

{% highlight c++ linenos %}
void *memmove(void *dst, void *src, size_t cnt){
    void *ret = dst;
    unsigned char *pdst = (unsigned char *)dst;
    const unsigned char *psrc = (const unsigned char *)src;

    if(pdst < psrc || pdst >= (psrc + cnt)){ //if overlap not exists
        //copy from start to end
        while(cnt--){
            *pdst++ = *psrc++;
        }
    }else{ //if overlap exists
        pdst += cnt - 1;
        psrc += cnt - 1;
        //copy from end to start
        while(cnt--){
            *pdst-- = *psrc--;
        }
    }

    return ret;
}

void memmoveTest(){
    char buf[256] = "hello my friend";
    char *dst = (char *)memmove(buf + 10, buf, strlen(buf)+1);
    printf("%p->%p: %s\n", buf, dst, dst);
}
{% endhighlight %}
测试一下
```
0x7ffee7e1e6a0->0x7ffee7e1e6aa: hello my friend
```

{% highlight c++ linenos %}

{% endhighlight %}