---
layout: article
title: SystemVerilog中13种数据类型理解
tags: 
- SystemVerilog
- 数据类型
key: a20210709
---

SV中的十三种基本数据类型分别是：  
SV引入的7种数据类型：logic bit byte shortint int longint shortreal  
Verilog中自带的6种数据类型：reg net integer real  realtime time

<!--more-->

### 记忆SV引入的7种数据类型
对应SV中引入的七种数据类型logic bit byte shortint int longint shortreal  
1. 其中logic用来取代Verilog中的reg, 两者只是在名字上不同
2. bit byte shortint int longint 这五种默认的bit大小是从小到大的，分别对应1，8，16，32，64bit.
3. shortreal目前还没用过，后面补上。

对于SV中的所有的数据类型，如果默认是单bit的(如logic,bit,reg,net四种)，那么其对应的必然是无符号类型(unsigned). 多bit的(如byte,shorint,int,longint,integer)默认是有符号类型(signed)。

### signed or unsigned, 2-states or 4-states
有符号类型(signed)和无符号类型(unsigned)的区别：  
bytes是8bits的signed类型所以其的范围是(-128, 127)  
而通过bit [7:0] 方式声明的范围是(0, 255).  
(signed的类型最高位是符号位，unsigned类型所有位都是数值)

SV引入的除了logic以外其他的都是二值逻辑(2-states), 及当其没有初始化时，初始值为0；而四值逻辑(4-states)在没有初始化时，其的初始值为x。

---
本文原创，错误之处在所难免！盼指出！
