---
layout: article
title: SystemVerilog Clocking block简单介绍
tags:
- SystemVerilog
- Clocking Block
key: a20210721
---

SV中Clock blocking的出现是为了解决testbench中的时序和同步问题的。(避免竞争冒险现象。)

<!--more-->

Clocking block的声明位置：module、interface、program

#### Clocking block具体两个作用：
1. 从testbench中抽出时序和同步处理部分的代码，通过clocking block对其进行单独管理，增加代码的可维护性和降低testbench的复杂度。
2. testbench会在clock沿的前面进行采样操作，在clock沿的后面进行驱动输出。明确采样和驱动位置，避免竞争冒险现象。

> (竞争冒险现象：不确定是先驱动还是先采样, 这种情况下会导致采样的值可能是不一样的，如果是先驱动那么采样的就是这个驱动对应的输出值，如果是先采样那对应的就是上个驱动的输出值。) 所以说我们这里明确的指定是先采样然后再驱动，跟实际电路的运行情况一致。实际电路testbench采样的值总是上个驱动对design进行操作输出的值。

## Clock Skew
实际情况中时钟信号到达每个寄存器的时间是有偏差的，而skew就是两个时钟沿之间的偏差，通过合理的设定针对testbench的clocking block中的input skew和output skew，我们可以模拟这一现象。input skew代表testbench进行采样的时间是clock沿前方skew time对应的值(相对应的是时钟到达采样); 而output skew代表的是testbench进行驱动时使用的值是clock沿后面skew time对应的值。

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210721221501.png)

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210721221510.png)

最后一个clocking block的例子

```
clocking cb @(posedge clk);
  default input #1 output #2;
  input  from_Dut;
  output to_Dut;
endclocking
```

---
本文原创，错误之处在所难免！盼指出！
