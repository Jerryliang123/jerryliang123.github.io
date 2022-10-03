---
layout: article
title: 跨时钟域问题的解决办法
tags:
- 电路基础知识点
key: a20210824
---

UVM书籍-仅学习用途

<!--more-->

# 前言

从我参加工作开始，就一直在使用OVM/UVM，最初是OVM，后来当UVM1.0发布后，我所在的公司迅速切换到UVM。在学习的过程中，自己尝遍艰辛。当时资料非常匮乏（其实今天依然比较匮乏），能够参考的只有两份，一是《OVM Cookbook》

（这本英文资料一直没有在国内出版过），二是OVM/UVM官方的英文参考文档。这两份资料所采用的行文方式都是硬生生地不断引入某些概念，并附加一定的代码来阐述这些概念。在这些前后引入的概念之间，几乎没有逻辑关系。有时候看完一整章都不知道该章介绍的内容有何用处，看完整本书也不知道如何搭建一个验证平台。虽然OVM/UVM的发行包中附带了一个例子，但是这个例子对于初学者来说实在太复杂，这种复杂使很多用户望而生畏。身边的同事虽然对OVM/UVM有一定了解，但是并不深 入，知其然却不知其所以然。在这种情况下，我只能通过查看源代码的形式来学习OVM/UVM。这个过程非常艰苦，但是使得我对整个UVM的运行了如指掌，同时在这个过程中我充分领悟到了OVM/UVM的设计理念，也为其中的实现拍案叫绝。

我非常渴望将OVM/UVM中的美妙实现分享给所有OVM/UVM用户，这种想法一直在我脑海盘旋。当时，国内没有任何中文的UVM学习文档，同时自己在学习OVM/UVM过程中的痛苦记忆犹新。为了使得后来者能够更加容易地学习OVM/UVM，减轻学习的痛苦，2011年8月初，我忽然有了将我对OVM/UVM的理解记录成一份文档的想法。这个想法在诞生后就迅速壮大，我很快列出了提纲，根据这些提纲，经过4个多月的写作与完善，终于完成了名为《UVM1.1应用指南及源代码解析》的文档，并将其放到网上供广大用户免费下载。

在这份文档中，我一开始就尝试着为广大读者呈现出一个完整的UVM验证平台。这种行文方式主要是为了避免《OVM Cookbook》及OVM/UVM官方参考文档的那种看完整本书都不知道如何搭建验证平台的情况出现。虽然在写作时曾经犹豫过这种



方式会有一些激进，但是通过后来读者的反馈证明这种方式是完全可以接受的。

在网上发布这份文档后，我收到了众多用户发来的邮件。有很多用户对我的无私表示感谢，这让我非常欣慰：至少我做的事情帮助了一些人；还有众多的用户指出了整份文档中的一些笔误；除此之外，还有一些用户建议文档的某些部分应该阐述得更加清楚些，并增加某些部分的内容。在这里我衷心地向这些用户表示感谢，由于人数众多，这里不再一一列举出他们的名字。

2013年，当我重新审视自己两年前写的文档时，发现了其中诸多的不足。大量的笔误自不必提，更多的不足来自于内容上。

2011年的文档中分为明显的前后两部分，前9章讲述如何使用UVM，后10章讲述UVM的源代码。在给我发来邮件的众多用户中，

99%都是只看前9章的。我最初的想法是与广大OVM/UVM用户分享读UVM源代码的心得，所以后10章是我花费大量精力写的，而前9章则是顺手而为。这造成了前9章太简单，同时里面问题较多，而后10章太难、太复杂，没有太多人能看懂。至于介于简单和复杂之间的那部分中等难度的内容，却没有在整本书中覆盖。

恰在此时，机械工业出版社华章公司的张国强编辑联系到我，询问我是否考虑把整份文档出版。在此之前，已经有众多的读者通过邮件询问关于文档的出版情况。在和张编辑沟通并去除某些疑虑后，我和张编辑达成了出版意向。经过几个月的修改，并增添了大量内容后，形成了本书。与电子版的《UVM1.1应用指南及源代码解析》相比，这本书有如下特点：

1） 增加了一些中等难度的内容，消除了《UVM1.1应用指南及源代码解析》中太简单内容与太复杂内容之间的空白。比如加入了大量factory模式的内容，详细阐述了寄存器模型中的后门（BACKDOOR）访问等。新增加的内容及例子几乎占据整本书的2/3篇幅。



2） 在《UVM1.1应用指南及源代码解析》中，一开始就给出一个验证平台的例子，但是这个例子是以一个整体的形式呈现在读者面前，而没有说明白这个例子为什么会是这样，这好比从0直接跳到了1，中间没有任何过渡。而在本书中，我将此例一步步拆解，从0到0.1，再到0.2，一直慢慢增加到1。在增加每一步时，都尽量讲述明白为什么会这样增加，以方便用户的学习。

3） 书中的每一个例子都经过了验证，这些例子都能在本书附带的源代码中找到。用户可以登录华章网站

（http：//[www.hzbook.com](http://www.hzbook.com/)）下载这些源代码并在自己的电脑上运行它们，这会极大提高学习的速度。

4） 本书第11章专门讲述了从OVM到UVM的迁移。UVM是从OVM迁移来的，虽然很多公司现在使用的是UVM，但是由于一些历史遗留问题，在它们的代码库中依然有很多OVM式的已经被UVM丢弃的用法。通过这一章的学习，用户可以迅速适应这些过时的用法。

这本书能够出版，首先感谢机械工业出版社华章公司给我这样一个机会，特别感谢华章公司的张国强编辑和李燕编辑，没有他们的辛苦工作，这本书不可能与广大读者见面。

我要感谢我的父母和姐姐，是他们一直在背后默默地支持我、鼓励我，无论是高潮和低谷，他们都一直在我的身边。

我要感谢我在上海工作期间的领导和同事：魏斌、向伟、孙唐、宋亚平、王勇、王天、陈晨、赖琳晖、沈晓、胡晓飞、何刚、鲍敏祺、林健、龙进凯、汪永威、常勇。他们给了我很多写作的灵感和素材。

我还要感谢我在杭州工作期间的领导和同事：袁锦辉、吴洪涛、陈国华、梁力、高世超、王旭霞、王兆明、乐东坡、陆礼红、甘滔、潘永斌、陈钰飞、朱明鉴，他们在我写《UVM1.1应用指南及源代码解析》期间给了我各种各样的帮助。



由于时间仓促，同时作者水平所限，书中难免存在错误，恳请广大读者批评指正！

张强 2014年4月



# 第1章  与UVM的第一次接触

 

1.1       UVM是什么

## 1.1.1             验证在现代IC流程中的位置

现代IC（Integrated circuit，集成电路）前端的设计流程如图1-1所示。通常的IC设计是从一份需求说明书开始的，这份需求说明书一般来自于产品经理（有些公司可能没有单独的职位，而是由其他职位兼任）。从需求说明书开始，IC工程师会把它们细化为特性列表。设计工程师根据特性列表，将其转化为设计规格说明书，在这份说明书中，设计工程师会详细阐述自己的设计方 案，描述清楚接口时序信号，使用多少RAM资源，如何进行异常处理等。验证工程师根据特性列表，写出验证规格说明书。在验证规格说明书中，将会说明如何搭建验证平台，如何保证验证完备性，如何测试每一条特性，如何测试异常的情况等。



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image002.jpg)



图1-1  现代IC前端设计流程

当设计说明书完成后，设计人员开始使用Verilog（或者VHDL，这里以Verilog为例）将特性列表转换成RTL代码，而验证人员则开始使用验证语言（这里以SystemVerilog为例）搭建验证平台，并且着手建造第一个测试用例（test case）。当RTL代码完成后，验证人员开始验证这些代码（通常被称为DUT（Design Under Test），也可以称为DUV（Design Under Verification），本书统一使用DUT）的正确性。

验证主要保证从特性列表到RTL转变的正确性，包括但不限于以下几点：

· DUT的行为表现是否与特性列表中要求的一致。

· DUT是否实现了所有特性列表中列出的特性。

· DUT对于异常状况的反应是否与特性列表和设计规格说明书中的一致，如中断是否置起。

· DUT是否足够稳健，能够从异常状态中恢复到正常的工作模式。



## 1.1.2             验证的语言

验证使用的语言五花八门，很难统计出到底有多少种语言曾经被用于验证，且验证这个词是从什么时候开始独立出现的也有待考证。验证是服务于设计的，目前来说，有两种通用的设计语言：Verilog和VHDL。伴随着IC的发展，Verilog由于其易用性，在IC设计领域占据了主流地位，使用VHDL的人越来越少。基于Verilog的验证语言主要有如下三种。

1） Verilog：Verilog是针对设计的语言。Verilog起源于20世纪80年代中期，并在1995年正式成为IEEE标准，即IEEE Standard 1364TM—1995。其后续版本是2001年推出的，与1995版差异比较大。很多Verilog仿真器中都会提供一个选项来决定使用1995版还是2001版。目前最新的标准是2005年推出的，即IEEE Standard 1364TM—2005，它与2001版的差距不大。验证起源于设计，在最初的时候是没有专门的验证的，验证与设计合二为一。考虑到这种现状，Verilog在其中还包含了一个用于验证的子集，其中最典型的语句就是initial、task和function。纯正的设计几乎是用不到这些语句的。通过这些语句的组合，可以给设计施加激励，并观测输出结果是否与期望的一致，达到验证的目的。Verilog在验证方面最大的问题是功能模块化、随机化验证上的不足，这导致更多的是直接测试用例（即direct test case，激励是固定的，其行为也是固定的），而不是随机的测试用例（即random test case，激励在一定范围内是随机的，可以在几种行为间选择一种）。笔者亲身经历过一个使用Verilog编写的设计，包含有6000多个测试用

例。假如使用SystemVerilog，这个数字至少可以除以10。

2） SystemC：IC行业按照摩尔定律快速发展，晶体管的数量越来越多，整个系统越来越复杂。此时，单纯的Verilog验证已经难以满足条件。1999年，OSCI（Open SystemC Initiative）成立，致力于SystemC的开发。通常来说，可以笼统地把IC分为两类， 一类是算法需求比较少的，如网络通信协议；另一类是算法需求非常复杂的，如图形图像处理等。那些对算法要求非常高的设计



在使用Verilog编写代码之前，会使用C或者C++建立一个算法模型，即参考模型（reference model），在验证时需要把此参考模型的输出与DUT的输出相比，因此需要在设计中把基于C++/C的模型集成到验证平台中。SystemC本质上是一个C++的库，这种天然的特性使得它在算法类的设计中如鱼得水。当然，在非算法类的设计中，SystemC也表现得相当良好。SystemC最大的优势在于它是基于C++的，但这也是它最大的劣势。在C++中，用户需要自己管理内存，指针会把所有人搞得头大，内存泄露是所有C++用户的噩梦。除了内存泄露的问题外，SystemC在构建异常的测试用例时显得力不从心，因此现在很多公司已经转向使用 SystemVerilog。

3） SystemVerilog：它是一个Verilog的扩展集，可以完全兼容Verilog。它起源于2002年，并在2005年成为IEEE的标准，即IEEE

1800TM—2005，目前最新的版本是IEEE 1800TM—2012。SystemVerilog刚一推出就受到了热烈欢迎，它具有所有面向对象语言的特性：封装、继承和多态，同时还为验证提供了一些独有的特性，如约束（constraint）、功能覆盖率（functional coverage）。由于其与Verilog完全兼容，很多使用Verilog的用户可以快速上手，且其学习曲线非常短，因此很多原先使用Verilog做验证的工程师们迅速转到SystemVerilog。在与SystemC的对比中，SystemVerilog也不落下风，它提供了DPI接口，可以把C/C++的函数导入SystemVerilog代码中，就像这个函数是用SystemVerilog写成的一样。与C++相比，SystemVerilog语言本身提供内存管理机制，用户不用担心内存泄露的问题。除此之外，它还支持系统函数$system，可以直接调用外部的可执行程序，就像在Linux的shell下直接调用一样。用户可以把使用C++写成的参考模型编译成可执行文件，使用$system函数调用。因此，对于那些用Verilog写成的设计来说，SystemVerilog比SystemC更受欢迎，这就类似于用C++来测试C写成的代码显然比用Java测试更方便、更受欢迎。无论是对算法类或者非算法类的设计，SystemVerilog都能轻松应付。



## 1.1.3             何谓方法学

有了SystemVerilog之后，是不是足以搭建一个验证平台呢？这个问题的答案是肯定的，只是很难。就像汉语是很优秀的语言一样，自古以来，无数的名人基于它创作出很多优秀的篇章。有很多篇章经过后人的浓缩，变成了一个又一个的成语和典故。在这些篇章的基础上，作家写作的时候稍微引用几句就会让作品增色不少。而如果一个成语都不用，一点语句都不引用，也能写出优秀的文章，但是相对来说比较困难。这些优秀的作品就是汉语的库。同样，SystemVerilog是一门优秀的语言，但是如果仅仅使用SystemVerilog来进行验证显然不够，有很多直接的问题需要考虑，比如：

· 验证平台中都有哪些基本的组件，每个组件的行为有哪些？

· 验证平台中各个组件之间是如何通信的？

· 验证中要组建很多测试用例，这些测试用例如何建立、组织的？

· 在建立测试用例的过程中，哪些组件是变的，哪些组件是不变的？

同时，也有一些更高层次的问题需要考虑：

· 验证平台中数据流与控制流如何分离？

· 验证平台中的寄存器方案如何解决？



· 验证平台如何保证是可重用的？

读者可以尝试自己回答这些问题，回答的时候不要空想，要把真正的代码写出来。

何谓方法学？方法学这个词是一个很抽象、很宽泛的概念，很难用简单的词语把它描绘出来。当然了，即使是一本专业讲述方法学的书籍，几百多页，看过之后可能依然会觉得不知所云。

在对方法学的理解上，有三个层次：

第一个层次，在刚刚接触到这个概念时，很容易把方法学和世界观、人生观、价值观等词语联系到一起，认为它是一个哲学的词汇。这种想法是不可避免的，而且，从根本上来说，它是正确的。只是这种理解太过浅显，因为方法学的真谛不在于概念本身，而在于其背后所表示的东西。

第二个层次，当初步学习完本书后，读者会觉得自己以前的想法太天真：方法学怎么会有那么神秘？至少从UVM的角度来说，方法学只是一个库。这种理解基本上没错。无论任何抽象的概念，一个程序员要使用它，唯一的方法是把其用代码实现。就如同上面的那些问题，如果能够把它们都完整地规划清楚，那么这就是属于读者自己的验证方法学；如果把思考结果用代码实 现，那就是一个包含了验证方法学的库，是读者自己的UVM！

第三个层次，当读者从事验证工作几年之后，对UVM的各种用法信手拈来，就会发现方法学又不仅仅是一个库，库只是方法学的具体实现。从理论上来说，用一个东西表达另外一个东西的时候，只要两者不是一一对应的关系，那么一般会有很多遗漏。自然语言尚且无法完全地表述清楚方法学，而比自然语言更加简单的编程语言，则更加不可能表述清楚了。所以，一个库不能完



全地表述清楚一种方法学。在这个阶段，读者再回过头来仔细想想上面的那些问题，想想它们在UVM中的实现，就会为UVM的优秀而拍案叫绝。

关于什么是方法学这个问题，读者可以不必太过于纠结，因为它属于相对高级的东西，在开始的时候追究这个问题只会增加自己学习UVM的难度。把这个问题放在一边，只把它当成一个库，等初步学完本书后再来回味这个问题。



## 1.1.4             为什么是UVM

在基于SystemVerilog的验证方法学中，目前市面上主要有三种。

VMM（Verification Methodology Manual），这是Synopsys在2006年推出的，在初期是闭源的。当OVM出现后，面对OVM的激烈竞争，VMM开源了。VMM中集成了寄存器解决方案RAL（Register Abstraction Layer）。

OVM（Open Verification Methodology），这是Candence和Mentor在2008年推出的，从一开始就是开源的。它引进了factory机制，功能非常强大，但是它里面没有寄存器解决方案，这是它最大的短板。针对这一情况，Candence推出了RGM，补上了这一短板。只是很遗憾的是，RGM并没有成为OVM的一部分，要想使用RGM，需要额外下载。现在OVM已经停止更新，完全被UVM代替。

UVM（Universal Verification Methodology），其正式版是在2011年2月由Accellera推出的，得到了Sysnopsys、Mentor和Cadence 的支持。UVM几乎完全继承了OVM，同时又采纳了Synopsys在VMM中的寄存器解决方案RAL。同时，UVM还吸收了VMM中的一些优秀的实现方式。可以说，UVM继承了VMM和OVM的优点，克服了各自的缺点，代表了验证方法学的发展方向。

在决定一种验证方法学的命运时，有三个主要的问题：

1） EDA厂商支持吗？得到EDA厂商的支持是最重要的。在IC设计中，必然要使用一些EDA工具，因此，EDA厂商支持什 么，什么就可能获得成功。目前，三大EDA厂商synopsys、Mentor、Cadence都完美地支持UVM。UVM本身就是这三家厂商联合



推出的，读者打开任意一个UVM的源文件，都能在开头看到这三家公司关于版权的联合声明。

2） 现在用的公司多了吗？一种方法学，如果本身比较差，不方便使用，那么即使得到了EDA厂商的支持，也不会受到广大验证工程师的欢迎。因此，当方法学刚开始推出时，第一个用户是要冒着很大风险的。但是幸运的是，读者肯定不是这样的“小白鼠”。因为现在市面上很多IC设计公司都已经在使用UVM，并且越来越多的公司开始转向使用UVM，UVM已经得到了市场的验 证。

3） 有更好的验证方法学出现了吗？没有。UVM是2011年推出的，非常年轻，非常有活力。



## 1.1.5             UVM的发展史

UVM的前身是OVM，由Mentor和Cadence于2008年联合发布。2010年，Accellera（SystemVerilog语言标准最初的制定者）把OVM采纳为标准，并在此基础上着手推出新一代验证方法学UVM。为了能够让用户提前适应UVM，Accellera于2010年5月推出了UVM1.0EA，EA的全拼是early adoption，在这个版本里，几乎没有对OVM做任何改变，只是单纯地把ovm_*前缀变为了uvm_*。

2011年2月，备受瞩目的UVM1.0正式版本发布。此版本加入了源自Synopsys的寄存器解决方案。但是，由于发布仓促，此版本中有大量bug存在，所以仅仅时隔四个月就又发布了新的版本。

2011年6月，UVM1.1版发布，这个版本修正了1.0中的大量问题，与1.0相比有较大差异。

2011年12月，UVM1.1a发布。

2012年5月，UVM1.1b发布。

2012年10月，UVM1.1c发布。

2013年3月，UVM1.1d发布。

从UVM1.1到UVM1.1d，从版本命名上就可以看出并没有太多的改动。本书所有的例子均基于UVM1.1d。



# 1.2       学了UVM之后能做什么

## 1.2.1             验证工程师

验证工程师能够从本书学会如下内容：

· 如何用UVM搭建验证平台，包括如何使用sequence机制、factory机制、callback机制、寄存器模型（register model）等。

· 一些验证的基本常识，将会散落在各个章节之间。

· UVM的一些高级功能，如何灵活地使用sequence机制、factory机制等。

· 如何编写代码才能保证可重用性。可重用性是目前IC界提及最多的几个词汇之一，它包含很多层次。对于个人来说，如何保证自己在这个项目写的代码在下一个项目中依然可以使用，如何保证自己写出来的东西别人能够重用，如何保证子系统级的代码在系统级别依然可以使用；对于同一公司来说，如何保证下一代的产品在验证过程中能最大程度使用前一代产品的代码。

· 同样的一件事情有多种实现方式，这多种方式之间分别都有哪些优点和缺点，在权衡利弊之下哪种是最合理的。

· 一些OVM用法的遗留问题。

可以说，本书特别适合欲使用UVM作为平台的广大验证工程师阅读。当前众多IC公司在招聘验证人员时，最基本的一条是懂



得UVM，学完本书并熟练使用其中的例子后，读者可以满足绝大多数公司对UVM的要求。



## 1.2.2             设计工程师

在IC设计领域，有一句很有名的话是“验证与设计不分家”。甚至目前在一些IC公司里，依然存在着同一个人兼任设计人员与验证人员的情况。验证与设计只是从不同的角度来做同一件事情而已。验证工程师应该更多地学习些设计的知识，从项目的早期就参与进去，而不要抱着“只搭平台只建测试用例，调试都交给设计人员”的想法。同样，设计工程师也有必要学习一点验证的知识。一个一点不懂验证的设计工程师不是一个好的设计工程师。考虑到设计人员可能没有任何的SystemVerilog基础，本书在附录A 中专门讲述SystemVerilog的使用。设计人员可以在读本书之前学习一下附录A，以更好地理解本书。另外，本书与其他书最大的不同在于，本书开始就提供了一个完整的、用UVM搭建的例子，设计人员只要学习完第2章的例子，再把它和自己公司的验证环境结合一下，就可以搭建简单的测试用例了。而其他书，则通常需要看完整本书才能达到同样的目的。



# 第2章  一个简单的UVM验证平台

 

2.1       验证平台的组成

验证用于找出DUT中的bug，这个过程通常是把DUT放入一个验证平台中来实现的。一个验证平台要实现如下基本功能：

·  ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image003.gif)验证平台要模拟DUT的各种真实使用情况，这意味着要给DUT施加各种激励，有正常的激励，也有异常的激励；有这种模式的激励，也有那种模式的激励。激励的功能是由driver来实现的。

·  验证平台要能够根据DUT的输出来判断DUT的行为是否与预期相符合，完成这个功能的是记分板（scoreboard，也被称为checker，本书统一以scoreboard来称呼）。既然是判断，那么牵扯到两个方面：一是判断什么，需要把什么拿来判断，这里很明显是DUT的输出；二是判断的标准是什么。

·  ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image004.gif)验证平台要收集DUT的输出并把它们传递给scoreboard，完成这个功能的是monitor。

·  ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image005.gif)验证平台要能够给出预期结果。在记分板中提到了判断的标准，判断的标准通常就是预期。假设DUT是一个加法器，那么当在它的加数和被加数中分别输入1，即输入1+1时，期望DUT输出2。当DUT在计算1+1的结果时，验证平台也必须相应完成同样的过程，也计算一次1+1。在验证平台中，完成这个过程的是参考模型（reference model）。



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image006.jpg)

图2-1  简单验证平台框图

一个简单的验证平台框图如图2-1所示。在UVM中，引入了agent和sequence的概念，因此UVM中验证平台的典型框图如图2-2 所示。

从下一节开始，将从只有一个driver的最简单的验证平台开始，一步一步搭建如图2-2所示的验证平台。



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image007.jpg)

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image008.gif)图2-2  典型UVM验证平台框图



# 2.2       只有driver的验证平台

driver是验证平台最基本的组件，是整个验证平台数据流的源泉。本节以一个简单的DUT为例，说明一个只有driver的UVM验证平台是如何搭建的。



 

##                     [[1\]](#_bookmark15)                   *2.2.1  最简单的验证平台

 

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image009.gif)在本章中，假设有如下的DUT定义： 代码清单         2-1

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image010.gif)文件：src/ch2/dut/dut.sv[[2\]](#_bookmark16)

1 module dut(clk,

2           rst_n,

3           rxd,

4           rx_dv,

5           txd,

6           tx_en);

7 input clk;

8 input rst_n;

9 input[7:0] rxd;

10  input rx_dv;

11  output [7:0] txd;

12  output tx_en; 13

14  reg[7:0] txd;

15  reg tx_en; 16

17  always @(posedge clk) begin

18     if(!rst_n) begin 19  txd <= 8'b0;

20    tx_en <= 1'b0;

21     end

22     else begin

23        txd <= rxd;



| 24   |        | tx_en <= rx_dv; |
| ---- | ------ | --------------- |
| 25   | end    |                 |
| 26   | end    |                 |
| 27   | endmod | ule             |

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image011.gif) |





这个DUT的功能非常简单，通过rxd接收数据，再通过txd发送出去。其中rx_dv是接收的数据有效指示，tx_en是发送的数据有效指示。本章中所有例子都是基于这个DUT。

UVM中的driver应该如何搭建？UVM是一个库，在这个库中，几乎所有的东西都是使用类（class）来实现的。driver、 monitor、reference model、scoreboard等组成部分都是类。类是像SystemVerilog这些面向对象编程语言中最伟大的发明之一，是面向对象的精髓所在。类有函数（function），另外还可以有任务（task），通过这些函数和任务可以完成driver的输出激励功能，完成monitor的监测功能，完成参考模型的计算功能，完成scoreboard的比较功能。类中可以有成员变量，这些成员变量可以控制类的行为，如控制driver的行为等。当要实现一个功能时，首先应该想到的是从UVM的某个类派生出一个新的类，在这个新的类中实现所期望的功能。所以，使用UVM的第一条原则是：验证平台中所有的组件应该派生自UVM中的类。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image009.gif)UVM验证平台中的driver应该派生自uvm_driver，一个简单的driver如下例所示： 代码清单  2-2

文件：src/ch2/section2.2/2.2.1/my_driver.sv

3 class my_driver extends uvm_driver; 4

5    function new(string name = "my_driver", uvm_component parent = null);

6       super.new(name, parent);



7    endfunction

8    extern virtual task main_phase(uvm_phase phase);

9 endclass 10

11  task my_driver::main_phase(uvm_phase phase);

12     top_tb.rxd <= 8'b0;

13     top_tb.rx_dv <= 1'b0;

14     while(!top_tb.rst_n)

15        @(posedge top_tb.clk);

16     for(int i = 0; i < 256; i++)begin

17        @(posedge top_tb.clk);

18        top_tb.rxd <= $urandom_range(0, 255);

19        top_tb.rx_dv <= 1'b1;

20        `uvm_info("my_driver", "data is drived", UVM_LOW)

21     end

22     @(posedge top_tb.clk);

23     top_tb.rx_dv <= 1'b0;

24  endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image012.gif) |





这个driver的功能非常简单，只是向rxd上发送256个随机数据，并将rx_dv信号置为高电平。当数据发送完毕后，将rx_dv信号置为低电平。在这个driver中，有两点应该引起注意：

·  ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image013.gif)![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image014.gif)所有派生自uvm_driver的类的new函数有两个参数，一个是string类型的name，一个是uvm_component类型的parent。关于name参数，比较好理解，就是名字而已；至于parent则比较难以理解，读者可暂且放在一边，下文会有介绍。事实上，这两个参数是由uvm_component要求的，每一个派生自uvm_component或其派生类的类在其new函数中要指明两个参数：name和parent，这是uvm_component类的一大特征。而uvm_driver是一个派生自uvm_component的类，所以也会有这两个参数。



·   

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image015.gif) |


![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image016.gif)![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image017.gif)driver所做的事情几乎都在main_phase中完成。UVM由phase来管理验证平台的运行，这些phase统一以xxxx_phase来命名，且都有一个类型为uvm_phase、名字为phase的参数。main_phase是uvm_driver中预先定义好的一个任务。因此几乎可以简单地认为，



上述代码中还出现了uvm_info宏。这个宏的功能与Verilog中display语句的功能类似，但是它比display语句更加强大。它有三个参数，第一个参数是字符串，用于把打印的信息归类；第二个参数也是字符串，是具体需要打印的信息；第三个参数则是冗余级别。在验证平台中，某些信息是非常关键的，这样的信息可以设置为UVM_LOW，而有些信息可有可无，就可以设置为UVM_HIGH，介于两者之间的就是UVM_MEDIUM。UVM默认只显示UVM_MEDIUM或者UVM_LOW的信息，本书3.4.1节会讲述如何显示UVM_HIGH的信息。本节中uvm_info宏打印的结果如下：

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image018.gif) |





UVM_INFO my_driver.sv

（20

）@48500000

：drv[my_driver]data is drived

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image019.gif) |





在uvm_info宏打印的结果中有如下几项：

·  ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image020.gif)UVM_INFO关键字：表明这是一个uvm_info宏打印的结果。除了uvm_info宏外，还有uvm_error宏、uvm_warning宏，后文中将会介绍。

·  ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image021.gif)my_driver.sv（20）：指明此条打印信息的来源，其中括号里的数字表示原始的uvm_info打印语句在my_driver.sv中的行号。



·  48500000：表明此条信息的打印时间。

·  ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image022.gif)drv：这是driver在UVM树中的路径索引。UVM采用树形结构，对于树中任何一个结点，都有一个与其相应的字符串类型的路径索引。路径索引可以通过get_full_name函数来获取，把下列代码加入任何UVM树的结点中就可以得知当前结点的路径索引：

代码清单  2-3

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image019.gif) |





$display("the full name of current component is: %s", get_full_name());

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image023.gif) |





·  [my_driver]：方括号中显示的信息即调用uvm_info宏时传递的第一个参数。

·  data is drived：表明宏最终打印的信息。

可见，uvm_info宏非常强大，它包含了打印信息的物理文件来源、逻辑结点信息（在UVM树中的路径索引）、打印时间、对信息的分类组织及打印的信息。读者在搭建验证平台时应该尽量使用uvm_info宏取代display语句。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image012.gif)定义my_driver后需要将其实例化。这里需要注意类的定义与类的实例化的区别。所谓类的定义，就是用编辑器写下： 代码清单  2-4

classs A;

… endclass



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image024.gif)

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image019.gif)而所谓类的实例化指的是通过new创造出A的一个实例。如： 代码清单      2-5

A a_inst; a_inst = new();

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image011.gif) |





类的定义类似于在纸上写下一纸条文，然后把这些条文通知给SystemVerilog的仿真器：验证平台可能会用到这样的一个类， 请做好准备工作。而类的实例化在于通过new（）来通知SystemVerilog的仿真器：请创建一个A的实例。仿真器接到new的指令

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image025.gif)后，就会在内存中划分一块空间，在划分前，会首先检查是否已经预先定义过这个类，在已经定义过的情况下，按照定义中所指定的“条文”分配空间，并且把这块空间的指针返回给a_inst，之后就可以通过a_inst来查看类中的各个成员变量，调用成员函数/任务等。对大部分的类来说，如果只定义而不实例化，是没有任何意义的 [[3\]](#_bookmark17)；而如果不定义就直接实例化，仿真器将会报错。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image026.gif)对my_driver实例化并且最终搭建的验证平台如下： 代码清单 2-6

文件：src/ch2/section2.2/2.2.1/top_tb.sv

1 `timescale 1ns/1ps

2 `include "uvm_macros.svh" 3

4 import uvm_pkg::*;



5 `include "my_driver.sv" 6

7 module top_tb; 8

9 reg clk;

10  reg rst_n;

11  reg[7:0] rxd;

12  reg rx_dv;

13  wire[7:0] txd;

14  wire tx_en; 15

16  dut my_dut(.clk(clk),

17             .rst_n(rst_n),

18             .rxd(rxd),

19             .rx_dv(rx_dv),

20             .txd(txd),

21             .tx_en(tx_en)); 22

23  initial begin

24     my_driver drv;

25     drv = new("drv", null);

26     drv.main_phase(null);

27     $finish();

28  end 29

30  initial begin

31     clk = 0;

32     forever begin

33    #100 clk = ~clk;

34     end

35  end 36

37 initial begin

38  rst_n = 1'b0;



| 39   |      | #1000;        |
| ---- | ---- | ------------- |
| 40   |      | rst_n = 1'b1; |
| 41   | end  |               |
| 42   |      |               |
| 43   | end  | module        |

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image018.gif) |





第2行把uvm_macros.svh文件通过include语句包含进来。这是UVM中的一个文件，里面包含了众多的宏定义，只需要包含一次。

第4行通过import语句将整个uvm_pkg导入验证平台中。只有导入了这个库，编译器在编译my_driver.sv文件时才会认识其中的

uvm_driver等类名。

第24和25行定义一个my_driver的实例并将其实例化。注意这里调用new函数时，其传入的名字参数为drv，前文介绍uvm_info 宏的打印信息时出现的代表路径索引的drv就是在这里传入的参数drv。另外传入的parent参数为null，在真正的验证平台中，这个参数一般不是null，这里暂且使用null。

第26行显式地调用my_driver的main_phase。在main_phase的声明中，有一个uvm_phase类型的参数phase，在真正的验证平台中，这个参数是不需要用户理会的。本节的验证平台还算不上一个完整的UVM验证平台，所以暂且传入null。

第27行调用finish函数结束整个仿真，这是一个Verilog中提供的函数。运行这个例子，可以看到“data is drived”被输出了256次。



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image027.gif)所有带星号（*[）的章节表示本节提供源代码，可登录华章网站（http://www.hzbook.com](http://www.hzbook.com/)）获取。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image028.gif)![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image029.gif)本书各章节中有大量源代码。如果在“文件”关键字后有相应的文件名及路径，表明此段代码可以从本书的源码包中找到。这里的例外是一些静态类，其成员变量都是静态的，不实例化也可以正常使用。



## *2.2.2  加入factory机制

上一节给出了一个只有driver、使用UVM搭建的验证平台。严格来说这根本就不算是UVM验证平台，因为UVM的特性几乎一点都没有用到。像上节中my_driver的实例化及drv.main_phase的显式调用，即使不使用UVM，只使用简单的SystemVerilog也可以完成。本节将会为读者展示在初学者看来感觉最神奇的一点：自动创建一个类的实例并调用其中的函数（function）和任务

（task）。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image012.gif)要使用这个功能，需要引入UVM的factory机制： 代码清单        2-7

文件：src/ch2/section2.2/2.2.2/my_driver.sv

3 class my_driver extends uvm_driver; 4

5    `uvm_component_utils(my_driver)

6    function new(string name = "my_driver", uvm_component parent = null);

7       super.new(name, parent);

8       `uvm_info("my_driver", "new is called", UVM_LOW);

9    endfunction

10     extern virtual task main_phase(uvm_phase phase);

11  endclass 12

13  task my_driver::main_phase(uvm_phase phase);

14     `uvm_info("my_driver", "main_phase is called", UVM_LOW);

15     top_tb.rxd <= 8'b0;

16     top_tb.rx_dv <= 1'b0;



17     while(!top_tb.rst_n)

18        @(posedge top_tb.clk);

19     for(int i = 0; i < 256; i++)begin

20        @(posedge top_tb.clk);

21        top_tb.rxd <= $urandom_range(0, 255);

22        top_tb.rx_dv <= 1'b1;

23        `uvm_info("my_driver", "data is drived", UVM_LOW);

24     end

25     @(posedge top_tb.clk);

26     top_tb.rx_dv <= 1'b0;

27  endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image030.gif) |





factory机制的实现被集成在了一个宏中：uvm_component_utils。这个宏所做的事情非常多，其中之一就是将my_driver登记在UVM内部的一张表中，这张表是factory功能实现的基础。只要在定义一个新的类时使用这个宏，就相当于把这个类注册到了这张表中。那么factory机制到底是什么？这个宏还做了哪些事情呢？这些属于UVM中的高级问题，本书会在后文一一展开。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image026.gif)在给driver中加入factory机制后，还需要对top_tb做一些改动： 代码清单 2-8

文件：src/ch2/section2.2/2.2.2/top_tb.sv

7 module top_tb;

…

36  initial begin

37     run_test("my_driver");

38  end 39

40 endmodule



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image031.gif)

这里使用一个run_test语句替换掉了代码清单2-6中第23到28行的my_driver实例化及main_phase的显式调用。运行这个新的验证平台，会输出如下语句：

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image032.gif) |





new is called main_phased is called

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image012.gif) |





一个run_test语句会创建一个my_driver的实例，并且会自动调用my_driver的main_phase。仔细观察run_test语句，会发现传递给它的是一个字符串。UVM根据这个字符串创建了其所代表类的一个实例。如果没有UVM，读者自己能够实现同样的功能吗？

根据类名创建一个类的实例，这是uvm_component_utils宏所带来的效果，同时也是factory机制给读者的最初印象。只有在类定义时声明了这个宏，才能使用这个功能。所以从某种程度上来说，这个宏起到了注册的作用。只有经过注册的类，才能使用这个功能，否则根本不能使用。请记住一点：所有派生自uvm_component及其派生类的类都应该使用uvm_component_utils宏注册。

除了根据一个字符串创建类的实例外，上述代码中另外一个神奇的地方是main_phase被自动调用了。在UVM验证平台中，只要一个类使用uvm_component_utils注册且此类被实例化了，那么这个类的main_phase就会自动被调用。这也就是为什么上一节中会强调实现一个driver等于实现其main_phase。所以，在driver中，最重要的就是实现main_phase。

上面的例子中，只输出到“main_phase is called”。令人沮丧的是，根本没有输出“data is drived”，而按照预期，它应该输出256

次。关于这个问题，牵涉UVM的objection机制。



## *2.2.3  加入objection机制

在上一节中，虽然输出了“main_phase is called”，但是“data is drived”并没有输出。而main_phase是一个完整的任务，没有理由只执行第一句，而后面的代码不执行。看上去似乎main_phase在执行的过程中被外力“杀死”了，事实上也确实如此。

UVM中通过objection机制来控制验证平台的关闭。细心的读者可能发现，在上节的例子中，并没有如2.2.1节所示显式地调用finish语句来结束仿真。但是在运行上节例子时，仿真平台确实关闭了。在每个phase中，UVM会检查是否有objection被提起

（raise_objection），如果有，那么等待这个objection被撤销（drop_objection）后停止仿真；如果没有，则马上结束当前phase。加入了objection机制的driver如下所示：

代码清单  2-9

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image018.gif) |





文件：src/ch2/section2.2/2.2.3/my_driver.sv

13  task my_driver::main_phase(uvm_phase phase);

14    phase.raise_objection(this);

15    `uvm_info("my_driver", "main_phase is called", UVM_LOW);

16    top_tb.rxd <= 8'b0;

17    top_tb.rx_dv <= 1'b0;

18    while(!top_tb.rst_n)

19      @(posedge top_tb.clk);

20    for(int i = 0; i < 256; i++)begin

21      @(posedge top_tb.clk);

22      top_tb.rxd <= $urandom_range(0, 255);

23      top_tb.rx_dv <= 1'b1;

24      `uvm_info("my_driver", "data is drived", UVM_LOW);



25    end

26    @(posedge top_tb.clk);

27    top_tb.rx_dv <= 1'b0;

28    phase.drop_objection(this);

29  endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image009.gif) |





在开始学习时，读者可以简单地将drop_objection语句当成是finish函数的替代者，只是在drop_objection语句之前必须先调用raise_objection语句，raise_objection和drop_objection总是成对出现。加入objection机制后再运行验证平台，可以发现“data is drived”按照预期输出了256次。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image033.gif)raise_objection语句必须在main_phase中第一个消耗仿真时间 [[1\]](#_bookmark21)的语句之前。如$display语句是不消耗仿真时间的，这些语句可以放在raise_objection之前，但是类似@（posedge top.clk）等语句是要消耗仿真时间的。按照如下的方式使用raise_objection是无法起到作用的：

代码清单 2-10

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image034.gif) |





task my_driver::main_phase(uvm_phase phase); @(posedge top_tb.clk); phase.raise_objection(this);

`uvm_info("my_driver", "main_phase is called", UVM_LOW); top_tb.rxd <= 8'b0;

top_tb.rx_dv <= 1'b0; while(!top_tb.rst_n)

@(posedge top_tb.clk);

for(int i = 0; i < 256; i++)begin @(posedge top_tb.clk);



 

 

end



top_tb.rxd <= $urandom_range(0, 255); top_tb.rx_dv <= 1'b1;

`uvm_info("my_driver", "data is drived", UVM_LOW);



@(posedge top_tb.clk); top_tb.rx_dv <= 1'b0; phase.drop_objection(this);

endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image035.gif) |





 



## *2.2.4  加入virtual interface

在前几节的例子中，driver中等待时钟事件（@posedge top.clk）、给DUT中输入端口赋值（top.rx_dv<=1‘b1）都是使用绝对路径，绝对路径的使用大大减弱了验证平台的可移植性。一个最简单的例子就是假如clk信号的层次从top.clk变成了top.clk_inst.clk， 那么就需要对driver中的相关代码做大量修改。因此，从根本上来说，应该尽量杜绝在验证平台中使用绝对路径。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image030.gif)避免绝对路径的一个方法是使用宏： 代码清单         2-11

`define TOP top_tb

task my_driver::main_phase(uvm_phase phase); phase.raise_objection(this);

`uvm_info("my_driver", "main_phase is called", UVM_LOW);

`TOP.rxd <= 8'b0;

`TOP.rx_dv <= 1'b0; while(!`TOP.rst_n)

@(posedge `TOP.clk);

for(int i = 0; i < 256; i++)begin @(posedge `TOP.clk);

`TOP.rxd <= $urandom_range(0, 255);

`TOP.rx_dv <= 1'b1;

`uvm_info("my_driver", "data is drived", UVM_LOW);

end

@(posedge `TOP.clk);

`TOP.rx_dv <= 1'b0; phase.drop_objection(this);



endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image019.gif) |





这样，当路径修改时，只需要修改宏的定义即可。但是假如clk的路径变为了top_tb.clk_inst.clk，而rst_n的路径变为了

top_tb.rst_inst.rst_n，那么单纯地修改宏定义是无法起到作用的。

避免绝对路径的另外一种方式是使用interface。在SystemVerilog中使用interface来连接验证平台与DUT的端口。interface的定义比较简单：

代码清单  2-12

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image023.gif) |





文件：src/ch2/section2.2/2.2.4/my_if.sv

4 interface my_if(input clk, input rst_n); 5

6   logic [7:0] data;

7   logic valid;

8 endinterface

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image019.gif) |





![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image023.gif)定义了interface后，在top_tb中实例化DUT时，就可以直接使用： 代码清单         2-13

文件：src/ch2/section2.2/2.2.4/top_tb.sv

17  my_if input_if(clk, rst_n);

18  my_if output_if(clk, rst_n);



| 19   |      |                           |
| ---- | ---- | ------------------------- |
| 20   | dut  | my_dut(.clk(clk),         |
| 21   |      | .rst_n(rst_n),            |
| 22   |      | .rxd(input_if.data),      |
| 23   |      | .rx_dv(input_if.valid),   |
| 24   |      | .txd(output_if.data),     |
| 25   |      | .tx_en(output_if.valid)); |

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image023.gif) |





那么如何在driver中使用interface呢？一种想法是在driver中声明如下语句，然后再通过赋值的形式将top_tb中的input_if传递给它：

代码清单  2-14

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image036.gif) |





class my_driver extends uvm_driver; my_if    drv_if;

… endclass

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image037.gif) |





读者可以试一下，这样的使用方式是会报语法错误的，因为my_driver是一个类，在类中不能使用上述方式声明一个

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image038.gif)interface，只有在类似top_tb这样的模块（module）中才可以。在类中使用的是virtual interface： 代码清单         2-15

文件：src/ch2/section2.2/2.2.4/my_driver.sv

3 class my_driver extends uvm_driver;



4

5  virtual my_if vif;

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image039.gif) |





![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image012.gif)在声明了vif后，就可以在main_phase中使用如下方式驱动其中的信号： 代码清单  2-16

文件：src/ch2/section2.2/2.2.4/my_driver.sv

23  task my_driver::main_phase(uvm_phase phase);

24     phase.raise_objection(this);

25     `uvm_info("my_driver", "main_phase is called", UVM_LOW);

26     vif.data <= 8'b0;

27     vif.valid <= 1'b0;

28     while(!vif.rst_n)

29        @(posedge vif.clk);

30     for(int i = 0; i < 256; i++)begin

31        @(posedge vif.clk);

32        vif.data <= $urandom_range(0, 255);

33        vif.valid <= 1'b1;

34        `uvm_info("my_driver", "data is drived", UVM_LOW);

35     end

36     @(posedge vif.clk);

37     vif.valid <= 1'b0;

38     phase.drop_objection(this);

39  endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image030.gif) |





可以清楚看到，代码中的绝对路径已经消除了，大大提高了代码的可移植性和可重用性。



剩下的最后一个问题就是，如何把top_tb中的input_if和my_driver中的vif对应起来呢？最简单的方法莫过于直接赋值。此时一个新的问题又摆在了面前：在top_tb中，通过run_test语句建立了一个my_driver的实例，但是应该如何引用这个实例呢？不可能像引用my_dut那样直接引用my_driver中的变量：top_tb.my_dut.xxx是可以的，但是top_tb.my_driver.xxx是不可以的。这个问题的终极原因在于UVM通过run_test语句实例化了一个脱离了top_tb层次结构的实例，建立了一个新的层次结构。

对于这种脱离了top_tb层次结构，同时又期望在top_tb中对其进行某些操作的实例，UVM引进了config_db机制。在config_db机制中，分为set和get两步操作。所谓set操作，读者可以简单地理解成是“寄信”，而get则相当于是“收信”。在top_tb中执行set操作：

代码清单  2-17

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image036.gif) |





文件：src/ch2/section2.2/2.2.4/top_tb.sv

44  initial begin

45    uvm_config_db#(virtual my_if)::set(null, "uvm_test_top", "vif", input_if);

46  end

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image039.gif) |





![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image018.gif)在my_driver中，执行get操作： 代码清单 2-18

文件：src/ch2/section2.2/2.2.4/my_driver.sv

13    virtual function void build_phase(uvm_phase phase);

14      super.build_phase(phase);

15      `uvm_info("my_driver", "build_phase is called", UVM_LOW);



16      if(!uvm_config_db#(virtual my_if)::get(this, "", "vif", vif))

17         `uvm_fatal("my_driver", "virtual interface must be set for vif!!!")

18    endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image040.gif) |





这里引入了build_phase。与main_phase一样，build_phase也是UVM中内建的一个phase。当UVM启动后，会自动执行build_phase。build_phase在new函数之后main_phase之前执行。在build_phase中主要通过config_db的set和get操作来传递一些数据， 以及实例化成员变量等。需要注意的是，这里需要加入super.build_phase语句，因为在其父类的build_phase中执行了一些必要的操作，这里必须显式地调用并执行它。build_phase与main_phase不同的一点在于，build_phase是一个函数phase，而main_phase是一个任务phase，build_phase是不消耗仿真时间的。build_phase总是在仿真时间（$time函数打印出的时间）为0时执行。

在build_phase中出现了uvm_fatal宏，uvm_fatal宏是一个类似于uvm_info的宏，但是它只有两个参数，这两个参数与uvm_info宏的前两个参数的意义完全一样。与uvm_info宏不同的是，当它打印第二个参数所示的信息后，会直接调用Verilog的finish函数来结束仿真。uvm_fatal的出现表示验证平台出现了重大问题而无法继续下去，必须停止仿真并做相应的检查。所以对于uvm_fatal来

说，uvm_info中出现的第三个参数的冗余度级别是完全没有意义的，只要是uvm_fatal打印的信息，就一定是非常关键的，所以无需设置第三个参数。

config_db的set和get函数都有四个参数，这两个函数的第三个参数必须完全一致。set函数的第四个参数表示要将哪个interface通过config_db传递给my_driver，get函数的第四个参数表示把得到的interface传递给哪个my_driver的成员变量。set函数的第二个参 数表示的是路径索引，即在2.2.1节介绍uvm_info宏时提及的路径索引。在top_tb中通过run_test创建了一个my_driver的实例，那么这个实例的名字是什么呢？答案是uvm_test_top：UVM通过run_test语句创建一个名字为uvm_test_top的实例。读者可以通过把代码



清单2-3中的语句插入my_driver（build_phase或者main_phase）中来验证。

无论传递给run_test的参数是什么，创建的实例的名字都为uvm_test_top。由于set操作的目标是my_driver，所以set函数的第二个参数就是uvm_test_top。set函数的第一个参数null以及get函数的第一和第二个参数可以暂时放在一边，后文会详细说明。

set函数与get函数让人疑惑的另外一点是其古怪的写法。使用双冒号是因为这两个函数都是静态函数，而uvm_config_db#（virtual my_if）则是一个参数化的类，其参数就是要寄信的类型，这里是virtual my_if。假如要向my_driver的var变量传递一个int类型的数据，那么可以使用如下方式：

代码清单  2-19

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image041.gif) |





initial begin

uvm_config_db#(int)::set(null, "uvm_test_top", "var", 100);

end

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image019.gif) |





![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image009.gif)而在my_driver中应该使用如下方式： 代码清单         2-20

class my_driver extends uvm_driver; int var;

virtual function void build_phase(uvm_phase phase); super.build_phase(phase);

`uvm_info("my_driver", "build_phase is called", UVM_LOW);



if(!uvm_config_db#(virtual my_if)::get(this, "", "vif", vif))

`uvm_fatal("my_driver", "virtual interface must be set for vif!!!") if(!uvm_config_db#(int)::get(this, "", "var", var))

`uvm_fatal("my_driver", "var must be set!!!") endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image023.gif) |





从这里可以看出，可以向my_driver中“寄”许多信。上文列举的两个例子是top_tb向my_driver传递了两个不同类型的数据，其实也可以传递相同类型的不同数据。假如my_driver中需要两个my_if，那么可以在top_tb中这么做：

代码清单  2-21

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image041.gif) |





initial begin

uvm_config_db#(virtual my_if)::set(null, "uvm_test_top", "vif", input_if); uvm_config_db#(virtual my_if)::set(null, "uvm_test_top", "vif2", output_if);

end

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image040.gif) |





![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image036.gif)在my_driver中这么做： 代码清单 2-22

virtual my_if vif; virtual my_if vif2;

virtual function void build_phase(uvm_phase phase); super.build_phase(phase);

`uvm_info("my_driver", "build_phase is called", UVM_LOW); if(!uvm_config_db#(virtual my_if)::get(this, "", "vif", vif))



`uvm_fatal("my_driver", "virtual interface must be set for vif!!!") if(!uvm_config_db#(virtual my_if)::get(this, "", "vif2", vif2))

`uvm_fatal("my_driver", "virtual interface must be set for vif2!!!") endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |







# 2.3       为验证平台加入各个组件

## *2.3.1  加入transaction

在2.2节中，所有的操作都是基于信号级的。从本节开始将引入reference model、monitor、scoreboard等验证平台的其他组件。在这些组件之间，信息的传递是基于transaction的，因此，本节将先引入transaction的概念。

transaction是一个抽象的概念。一般来说，物理协议中的数据交换都是以帧或者包为单位的，通常在一帧或者一个包中要定义好各项参数，每个包的大小不一样。很少会有协议是以bit或者byte为单位来进行数据交换的。以以太网为例，每个包的大小至少是64byte。这个包中要包括源地址、目的地址、包的类型、整个包的CRC校验数据等。transaction就是用于模拟这种实际情况，一笔transaction就是一个包。在不同的验证平台中，会有不同的transaction。一个简单的transaction的定义如下：

代码清单  2-23

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.1/my_transaction.sv

4 class my_transaction extends uvm_sequence_item; 5

6    rand bit[47:0] dmac;

7    rand bit[47:0] smac;

8    rand bit[15:0] ether_type;

9    rand byte   pload[];

10     rand bit[31:0] crc; 11

12     constraint pload_cons{



13        pload.size >= 46;

14     pload.size <= 1500; 15 }

16

17     function bit[31:0] calc_crc();

18        return 32'h0;

19     endfunction 20

21     function void post_randomize();

22        crc = calc_crc;

23     endfunction 24

25 `uvm_object_utils(my_transaction) 26

27     function new(string name = "my_transaction");

28        super.new(name);

29     endfunction

30  endclass

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





其中dmac是48bit的以太网目的地址，smac是48bit的以太网源地址，ether_type是以太网类型，pload是其携带数据的大小，通过pload_cons约束可以看到，其大小被限制在46～1500byte，CRC是前面所有数据的校验值。由于CRC的计算方法稍显复杂，且其代码在网络上随处可见，因此这里只是在post_randomize中加了一个空函数calc_crc，有兴趣的读者可以将其补充完整。post_randomize是SystemVerilog中提供的一个函数，当某个类的实例的randomize函数被调用后，post_randomize会紧随其后无条件地被调用。

在transaction定义中，有两点值得引起注意：一是my_transaction的基类是uvm_sequence_item。在UVM中，所有的transaction都要从uvm_sequence_item派生，只有从uvm_sequence_item派生的transaction才可以使用后文讲述的UVM中强大的sequence机制。二



是这里没有使用uvm_component_utils宏来实现factory机制，而是使用了uvm_object_utils。从本质上来说，my_transaction与my_driver是有区别的，在整个仿真期间，my_driver是一直存在的，my_transaction不同，它有生命周期。它在仿真的某一时间产生，经过driver驱动，再经过reference model处理，最终由scoreboard比较完成后，其生命周期就结束了。一般来说，这种类都是派生自uvm_object或者uvm_object的派生类，uvm_sequence_item的祖先就是uvm_object。UVM中具有这种特征的类都要使用uvm_object_utils宏来实现。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)当完成transaction的定义后，就可以在my_driver中实现基于transaction的驱动： 代码清单  2-24

文件：src/ch2/section2.3/2.3.1/my_driver.sv

22  task my_driver::main_phase(uvm_phase phase);

23     my_transaction tr;

…

29     for(int i = 0; i < 2; i++) begin

30        tr = new("tr");

31        assert(tr.randomize() with {pload.size == 200;});

32        drive_one_pkt(tr);

33     end

…

36 endtask 37

38  task my_driver::drive_one_pkt(my_transaction tr);

39     bit [47:0] tmp_data; 40 bit [7:0] data_q[$]; 41

42     //push dmac to data_q



43     tmp_data = tr.dmac;

44     for(int i = 0; i < 6; i++) begin

45        data_q.push_back(tmp_data[7:0]);

46        tmp_data = (tmp_data >> 8);

47     end

48     //push smac to data_q

…

54  //push ether_type to data_q

…

60  //push payload to data_q

…

64     //push crc to data_q

65     tmp_data = tr.crc;

66     for(int i = 0; i < 4; i++) begin

67        data_q.push_back(tmp_data[7:0]);

68        tmp_data = (tmp_data >> 8);

69     end 70

71     `uvm_info("my_driver", "begin to drive one pkt", UVM_LOW);

72     repeat(3) @(posedge vif.clk); 73

74     while(data_q.size() > 0) begin

75        @(posedge vif.clk);

76        vif.valid <= 1'b1;

77        vif.data <= data_q.pop_front();

78     end 79

80     @(posedge vif.clk);

81     vif.valid <= 1'b0;

82     `uvm_info("my_driver", "end drive one pkt", UVM_LOW);

83  endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |







在main_phase中，先使用randomize将tr随机化，之后通过drive_one_pkt任务将tr的内容驱动到DUT的端口上。在drive_one_pkt 中，先将tr中所有的数据压入队列data_q中，之后再将data_q中所有的数据弹出并驱动。将tr中的数据压入队列data_q中的过程相当于打包成一个byte流的过程。这个过程还可以使用SystemVerlog提供的流操作符实现。具体请参照SystemVerilog语言标准IEEE Std 1800 TM—2012（IEEE Standard for SystemVerilog—Unified Hardware Design，Specification，and Verification Language）的11.4.14

节。



## *2.3.2  加入env

在验证平台中加入reference model、scoreboard等之前，思考一个问题：假设这些组件已经定义好了，那么在验证平台的什么位置对它们进行实例化呢？在top_tb中使用run_test进行实例化显然是不行的，因为run_test函数虽然强大，但也只能实例化一个实例；如果在top_tb中使用2.2.1节中实例化driver的方式显然也不可行，因为run_test相当于在top_tb结构层次之外建立一个新的结构层次，而2.2.1节的方式则是基于top_tb的层次结构，如果基于此进行实例化，那么run_test的引用也就没有太大的意义了；如果在driver中进行实例化则更加不合理。

这个问题的解决方案是引入一个容器类，在这个容器类中实例化driver、monitor、reference model和scoreboard等。在调用run_test时，传递的参数不再是my_driver，而是这个容器类，即让UVM自动创建这个容器类的实例。在UVM中，这个容器类称为uvm_env：

代码清单  2-25

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.2/my_env.sv

4 class my_env extends uvm_env; 5

6 my_driver drv; 7

8   function new(string name = "my_env", uvm_component parent);

9     super.new(name, parent);

10    endfunction 11

12    virtual function void build_phase(uvm_phase phase);



13      super.build_phase(phase);

14      drv = my_driver::type_id::create("drv", this);

15    endfunction 16

17    `uvm_component_utils(my_env)

18  endclass

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





所有的env应该派生自uvm_env，且与my_driver一样，容器类在仿真中也是一直存在的，使用uvm_component_utils宏来实现

factory的注册。

在my_env的定义中，最让人难以理解的是第14行drv的实例化。这里没有直接调用my_driver的new函数，而是使用了一种古怪的方式。这种方式就是factory机制带来的独特的实例化方式。只有使用factory机制注册过的类才能使用这种方式实例化；只有使用这种方式实例化的实例，才能使用后文要讲述的factory机制中最为强大的重载功能。验证平台中的组件在实例化时都应该使用type_name：：type_id：：create的方式。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)在drv实例化时，传递了两个参数，一个是名字drv，另外一个是this指针，表示my_env。回顾一下my_driver的new函数： 代码清单 2-26

function new(string name = "my_driver", uvm_component parent = null); super.new(name, parent);

endfuncti

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |







这个new函数有两个参数，第一个参数是实例的名字，第二个则是parent。由于my_driver在uvm_env中实例化，所以my_driver 的父结点（parent）就是my_env。通过parent的形式，UVM建立起了树形的组织结构。在这种树形的组织结构中，由run_test创建的实例是树根（这里是my_env），并且树根的名字是固定的，为uvm_test_top，这在前文中已经讲述过；在树根之后会生长出枝叶（这里只有my_driver），长出枝叶的过程需要在my_env的build_phase中手动实现。无论是树根还是树叶，都必须由uvm_component或者其派生类继承而来。整棵UVM树的结构如图2-3所示。



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image043.jpg)

图2-3  UVM树的生长：加入env



当加入了my_env后，整个验证平台中存在两个build_phase，一个是my_env的，一个是my_driver的。那么这两个build_phase按照何种顺序执行呢？在UVM的树形结构中，build_phase的执行遵照从树根到树叶的顺序，即先执行my_env的build_phase，再执行my_driver的build_phase。当把整棵树的build_phase都执行完毕后，再执行后面的phase。

my_driver在验证平台中的层次结构发生了变化，它一跃从树根变成了树叶，所以在top_tb中使用config_db机制传递virtual my_if时，要改变相应的路径；同时，run_test的参数也从my_driver变为了my_env：

代码清单  2-27

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.2/top_tb.sv

42  initial begin

43    run_test("my_env");

44  end 45

46  initial begin

47    uvm_config_db#(virtual my_if)::set(null, "uvm_test_top.drv", "vif", inp ut_if);

48  end

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





set函数的第二个参数从uvm_test_top变为了uvm_test_top.drv，其中uvm_test_top是UVM自动创建的树根的名字，而drv则是在my_env的build_phase中实例化drv时传递过去的名字。如果在实例化drv时传递的名字是my_drv，那么set函数的第二个参数中也应该是my_drv：

代码清单  2-28

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image044.gif) |







![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image045.gif)

class my_env extends uvm_env

…

drv = my_driver::type_id::create("my_drv", this);

…

endclass module top_tb;

…

initial begin

uvm_config_db#(virtual my_if)::set(null, "uvm_test_top.my_drv", "vif", inpu t_if); end

endmodule

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |







## *2.3.3  加入monitor

验证平台必须监测DUT的行为，只有知道DUT的输入输出信号变化之后，才能根据这些信号变化来判定DUT的行为是否正确。

验证平台中实现监测DUT行为的组件是monitor。driver负责把transaction级别的数据转变成DUT的端口级别，并驱动给DUT， monitor的行为与其相对，用于收集DUT的端口数据，并将其转换成transaction交给后续的组件如reference model、scoreboard等处理。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)一个monitor的定义如下： 代码清单 2-29

文件：src/ch2/section2.3/2.3.3/my_monitor.sv

3 class my_monitor extends uvm_monitor; 4

5 virtual my_if vif; 6

7    `uvm_component_utils(my_monitor)

8    function new(string name = "my_monitor", uvm_component parent = null);

9       super.new(name, parent);

10     endfunction 11

12     virtual function void build_phase(uvm_phase phase);

13        super.build_phase(phase);

14        if(!uvm_config_db#(virtual my_if)::get(this, "", "vif", vif))



15           `uvm_fatal("my_monitor", "virtual interface must be set for vif!!!")

16     endfunction 17

18     extern task main_phase(uvm_phase phase);

19     extern task collect_one_pkt(my_transaction tr);

20  endclass 21

22  task my_monitor::main_phase(uvm_phase phase);

23     my_transaction tr;

24     while(1) begin

25        tr = new("tr");

26        collect_one_pkt(tr);

27     end

28  endtask 29

30 task my_monitor::collect_one_pkt(my_transaction tr); 31 bit[7:0] data_q[$];

32     int psize;

33     while(1) begin

34       @(posedge vif.clk);

35       if(vif.valid) break;

36     end 37

38     `uvm_info("my_monitor", "begin to collect one pkt", UVM_LOW);

39     while(vif.valid) begin

40        data_q.push_back(vif.data);

41        @(posedge vif.clk);

42     end

43     //pop dmac

44     for(int i = 0; i < 6; i++) begin

45        tr.dmac = {tr.dmac[39:0], data_q.pop_front()};

46     end

47     //pop smac

…



51  //pop ether_type

…

58  //pop payload

…

62     //pop crc

63     for(int i = 0; i < 4; i++) begin

64        tr.crc = {tr.crc[23:0], data_q.pop_front()};

65     end

66     `uvm_info("my_monitor", "end collect one pkt, print it:", UVM_LOW);

67     tr.my_print();

68  endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





有几点需要注意的是：第一，所有的monitor类应该派生自uvm_monitor；第二，与driver类似，在my_monitor中也需要有一个virtual my_if；第三，uvm_monitor在整个仿真中是一直存在的，所以它是一个component，要使用uvm_component_utils宏注册；第四，由于monitor需要时刻收集数据，永不停歇，所以在main_phase中使用while（1）循环来实现这一目的。

在查阅collect_one_pkt的代码时，可以与my_driver的drv_one_pkt对比来看，两者代码非常相似。当收集完一个transaction后， 通过my_print函数将其打印出来。my_print在my_transaction中定义如下：

代码清单  2-30

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.3/my_transaction.sv

31     function void my_print();

32       $display("dmac = %0h", dmac);

33       $display("smac = %0h", smac);

34       $display("ether_type = %0h", ether_type);

35       for(int i = 0; i < pload.size; i++) begin



36         $display("pload[%0d] = %0h", i, pload[i]);

37       end

38       $display("crc = %0h", crc);

39     endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)当完成monitor的定义后，可以在env中对其进行实例化： 代码清单  2-31

文件：src/ch2/section2.3/2.3.3/my_env.sv

4 class my_env extends uvm_env; 5

6   my_driver drv;

7   my_monitor i_mon; 8

9  my_monitor o_mon;

…

15    virtual function void build_phase(uvm_phase phase);

16      super.build_phase(phase);

17      drv = my_driver::type_id::create("drv", this);

18      i_mon = my_monitor::type_id::create("i_mon", this);

19      o_mon = my_monitor::type_id::create("o_mon", this);

20    endfunction

…

23 endclass

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





需要引起注意的是这里实例化了两个monitor，一个用于监测DUT的输入口，一个用于监测DUT的输出口。DUT的输出口设置一个monitor没有任何疑问，但是在DUT的输入口设置一个monitor有必要吗？由于transaction是由driver产生并输出到DUT的端口



上，所以driver可以直接将其交给后面的reference model。在2.1节所示的框图中，也是使用这样的策略。所以是否使用monitor，这个答案仁者见仁，智者见智。这里还是推荐使用monitor，原因是：第一，在一个大型的项目中，driver根据某一协议发送数据，而monitor根据这种协议收集数据，如果driver和monitor由不同人员实现，那么可以大大减少其中任何一方对协议理解的错误；第

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image047.jpg)二，在后文将会看到，在实现代码重用时，使用monitor是非常有必要的。现在，整棵UVM树的结构如图2-4所示。

 

 

 

 

 

 

图2-4  UVM树的生长：加入monitor

在env中实例化monitor后，要在top_tb中使用config_db将input_if和output_if传递给两个monitor：



代码清单  2-32

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.3/top_tb.sv

47  initial begin

48     uvm_config_db#(virtual my_if)::set(null, "uvm_test_top.drv", "vif", input_if);

49     uvm_config_db#(virtual my_if)::set(null, "uvm_test_top.i_mon", "vif", input_if);

50     uvm_config_db#(virtual my_if)::set(null, "uvm_test_top.o_mon", "vif", output_if);

51  end

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |







## *2.3.4  封装成agent

上一节在验证平台中加入monitor时，读者看到了driver和monitor之间的联系：两者之间的代码高度相似。其本质是因为二者处理的是同一种协议，在同样一套既定的规则下做着不同的事情。由于二者的这种相似性，UVM中通常将二者封装在一起，成为一个agent。因此，不同的agent就代表了不同的协议。

代码清单  2-33

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.4/my_agent.sv

4 class my_agent extends uvm_agent ;

5   my_driver   drv;

6   my_monitor mon; 7

8   function new(string name, uvm_component parent);

9     super.new(name, parent);

10    endfunction 11

12    extern virtual function void build_phase(uvm_phase phase);

13    extern virtual function void connect_phase(uvm_phase phase); 14

15    `uvm_component_utils(my_agent)

16  endclass 17

18

19  function void my_agent::build_phase(uvm_phase phase);

20    super.build_phase(phase);

21    if (is_active == UVM_ACTIVE) begin

22     drv = my_driver::type_id::create("drv", this);



23    end

24    mon = my_monitor::type_id::create("mon", this);

25  endfunction 26

27  function void my_agent::connect_phase(uvm_phase phase);

28    super.connect_phase(phase);

29  endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





所有的agent都要派生自uvm_agent类，且其本身是一个component，应该使用uvm_component_utils宏来实现factory注册。

这里最令人困惑的可能是build_phase中为何根据is_active这个变量的值来决定是否创建driver的实例。is_active是uvm_agent的一个成员变量，从UVM的源代码中可以找到它的原型如下：

代码清单  2-34

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





来源：UVM 源代码

uvm_active_passive_enum is_active = UVM_ACTIVE;

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)而uvm_active_passive_enum是一个枚举类型变量，其定义为： 代码清单         2-35

来源：UVM 源代码



typedef enum bit { UVM_PASSIVE=0, UVM_ACTIVE=1 } uvm_active_passive_enum;

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





这个枚举变量仅有两个值：UVM_PASSIVE和UVM_ACTIVE。在uvm_agent中，is_active的值默认为UVM_ACTIVE，在这种模式下，是需要实例化driver的。那么什么是UVM_PASSIVE模式呢？以本章的DUT为例，如图2-5所示，在输出端口上不需要驱动任何信号，只需要监测信号。在这种情况下，端口上是只需要monitor的，所以driver可以不用实例化。

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image049.jpg) |





图2-5  DUT输入和输出端的agent

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)在把driver和monitor封装成agent后，在env中需要实例化agent，而不需要直接实例化driver和monitor了： 代码清单  2-36



文件：src/ch2/section2.3/2.3.4/my_env.sv

4 class my_env extends uvm_env; 5

6   my_agent i_agt;

7   my_agent o_agt;

…

13    virtual function void build_phase(uvm_phase phase);

14      super.build_phase(phase);

15      i_agt = my_agent::type_id::create("i_agt", this);

16      o_agt = my_agent::type_id::create("o_agt", this);

17      i_agt.is_active = UVM_ACTIVE;

18      o_agt.is_active = UVM_PASSIVE;

19    endfunction

…

22 endclass

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





完成i_agt和o_agt的声明后，在my_env的build_phase中对它们进行实例化后，需要指定各自的工作模式是active模式还是passive 模式。现在，整棵UVM树变为了如图2-6所示形式。



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image050.jpg)

图2-6  UVM树的生长：加入agent

由于agent的加入，driver和monitor的层次结构改变了，在top_tb中使用config_db设置virtual my_if时要注意改变路径：



 

代码清单  2-37

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.4/top_tb.sv

48  initial begin

49     uvm_config_db#(virtual my_if)::set(null, "uvm_test_top.i_agt.drv", "vif", input_if);

50     uvm_config_db#(virtual my_if)::set(null, "uvm_test_top.i_agt.mon", "vif", input_if);

51     uvm_config_db#(virtual my_if)::set(null, "uvm_test_top.o_agt.mon", "vif", output_if);

52  end

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





在加入了my_agent后，UVM的树形结构越来越清晰。首先，只有uvm_component才能作为树的结点，像my_transaction这种使用uvm_object_utils宏实现的类是不能作为UVM树的结点的。其次，在my_env的build_phase中，创建i_agt和o_agt的实例是在build_phase中；在agent中，创建driver和monitor的实例也是在build_phase中。按照前文所述的build_phase的从树根到树叶的执行顺序，可以建立一棵完整的UVM树。UVM要求UVM树最晚在build_phase时段完成，如果在build_phase后的某个phase实例化一个component：

代码清单  2-38

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





class my_env extends uvm_env;

…

virtual function void build_phase(uvm_phase phase); super.build_phase(phase);

endfunction

virtual task main_phase(uvm_phase phase);

i_agt = my_agent::type_id::create("i_agt", this);



o_agt = my_agent::type_id::create("o_agt", this); i_agt.is_active = UVM_ACTIVE;

o_agt.is_active = UVM_PASSIVE; endtask

endclass

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





如上所示，将在my_env的build_phase中的实例化工作移动到main_phase中，UVM会给出如下错误提示：

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





UVM_FATAL @ 0: i_agt [ILLCRT] It is illegal to create a component ('i_agt' under 'uvm_test_top') a

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





那么是不是只能在build_phase中执行实例化的动作呢？答案是否定的。其实还可以在new函数中执行实例化的动作。如可以在

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)my_agent的new函数中实例化driver和monitor： 代码清单      2-39

function new(string name, uvm_component parent); super.new(name, parent);

if (is_active == UVM_ACTIVE) begin

drv = my_driver::type_id::create("drv", this);

end

mon = my_monitor::type_id::create("mon", this); endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





这样引起的一个问题是无法通过直接赋值的方式向uvm_agent传递is_active的值。在my_env的build_phase（或者new函数）中，



向i_agt和o_agt的is_active赋值，根本不会产生效果。因此i_agt和o_agt都工作在active模式（is_active的默认值是UVM_ACTIVE）， 这与预想差距甚远。要解决这个问题，可以在my_agent实例化之前使用config_db语句传递is_active的值：

代码清单  2-40

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





class my_env extends uvm_env;

virtual function void build_phase(uvm_phase phase); super.build_phase(phase);

uvm_config_db#(uvm_active_passive_enum)::set(this, "i_agt", "is_active", UVM_ACTIVE); uvm_config_db#(uvm_active_passive_enum)::set(this, "o_agt", "is_active", UVM_PASSIVE); i_agt = my_agent::type_id::create("i_agt", this);

o_agt = my_agent::type_id::create("o_agt", this); endfunction

endclass

class my_agent extends uvm_agent ;

function new(string name, uvm_component parent); super.new(name, parent);

uvm_config_db#(uvm_active_passive_enum)::get(this, "", "is_active", is_active); if (is_active == UVM_ACTIVE) begin

drv = my_driver::type_id::create("drv", this); end

mon = my_monitor::type_id::create("mon", this); endfunction

endclass

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





只是UVM中约定俗成的还是在build_phase中完成实例化工作。因此，强烈建议仅在build_phase中完成实例化。



## *2.3.5  加入reference model

在2.1节中讲述验证平台的框图时曾经说过，reference model用于完成和DUT相同的功能。reference model的输出被scoreboard 接收，用于和DUT的输出相比较。DUT如果很复杂，那么reference model也会相当复杂。本章的DUT很简单，所以reference model 也相当简单：

代码清单  2-41

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.5/my_model.sv

4 class my_model extends uvm_component; 5

6    uvm_blocking_get_port #(my_transaction) port;

7    uvm_analysis_port #(my_transaction)   ap; 8

9    extern function new(string name, uvm_component parent);

10     extern function void build_phase(uvm_phase phase);

11     extern virtual   task main_phase(uvm_phase phase); 12

13     `uvm_component_utils(my_model)

14  endclass 15

16  function my_model::new(string name, uvm_component parent);

17     super.new(name, parent);

18  endfunction 19

20  function void my_model::build_phase(uvm_phase phase);

21     super.build_phase(phase);

22     port = new("port", this);



23     ap = new("ap", this);

24  endfunction 25

26  task my_model::main_phase(uvm_phase phase);

27     my_transaction tr;

28     my_transaction new_tr;

29     super.main_phase(phase);

30     while(1) begin

31        port.get(tr);

32        new_tr = new("new_tr");

33        new_tr.my_copy(tr);

34        `uvm_info("my_model", "get one transaction, copy and print it:", UVM_LOW)

35        new_tr.my_print();

36        ap.write(new_tr);

37     end

38  endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





在my_model的main_phase中，只是单纯地复制一份从i_agt得到的tr，并传递给后级的scoreboard中。my_copy是一个在

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)my_transaction中定义的函数，其代码为： 代码清单         2-42

文件：src/ch2/section2.3/2.3.5/my_transaction.sv

41    function void my_copy(my_transaction tr);

42       if(tr == null)

43         `uvm_fatal("my_transaction", "tr is null!!!!")

44       dmac = tr.dmac;

45       smac = tr.smac;

46       ether_type = tr.ether_type;

47       pload = new[tr.pload.size()];



48       for(int i = 0; i < pload.size(); i++) begin

49         pload[i] = tr.pload[i];

50       end

51       crc = tr.crc;

52    endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





这里实现了两个my_transaction的复制。

完成my_model的定义后，需要将其在my_env中实例化。其实例化方式与agent、driver相似，这里不具体列出代码。在加入

my_model后，整棵UVM树变成了如图2-7所示的形式。



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image052.jpg)

图2-7  UVM树的生长：加入reference model

my_model并不复杂，这其中令人感兴趣的是my_transaction的传递方式。my_model是从i_agt中得到my_transaction，并把my_transaction传递给my_scoreboard。在UVM中，通常使用TLM（Transaction Level Modeling）实现component之间transaction级别的通信。



要实现通信，有两点是值得考虑的：第一，数据是如何发送的？第二，数据是如何接收的？在UVM的transaction级别的通信中，数据的发送有多种方式，其中一种是使用uvm_analysis_port。在my_monitor中定义如下变量：

代码清单  2-43

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.5/my_monitor.sv

7  uvm_analysis_port #(my_transaction) ap;

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





uvm_analysis_port是一个参数化的类，其参数就是这个analysis_port需要传递的数据的类型，在本节中是my_transaction。声明了ap后，需要在monitor的build_phase中将其实例化：

代码清单  2-44

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.5/my_monitor.sv

14  virtual function void build_phase(uvm_phase phase);

…

18        ap = new("ap", this);

19     endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





在main_phase中，当收集完一个transaction后，需要将其写入ap中： 代码清单 2-45



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image053.gif)

task my_monitor::main_phase(uvm_phase phase); my_transaction tr;

while(1) begin

tr = new("tr"); collect_one_pkt(tr); ap.write(tr);

end endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





write是uvm_analysis_port的一个内建函数。到此，在my_monitor中需要为transaction通信准备的工作已经全部完成。

UVM的transaction级别通信的数据接收方式也有多种，其中一种就是使用uvm_blocking_get_port。这也是一个参数化的类，其参数是要在其中传递的transaction的类型。在my_model的第6行中，定义了一个端口，并在build_phase中对其进行实例化。在main_phase中，通过port.get任务来得到从i_agt的monitor中发出的transaction。

在my_monitor和my_model中定义并实现了各自的端口之后，通信的功能并没有实现，还需要在my_env中使用fifo将两个端口联系在一起。在my_env中定义一个fifo，并在build_phase中将其实例化：

代码清单  2-46

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.5/my_env.sv

10  uvm_tlm_analysis_fifo #(my_transaction) agt_mdl_fifo;

…

23   agt_mdl_fifo = new("agt_mdl_fifo", this);

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |







fifo的类型是uvm_tlm_analysis_fifo，它本身也是一个参数化的类，其参数是存储在其中的transaction的类型，这里是my_transaction。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)之后，在connect_phase中将fifo分别与my_monitor中的analysis_port和my_model中的blocking_get_port相连： 代码清单 2-47

文件：src/ch2/section2.3/2.3.5/my_env.sv

31  function void my_env::connect_phase(uvm_phase phase);

32    super.connect_phase(phase);

33    i_agt.ap.connect(agt_mdl_fifo.analysis_export);

34    mdl.port.connect(agt_mdl_fifo.blocking_get_export);

35  endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





这里引入了connect_phase。与build_phase及main_phase类似，connect_phase也是UVM内建的一个phase，它在build_phase执行完成之后马上执行。但是与build_phase不同的是，它的执行顺序并不是从树根到树叶，而是从树叶到树根——先执行driver和monitor的connect_phase，再执行agent的connect_phase，最后执行env的connect_phase。

为什么这里需要一个fifo呢？不能直接把my_monitor中的analysis_port和my_model中的blocking_get_port相连吗？由于analysis_port是非阻塞性质的，ap.write函数调用完成后马上返回，不会等待数据被接收。假如当write函数调用时， blocking_get_port正在忙于其他事情，而没有准备好接收新的数据时，此时被write函数写入的my_transaction就需要一个暂存的位置，这就是fifo。



![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)在如上的连接中，用到了i_agt的一个成员变量ap，它的定义与my_monitor中ap的定义完全一样： 代码清单   2-48

文件：src/ch2/section2.3/2.3.5/my_agent.sv

8  uvm_analysis_port #(my_transaction) ap;

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





与my_monitor中的ap不同的是，不需要对my_agent中的ap进行实例化，而只需要在my_agent的connect_phase中将monitor的值赋给它，换句话说，这相当于是一个指向my_monitor的ap的指针：

代码清单  2-49

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





文件：src/ch2/section2.3/2.3.5/my_agent.sv

29  function void my_agent::connect_phase(uvm_phase phase);

30     super.connect_phase(phase);

31     ap = mon.ap;

32  endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





根据前面介绍的connect_phase的执行顺序，my_agent的connect_phase的执行顺序早于my_env的connect_phase的执行顺序，从而可以保证执行到i_agt.ap.connect语句时，i_agt.ap不是一个空指针。



## *2.3.6  加入scoreboard

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)在验证平台中加入了reference model和monitor之后，最后一步是加入scoreboard。my_scoreboard的代码如下： 代码清单   2-50

文件：src/ch2/section2.3/2.3.6/my_scoreboard.sv

3 class my_scoreboard extends uvm_scoreboard;

4   my_transaction expect_queue[$];

5   uvm_blocking_get_port #(my_transaction) exp_port;

6   uvm_blocking_get_port #(my_transaction) act_port;

7   `uvm_component_utils(my_scoreboard) 8

9   extern function new(string name, uvm_component parent = null);

10    extern virtual function void build_phase(uvm_phase phase);

11    extern virtual task main_phase(uvm_phase phase);

12  endclass 13

14  function my_scoreboard::new(string name, uvm_component parent = null);

15    super.new(name, parent);

16  endfunction 17

18  function void my_scoreboard::build_phase(uvm_phase phase);

19    super.build_phase(phase);

20    exp_port = new("exp_port", this);

21    act_port = new("act_port", this);

22  endfunction 23

24  task my_scoreboard::main_phase(uvm_phase phase);

25    my_transaction get_expect, get_actual, tmp_tran;



26    bit result; 27

28    super.main_phase(phase);

29    fork

30      while (1) begin

31        exp_port.get(get_expect);

32        expect_queue.push_back(get_expect);

33      end

34      while (1) begin

35        act_port.get(get_actual);

36        if(expect_queue.size() > 0) begin

37          tmp_tran = expect_queue.pop_front();

38          result = get_actual.my_compare(tmp_tran);

39          if(result) begin

40             `uvm_info("my_scoreboard", "Compare SUCCESSFULLY", UVM_LOW);

41          end

42          else begin

43             `uvm_error("my_scoreboard", "Compare FAILED");

44             $display("the expect pkt is");

45             tmp_tran.my_print();

46             $display("the actual pkt is");

47             get_actual.my_print();

48          end

49        end

50        else begin

51          `uvm_error("my_scoreboard", "Received from DUT, while Expect Que ue is empty");

52          $display("the unexpected pkt is");

53          get_actual.my_print();

54        end

55     end

56   join

57  endtask

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |







my_scoreboard要比较的数据一是来源于reference model，二是来源于o_agt的monitor。前者通过exp_port获取，而后者通过act_port获取。在main_phase中通过fork建立起了两个进程，一个进程处理exp_port的数据，当收到数据后，把数据放入expect_queue中；另外一个进程处理act_port的数据，这是DUT的输出数据，当收集到这些数据后，从expect_queue中弹出之前从exp_port收到的数据，并调用my_transaction的my_compare函数。采用这种比较处理方式的前提是exp_port要比act_port先收到数据。由于DUT处理数据需要延时，而reference model是基于高级语言的处理，一般不需要延时，因此可以保证exp_port的数据在act_port的数据之前到来。

act_port和o_agt的ap的连接方式及exp_port和reference model的ap的连接方式与2.3.5节讲述的i_agt的ap和reference model的端口的连接方式类似，这里不再赘述。

![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif)代码清单2-50中的第38行用到了my_compare函数，这是一个在my_transaction中定义的函数，其原型为： 代码清单 2-51

文件：src/ch2/section2.3/2.3.6/my_scoreboard.sv

54     function bit my_compare(my_transaction tr);

55        bit result; 56

57        if(tr == null)

58           `uvm_fatal("my_transaction", "tr is null!!!!")

59        result = ((dmac == tr.dmac) &&

60                 (smac == tr.smac) &&

61                 (ether_type == tr.ether_type) &&

62                 (crc == tr.crc));

63        if(pload.size() != tr.pload.size())



64           result = 0;

65        else

66           for(int i = 0; i < pload.size(); i++) begin

67              if(pload[i] != tr.pload[i])

68                result = 0;

69           end

70        return result;

71     endfunction

 

|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image042.gif) |





它逐字段比较两个my_transaction，并给出最终的比较结果。

完成my_scoreboard的定义后，也需要在my_env中将其实例化。此时，整棵UVM树变为如图2-8所示的形式。

---

本文非原创