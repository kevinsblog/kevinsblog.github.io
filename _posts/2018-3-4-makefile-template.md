---
layout: post
title: C/C++通用Makefile模板
tags: [C/C++]
bigimg: /img/path.jpg
comments: true
---

**有时候需要维护一个单目录的C/C++小工程，makefile是一个好的选择。但是，许多工程在添加源代码文件时都需要修改makefile，这很不方便，结合网上查的资料，在这里列举一个makefile模板，可以自动编译目录中的源代码，不需要频繁修改makefile**

# C的makefile模板

```makefile
src = $(wildcard *.c)
obj = $(src:.c=.o)

LDFLAGS = 

ds: $(obj)
	$(CC) -std=c99 -g -o $@ $^ $(LDFLAGS)

.PHONY: clean
clean:
	rm -f $(obj) ds
```

# C++的makefile模板

```makefile
src = $(wildcard *.cpp)
obj = $(src:.c=.o)

LDFLAGS =

ds: $(obj)
	$(CXX) -std=c++14 -o $@ $^ $(LDFLAGS)

.PHONY: clean
clean:
	rm -f $(obj) ds
```

makefile将目录内的c/cpp文件一一匹配找出，逐个编译成obj目标文件。LDFLAGS定义了
编译过程中需要链接的共享库，clean命令用于清理编译结果文件。

使用时
```shell
make
make clean
```