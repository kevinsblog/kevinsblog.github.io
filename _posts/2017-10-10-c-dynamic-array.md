---
layout: post
title: C实现的动态数组
---

#### `关于动态数组`

动态数组vector是日常业务代码最常用的数据结构，大多数高级语言都提供了动态数组的实现， 如c++中的std::vector<T>， python和golang中的[]。然而在c中没有提供这一重要的轮子，我们在这里一步一步构建一个c中的vector，可能不能在正式场景中使用，但是可以作为一个研习数据结构和内存分配的工具。

####  建立动态数组

首先，创建动态数组的数据结构。动态数组存储的元素是void*指针，可以用来存放任意类型的数据，用户负责管理元素内存的分配和释放。

```c
//vector.h
#ifndef VECTOR_H_
#define VECTOR_H_

#define INIT_CAP   10
typedef struct vector{
    int    cap;
    int    len;
    void   **items; 
}vector;

void init_vec(vector *v, int cap);
#endif
```

编写函数，按照初始容量分配初始的动态数组内存，
```c
//vector.c
#include "vector.h"
#include "comm.h"

void init_vec(vector *v, int cap){
    v->cap = cap;
    v->len = 0;
    v->items = calloc(v->cap, sizeof(void *));
    if(!v->items)
        err_exit(MEM_ERR);
}
```

*   （1）设置动态数组的容量为INIT_CAP，使用长度为0。
*   （2）调用calloc() 为数组分配内存，同时初始化为0，在c中对于指针而言是NULL
*   （3）若创建失败，调用err_exit，打印错误信息，同时终止进程。

##### 向动态数组添加元素

push_back()
当新加入元素没有超过数组容量时，在数组末尾加入元素，否则调整容量，分配更多内存，再在尾部加入元素。

push_front()
容量不够时调整数组容量，由于数组内元素在内存中是连续分布的，用memmove()将所有元素后移一个位置，在头部加入元素。

insert()
同push_front()一样，只是后移的元素从准备插入的位置开始。
```c
//vector.c
void push_back_vec(vector *v, void *item){
    if(v->len == v->cap)
        resize_vec(v, v->cap*2);
    v->items[v->len++] = item;
}

void push_front_vec(vector *v, void *item){
    if(v->len == v->cap)
        resize_vec(v, v->cap*2);
    memmove(v->items + 1, v->items, v->len*sizeof(void *));
    v->items[0] = item;
    v->len++;
}

void insert_vec(vector *v, void *item, int ix){
    if(ix <= 0){
        push_front_vec(v, item);
    }else if(ix >= v->len){
        push_back_vec(v, item);
    }else{
        if(v->len == v->cap)
            resize_vec(v, v->cap*2);
        memmove(v->items + ix + 1, v->items + ix, (v->len - ix)*sizeof(void *));
        v->len++;
        v->items[ix] = item;
    }
}
```

*   （1）增加元素前检查数组容量，并进行调整
*   （2）使用memmove减少元素移动的次数

#### 调整动态数组容量

调整数组容量是一个通用方法。
```c
//vector.c
static void resize_vec(vector *v, int new_cap){
    v->cap = new_cap;
    v->items = realloc(v->items, sizeof(void *)*v->cap);
    if(!v->items)
        err_exit(MEM_ERR);
}
```

*   （1）调用realloc()重新分配内存，转移元素，从而调整数组容量

#### 从动态数组删除元素

pop_back()
删除数组尾部的元素，数组元素足够小时调整数组容量。

pop_front()
删除数组头部的元素，通过往前移动后续元素完成。

del_at()
与pop_front()一样前移元素。

删除完成后，返回被删除的元素。
```c
//vector.c
void *pop_back_vec(vector *v){
    if(v->len < 1)
        return NULL;
    int last = v->len-1;
    void *ret = v->items[last];
    v->items[last] = NULL;
    --v->len;
    if(v->len < v->cap/3)
        resize_vec(v, v->cap/2);
    return ret;
}

void *pop_front_vec(vector *v){
    if(v->len < 1)
        return NULL;
    void *ret = v->items[0];
    v->len--;
    memmove(v->items, v->items + 1, v->len*sizeof(void *));
    if(v->len < v->cap/3)
        resize_vec(v, v->cap/2);
    return ret;    
}

void *del_at_vec(vector *v, int ix){
    if(v->len == 0 || v->len <= ix || ix < 0)
        return NULL;
    else if(v->len - 1 == ix){
        return pop_back_vec(v);
    }else if(ix == 0){
        return pop_front_vec(v);
    }

    void *ret = v->items[ix];
    memmove(v->items + ix, v->items + ix + 1, (v->len - ix - 1)*sizeof(void *));
    v->len--;
    if(v->len < v->cap/3)
        resize_vec(v, v->cap/2);
    return ret;
}
```

*   （1）为了实现队列和栈，需要实现两端插入和推出
*   （2）为了实现随机存取，需要实现按索引插入和推出

#### 添加测试用例

添加一系列简单的测试用例测试功能。
```
//vector.h
#define INIT_VEC(vec) vector vec; init_vec(&vec, INIT_CAP)
#define FREE_VEC(vec) reset_vec(&(vec))
#define REINIT_VEC(vec) reset_vec(&(vec)); init_vec(&vec, INIT_CAP)</pre>
```
```
//vector.c
void test_vec(){
    vector v;
    init_vec(&v, INIT_CAP);
    for(long i = 0; i < 20; i++){
        push_back_vec(&v, (void *)(i));
    }
    for(int i = 0; i < 3; i++){
        pop_front_vec(&v);
        pop_back_vec(&v);
    }
    insert_vec(&v, (void *)3, 3);
    del_at_vec(&v, 3);
    print_vec(&v);

    reset_vec(&v);
}
```

*   （1）为了方便调用，添加了宏定义INIT, REINIT 和FREE

#### C++中的数组和动态数组

###### C/C++原生数组

数组是一系列同类型元素的集合，在内存中以连续方式存储。

如下，我们初始化了一个数组，
```c++
int bar [5]; //uninitialized
int poo [5] = {}; //init to 0
int foo [5] = { 16, 2, 77, 40, 12071 }; //init to some values
```
```
uninitialized: 2 0 1832921856 32723 2 
initialized: 0 0 0 0 0
16 2 77 40 12071
```
![数组内存分布](http://www.cplusplus.com/doc/tutorial/arrays/arrays2.png)

通过索引访问数组。

###### std::array<T>

std::array<T>是一个STL容器，它封装了一个长度在编译期确定的数组，
```c++
array<int,3> myarray {10,20,30};

for (int i=0; i < myarray.size(); ++i)
    ++myarray[i];

for (int elem : myarray)
    cout << elem << '\n';
```
###### std::vector<T>

std::vector<T>封装的是动态数组
```c++
// Create a vector containing integers
std::vector<int> v = {7, 5, 16, 8};

// Add two more integers to vector
v.push_back(25);
v.push_back(13);

// Iterate and print values of vector
for(int n : v) {
    std::cout << n << '\n';
}
```

[代码清单](https://github.com/KevinACoder/Learning/tree/master/ds)
