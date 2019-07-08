---
layout: post
title: 剑指offer--设计模式
tags: [Offer, C/C++, Design Pattern]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# Singleton模式

单例模式保证一个类只会有一个实例。

```c++
class Singleton1{
public:
    static Singleton1 *get(){
        if(m_p == nullptr){
            m_p = new Singleton1(0);
        }
        return m_p;
    }
    void SetNum(int n){num = n;}
    int GetNum() const {return num;}
private:
    Singleton1(int n):num(n){} //other people cannot create instance
    static Singleton1 *m_p;
    int num;
};
Singleton1 *Singleton1::m_p = nullptr;

void DesignPatternTest(){
    Singleton1::get()->SetNum(10);
    cout << Singleton1::get()->GetNum() << endl;
}
```
测试一下，
```
10
```
这种实现只能用于单线程环境，多个线程可能会竞争着去创建实例。加锁可以防止竞争的出现。

```c++
mutex       m_lock;
class Singleton1{
public:
    static Singleton1 *get(){
        
        if(m_p == nullptr){
            m_lock.lock();
            if(m_p == nullptr)
                m_p = new Singleton1(0);
            m_lock.unlock();
        }
        
        return m_p;
    }
    .....
```
控制加锁的范围可以降低加锁对性能的影响。