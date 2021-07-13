---
layout: article
title: SV中的super, this, package关键字，以及UVM中的phase概念
tags: 
- UVM
- SystemVerilog
key: a20210713
---

super关键词常用来调用父class中的方法、变量等，而this关键词往往是在当前class中声明函数时调用当前class的变量。package主要是为了增加代码复用性，简洁性而出现的。而UVM中的phase相当于一个阶段，主要是用来起到同步UVM中所有component执行的作用。

<!--more-->

## super and this
super 关键字指向的父class，super.new()表明指向的是父class里面的new()方法。(因此只有当class从某个class继承时才能在里面使用super)。如:

```
class extPacket extends Packet;                       
	function new ();
		super.new ();
	endfunction
endclass
直接调用Packet里面的new方法。
```

this 关键字指向的 是当前class, 可以调用当前class的方法，数据。如：

```
test = my_test::type_id::create("test", this);  这里的this直接指向当前class.
```

## package
### 怎么声明要给package?
    - package的声明需要包含在package和endpackage两个关键字之间，下面是声明的一个例子

```systemverilog
package my_pkg;
	typedef enum bit [1:0] { RED, YELLOW, GREEN, RSVD } e_signal;
	typedef struct { bit [3:0] signal_id;
                     bit       active;
                     bit [1:0] timeout; 
                   } e_sig_param;
    
	function common ();
    	$display ("Called from somewhere");
   	endfunction
    
    task run ( ... );
    	...
    endtask
endpackage
```

### 使用package有什么好处？
将一些需要在多个模块中使用的数据，方法，参数等性质打包在一起，增加代码的可维护性，易用性。(或为了增加模块中代码的简洁性，而将模块中的一些数据方法的声明打包起来)

### 怎么调用package?
在调用package内容之前，需要将使用的package通过import关键字引入。如`import package_Name::item`引入package中的item. item可以是一个class，也可以是一个data.

`run_test("my_test")`  直接调用my_testbench_pkg中的my_test类(类的内容如下)，会完整的执行其中的语句。

```systemverilog
  class my_test extends uvm_test;
    `uvm_component_utils(my_test)
    
    my_env env;

    function new(string name, uvm_component parent);
      super.new(name, parent);
    endfunction
    
    function void build_phase(uvm_phase phase);
      env = my_env::type_id::create("env", this);
    endfunction
    
    task run_phase(uvm_phase phase);
      // We raise objection to keep the test from completing
      phase.raise_objection(this);
      #10;
      `uvm_warning("", "Hello World!")
      // We drop objection to allow the test to complete
      phase.drop_objection(this);
    endtask

endclass
```

## phase

![](https://image-icons.oss-cn-beijing.aliyuncs.com/img/20210713143303.png)

* phase这里翻译成阶段，每一个组件都会依次执行不同的phase, 如build_phase > connect_phase > .... ; 而只有当所有component的相同phase(如build_phase)执行完全后才会执行下一个phase(如connect_phase).
* All testbench components are derived from uvm_component and are aware of the __phase__ concept.**Each component goes through a pre-defined set of phases, and it cannot proceed to the next phase until all components finish their execution in the current phase.** So UVM phases act as a **synchronizing **mechanism in the life cycle of a simulation.
* The common phases are the set of function and task phases that all uvm_components execute together.  All uvm_components are always synchronized with respect to the common phases.

#### 参考链接:
[SystemVerilog super Keyword (chipverify.com)](https://www.chipverify.com/systemverilog/systemverilog-super)
[UVM Phases (chipverify.com)](https://www.chipverify.com/uvm/uvm-phases)
[SystemVerilog Package (chipverify.com)](https://www.chipverify.com/systemverilog/systemverilog-package)

---
本文原创，错误之处在所难免！盼指出！
