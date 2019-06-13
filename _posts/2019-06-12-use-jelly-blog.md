---
layout: post
title: Jekyll博客配置笔记
subtitle: Beautiful Jekyll
tags: [css]
comments: true
---

# 创建Jekyll博客

# Markdown语法

## 页面内跳转

先定义[起跳点](#jump)，
```
[55. Jump Game](#jump55)
```

然后定义目标点，
<span id="jump"></span>
```
<span id="jump55">Medium</span>
```

# 用Markdown写简历

* 开启Docker服务
* 后台运行Docker镜像markdown-resume
* 将markdown文件转换成pdf
```
docker run -v ${PWD}:/resume there4/markdown-resume md2resume pdf --no-ansi examples/source/zhsample.md examples/output/
```
* 将markdown文件转换成html文件
```
docker run -v ${PWD}:/resume there4/markdown-resume md2resume html --no-ansi examples/source/zhsample.md examples/output/
```