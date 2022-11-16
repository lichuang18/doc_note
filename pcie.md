# PCIE设备初始化及配置过程

> 注意：https://zhuanlan.zhihu.com/p/349877381 这里给出了pcie的扩展能力的访问方式，是从100h开始，指向第一个扩展能力，然后通过链表能访问到所有的扩展能力，最后一个扩展能力的下一个指针为0。如果一个扩展能力也没有，则100h扩展能力的ID用0xFFFF表示，next依然用0表示。


## PCIE设备初始化（从上电开始）
设备驱动模型的概念，设备和驱动都要有
当设备和驱动能够匹配，执行probe
1.先在内核创建一个PCI总线，用于挂接pci设备和pci驱动，pci_driver_init，调用bus_register用于创建一个总线结构pci_bus_type（这个结构体提供match和probe）
2.match的处理，换取到设备和驱动，比对vendor和device信息，在驱动中，如果设置成PCI_ANY_ID就能支持所有设备
3.匹配成功，调用驱动程序的probe函数



### 设备扫描
+ subsys_initcall(acpi_init); //这个是initcall第4级别的初始化
+ acpi_init() 
+ acpi_scan_init()->acpi_bus_scan()->acpi_bus_check_add->acpi_scan_init_hotplug->acpi_scan_match_handler->acpi_scan_handler_matching
+ 如果上面函数执行返回期待的结果（在最后一层函数内比对数值，pcie的root），那就返回到acpi_bus_scan函数继续执行，
+ 执行acpi_bus_attach->acpi_scan_attach_handler
+ acpi_scan_attach_handler这个函数最终调用handler->attach,acpi_scan_handler是一个结构体，对于pcie root则是对应的函数指针的attach则是acpi_pci_root_add，这个函数中就会scan root bridge，童通过调用pci_acpi_scan_root来实现.
+ pci_acpi_scan_root->acpi_pci_root_create->pci_create_root_bus
+ acpi_pci_root_create函数同时会调用pci_scan_child_bus 
+ 最终调用pci_scan_slot扫描单个pci设备，然后调用pci_scan_bridge 来scan子总线的设备。在pci_scan_bridge 中又会递归调用pci_scan_child_bus。从而完成整个pci 拓扑结构的建立. 
+ pci_scan_child_bus会调用pci_scan_child_bus_extend（我是在linux内核6.0的版本上看到的调用关系）来完成整个pcie拓扑结构的建立->调用pci_scan_slot->pci_scan_single_device->pci_scan_device
+ pci_scan_device会在scan过程中 初始化设备

当然对于不支持ACPI的平台，PCIe设备枚举的入口函数会从pci_host_probe开始。

### 驱动匹配执行
pci_driver结构体
如果我的SRIOV实验中使用的igb的网卡，它的驱动声明就是：
static struct pci_driver igb_driver。。。
igb_probe是该驱动的关键函数，当igb驱动和网卡设备匹配上后会触发该函数的执行

但是对于一个具备SRIOV功能的设备而言，其驱动模块的代码不止要提供probe函数，还要提供sriov_configure的函数指针。
在sriov_configure这里会进行SRIOV的使能及配置（probe值得看一遍，目前我还没仔细看）
igb_pci_sriov_configure
->igb_pci_enable_sriov
->igb_enable_sriov

### 热插拔相关
pci_rescan_bus(b)在pci hotplug中是很有用
这个函数是在pci_driver_init这个函数内会有调用



#### 在/sys/class/*/sriov_numvfs文件里修改数值后，发生了啥？

sys文件系统属性操作方法，可想而知是各种回调。我们在使用cat echo等工具(read()/write()系统调用)进行读写sysfs中相应驱动的属性时，其实就是回调驱动的show()和store()。


open、write等产生系统调用进入内核，
通过sys的用户属性接口，从dev_sysfs_ops或者bus_sysfs_ops，或者driver_sysfs_ops进入，调用对应的*_attr_show或者 *_attr_store，然后如下宏函数
static DEVICE_ATTR_RW(sriov_numvfs)被触发，最终执行sriov_numvfs_store等函数，

sriov_numvfs_store 这个函数能判断设备是否支持SRIOV
在这个函数内，会执行ret = pdev->driver->sriov_configure(pdev, 0);
这会调到驱动，
往num_vfs写0就是disable 然后调用对应驱动里的sriov_configure函数指针sriov_configure，进入到igb_pci_sriov_configure函数执行，再调用igb_pci_enable_sriov
-> igb_enable_sriov
-> pci_enable_sriov 这个函数在drivers/pci/iov.c
执行完毕，函数igb_enable_sriov还会调用igb_vf_configure对vf进行配置
pci_enable_sriov函数会调用sriov_enable

### SRIOV字段描述

> 00h 低16位  扩展能力ID：sriov就是10h
> 00h 中4位   能力版本，这个有pci-sig决定  4位，我所看的pcie4.0手册规定这里必须是1
> 00h 高12位  下一个能力的offset，
> 04h SRIOV能力，migration、ARI等bit位的使能
> 08h SRIOV Control 这里有VF使能、MSE使能等各种使能控制位
> 0ah SRIOV State 这里主要是一个VF migration状态，
> 0ch InitialVFs  常用 
> 0eh TotalVFs
> 10h NumVFs
> 14h First VF Offset
> 16h VF Stride
> 1Ah VF Device ID
> 1Ch Supported Page Sizes
> 20h System Page Size
> 24h VF BAR0
。。。



> VF offset和VF Stride这两个是路由偏移量,这两个值会受到NumVFs的影响

### 其他事项
VF的路由算法
VF1的路由ID：（PF路由ID + First VF Offset） mod 2^16
VF2的路由ID：（PF路由ID + First VF Offset + VF Stride） mod 2^16
VFn的路由ID：（PF路由ID + First VF Offset + (n-1)*VF Stride） mod 2^16
VF NumVFs的路由ID：（PF路由ID + First VF Offset + (NumVFs-1)*VF Stride） mod 2^16
这里以2^16为基，我猜测是由于B：D：F一共16bit的表示有关，生成的VF不能超过这个范围


SRIOV的扩展能力ID是0010h

```
```
在调试驱动，或驱动涉及一些参数的输入输出时，难免需要对驱动里的某些变量或内核参数进行读写，或函数调用。此时sysfs接口就很有用了，它可以使得可以在用户空间直接对驱动的这些变量读写或调用驱动的某些函数。



### 问题：


参考 ：
https://blog.csdn.net/Runner_Linux/article/details/107109011