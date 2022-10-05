---
layout: article
title: uvm_cookbook书籍网页版(用作学习用途)
tags:
- UVM 
key: a20221005

---

uvm cookbook

<!--more-->

## UVM Basics

**UVM** **Basics**

Before we can get into discussing the recipes presented in the UVM Cookbook, we have to make sure that we're all talking about the same ingredients. This chapter introduces the UVM concepts that the reader should know in order to understand the recipes presented herein. This section will be incredibly valuable to new UVM users, but experienced UVM users may be able to just straight to the UVM Testbench chapter.

**Testbench** **Basics**

**UVM** **Testbench** **Basics**

The UVM employs a layered, object-oriented approach to testbench development that allows “separation of      concerns” among the various team members. Each component in a UVM testbench has a specific purpose and a   well-defined interface to the rest of the testbench to enhance productivity and facilitate reuse. When these        components are assembled into a testbench, the result is a modular reusable verification environment that allows the test writer to think at the transaction level, focusing on the functionality that must be verified, while the testbench  architect focuses on how the test interacts with the Design Under Test (DUT).

The Design Under Test (DUT) is connected to a layer of transactors (drivers, monitors, responders). These       transactors communicate with the DUT at the pin level by driving and sampling DUT signals, and with the rest of  the UVM testbench by passing transaction objects. They convert data between pins and transactions, i.e. from/to   signal to/from transaction level. The testbench layer above the transactor layer consists of components that interact exclusively at the transaction level, such as scoreboards, coverage collectors, stimulus generators, etc. All structural elements in a UVM testbench are extended from the uvm_component base class.

The lowest level of a UVM testbench is interface-specific. For each interface, the UVM provides a uvm_agent that includes the driver, monitor, stimulus generator (sequencer) and (optionally) a coverage collector. The Agent thus embodies all of the protocol-specific communication with the DUT. The Agent(s) and other design-specific       components are encapsulated in a uvm_env Environment component which is in turn instantiated and customize by a top-level uvm_test component.

The uvm_sequence_item – sometimes referred to as a transaction – is a uvm_object that contains the data fields    necessary to implement the protocol and communicate with the DUT. The uvm_driver is responsible for converting the sequence_item(s) into “pin wiggles” on the signal-level interface to send and receive data to/from the DUT. The sequence_items are provided by one or more uvm_sequence objects that define stimulus at the transaction level and execute on the agent’s uvm_sequencer component. The sequencer is responsible for executing the sequences,      arbitrating between them and routing sequence items between the driver and the sequence.

![IM 23](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image005.gif)

 

The uvm_monitor is responsible for passively observing the pin-level behavior on the DUT interface, converting it into sequence items and providing those sequence items to analysis components in the agent or elsewhere in the testbench such as coverage collectors or scoreboards. UVM Agents also have a *configuration* *object* that allows the test writer to control aspects ofthe agent as the testbench is assembled and executed.

By providing a uniform interface to the testbench, a UVM Agent isolates the testbench and the UVM Sequence from details of the interface implementation. A sequence that provides data packets, for example, can be reused with different UVM Agents that may implement AHB, PCI or other protocols. A UVM testbench will typically have one agent per DUT interface.

For a given design, the UVM Agents and other components are encapsulated in a uvm_env environment component, which is typically design-specific. Like an agent, an environment typically has a configuration object associated with it that allows the test to control aspects of the environment as well as to control the agents instantiated  in  the environment.  Because  environments  are  themselves  UVM  components,  they  can be assembled into a higher-level environment. As block-level designs are assembled into subsystems and systems, the block-level UVM environment associated with the block may be reused as a component in the subsystem-level environment, which can itself be reused in the system-level testbench.

Once the environment has been defined, the uvm_test will instantiate, configure and build the environment, including customizing key aspects of the overall testbench, including ..*choosing variations of components to be used in the environment ..*choosing UVM Sequences to be run either in the background or as the main portion of the test ..*defining configuration objects for the environment, sub-environment(s) (if any) and agent(s) in the testbench

The UVM test is started from an initial block in the top-level HVL module by calling run_test().

**UVM** **Components**

A UVM testbench is composed of component objects extended from the uvm_component base class. When a uvm_component derived class object is created, it becomes part of the testbench hierarchy which persists for the duration of the simulation. This contrasts with the sequence branch of the UVM class hierarchy which involves transient objects - objects that are created, used and destroyed (i.e. garbage collected) once dereferenced.

![IM 26](https://image-icons.oss-cn-beijing.aliyuncs.com/clip_image009.gif)

The (quasi) static uvm_component hierarchy is used by the UVM reporting infrastructure to print the scope of a component issuing a report message, by the configuration process to determine which components can access a configuration object, and by the UVM factory to apply factory overrides. This component hierarchy is represented by a linked list built up incrementally as each component is created. The hierarchical location of each component is determined by the name and parent arguments passed to its create method at the time of construction.

For instance, in the code fragment below, an apb_agent component is created within the spi_env. Assuming the spi_env is instantiated in the top-level test component with the name "m_env," the hierarchical path name of the agent is the concatenation of the spi_env component's name, "uvm_test_top.m_env", the "dot" (".") operator, and the name passed as the first argument to the "create()" method, resulting in a hierarchical name for the agent of "uvm_test_top.m_env.m_apb_agent". Any references to the agent would need to use this string name.

![image-20221006000805566](https://image-icons.oss-cn-beijing.aliyuncs.com/image-20221006000805566.png)

The uvm_component class inherits from the uvm_report_object class which lies at the heart of the UVM Messaging infrastructure. The reporting process uses the component static hierarchy to add the scope of a component to the report message string.

The uvm_component base class template has a virtual method for each of the UVM Phases and these are to be implemented by the user as required. A phase level virtual method that is not implemented results in the component effectively not participating in that phase.

Also embedded into the uvm_component base class is support for a configuration table to store configuration objects that are relevant for the component's child nodes in the testbench hierarchy. When the uvm_config_db API is used, this static hierarchy is employed as part of the path mechanism to control which components may access a given configuration object.

In order to provide flexibility in configuration and to allow the UVM testbench hierarchy to be built in an intelligent way, uvm_components are registered with the UVM factory. When a UVM component is created during the build phase, the factory is used to construct the component object. The UVM factory enables a component to be swapped for another of a compatible, derived type using a factory override. This is a useful technique for altering the functionality of a testbench without changing the testbench source code directly, which would require recompilation and hinder reuse. There are a number of coding conventions required for the factory to work and these are outlined in the article on the UVM Factory.

The UVM package contains a number of extensions (i.e. derived classes) of the uvm_component base class for common testbench components. Most of these extensions are very "thin", i.e. they are literally just a small extension of the uvm_component class to add a new name space. While these are non-critical and in principle a uvm_component class could be used instead still, they do help with "self-documentation" as they indicate clearly the kind of component that is actually represented, such as a driver or monitor. Additionally, there are analysis utilities available that also use these extraneous base classes as clues to help establish a picture of the testbench hierarchy. On the other hand, some of the pre-built uvm_component extensions are in fact building blocks providing more profound added value, by instantiate concrete sub-components. The following table summarizes the available UVM component classes directly derived from the uvm_component base class:

| Class          | Description                                                  | Contains  sub-components? |
| -------------- | ------------------------------------------------------------ | ------------------------- |
| uvm_driver     | Encapsulates sub-components for sequence communication with the  uvm_sequencer | No                        |
| uvm_sequencer  | Encapsulates sub-components for sequence communication with the  uvm_driver | No                        |
| uvm_subscriber | Encapsulates a  uvm_analysis_export and associated virtual *write* method to implement analysis transaction processing | No                        |
| uvm_env        | Basis for aggregating verification components around a DUT, or other envs in case of vertical (sub-)system integration | Yes                       |
| uvm_test       | Basis for a concrete top level test                          | Yes                       |
| uvm_monitor    | Basis for a concrete monitor transactor                      | Yes                       |
| uvm_scoreboard | Basis for a concrete scoreboard                              | Yes                       |
| uvm_agent      | Basis for concrete agent including a sequencer-driver pair and a monitor | Yes                       |

 

**The** **UVM** **Factory**

**The** **UVM** **Factory**

The purpose of the UVM factory is to enable an object of one type to be substituted with an object of a derived type without changing the testbench structure or even the testbench code. The mechanism used is referred to as an override, by either instance or type. This functionality is very handy for changing sequence behavior or replacing one version of a component by another. Any two components to be swapped must be polymorphically compatible. This includes the requirement that all the same TLM interface handles and TLM objects must be created by the replacement component. Additionally, in order to take advantage of the factory certain coding conventions must be followed.

**Factory** **Coding** **Convention** **1:** **Registration**

A component or object must contain factory registration code comprised of the following elements:

•  A uvm_component_registry wrapper, typedefed to type_id

•  A static function to get the type_id

•  A function to get the type name

For example:

![image-20221006000945305](https://image-icons.oss-cn-beijing.aliyuncs.com/image-20221006000945305.png)

The registration code has a regular pattern and can be safely generated with one of a set of four factory registration

macros:

![image-20221006001011347](https://image-icons.oss-cn-beijing.aliyuncs.com/image-20221006001011347.png)

**Factory** **Coding** **Convention** **2:** **Constructor** **Defaults**

The uvm_component and uvm_object constructors are virtual methods with a prototype template that must be adhered to by users. In order to support deferred construction during the build phase, the factory constructor should contain defaults for the constructor arguments. This allows a factory registered class to be built inside the factory using the defaults and then the class properties can be re-assigned to the arguments passed via the create method of the uvm_component_registry wrapper class. The defaults are different for components and objects:

![image-20221006001053494](https://image-icons.oss-cn-beijing.aliyuncs.com/image-20221006001053494.png)

**Factory** **Coding** **Convention** **3:** **Component** **and** **Object** **Creation**

Testbench components are created during the build phase using the create method of the uvm_component_registry. This first constructs the class, then assigns the handle to the class to its declaration handle in the testbench after the name and parent arguments are assigned correctly. For components the build process is top-down, which allows higher level components and configurations to control what actually gets built.

Object classes are created as required, again using the create method. The following code snippet illustrates how this is done:

![image-20221006001130021](https://image-icons.oss-cn-beijing.aliyuncs.com/image-20221006001130021.png)

---

本文非原创