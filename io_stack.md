nvme驱动

下面这个调用流程估计是要过page_cache

抽空继续追追追
 + submit_bh      fs/buffer.c
   + submit_bh_wbc      fs/buffer.c
     + submit_bio      block/blk-core.c
       + submit_bio_noacct    block/blk-core.c
         + submit_bio_noacct_nocheck     block/blk-core.c
           + __submit_bio_noacct_mq  或  __submit_bio_noacct   block/blk-core.c
             + __submit_bio()               block/blk-core.c
               + blk_mq_submit_bio()      block/blk-mq.c

下面这个调用是对block设备的直接IO调用流程
系统调用
 + ksys_write                                                                                          fs/read_write.c  
   + vfs_write                                                                                       fs/read_write.c            
     + new_sync_write                                                                              fs/read_write.c
       + call_write_iter                                                                         include/linux/fs.h
         + file->f_op->write_iter  //                                                          include/linux/fs.h
           + blkdev_write_iter      //   对于block设备的IO流， 调到由file_operations def_blk_fops定义的blkdev_write_iter   block/fops.c
             + __generic_file_write_iter                                                       mm/filemap.c
               + generic_file_direct_write                                                   mm/filemap.c
                 + mapping->a_ops->direct_IO    会调用到def_blk_aops，然后调用direct_IO，def_blk_aops是由bdev_alloc函数赋值  mm/filemap.c
                   + blkdev_direct_IO                                                        block/fops.c
                     + __blkdev_direct_IO_async                                            block/fops.c
                       + submit_bio                                                      block/blk-core.c
                         + submit_bio_noacct    block/blk-core.c
                           + submit_bio_noacct_nocheck                               block/blk-core.c
                             + __submit_bio_noacct_mq  或  __submit_bio_noacct     block/blk-core.c
                               + __submit_bio()                                  block/blk-core.c
                                 + blk_mq_submit_bio()                         block/blk-mq.c
                                   + bio转化为rq    
                                     + blk_mq_try_issue_directly               block/blk-mq.c
                                       + __blk_mq_try_issue_directly         block/blk-mq.c
                                         + __blk_mq_issue_directly         block/blk-mq.c
                                           + q->mq_ops->queue_rq         block/blk-mq.c
    
    SCSI设备走的分支   scsi_mq_ops  这里是RAID卡的RAID  IO路线   
+ scsi_queue_rq()
  + ->scsi_dispatch_cmd()
    + ->.queuecommand = megasas_queue_command  这里调的是scsi_host_template，RAID卡
      + ->megasas_queue_command()
        + ->instance->instancet->build_and_issue_cmd(instance, scmd);   在megasas_init_fw初始化函数中，对于不同类别的RAID卡，对instance赋值也不同，93 94 95系列使用的fusion，dell perc5是MFI,博通老系列的卡也有的被分到了MFI类别
          + ->megasas_build_and_issue_cmd_fusion()  基本上博通的卡，都是走的fusion的路   
            + ->megasas_build_io_fusion()
              + ->megasas_build_ldio_fusion()
                + ->MR_BuildRaidContext()     对于iMR，RAID计算使用主机内存，这里
                  + ->mr_get_phy_params_r56_rmw() 
            + ->megasas_fire_cmd_fusion()  发送命令给固件


 RAID卡的JBOD模式  路线
+ scsi_queue_rq()
  + ->scsi_dispatch_cmd()
    + ->.queuecommand = megasas_queue_command  这里调的是scsi_host_template，RAID卡
      + ->megasas_queue_command()
        + ->instance->instancet->build_and_issue_cmd(instance, scmd);   在megasas_init_fw初始化函数中，对于不同类别的RAID卡，对instance赋值也不同，93 94 95系列使用的fusion，dell perc5是MFI
          + ->megasas_build_and_issue_cmd_fusion()  基本上博通的卡，都是走的fusion的路   
            + ->megasas_build_io_fusion()
              + ->megasas_build_syspd_fusion()
            + ->megasas_fire_cmd_fusion()  发送命令给固件



 
 + megasas_build_and_issue_cmd    xscale(dell和其他)  ppc  skinny  gen2
   + megasas_build_ldio() 
     + MFI_CMD_LD_WRITE赋值


 + 



    
    nvme设备走的分支
    nvme_queue_rq接到的数据就是下面的bd这个结构体
    struct blk_mq_queue_data bd = {
		.rq = rq,
		.last = last,
	};




SCSI设备IO调用栈以及SCSI驱动初始化

IO调用栈
 + __blk_mq_issue_directly    块设备
    ->q->mq_ops->queue_rq()
        ->scsi_queue_rq()         scsi_lib.c的文件
            ->scsi_dispatch_cmd() scsi_lib.c的文件

驱动初始化
megasas_probe_one
    ->scsi_host_alloc 为主机适配器分配数据结构
    ->megasas_io_attach  将驱动关联到scsi mid
        ->scsi_add_host   将scsi_host公开给scsi mid



scsi_dispatch_cmd

这个是内核里识别盘符
    char* disk_name = NULL;
    disk_name = req->disk->disk_name;
    if (strcmp(disk_name, "nvme0n1") != 0){
        return 0;
    }

struct request存放的cmd_flags就是bio的opf，里面包含struct request_queue,
    struct request_queue里的struct gendisk	*disk，包含一个char disk_name[]存放的就是盘符，req->disk->disk_name就是指向的具体盘符，
    但是不能直接再nvme_queue_rq这里直接比，因为nvme驱动初始化会调用这个函数，首次执行这里的盘符位置会得到一个非法指针，系统容易出错。
    建议比较第一个字符是否时‘n’（nvme0n1这是完整的一个盘符）。


dump_stack();  这玩意能打印出来内核调用栈



## kernel RAID相关处理

系统调用
 + ksys_write                                                                                          fs/read_write.c  
   + vfs_write                                                                                       fs/read_write.c            
     + new_sync_write                                                                              fs/read_write.c
       + call_write_iter                                                                         include/linux/fs.h
         + file->f_op->write_iter  //                                                          include/linux/fs.h
           + blkdev_write_iter     //  对于block设备的IO流， 调到由file_operations def_blk_fops定义的blkdev_write_iter block/fops.c
             + __generic_file_write_iter                                                       mm/filemap.c
               + generic_file_direct_write                                                   mm/filemap.c
                 + mapping->a_ops->direct_IO    会调用到def_blk_aops，然后调用direct_IO，def_blk_aops是由bdev_alloc函数赋值  mm/filemap.c
                   + blkdev_direct_IO                                                        block/fops.c
                     + __blkdev_direct_IO_async                                            block/fops.c
                       + submit_bio                                                      block/blk-core.c
                         + submit_bio_noacct    block/blk-core.c
                           + submit_bio_noacct_nocheck                               block/blk-core.c
                             + __submit_bio_noacct_mq  或  __submit_bio_noacct     block/blk-core.c
                               + __submit_bio()                                  block/blk-core.c
                                 + disk->fops->submit_bio(bio)
                                   + md_submit_bio        这里是md设备所以会走到md设备的md_ops，且md设备支持submit_bio的方法   drivers/md/md.c
                                     + md_handle_request
                                       + mddev->pers->make_request
                                         + raid5_make_request   进行IO预处理以及守护进程raid5d完成具体处理
                                           + make_stripe_request
                                             + raid5_compute_sector
                                             + raid5_get_active_stripe
                                             + add_all_stripe_bios
                                             + release_stripe_plug
                                             + md_wakeup_thread(mddev->thread);  //唤醒raid5d

    + raid5d()
      + md_check_recovery  检查是否有故障，如果有调用md_do_sync
      + handle_active_stripes 
        + handle_stripe
          + analyse_stripe
          + handle_stripe_fill




RAID构造流程   待补充

 + raid_ctr
   + md_run
     + err = pers->run(mddev);
       + .run		= raid5_run   调到这个raid5_run函数
         + setup_conf
           + md_register_thread(raid5d, mddev, pers_name)    将raid5d的守护进程注册到内核中


