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
+ 3.7款主流的企业级SSD产品
  * Micron 7400 Pro
  * Samsung PM9A3
  * Memblaze PBlaze6 6920
  * Solidigm P5520
  * Union Memory UH810a
  * KIOXIA CD6
  * DapuStor Roealsen5 R5100
+ 4.pcie5.0主控性能（2022.12）
  * seq读14GB/s
  * seq写11GB/s
  * 随机读3M IOPS
  * 随机写2.5M IOPS
+ 5.缓存穿透
  >待处理数据既不在缓存，也不在盘，缓存没有了意义
+ 6.缓存雪崩
  >缓存会设置过期时间，当缓存中的大量热点数据采用了相同的失效时间，导致某一时刻缓存同时失效。
+ 7.缓存击穿
  >同缓存雪崩类似，只是失效的是某单个热点数据。
+ 8.布隆过滤器
  >在超大数据集中快速告诉你查找的数据存在不存在，返回不存在的话结果一定不存在，但是返回存在的话，结果不是一定存在，

+ 9.去中心化的安全保护，未来的目标模式
与以往将信息全部寄存在某处不同，它的上链依赖于各个节点，各方节点都会对数据进行记录，通过链式及分布式的结构，来实现数据上链后不得随意篡改，保证数据真实性的目标。但是区块链技术却无法保证数据在上链前的真实性及完整性，它需要与其他技术相配合，才能真正实现数据产生、交换、传输、存储全过程的真实性目标。

+ 10.高性能嵌入式系统定义了AMBA(Advanced Micro-Controller Bus Architecture，高级微控制器总线架构)On-Chip Bus片上总线规范，这个协议定义的主要是一系列系统总线。比如，AHB（Advanced High-Performance Bus，高级高性能总线）、APB（Advanced Peripheral Bus，高级外设总线）、AXI（Advanced Extensible Interface，高级可扩展接口总线）、ACE（AXI Coherency Extensions，AXI一致扩展总线）、ASB（Advanced System Bus，高级系统总线）等。接下来重点介绍一下AHB、APB、AXI总线。
>AHB（用于高性能，高数据吞吐部件，如CPU、DMA、DSP之间的连接）
>APB（为系统的低速外部设备提供低功耗的简易互连）
>AXI（主要面向高性能地址映射通信的需求）
AMBA：1996最早只有APB ASB
AMBA2: 1999加入了AHB
AMBA3: 2003加入了AXI  ATB
AMBA4: 2010引入了ACE(AXI一致性扩展总线)
AMBA5: 2013提供CHI（相干集线器接口）

## 测试工具
+ sysbench 这是一个包括CPU 内存 磁盘 IO等方面的开源模块化性能测试工具。


## 思考
+ 面对一般问题，根据不同的数据规模，转换为计算机问题之后就落地到实际的数据结构：数组、链表、树等



## 问题
+ SSD读写时存在满盘掉速的问题
  >垃圾回收不及时
  >ssd剩余空间小时，cache容量也在不断减小。

## 现状及挑战
+ 随着专业相机和高端摄影设备的更新迭代，高分辨率的设备出现和普及，用户对于存储卡的性能要求也越来越高，
 >指标1： 读写速度
 >指标2： 读写稳定性




48 PCIe lanes = 3 Intel VMD Domains
64 PCIe Lanes = 4 Intel VMD Domains

近年来，人工智能、机器学习和云计算等技术的发展，催生了海量的数据。随着数据驱动型技术的兴起，对更强大计算机硬件架构的需求也随之而生。为了创造强大的处理器，人们在单个处理器芯片上集成越来越多的内核，以满足数据密集型应用的处理和性能需求。但存储器带宽和容量未能跟上 CPU 内核数量的增长步伐，使处理器和存储器性能之间出现鸿沟。

而存储器容量和带宽方面的需求得不到满足，正在推动现有存储器技术突破藩篱。由于现有常规 DRAM 设计的局限，使存储器容量的扩展难以突破既定量级，因此需要全新的存储器接口技术。此外，人工智能 (AI) 和大数据的兴起推动了异构计算的潮流，多个不同类型的处理器能够并行处理大量数据。

鉴于这样的趋势，必须针对异构计算和组合基础架构开发下一代互联技术，以实现高效的资源利用。


+ 块、文件、对象存储
  >块存储速度快,但对存储的数据没有进行组织管理，用户读写数据不方便（块设备位置offset+数据length）
  >文件系统是建立在块设备上，文件系统帮助组织管理数据，数据存储对用户更加友好（以文件名来读写数据）且能够让用户共享读写，但是由于文件系统的复杂性导致了存储性能较块设备慢
  >对象存储是一种折中，保证一定的存储性能，又能支持多用户的共享读写

 
### 总结
VMD对nvme盘更多的管理，而且并不涉及性能管理

RAID方面的东西很好

### 优秀的文档链接
https://semiconductor.samsung.com/cn/newsroom/tech-blog/expanding-the-limits-of-memory-bandwidth-and-density-samsungs-cxl-dram-memory-expander/
https://zhuanlan.zhihu.com/p/127170201 

### 问题：
+ PCIE的 domain  
+ root complex
