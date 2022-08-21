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


### 总结
VMD对nvme盘更多的管理，而且并不涉及性能管理


### 问题：
+ PCIE的 domain  
+ root complex