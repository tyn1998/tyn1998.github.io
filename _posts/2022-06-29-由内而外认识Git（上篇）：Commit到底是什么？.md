---
layout: post
title: 由内而外认识Git（上篇）：Commit到底是什么？
categories: [Git]
---

# 前言

在使用Git时，你曾经有过这些困惑吗？

1、 每次`git commit`提交的内容是不是只有代码中被修改的那部分？

2、 我在前面的某个commit中实现了一个功能，若我把该commit删掉，那这部分功能是不是被移除了？

3、 commit是不是增量？我是不是要把所有commit连起来才能得到完整的代码？

这些困惑归结起来其实是一个问题：“commit到底是什么？”要想回答这个问题，你必须知道Git管理版本的原理。什么？原理？不不不，我只想知道怎么用Git就行，我不想知道原理！亲爱的读者请你放心，这个原理真的很好懂，掌握了它就像掌握了Git世界的牛顿第二定律——你会恍然大悟，你会茅塞顿开，你会感到相见恨晚！

# 从树说起

如果你学过数据结构的话，肯定认识树。如下图，一棵树有一个根节点，它可以**指向**任意多的子节点；每个子节点又可以是一棵树，或者是一个叶子。So easy！

<img src="https://cdn.nlark.com/yuque/0/2022/png/21625412/1656470231686-3e3b6163-3e92-47b5-8e6a-b46771711f0e.png#clientId=ua9a6e332-ed98-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=220&id=ub4639cc5&margin=%5Bobject%20Object%5D&name=image.png&originHeight=440&originWidth=658&originalType=binary&ratio=1&rotation=0&showTitle=false&size=117641&status=done&style=none&taskId=ub2da01d8-cf4d-4534-ab33-e3f3f5e53dc&title=&width=329" style="width:400px;"/>

有一棵树，我们天天和它打交道，那就是文件树。如下图，[`Hypercrx`](https://github.com/hypertrons/hypertrons-crx)是该项目文件树的根节点；根节点**指向**许多子节点，每个子节点不是目录就是文件；从根节点逐层而下，就能得到`Hypercrx`的整个文件树。

<img src="https://cdn.nlark.com/yuque/0/2022/png/21625412/1656478513960-8af274e5-50ff-480c-9650-6ac90b760f5d.png#clientId=ua9a6e332-ed98-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=429&id=u0a96826c&margin=%5Bobject%20Object%5D&name=image.png&originHeight=858&originWidth=2020&originalType=binary&ratio=1&rotation=0&showTitle=false&size=238289&status=done&style=none&taskId=u14fced43-b36b-4930-a697-290f741c3df&title=&width=1010" style="width:600px;"/>

# Git Objects（版本管理核心）

在Git中，**每一个commit对应一棵文件树**。怎么理解呢？让我们先来认识一下Git中的3种[objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)：**blob** object、**tree** object、**commit** object。每个object由一个40位的哈希值唯一标识，如下图，你可以在`.git/objects`目录中找到仓库中所有的objects，可以发现，哈希值前2位作为目录名，后38位作为文件名（[为什么要拆成2+38？](https://stackoverflow.com/a/18732276/10369621)）。

<img src="https://cdn.nlark.com/yuque/0/2022/png/21625412/1656478465914-10dcad7d-1a0b-45f1-ba55-3ed288f85052.png#clientId=ua9a6e332-ed98-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=429&id=YrSgz&margin=%5Bobject%20Object%5D&name=image.png&originHeight=858&originWidth=2004&originalType=binary&ratio=1&rotation=0&showTitle=false&size=216847&status=done&style=none&taskId=u65b46d07-ec63-43b0-a5df-dab33201aa4&title=&width=1002" style="width:600px;"/>

在3种objects中，blob对应文件，tree对应目录，每个文件的每个版本都会生成一个blob，每个目录的每个版本都会生成一个tree。

Git的精妙之处在于，如果两个版本之间只修改了一个文件，那么只有那个文件会再生成一个blob，并且包含那个文件的所有目录都再生成新的tree，其他不变，通过调整**指向**，最后得到一棵“新”的文件树。而commit的一个重要作用就是记录这棵新文件树对应的tree的哈希值。所以说，一个commit对应一棵文件树。

<img src="https://cdn.nlark.com/yuque/0/2022/png/21625412/1656480063375-2e5f1889-3ec1-4372-9edb-6d31f8d06d3a.png#clientId=ua9a6e332-ed98-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=298&id=u8b4e8bab&margin=%5Bobject%20Object%5D&name=image.png&originHeight=595&originWidth=800&originalType=binary&ratio=1&rotation=0&showTitle=false&size=94179&status=done&style=none&taskId=u7e7801b5-6c5b-4e7b-98f0-9dfbbd551e8&title=&width=400" style="width:400px;"/>

举个例子。在上图中，这个Git仓库里一共有9个objects：3个commit objects、3个tree objects和3个blob objects。3个commits说明有3个版本，每个commit指向的tree对应的就是该版本中项目的根目录。现在请你做个小练习：根据“third commit”还原该版本对应的文件树结构。

```bash
./
├── bak
│   └── test.txt
├── new.txt
└── test.txt
```

# Commit到底是什么？

| 这是commit | 这不是commit |
| --- | --- |
| <img src="https://cdn.nlark.com/yuque/0/2022/png/21625412/1656486211159-eaf520bd-e01a-4d77-bced-680fed5413e9.png#clientId=ua9a6e332-ed98-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=599&id=u3ca4c004&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1198&originWidth=1228&originalType=binary&ratio=1&rotation=0&showTitle=false&size=260585&status=done&style=none&taskId=u7c85f51a-3dc5-46af-827a-e13c9c392f7&title=&width=614" style="width:350px;"/> | <img src="https://cdn.nlark.com/yuque/0/2022/png/21625412/1656486316399-501a855f-eea0-4627-9511-0d1a9fc5ecb1.png#clientId=ua9a6e332-ed98-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=904&id=ud279f50b&margin=%5Bobject%20Object%5D&name=image.png&originHeight=1808&originWidth=2002&originalType=binary&ratio=1&rotation=0&showTitle=false&size=436329&status=done&style=none&taskId=u7adf38d7-8262-4221-973f-7418e0e940a&title=&width=1001" style="width:350px;"/> | 
| 利用`git cat-file -p <hash>`命令可以根据哈希值查看任何一个object的内容。可以看到，commit内容的第一行就说明它指向了哪个tree。 | GitHub的commit页面呈现的不是commit本身的内容，而是该commit和它的parent commit（下一篇讲）之间的diff。不信的话请在url后面加个.patch，它会现出原形。 |

# 结语

Git就是这么管理版本的，多么巧妙啊！现在我们可以回答开头的3个问题了：

1、 每次`git commit`提交的内容是不是只有代码中被修改的那部分？

答：不是。每次commit会生成若干个objects，代码就算只改了一个逗号，对应的文件也会生成一个新的blob。Git无所谓你改了什么，它会为有改动的文件新生成一个blob（所以不推荐用Git管理大文件）。

2、 我在前面的某个commit中实现了一个功能，若我把该commit删掉，那这部分功能是不是被移除了？

答：不会。真正的东西在tree和blob中，删除前面某个commit不会删除它直接或间接引用的tree和blob，这些objects仍然可以被其他commit引用。

3、 commit是不是增量？我是不是要把所有commit连起来才能得到完整的代码？

答：不是。任何一个commit都指向对应版本的一棵完整的文件树。

快去用`git cat-file -p <hash>`命令看看各个object里都是什么内容吧，你会更有感觉的！下一篇系列博客会带你认识Git中的References，不要错过哦~
