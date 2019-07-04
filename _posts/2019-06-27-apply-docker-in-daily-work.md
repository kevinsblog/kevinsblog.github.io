---
layout: post
title: Docker与日常工作
tags: [Docker]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# Resume

用Markdown写简历，再将markdown简历转成pdf或者html文档。
```
docker run -v ${PWD}:/resume there4/markdown-resume md2resume html examples/source/myresume.md examples/output/
```