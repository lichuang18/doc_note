1.下载内核源码
    git clone git@github.com:torvalds/linux.git
    git tag查看可选的tag版本
    git checkout tag-version 切换对应的内核版本
    当然也可以直接下载对应版本的release源码包，下载时间更快
 1.1 ubuntu下载内核对应本机的内核源码，方便调试，对于某些系统而言，与使用的内核版本保持一致的源码包不一定好找
    apt search source |grep linux  查找apt提供的可下载的源码包
    比如我使用的是Ubuntu 22.04的桌面版，内核版本6.20，检索到apt提供linux-source-6.2.0
    可直接apt install linux-source-6.2.0，对应源码就会下载到/usr/src目录下，注意需要解压2层


2.下载调试包
    git clone git@github.com:lichuang18/debug_kernel.git
    cd debug_kernel
    make mad
    make gdk
    make dr
    make dnvme 甄姬穿透，好名字

    cgdb -q -x gdbinit
    ctrl+a xt退出qemu

2.1 安装qemu
    参考https://www.qemu.org/download/
    wget https://download.qemu.org/qemu-8.0.0-rc1.tar.xz
    tar xvJf qemu-8.0.0-rc1.tar.xz
    cd qemu-8.0.0-rc1
    ./configure --target-list=x86_64-softmmu  不指定x86的话 会把所有的模拟器都配置，make时间过长
    make

1.   ktime_get_real_ns()

        u64 ktime_get_ns(void)
        u64 ktime_get_boottime_ns(void)¶
        u64 ktime_get_real_ns(void)
        u64 ktime_get_clocktai_ns(void)
        u64 ktime_get_raw_ns(void)
            Same as the plain ktime_get functions, 
            but returning a u64 number of nanoseconds in the respective time reference, 
            which may be more convenient for some callers.
        example:
            u64 t1,t2,d0;
            t1 = ktime_get_real_ns();
            {
                // 运行程序段  
            }
            t2 = ktime_get_real_ns();
            d0 = t2 - t1;
            printk("duration: %llu.",d0);


2.   ktime_get_ts64()
3.   ktime_get_real_ts64

        void ktime_get_ts64(struct timespec64*)¶
        void ktime_get_boottime_ts64(struct timespec64*)
        void ktime_get_real_ts64(struct timespec64*)
        void ktime_get_clocktai_ts64(struct timespec64*)
        void ktime_get_raw_ts64(struct timespec64*)

            Same above, but returns the time in a ‘struct timespec64’, split into seconds and nanoseconds. 
            This can avoid an extra division when printing the time, 
            or when passing it into an external interface that expects a ‘timespec’ or ‘timeval’ structure.

            example:
                struct timespec64 s;
                ktime_get_real_ts64(&s)
                int cur = s.tv_sec * 1000000 + s.tv_nsec/1000;



