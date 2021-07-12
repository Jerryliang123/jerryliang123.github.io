---
layout: article
title: 从Hello world初步理解UVM工作机制
tags: 
- UVM
- SystemVerilog
key: a20210712
---

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210712100536.png)

UVM是目前最流行的一种搭建Testbench验证DUT的方法论，其是基于SystemVerilog的，所以这一套方法中充分利用了SystemVerilog作为OOP语言的三大特性(继承，封装，多态)。UVM为验证提供了整个搭建TB的框架，将每一个部分分离，使得协同合作变得更加高效，通过标准库，让搭建TB的时候直接继承标准库，并通过`uvm_component_utils(my_env) 这种宏定义的形式将当前自定义的environment放在整个tb的合适位置。

<!--more-->

本文从编写一个最简单的UVM例子Hello World入手，简单了解UVM的一个工作机制，搭建TB的一个过程。

* 本文例子中Testbench里面有四个.sv文件，DUT里面一个.sv文件。其中四个.sv文件分别是：testbench.sv, my_testbench_pkg.svh, my_sequence.svh, my_driver.svh

> .sv与.svh的区别；并没有实质性的区别，主要方便代码管理，一般通过命令行编译文件时使用.sv，使用`include加载文件时使用.svh。

### testbench.sv
UVM提供了一种分层机制，这里的 testbench.sv 是top层，其是整个测试的启动点(牵一发而动全身)。

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210712100751.png)

* 首先通过 `import my_testbench_pkg::*` 将testbench中注入自定义环境。
* `dut_if dut_if1()` 直接例化dut_if并命名为dut_if1，实例化一个接口，用于第二步连接DUT和TB；
* `dut dut1(.dif(dut_if1))` 例化DUT并通过接口将其与TB连接起来
* 在第一个initial begin块中给接口注入时钟信号 dut_if1.clock = 0;
* 在第二个initial begin块中配置UVM database (将接口加入到UVM配置库中)，并直接运行测试run_test("my_test"); 这里的"my_test"是一个class，位于my_testbench_pkg.svh文件中.
* 最后dump波形；$dumpfile("dump.vcd");

### my_testbench_pkg.svh
my_testbench_pkg.svh 里面包含了三个类 test, environment以及agent类，这三个类是分层关系，test里面会实例化my_env, environment里面会实例化my_agent。而在agent里面则会实例化my_driver, my_sequence. (由于hello world例子很简单所以这里没有monitor和scoreboard)

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210712100830.png)

* 这个package是由三个类组成：my_agent, my_env, my_test
* my_agent中会指定driver(my_driver), transaction(my_transaction), 以及sequence(my_sequence)；sequence是调用transaction来分派具体激励的。通过sequence将产生的seq送到driver。driver.seq_item_port.connet(sequencer.seq_item_export);
* my_env 中例化 agent(my_agent), 并提供build_phase来为整个tb指定my_agent为当前tb的agent (后期多个测试可能这里可以指定多个agent)
* my_test 为tb提供test模块，其中会例化environment(my_env env). 并通过build_phase指定env为当前tb的env。在里面通过raise objection让程序等待10s并输出`hello world`
* 其中的`uvm_component_utils(my_env)  表明让testbench里面使用my_env作为我的环境。

### my_sequence.svh
my_sequence.svh 里面包含两个类: my_transaction, my_sequence; 

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210712100931.png)

* 这个文件里面包含了两个类：my_transaction, my_sequence
* 其中的my_transaction用于产生随机激励，而my_sequence用于调用transaction产生机理，并监控其是否产生了随机激励。
* my_transaction里面使用rand bit cmd产生bit类型的cmd，使用constraint c_addr{ addr>=0;addr<256} 将addr限制在0到256之间。
* `req = my_transaction::type_id::create("req");`  请求my_transaction产生一组随机激励。使用start_item(req) 开始请求。

### my_driver.svh
my_driver.svh 里面包含一个类：my_driver

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210712101046.png)

* virtual dut_if dut_vif; 在class里面调用interface里面的信号需要使用virtual来例化interface.
* 使用run_phase来正式开始向DUT里面注入激励，dut_vif.reset = 1  直接在driver里面给interface里面的信号赋值。
* 通过seq_item_port.get_next_item(req); 一直产生激励。并通过dut_vif.cmd = req.cmd; 将拿到的激励给到接口里面。

---
本文原创，错误之处在所难免！盼指出！
