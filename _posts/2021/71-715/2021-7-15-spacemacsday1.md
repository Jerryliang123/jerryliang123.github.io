---
layout: article
title: Spacemacs学习日志 | day1
tags:
- Spacemacs
- 工具
key: a20210715
---

官网介绍spacemacs: A community-driven Emacs distribution. The best editor is neither Emacs nor Vim, it's Emacs and Vim!

<!--more-->

所以Emacs是什么呢？ 简单来说Emacs就是伪装成编辑器的操作系统，你可以在里面做任何你在Linux需要完成的事情。而且整个Emacs都是可以通过配置文件进行自定义的(使用Lisp语言)

Emacs让你能够在一个高度可定制化文本环境中操控Linux系统。(范围很广)

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210715232300.png)

那Vim又是什么呢？Vim是一款高度可定制化的文本编辑软件(任何复杂繁琐的流程都可以通过配置Vim简化)，其可以对任何文本进行非常高效的操作。

Vim对专精于文本操作 (专精于文本操作)

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210715232046.png)

而Spacemacs是一个社区维护的Emacs distribution, 使得在Emacs环境中能够使用Vim对文本进行操作。从而同时兼顾了Vim和Emacs的优点

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210715232419.png)

* 进入Spacemacs环境: 输入emacs;  退出: C-x C-c (C对应的是Ctrl按键)
* 查找或打开文件: M-x 然后输入find-file, 输入查找的地址然后enter就可以打开文件了(M对应的是Meta按键，对应Windows上面的alt) M-x > find-file > (file-path) > enter
* 然后就可以直接开始使用Vim编辑目标文件了，今天练习的是打开一个.md文件，在里面使用的命令跟vim完全一致。(但是可以使用鼠标指定光标的位置)
* 复制文件: M-x > copy-file > 需要复制的文件 > 复制后的文件名字(包含路径)
* 删除文件: M-x > delete-file > 指定文件删除

今天是学习Spacemacs的第一天。

---
本文原创，错误之处在所难免！盼指出！
