# 内核驱动

## 驱动总线设备模型
+ 0. 总线什么时候确定


+ 1. 驱动向总线注册，在驱动代码里，
实例1：pci驱动注册    pci_bus_type
megaraid_sas驱动加载时调用pci驱动注册函数
megasas_init()
  + ->pci_register_driver()
    + +->__pci_register_driver()    
      + ->int driver_register()
        + ->driver_find()
        + ->bus_add_driver()
          + ->driver_attach()
            + ->bus_for_each_dev( , , , __driver_attach);
              + ->__driver_attach()
                + ->driver_match_device()
                  + ->drv->bus->match(dev, drv)
                    + ->pci_bus_match()   会找到pci_bus_type中的match方法调用，就是pci_bus_match
                      + ->pci_match_device() pci驱动和设备进行匹配
                        + ->pci_match_one_device()   比对vendor等相关信息，一致即匹配成功
              + ->driver_probe_device()
                + ->__driver_probe_device()
                  + ->really_probe()
                    + ->call_driver_probe()
                      + ->ret = dev->bus->probe(dev);
                      + 或者->ret = drv->probe(dev);
                        + 最终调到驱动的probe函数

实例2：scsi驱动注册   scsi_bus_type
sd驱动加载时调用scsi驱动注册函数
init_sd()
  + ->scsi_register_driver()
    + ->int driver_register()
      + ->driver_find()
      + ->bus_add_driver()
        + ->driver_attach()
          + ->bus_for_each_dev( , , , __driver_attach);
            + ->__driver_attach()
              + ->driver_match_device()
                + ->drv->bus->match(dev, drv)
                  + ->scsi_bus_match()   会找到scsi_bus_type中的match方法调用，就是scsi_bus_match
                  + 

+ 2. 设备向总线注册，

（待补充）








+ 3. 驱动实例  megaraid_sas.ko模块
  +   该驱动代码位于linux/drivers/scsi/megaraid/megaraid_sas_base.c
  +   直接从probe函数开始介绍
  +   megasas_probe_one()
      + ->scsi_host_alloc() 分配主机适配器结构体   
        + ->megasas_io_attach()  注册host
          + ->scsi_add_host() 
              + ->scsi_add_host_with_dma()
        + ->scsi_scan_host()  SCSI总线探测
    + scsi主机适配器的下一级总线也要扫描，scsi总线扫描
        + ->scsi_scan_host()   探测函数
          + ->do_scsi_scan_host() 同步扫描，异步扫描是另外的分支
            + ->scsi_scan_host_selected() 扫描选定的HBA 传入参数通道号、ID号、LUN号都是通配符SCAN_WILD_CARD，表示需要尝试所有的可能值
              + ->scsi_scan_channel()
                + ->__scsi_scan_target()
                  + ->scsi_probe_and_add_lun()
                    + ->scsi_probe_lun()  发送scsi_inquiry命令探测LU
                    + ->scsi_add_lun()   如果相应，说明LU存在，将他添加到系统中
                  + ->scsi_sequential_lun_scan()
                    + ->scsi_probe_and_add_lun()
                  + ->scsi_report_lun_scan()
                    + ->scsi_probe_and_add_lun()

异步扫描  （待补充）   对比同步扫描差异
+ ->scsi_scan_host()   探测函数
  + ->scsi_prep_async_scan()  为异步扫描做准备
  + ->async_schedule(do_scan_async, data);
    + ->do_scan_async() 执行异步扫描
      + ->do_scsi_scan_host() 
        + ->do_scsi_scan_host()    异步扫描也走到了这
            + ->scsi_scan_host_selected()
              + ->scsi_scan_channel()
                + ->__scsi_scan_target()
                  + ->scsi_alloc_target() 分配一个目标节点结构
                  + ->scsi_probe_and_add_lun()   分支1
                    + ->scsi_probe_lun()  发送scsi_inquiry命令探测LU
                    + ->scsi_add_lun()   如果相应，说明LU存在，将他添加到系统中
                  + ->scsi_sequential_lun_scan()   分支2，调用分支1
                    + ->scsi_probe_and_add_lun()
                  + ->scsi_report_lun_scan()  分支3，调用分支1
                    + ->scsi_probe_and_add_lun()
        + ->scsi_finish_async_scan() 结束异步扫描
