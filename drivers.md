# 内核驱动 存储相关

`
存储设备驱动通俗的描述就是：
    系统上电，识别到HBA、RAID硬件，加载驱动，调用设备的probe函数
    调probe会执行SCSI总线探测，检查HBA、RAID后面链接的盘的情况，汇报给所谓的SCSI高层
    在SCSI高层，sd驱动和设备匹配，执行probe，所以内核系统中才能lsblk看到盘的信息
    对于盘，是支持热插拔的，所以插拔盘，会触发中断，设备驱动再次匹配，才能lsblk再一次看到盘

`

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
              + ->driver_probe_device()
                  + ->__driver_probe_device()
                    + ->really_probe()
                      + ->call_driver_probe()
                        + ->ret = dev->bus->probe(dev);
                        + 或者->ret = drv->probe(dev);
                          + 最终调到驱动的probe函数 

+ 2. 设备向总线注册，
起始点暂未清楚，下面是以VMD内核代码为流程展开的调用追踪
  + vmd_probe()
    + -> vmd_enable_domain()
      + -> pci_create_root_bus()
      + -> pci_bus_add_devices()
        + -> pci_bus_add_device()
          + -> device_attach()
            + -> __device_attach()
              + -> bus_for_each_drv(dev->bus, NULL, &data,__device_attach_driver);   调用到 __device_attach_driver()
                + -> driver_match_device()
                + -> driver_probe_device()
                  + -> __driver_probe_device()
                    + -> really_probe()
                      + -> call_driver_probe()
                        + ->ret = dev->bus->probe(dev);
                          + 或者->ret = drv->probe(dev);
                            + 最终调到驱动的probe函数 

（待补充）


+ 3.1 驱动实例  megaraid_sas.ko模块
  +   该驱动代码位于linux/drivers/scsi/megaraid/megaraid_sas_base.c
  +   直接从probe函数开始介绍
  +   megasas_probe_one()
      + ->scsi_host_alloc() 分配主机适配器结构体   
      + ->megasas_set_adapter_type() 设置适配器类型，这里主要有5个大类，分别是MFI_SERIES、AERO_SERIES、VENTURA_SERIES、THUNDERBOLT_SERIES、INVADER_SERIES
      + ->megasas_init_fw() 这个函数里有申请中断的操作，后续的插拔盘会触发中断，中断处理里应该包含有设备和驱动的再次绑定
        + ->megasas_setup_irqs_msix()
          + ->request_irq()   申请中断号
      + ->megasas_io_attach()  注册host
        + ->scsi_add_host() 
            + ->scsi_add_host_with_dma()
        + ->scsi_scan_host()  SCSI总线探测


同步扫描
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

+ 3.2 驱动实例  sd驱动实例
  +   该驱动代码位于linux/drivers/scsi/sd.c
  +   直接从probe函数开始介绍
  +   sd_probe()
      + ->创建sd设备盘符  

+ 4. scsi命令的执行
    + scsi_execute_req()
      + ->scsi_execute()
        + ->__scsi_execute()
          + ->blk_execute_rq()