---
layout: post
title: Light -> Cloud -> Heavy
---

<a name="MBRET"></a>

## Why

你在寝室里有一台笨重但性能好的电脑**Heavy**（内网环境，没有公网 ip），<br />你还有一台低配的阿里云服务器**Cloud**（什么优点都没有，但是它有公网 ip）。

现在你要带着一台轻薄本**Light**去上无聊的课，<br />轻薄本性能不行，你想`ssh`连到寝室的电脑干活。

<a name="MYebq"></a>

## How

1. 先配好 ssh[免密登录](https://blog.csdn.net/doushuopeng/article/details/106919384?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-0.no_search_link&spm=1001.2101.3001.4242)（从**Heavy**到**Cloud**的免密）
   <br />（一句话：把本地机生成的 xxx.pub 的内容追加到远程机的~/.ssh/authorized_keys 中）

2. [https://blog.mythsman.com/post/5d301903976abc05b3454691/](https://blog.mythsman.com/post/5d301903976abc05b3454691/)
   这篇博客里的**A**就是**Cloud**，**B**就是**Heavy**。<br />（不要忘记在**Cloud**以及提供**Cloud**的运营商控制面板开放端口）

<a name="rLlQm"></a>

## Enjoy

这样，在教室落座后，你便可以先`ssh`到你的**Cloud**，再在**Cloud**中`ssh`到你的**Heavy**。<br />![EC83B42A-D8DD-4CE6-87F9-89CBBDFA3B55.jpeg](https://cdn.nlark.com/yuque/0/2021/jpeg/21625412/1634694898495-d3fcef73-8f49-41ec-99c6-13d431bc4459.jpeg#clientId=u02047b27-bcea-4&from=ui&id=u106c9f18&margin=%5Bobject%20Object%5D&name=EC83B42A-D8DD-4CE6-87F9-89CBBDFA3B55.jpeg&originHeight=958&originWidth=1280&originalType=binary&ratio=1&size=291996&status=done&style=shadow&taskId=ue99e83fb-48db-4241-8250-7465c5051da)
