---
layout: article
title: a little bit Spacemacs and SystemVerilog
tags:
- SystemVerilog
- Spacemacs
key: a20210718
---

今天学习了一点SV中的OOP概念(object的复制，构造函数new的参数传递，直接回收对象)，以及一点点Spacemacs的知识(快捷键，打开vim模式下的line number显示)。

<!--more-->

## SV的一些知识点
* SV里面的关注点主要是transaction和transactor，在UVM中就是transaction和components, UVM中将所有的transactor都当作components来进行处理
* mailboxes是由一组FIFO构成的，可以用来stall a thread until a new value is added.
* 有时候我们为了对已经创建好的object进行修改，但是又不想修改当前的object，这个时候我们可以创建一个object的副本(通过复制创建好的object)

> 注意：如果原先的object中有其他对象(object2)的handle, 那么复制的时候会将原先object中handle复制过来，这两个handle指向的是同一个对象(object2)。

* 如果class中没有构造函数new(), 也是不影响在其他地方使用new()构建这个class的对象。
* 在通过new()创建构造函数时，如果需要在里面传入参数时，里面的变量名不能与class中的变量名一致。可以通过new(input logic [3:0] addr_p=0)的形式传入默认值为0的addr_p参数。
* 直接回收对象object，可以使用t = null的形式(t为handle名).
* 在SV语言中Class中的一切都是属于public类型的。

## Spacemacs知识点
#### 安装和使用Emacs的Verilog-Mode
* 通过快捷键M+x list-packages可以看到emacs仓库的中的所有package. (今天通过这个方式安装了最新的Verilog-mode包(可以直接通过/搜索Verilog找打))，现在安装的emacs默认里面都有verilog-mode包。
* 通过M+a M+c 启动自动连线(在端口位置先写上/*AUTOARG*/)，也可以通过命令搜索 `spc spc verilog-`来实行verilog-mode中的一些方法(创建header, task, function... 会减少很多敲击重复代码的时间)。
#### 在编辑器中显示行数
* 临时显示：spc tna
* 默认显示: 需要将下面的代码加入到 .spacemacs配置文件中。在dotspacemacs/init函数中找到dotspacemacs-line-numbers配置的位置，然后复制下列代码到相应位置。

```
dotspacemacs-line-numbers '(:visual t
                            :disabled-for-modes dired-mode
                                                doc-view-mode
                                                pdf-view-mode
                            :size-limit-kb 1000)
```

---
本文原创，错误之处在所难免！盼指出！
