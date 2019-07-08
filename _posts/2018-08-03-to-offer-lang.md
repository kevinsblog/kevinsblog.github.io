---
layout: post
title: 剑指offer--语言特性
tags: [Offer, C/C++]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# C++类型sizeof的值

* 空类型实例声明时需要占一定内存，一般为1byte
    > 仅包含一个占位符
* 只拥有函数不拥有成员，类实例也为1byte
    > 函数地址与类型有关，和类型的实例无关
* 拥有虚函数，类实例为4byte或者8byte，取决于机器的体系结构是32bit还是64bit
    > virtual为类型生成虚函数表，每个实例中都有一个指向虚函数表的指针

```c++
//1
class A{

};

//1
class B{
    B(){}
    ~B(){}
};

//8
class C{
    C(){}
    virtual ~C(){}
};

//4
class D{
    D(){}
    ~D(){}
    int d;
};

//16
class E{
    E(){}
    virtual ~E(){}
    int e;
};

void sizeTest(){
    cout << "A "<< "B "<< "C "<<"D "<<"E "<<endl;
    cout << sizeof(A) << " " << sizeof(B) << " " <<
        sizeof(C) << " " <<sizeof(D) << " "<<sizeof(E)<<endl;
}
```
测试一下
```
A B C D E 
1 1 8 4 16
```

# C++的复制构造函数不能传值

* 类型A的复制构造函数若按值传入A的实例，则会在复制构造函数中调用复制构造函数，形成无休止的递归调用，最后栈溢出。
* 索性编译器会阻止这一尝试。

```c++
class E{
    E(){}
    E(E ee){e = ee.e;} //not ok
    //E(const E &&ee){e = ee.e;} //ok
    ~E(){}
    long e;
};
```
编译失败，
```
cpp:247:9: error: copy constructor must pass its first argument by
      reference
    E(E ee){e = ee.e;}
        ^
        const &
```

# C++的异常安全性

考虑写一个字符串封装类型的复制构造操作符。
```c++
class CMString{
public:
    CMString(char * pd = NULL):m_pd(NULL){
        if(pd == NULL) return;
        m_pd = new char[strlen(pd)+1];
        strcpy(m_pd, pd);
    }
    CMString(const CMString &s) = delete;
    CMString & operator=(const CMString &s){
        /* no exception-safe solution */
        //check if src and dst are of the same instance
        if(this == &s) return *this; //return reference to instance
        //release original memory
        delete []m_pd; //take care !!
        m_pd = NULL;

        m_pd = new char[strlen(s.m_pd)+1];
        strcpy(m_pd, s.m_pd);
        return *this;
    }
    ~CMString(){
        if(m_pd)
            delete []m_pd;
    }
    char * get() {
        return m_pd;
    }

private:
    char * m_pd;
};

void strTest(){
    CMString s1((char *)"string");
    CMString s2((char *)"str");
    s2 = s1;
    cout << s2.get() << endl;
}
```
测试一下，
```
string
```
测试结果虽然是对的，但是在分配内存之前先释放了m_pd的内存，如果分配失败，异常抛出，实例不再有效，这违背了异常安全性的原则。

可以修改一下，用一个临时的中间实例来中转数据，旧数据通过中间实例来释放。这样可以在内存分配失败时确保原来的数据不会被修改。
```c++
    CMString & operator=(const CMString &s){
        if(this != &s){
            CMString tmp(s.m_pd);

            //exchange internal data and release the old data
            //  through tmp executing deconstructor 
            char *pTmp = tmp.m_pd;
            tmp.m_pd = m_pd;
            m_pd = pTmp;
        }

        return *this;
    }
```
测试一下，结果同样是正确的。

# C/C++没有记录数组的大小

C中，数组和指针的概念是关联而又存在区别的，数组的名字也是一个指针，指向第一个元素。但是C没有记录数组的大小。
```c++
int GetSize(int data[]){
    return sizeof(data);
}

void sizeTest(){
    int data1[] = {1, 2, 3, 4, 5};
    int *data2 = data1;
    cout << sizeof(data1) << " "<<GetSize(data1)<<" "<<sizeof(data2)<<endl;
}
```
测试一下，sizeof(data1)是求数组的大小4*5=20，传入函数后按指针处理，64bit上指针为8字节长。
```
20 8 8
```