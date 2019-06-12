---
layout: post
title: Mac/Linux 配置开发环境
---

**配置开发环境的过程繁琐而易错，每次安装新环境都要花费相当的时间，因此在本文中稍作总结**

# 安装和配置Git服务

## 安装Git
```
sudo apt-get install git-core
```
## 设置当前GitHub用户
```
git config --global user.name <user id>
git config --global user.email <email>
```
## 配置GitHub SSH密钥
测试GitHub的连接，
```
ssh -T git@github.com
```
创建本地SSH密钥，
```
ssh-keygen -t rsa -C "<email>"
```
在GitHub上添加密钥，
```
cat 
```
> 依次点击Account settings（右上角倒数第二个图标） -> SSH Keys -> Add SSH Key，将id_rsa.pub文件中的字符串复制进去，注意字符串中没有换行和空格。

再次用第一步的方法测试GitHub的连接。
```
Hi kevinsblog! You've successfully authenticated, but GitHub does not provide shell access.
```