---
layout: post
title: Jekyll博客配置笔记
subtitle: Beautiful Jekyll
tags: [css]
comments: true
---

* toc
{:toc}

# 创建Jekyll博客

# Markdown语法

## 添加目录页

在文章开头写下，
```
* toc
{:toc}
```

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

# 利用StaticMan为博客添加评论功能

```
Staticman comments

To use Staticman, you first need to invite staticmanlab as a collaborator to your repository (by going to your repository Settings page, navigate to the Collaborators tab, and add the username staticmanlab), and then accept the invitation by going to https://staticman3.herokuapp.com/v3/connect/github/<username>/<repo-name>. Lastly, fill in your repository and branch in the Staticman section of _config.yml.
```

* 在仓库repository的设置setting中添加贡献者Collaborators--staticmanlab。
* 访问网页https://staticman3.herokuapp.com/v3/connect/github/<username>/<repo-name>
    接受贡献添加邀请
    https://staticman3.herokuapp.com/v3/connect/github/kevinsblog/kevinsblog.github.io
* 更改_config.yml设置

```
# Staticman support
staticman:
  repository : "KevinACoder/kevinacoder.github.io" # GitHub username/repository eg. "daattali/beautiful-jekyll"
  branch     : "master"# eg. "master" If you're not using `master` branch, then you also need to update the `branch` parameter in `staticman.yml`
  endpoint   : "https://kevinacoder.github.io/" # URL of your own deployment (with trailing slash) (will fallback to a public GitLab instance)
  reCaptcha:
    # reCaptcha for Staticman (OPTIONAL)
    # If you use reCaptcha, you must also set these parameters in staticman.yml
    siteKey  : # Use your own site key, you need to apply for one on Google
    secret   : # ENCRYPT your password by going to https://staticman3.herokuapp.com/v3/encrypt/<your-site-secret>

```

* 在文章设置中加入 comments: true
* 可以得到评论页。

