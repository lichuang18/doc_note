
# example:
qemu-system-x86_64 -nographic  --enable-kvm \
            -cpu host -smp 1 \
            -m 2048 -object memory-backend-file,id=mem,size=2048M,mem-path=/dev/hugepages,share=on \
            -numa node,memdev=mem -mem-prealloc \
            -hda imgs/v$i.img \
             -net nic -net "user,hostfwd=tcp::$sshport-:22,hostfwd=tcp::$otherport-:8765" \
             -chardev socket,id=char1,path=/var/tmp/vhost.$i \
             -device vhost-user-blk-pci,id=blk0,chardev=char1 &

qemu是纯软件实现的，所有的指令都要经qemu过一手，性能非常低，所以在生产环境中，大多数做法都是配合KVM一起完成虚拟化的工作，
KVM是硬件虚拟化的技术，主要负责比较繁琐的CPU和内存虚拟化，而qemu则负责IO虚拟化，两者合作各自发挥自身的优势，相得益彰


使用qemu-img创建虚拟机镜像
虚拟机镜像用来模拟虚拟机的硬盘，在启动虚拟机之前需要创建镜像文件。

+ qemu-img create -f qcow2 test-vm-1.qcow2 50G
    
-f 选项用于指定镜像的格式，qcow2 格式是 Qemu 最常用的镜像格式，采用来写时复制技术来优化性能。
test-vm-1.qcow2 是镜像文件的名字，10G是镜像文件大小。镜像文件创建完成后，可使用 qemu-system-x86 来启动x86 架构的虚拟机：

使用 qemu-system-x86 来启动 x86 架构的虚拟机
+ qemu-system-x86_64 test-vm-1.qcow2
因为 test-vm-1.qcow2 中并未给虚拟机安装操作系统，所以会提示 “No bootable device”，无可启动设备。
启动 VM 安装操作系统镜像

+ qemu-system-x86_64 -boot d -cdrom ubuntu-20.04.2-desktop-amd64.iso -hda ubuntu.img -m 2048 -enable-kvm

qemu-system-x86_64 -m 2048 -enable-kvm test-vm-1.qcow2 -cdrom ./Centos-Desktop-x86_64-20-1.iso



-m 指定虚拟机内存大小，默认单位是 MB， -enable-kvm 使用 KVM 进行加速，-cdrom 添加 fedora 的安装镜像。
可在弹出的窗口中操作虚拟机，安装操作系统，安装完成后重起虚拟机便会从硬盘 ( test-vm-1.qcow2 ) 启动。之后再启动虚拟机只需要执行：
qemu-system-x86_64 -m 2048 -enable-kvm test-vm-1.qcow2


简历网络
https://wzt.ac.cn/2021/05/28/QEMU-networking/

apt-get install bridge-utils
apt-get install uml-utilities
ifconfig <你的网卡名称(能上网的那张)> down    # 首先关闭宿主机网卡接口
brctl addbr br0                     # 添加名为 br0 的网桥
brctl addif br0 <你的网卡名称>        # 在 br0 中添加一个接口
brctl stp br0 off                   # 如果只有一个网桥，则关闭生成树协议
brctl setfd br0 1                   # 设置 br0 的转发延迟
brctl sethello br0 1                # 设置 br0 的 hello 时间
ifconfig br0 0.0.0.0 promisc up     # 启用 br0 接口
ifconfig <你的网卡名称> 0.0.0.0 promisc up    # 启用网卡接口
dhclient br0                        # 从 dhcp 服务器获得 br0 的 IP 地址
brctl show br0                      # 查看虚拟网桥列表
brctl showstp br0                   # 查看 br0 的各接口信息



tunctl -t tap0 -u root              # 创建一个 tap0 接口，只允许 root 用户访问
brctl addif br0 tap0                # 在虚拟网桥中增加一个 tap0 接口
ifconfig tap0 0.0.0.0 promisc up    # 启用 tap0 接口
brctl showstp br0                   # 显示 br0 的各个接口


-net nic -net tap,ifname=tap0,script=no,downscript=no  启动
