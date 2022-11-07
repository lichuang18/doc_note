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

#### 在/sys/class/*/sriov_numvfs文件里修改数值后，发生了啥？
sys的用户属性接口 static DEVICE_ATTR_RW(sriov_numvfs)被触发，
调用宏函数


```
```
在调试驱动，或驱动涉及一些参数的输入输出时，难免需要对驱动里的某些变量或内核参数进行读写，或函数调用。此时sysfs接口就很有用了，它可以使得可以在用户空间直接对驱动的这些变量读写或调用驱动的某些函数。

### 问题：
+ 