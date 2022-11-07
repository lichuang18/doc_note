# Operating System: Three Easy Pieces阅读记录
相关链接：
> https://zhuanlan.zhihu.com/p/49286109
> https://pages.cs.wisc.edu/~remzi/OSTEP/

涉及的代码：
> https://github.com/remzi-arpacidusseau/ostep-code/tree/master/cpu-api
> 练手项目https://github.com/remzi-arpacidusseau/ostep-projects


## 关键点
阅读后提取出的比较有用的技巧：
写入磁盘时，延迟写，批处理成更大的组再进行写


## Introduction  
代码：https://github.com/remzi-arpacidusseau/ostep-code/tree/master/intro

os被当作虚拟机：将硬件转换为更通用、易于使用的虚拟形式
os被当作资源管理器：允许许多程序共享CPU、共享内存、共享磁盘等
os被当作标准库：提供API接口，让应用程序可以使用

### 0.1虚拟化----内存

内存是一个字节数组
物理内存是一种共享资源，由操作系统管理分配给各种进程，每个进程看似拥有全部的内存空间访问权限，这就是虚拟化的一种体现
### 0.2concurrency 并发

### 0.3持久化存储
对于CPU和内存，OS会提供一定的虚拟化抽象，为各个进程提供私有的虚拟化
但是对于硬盘，通常希望共享文件中的信息

写过程中的系统崩溃的处理： 日志、copy on write、认真地写入排序  

### 0.4总结
设计和实现操作系统的一个目标是提供高性能;换句话说，我们的目标是最小化操作系统的开销。
性能
--额外的时间  使用更多的指令
--额外的空间  使用更多的内存和外存空间
功能
--保护？
--隔离  实现保护
--可靠
兼顾考虑，均衡设计，学会容忍某些不足

## 1.虚拟化
### 1.1进程 process
程序本身是没有生命的东西，他只是存在磁盘上。
进程就是正在运行的程序。
OS如何提供CPU虚拟化，给出提供了多个CPU的错觉？
> 如分时系统（time sharing）
进程的内存
寄存器

### 1.3

## 2.并发

## 3.持久化

## 4.OS补充
换页

没有虚拟内存支持的计算机上，系统直接将访问数据的地址发送到内存总线上，读写操作使用物理地址，
而在使用虚拟内存的计算机上，系统将访问数据的地址发送到内存管理单元MMU，MMU把虚拟地址映射为物理内存地址。

