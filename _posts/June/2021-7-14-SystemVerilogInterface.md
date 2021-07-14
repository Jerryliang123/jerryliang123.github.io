---
layout: article
title: SV中的接口(interface)
tags: 
- SystemVerilog
key: a20210713
---

sv中引入接口的概念，其可以很方便的对信号进行集中管理；当后期信号发生变动时可以很好的维护。这里以通过接口形式搭建一个D-flipflop testbench为例介绍其简单使用。

<!--more-->

## 声明接口(interface)

```
interface dff_if();
  logic [1:0] D, out;
  bit rst, clk;
endinterface
```

* 声明需要包含着interface ... endinterface中，可以传入参数也可以不传入参数。注意interface ... endinterface属于sv语言的软件部分。
* 声明内容可以放到dut文件或者testbench文件中，也可以单独创建一个文件，但是要记得在testbench的最上面通过`include "interface.svh"的形式引入文件。(反正成功编译到接口代码就好了)
* 不用指定output 或者 input，只需要给定数据结构和储存宽度。


## D-flipflop design code

```
module dff_with_ifc(dff_if dffif); 
  always @(posedge dffif.clk or negedge dffif.rst) begin
    if (!dffif.rst) dffif.out <= 0;
    else dffif.out <= dffif.D;
  end 
endmodule
```

* dut里面传入参数是接口，调用信号的方式是使用`dffif.out`这种形式。

## 验证平台(Testbench)

```
`include "interface.sv"
module test_with_ifc;
  
  dff_if dffif();
  
  dff_with_ifc dff_ifc(.dffif(dffif));
  
  // inject stimulus
  initial begin
    dffif.clk = 0;
    forever #10 dffif.clk = ~dffif.clk;
  end
  initial begin
    repeat (2) begin
      @(posedge dffif.clk);
      dffif.rst <= 1;
      dffif.D <= 2'b11;
      $display("@%0t: D = %b", $time, dffif.D);
    end
    $finish;
  end
  
endmodule
```

* 注意需要例化接口 dff_if dffif()
* forever语句会一直执行，应该放在一个分开的initial块中。

---
本文原创，错误之处在所难免！盼指出！
