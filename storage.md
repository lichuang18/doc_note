# intel VMD

+ intel官称下一代的存储
+ VMD是Xeon处理器的一个特性能够在不需要额外的硬件适配的情况下通过pcie总线直接控制和管理NVMe SSD

参考intel Virtual RAID on CPU（VROC） 
```
VMD提供的功能：
+ 错误隔离  from  主机os
+ 热插拔和意外移除支持
+ 标准化的LED管理
+ 可启动的RAID
```
## 基本概念
+ 1.volume
  * 
+ 2.PCIE domain
  * https://www.jianshu.com/p/129007a22297
  * linux内核启动主要就是对pcie设备进行枚举和配置。
  * 对于pci总线，有一个叫做pci桥的设备用来将父总线和子总线连接。https://www.freesion.com/article/63911365860/
  * PCI桥是一种特殊的PCIe设备

48 PCIe lanes = 3 Intel VMD Domains
64 PCIe Lanes = 4 Intel VMD Domains

近年来，人工智能、机器学习和云计算等技术的发展，催生了海量的数据。随着数据驱动型技术的兴起，对更强大计算机硬件架构的需求也随之而生。为了创造强大的处理器，人们在单个处理器芯片上集成越来越多的内核，以满足数据密集型应用的处理和性能需求。但存储器带宽和容量未能跟上 CPU 内核数量的增长步伐，使处理器和存储器性能之间出现鸿沟。

而存储器容量和带宽方面的需求得不到满足，正在推动现有存储器技术突破藩篱。由于现有常规 DRAM 设计的局限，使存储器容量的扩展难以突破既定量级，因此需要全新的存储器接口技术。此外，人工智能 (AI) 和大数据的兴起推动了异构计算的潮流，多个不同类型的处理器能够并行处理大量数据。

鉴于这样的趋势，必须针对异构计算和组合基础架构开发下一代互联技术，以实现高效的资源利用。


### 总结
VMD对nvme盘更多的管理，而且并不涉及性能管理

RAID方面的东西很好

### 优秀的文档链接
https://semiconductor.samsung.com/cn/newsroom/tech-blog/expanding-the-limits-of-memory-bandwidth-and-density-samsungs-cxl-dram-memory-expander/


### 问题：
+ PCIE的 domain  
+ root complex
