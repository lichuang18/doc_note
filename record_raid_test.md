# 记录RAID测试的操作

## 硬件raid
+ 删除硬RAID c1是指控制器1，对于一台插了多个RAID卡的机器而言，会存在c0到cn的控制器，v0是指组建的raid的volume编号为0，一般可用all代替
    + storcli64 /c1 /v0 delete force
+ 组建RAID （下面参数是针对SSD介质的硬盘）
    + storcli64 /c1 add vd r5 size=all name=md5 drives=134:0-7 nora pdcache=on wt strip=64

+ 设置参数
    + storcli64 /c0/v0 set iopolicy=cached
    + storcli64 /c0/v0 set pdcache=on
    + storcli64 /c0/v0 set wrcache=wt
    + storcli64 /c0 set personality=jbod
    + storcli64 /c0 set jbod=on
    + storcli64 /c0/eall/s* set jbod

    + storcli64 /c0 set alarm=silence
    + storcli64 /c0/eall/s3 add hotsparedrive dgs=0

+ 9460-8i
    + 1. RAID组建 storcli64 /c0 add vd r5 size=all name=md5 drives=134:0-3 strip=64 nora pdcache=on wt
    + 2. RAID初始化 storcli64 /c0/vall start init full
    + 3. 执行优化脚本，设置相关参数
    + 4. fio 测试，记录RAID结果 
    + 5. 对于RAID5测试，需要测试rebuild和degrade时的性能参数
    + 6. 将组建RAID的硬盘中的一块设置为offline  storcli64 /c0/e134/s0 set offline  之后系统就处于degrade状态
    + 7. fio 测试 记录degrade结果
    + 8. 对于处于dg状态的RAID，此时将一块多余的盘设置为hotsparedrive，RAID开始进入rebuild装填（拔掉，再插上，或者插新盘）
    + 8.1 storcli64 /c0/eall/s3 add hotsparedrive dgs=0
    + 9. 





## 软件RAID  这里默认指的是linux内核中的mdadm组建的RAID
+ 删除RAID
    + sudo mdadm -S /dev/md0  重启后依然存在
 
+ 清除RAID
    + sudo mdadm --zero-superblock /dev/sd[a-i] 消除md信息，重启后不会再次恢复

+ 查看RAID
    + sudo mdadm -D /dev/md0   查看组建的raid0信息

+ 组建RAID   --assume-clean参数对于RAID5有用，因RAID5刚组建完需要rebuild，浪费时间
    + sudo mdadm /dev/md5 -C -v -l5 --assume-clean -n8 /dev/sd[b-i] -c 256
    + sudo mdadm /dev/md0 -C -v -l0 -n8 /dev/sd[b-i] -c 256


+ 软raid调优

    + -c 256  设置chunk size

    + echo $(nproc) > /sys/block/md5/md/group_thread_cnt  #多线程

    + echo 1000000 >/proc/sys/dev/raid/speed_limit_max    #rebuild速度调整，单位是KB/s

    + echo 16348 > /sys/block/md5/md/stripe_cache_size

    + mdadm -f /dev/md5 /dev/sdi