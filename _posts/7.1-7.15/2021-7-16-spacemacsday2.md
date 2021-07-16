---
layout: article
title: Spacemacs学习日志 | day1
tags:
- Spacemacs
- 工具
key: a20210715
---

学习spacemacs的第二天，主要是了解一些快捷键，以及一些spacemacs的基础知识。

<!--more-->

### Spacemacs在哪里注入配置layer?
进入~/.spacemacs文件中(SPC fee)，找到 dotspacemacs-configuration-layers 这个代码块，uncomment可以加载的layer, 重启配置(SPC feR)会自动下载相应的layer.

## Shortcuts learned today
* 查找文件: spc + f f (find > files)
* 重新加载配置: spc + feR (file > env > reload)
* 打开.spacemacs配置文件: spc + fee   (file > env > .env)
* 打开project的neotree: spc + pt (project > tree)
* 进入已经打开的某个window: spc + 0-9 (0: focus on neotree if exists, 1: go to window 1)
* 对window进行缩放: spc + z f (zoom > frame)
* 搜索当前project下当前cursor下的单词: spc + * (search all)
* 在不同的buffer之间切换: spc + tab
* 进入右边的window: spc + wl (window > lower level(go to right), +wh(go to left))
* uncomment或者comment特定语句: spc + cl (comment > line)
* 退出emacs: spc + qq (quit > quit)
* 搜索当前project下面的文件: spc +pf (project > file)
* 列出当前buffer列表: spc +  bb (buffer > buffers)
* next buffer: spc + bn (buffer > next)

---
本文原创，错误之处在所难免！盼指出！
