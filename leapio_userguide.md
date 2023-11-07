
<br /><br /><br /><br /><br />
## <p align=right>Leap-IO for XXX服务器
#### <p align=right>Tri-mode RAID-HBA卡用户手册 


<div STYLE="page-break-after: always;"></div>

<font size=2 face="黑体"> Copyright © 2023 北京纵存科技有限公司及其许可者 版权所有，保留一切权利。  

&emsp;&emsp;<font size=3 face="宋体">未经本公司书面许可，任何单位和个人补得擅自摘抄、复制本书内容的部分或者全部，并不得以任何形式传播。<br />
&emsp;&emsp;**LeapIO**为北京纵存科技有限公司的商标。对于本手册中出现的其它公司的商标、产品标识及商品名称，由各自权利人拥有。<br />
&emsp;&emsp;由于产品版本升级或其他原因，本手册内容有可能变更。纵存科技保留在没有任何通知或者提示的情况下对本手册的内容进行修改的权利。本手册仅作为使用指导，纵存科技尽全力在本手册中提供准确的信息，但是纵存科技并不确保手册内容完全没有错误，本手册中的所有陈述、信息和建议也不构成任何明示或暗示的担保。

<div STYLE="page-break-after: always;"></div>

<font size=5 face="黑体">**目录**</font> <br />

<font size=3 face="宋体">1.RAID卡基本概念及功能介绍<br />
&emsp;&emsp;1.1 RAID卡的基本概念<br />
&emsp;&emsp;&emsp;1.1.1 RAID<br />
&emsp;&emsp;&emsp;1.1.2 硬盘组<br />
&emsp;&emsp;&emsp;1.1.3 虚拟硬盘<br />
&emsp;&emsp;1.2 RAID卡的常用功能<br />
&emsp;&emsp;&emsp;1.2.1   容错<br />
&emsp;&emsp;&emsp;1.2.2   硬盘条带化<br />
&emsp;&emsp;&emsp;1.2.3   校验<br />
&emsp;&emsp;&emsp;1.2.4   数据重建<br />
&emsp;&emsp;&emsp;1.2.5   硬盘镜像<br />
&emsp;&emsp;&emsp;1.2.6   一致性校验<br />
&emsp;&emsp;&emsp;1.2.7   初始化<br />
&emsp;&emsp;&emsp;1.2.8   巡读<br />
&emsp;&emsp;&emsp;1.2.9   外部配置<br />
&emsp;&emsp;&emsp;1.2.10  热备盘<br />
&emsp;&emsp;&emsp;1.2.11  紧急热备<br />
&emsp;&emsp;&emsp;1.2.12  硬盘直通<br />
&emsp;&emsp;&emsp;1.2.13  数据回拷<br />
&emsp;&emsp;&emsp;1.2.14  硬盘状态<br />
&emsp;&emsp;&emsp;1.2.15  读策略<br />
&emsp;&emsp;&emsp;1.2.16  写策略<br />
&emsp;&emsp;&emsp;1.2.17  掉电保护<br />
&emsp;&emsp;&emsp;1.2.18  硬盘节能<br />
2.RAID-HBA卡类型及芯片技术规格<br />
&emsp;&emsp;2.1 RAID-HBA卡类型<br />
&emsp;&emsp;2.2 RAID-HBA卡芯片技术规格<br />
3.芯片型号/卡型号？ <br />
&emsp;&emsp;3.1 legacy模式 ？<br />
&emsp;&emsp;&emsp;3.1.1 RAID级别及参数介绍<br />
&emsp;&emsp;&emsp;3.1.2 进入RAID卡管理界面<br />
&emsp;&emsp;&emsp;3.1.3 创建RAID组列<br />
&emsp;&emsp;&emsp;3.1.4 删除RAID组列<br />
&emsp;&emsp;&emsp;3.1.5 硬盘JBOD模式设置<br />
&emsp;&emsp;&emsp;3.1.6 硬盘Unconfigured Good状态设置<br />
&emsp;&emsp;&emsp;3.1.7 设置启动盘<br />
&emsp;&emsp;&emsp;3.1.8 硬盘定位<br />
&emsp;&emsp;&emsp;3.1.9 控制器信息查看<br />
&emsp;&emsp;3.2 UEFI模式？<br />
&emsp;&emsp;&emsp;3.1.x 和上面legacy差不多？<br/>
4.HBA芯片型号/卡型号？ <br />

5.RAID芯片型号/卡型号？<br /> 

6.FAQ<br />
&emsp;&emsp;6.x 常见问题及解决方法<br />

7.售后维护等<br />

<div STYLE="page-break-after: always;"></div>

## 1. RAID卡基本概念及功能介绍
### 1.1 RAID卡基本概念
#### 1.1.1 RAID
&emsp;&emsp;RAID(Redundant Arrays of Independent Disks)，意为“独立硬盘构成的具有冗余能力的阵列”，是由多个独立硬盘、按照一定策略组建成的新的存储介质或阵列。
&emsp;&emsp;<br />在存储技术还未普及时，大容量硬盘比较昂贵，为了以较低的成本获得与昂贵大容量硬盘相当的容量、性能、可靠性，将多个容量较小、相对廉价的硬盘进行有机组合的 RAID 技术应运而生。随着整个社会信息化水平的不断提高，各行各业有对数据处理、存储能力提出了越来越高的要求，进一步推动了 RAID 技术的发展。
&emsp;&emsp;<br />如今，通过RAID技术，我们可以获得更大的存储空间、更快的传输速度和更高的安全性。
#### 1.1.2 硬盘组
&emsp;&emsp;硬盘组（ Drive Group，简称“DG”）即一组物理硬盘组成的集合。
#### 1.1.3 虚拟硬盘
&emsp;&emsp;虚拟硬盘（ Virtual Drive，简称“VD”）也可称为 RAID 组列，是在硬盘组的基础上划分出的一组连续的存储单元，对于操作系统而言，相当于一个物理硬盘。该存储单元因使用的 RAID 技术不同，而具有不同的硬盘利用率、容错能力、读写性能和冗余度。
&emsp;&emsp;<br />一个虚拟硬盘可以由一个硬盘组构成，也可以由一个硬盘组的一部分构成。
### 1.2 RAID卡常用功能
#### 1.2.1 容错
&emsp;&emsp;容错（ Fault Tolerance）是指当硬盘组中一个或多个硬盘出现故障时，仍然可以正常进行数据处理、保障硬盘组数据的完整性不被破坏的能力。
#### 1.2.2 硬盘条带化
&emsp;&emsp;硬盘条带化（ Disk Striping）是指将一块连续的数据分成很多规定大小的数据块，并把它们分别存储到不同硬盘上的方法，这些硬盘会根据数据块的大小将存储空间划分出一个个大小相同的空间（条带），用于存放数据。由于数据存放在不同的硬盘上，那么在顺序访问这些数据的时候， 就可以同时从多个不同的硬盘按照并行的方式获取数据， 大大提高了 I/O 性能。
+ 硬盘条带大小：指每个硬盘划分出的，用于存放数据的单位空间的大小；
+ 条带宽度：指硬盘组中硬盘的数量；
+ 硬盘组条带大小：硬盘条带大小与条带宽度的乘积。
#### 1.2.3 校验
&emsp;&emsp;校验（ Parity）指的是从两个或多个父数据集生成一组冗余数据。当发生硬盘故障时，冗余数据可用于重建父数据集。奇偶校验数据不是完全复制父数据集。奇偶校验数据的计算、生成会减慢数据写入过程。
#### 1.2.4 数据重建
&emsp;&emsp;数据重建（ Data Rebuild）即当 RAID 组列中的硬盘发生故障或者一致性校验发现数据错误时，可以通过重新创建硬盘中之前的数据或者对数据进行修正的方式，对硬盘进行重建。RAID 控制器使用存储在 RAID 组列中其他硬盘上的数据（或校验位）重建数据。只有具有数据冗余能力的 RAID 组列才能执行重建，其中包括 RAID 1、 5、 6、 10、 50 和 60。
#### 1.2.5 硬盘镜像
&emsp;&emsp;硬盘镜像（ Disk Mirroring）指的是将数据写入一个硬盘的同时，控制器会把数据复制到另一块硬盘。硬盘镜像的主要优点是提供了 100％的数据冗余。由于数据写入到了两块盘中，即使一块盘发生故障，数据也不会丢失，另一块盘仍然可以供系统使用，并且在换上新的硬盘之后控制器还可以进行数据重建， 恢复冗余能力。
#### 1.2.6 一致性校验
&emsp;&emsp;一致性校验（ Consistency Check）是验证具有冗余能力的 RAID 组列(RAID1/5/6/10/50/60)中条带上的数据是否一致，如果不一致会尝试自动对错误进行修复或标记的操作。该过程中 RAID 控制器会对成员盘中的数据进行检查和计算，生成新的奇偶校验数据与校验盘中的数据进行比对，若出现不一致，则会使用新的奇偶校验数据修正错误数据。对于 RAID 1 组列，此操作将验证每个条带的镜像数据是否一致。建议至少一个月进行一次一致性校验。
#### 1.2.7 初始化
&emsp;&emsp;初始化（ Initialization）是为了保证硬盘数据的一致性而进行的操作。初始化会将零写入 RAID 组列中，以将 RAID 组列置于“就绪”状态的过程。具有容错能力的 RAID 组列在初始化时还会生成相应的奇偶校验。初始化会擦除驱动器上的所有先前数据。虽然 RAID 组列无需初始化也可以工作，但由于无法保证硬盘中数据的正确性、尚未生成奇偶校验字段，它们可能无法通过一致性检查。所以 RAID 组列必须经过初始化才可以使用。初始化可分为前台初始化和后台初始化。
##### 1.2.7.1 前台初始化
&emsp;&emsp;前台初始化分为两种：快速初始化(Fast)和全部初始化(Full)。
+ 快速初始化：控制器只需将硬盘组的前 100M（根据控制器不同大小可能会有差异）空间写零，就结束了初始化过程，用户可以快速使用硬盘组。
+ 全部初始化：控制器需要将硬盘组的全部空间写零，才会结束初始化过程。该初始化模式耗费时间较长，用户需要等待初始化结束可以使用硬盘组。
##### 1.2.7.2 后台初始化
&emsp;&emsp;后台初始化会在创建 RAID 组列后对成员盘进行检查，排查出硬盘错误、保证数据正确性。后台初始化会在 RAID 组列创建完成 5 分钟（不同固件版本可能存在差异） 后自动进行。后台初始化的目的是使具有冗余能力的 RAID 组列中各成员盘的数据满足 RAID 组列的级别要求：
+ 对于 RAID1、 10，后台初始化过程中如果发现主从成员盘的数据不一致，就会将主成员盘的数据复制到次成员盘中，覆盖不一致的数据；
+ 对于 RAID5、 6、 50、 60，后台初始化会对成员盘中条带的数据进行奇偶校验，如果发现新的校验位和组列中现存的校验数据不一致，就会使用新的校验数据替换掉旧数据。

&emsp;&emsp;后台初始化和一致性检验的功能类似，两者区别在于后台初始化是创建 RAID 组列后强制进行的操作，而一致性校验不是。后台初始化时要求RAID5、 6 级别的 RAID 组列满足最小成员盘数量，如果不能满足最小成员盘数量要求，后台初始化不会进行，需要手动进行。
+ RAID1 级别的 RAID 组列要求至少有 2 块成员盘，才可进行后台初始化；
+ RAID5 级别的 RAID 组列要求至少有 5 块成员盘，才可进行后台初始化；
+ RAID6 级别的 RAID 组列要求至少有 7 块成员盘，才可进行后台初始化；
#### 1.2.8 巡读
&emsp;&emsp;巡读（ Patrol Read）功能指的是通过对控制器下的硬盘进行巡回检查，以发现可能导致硬盘故障的潜在错误，然后采取措施纠正错误。巡读的目的是在数据损坏之前检测出硬盘故障，从而保护硬盘上数据的完整性。
&emsp;&emsp;发现错误后采取何种纠正措施取决于 RAID 组列的配置和出现的错误类型。巡读操作仅在控制器闲置了一段规定的时间后，并且没有其他后台任务执行时才会开始。
#### 1.2.9 外部配置
&emsp;&emsp;当插入服务器的硬盘带有RAID配置信息时(之前在其他 RAID 控制器上被配置为某一RAID组列的成员盘)， MegaRAID Storage Manager软件将会把 硬盘状态识别为外部配置(Foreign Configuration)，以提醒用户该硬盘带有之前的RAID配置信息，无法直接使用。这种情况下，用户可以将硬盘中带有的配置重新导入(Import)RAID控制器，实现RAID组列的迁移，或者可以清除配置，以便创建新的配置。
#### 1.2.10 热备盘
&emsp;&emsp;热备盘（ Hot Spares）是控制器系统下额外保留的、未使用的硬盘。它通常处于待机模式，如果RAID 组列中的成员盘发生故障，热备盘无需系统重启或用户干预即可自动更换故障盘。MegaRAID SAS RAID 控制器可以使用热备用驱动器实现故障驱动器的自动重建，从而提供高度的容错能力和零停机时间。
&emsp;&emsp;热备盘只对具有冗余能力的 RAID 组列生效： RAID1、 5、 6、 10、 50、 60。
&emsp;&emsp;RAID 控制器支持如下两种热备盘：
+ 局部热备盘（专用热备盘）：局部热备盘只能为一个或多个特定的 RAID 组列服务，如果其他组列出现故障盘，局部热备盘不会进行自动更换；
+ 全局热备盘：全局热备盘可以为 RAID 控制器下的所有 RAID 组列服务，当任何一个 RAID 组列出现成员盘故障时，全局热备盘都会自动顶替故障盘。
  
注意：设置热备盘时，其容量要等于或大于故障盘。
#### 1.2.11 紧急热备
&emsp;&emsp;与需要单独设置热备盘不同，紧急热备(Emergency Spare)策略允许RAID控制器在没有热备盘的情况下，当RAID组列出现故障盘时，使用空闲的Unconfigured Good状态硬盘作为故障盘的替代盘，自动对RAID组列进行补充，从而保证控制器下RAID组列的正常使用。
&emsp;&emsp;紧急热备要求用于备份的硬盘容量等于或大于故障盘，且只对具有冗余能力的RAID组列(RAID1、5、6、10、50、60)生效。
#### 1.2.12 硬盘直通
&emsp;&emsp;硬盘直通（ JBOD）又称指令透传，是指插在 RAID 卡上的物理硬盘在不配置虚拟硬盘的情况下，用户指令可以直接透传到硬盘、直接被操作系统识别、管理，而不经过 RAID 控制器处理。因为不受 RAID 控制器控制，所以直通硬盘无法组建 RAID 组列，但硬盘直通可以保证数据传输的稳定性，进而提高数据的安全性和传输性能。
#### 1.2.13 数据回拷
&emsp;&emsp;对于由冗余功能的 RAID 组列(RAID 1、5、6、10、50、60)，出现故障盘后，控制器会自动将数据重建至热备盘。当把故障盘换下、插上新的健康硬盘后，控制器会把重建好的热备盘数据回拷(Copyback)至健康硬盘中。回拷完成后，热备盘将自动恢复为热备状态。
#### 1.2.14 硬盘状态
&emsp;&emsp;硬盘状态反映了硬盘当前的工作模式、硬盘功能、正在进行何种操作等信息。<br/>
&emsp;&emsp;表1-1描述了HBA卡下硬盘的状态：</font>

<font size=2> 表1-1 HBA卡硬盘状态表</font> 
<font size=3 face="宋体">
| 状态| 含义 |
|--------|--------|
| Online| 控制器正在使用的硬盘，是现有 RAID 组列中的成员。|
| Ready|准备状态，该状态的硬盘可以用作 RAID 组列成员盘，也可以分配为热备盘。该状态的硬盘无法直接被操作系统发现、使用。|
|Available|该硬盘可能尚未准备好，因此不适合作为 RAID 成员盘或热备盘使用。|
|Failed|之前状态为“Online”或“Hot Spare”的硬盘，被固件检查出存在不可恢复的错误后，将变为 Failed 状态。|
|Missing|该硬盘被移除或处于未响应状态。|
|Standby|该设备不是硬盘设备。|
|Out of Sync|该硬盘为 IR RAID 组列的成员盘，但是和该 RAID 组列其他成员盘的数据不同步。|
|Degraded|该硬盘是 RAID 组列的一部分，且目前处于降级状态。|
|Rebuilding|硬盘正在进行数据重建。|
|Optimal|该硬盘为 RAID 组列成员盘，且状态良好。|</font> 

<font size=2> 表1-2 RAID卡硬盘状态表</font> 
<font size=3 face="宋体">
| 状态| 含义 |
|--------|--------|
| Online| 控制器正在使用的硬盘，是现有 RAID 组列中的成员。|
|Unconfigured Good|未被配置的良好状态，该状态的硬盘可以用作 RAID 组列成员盘，也可以分配为热备盘。该状态的硬盘无法直接被操作系统发现、使用。|
| Ready|准备状态，该状态的硬盘可以用作 RAID 组列成员盘，也可以分配为热备盘。该状态的硬盘无法直接被操作系统发现、使用。|
|Hot Spare|热备盘。该硬盘已经上电，并且准备在 RAID 组列中出现故障盘时，随时顶替故障盘。|
|Failed|之前状态为“Online”或“Hot Spare”的硬盘，被固件检查出存在不可恢复的错误后，将变为 Failed 状态。|
|Rebuild|硬盘正在进行数据重建，为了恢复 RAID 组列的冗余能力。|
|Unconfigured Bad|硬盘处在 Unconfigured Good 状态或未被初始化状态时，被固件检查出存在不可修复的错误，将会变为 Unconfigured Bad 状态。|
|Missing|丢失。RAID 组列中的硬盘被拔出后将处于丢失状态。|
|Offline|离线。该盘之前为 RAID 组列成员盘，现在处于离线状态，不可使用。|
|Shield State|临界状态。此时硬盘正在进行诊断操作。|
|Copyback|当硬盘正在替换 RAID 组列中 Failed 状态的硬盘时，会处于该状态。|
|JBOD|直通硬盘。不受RAID控制器的控制, 可直接被上层操作系统发现、使用。|

#### 1.2.15 读策略
&emsp;&emsp;RAID 组列的读策略有两种：
+ No Read Ahead：关闭预读取功能；
+ Read Ahead：开启预读取功能。在读取所请求的数据外，控制器会将请求数据所在地址之后的部分数据也读入到 Cache 中，以期望这些数据随后被系统使用时可以直接在 Cache 中命中，从而增快响应速度、提高读性能。
#### 1.2.16 写策略
&emsp;&emsp;RAID组列的写策略(Write Policy)有三种：
+ Always Write Back：一直使用写回策略。无论是否存在超级电容， RAID 组列都将使用写回策略进行数据写入。写回策略是指，控制器在将请求写入的数据写入 Cache 后，就向上层软件反馈写操作完成，可 RAID 控制器不会马上将数据写至硬盘，而是等到 Cache 写满后，才将这些数据一起写入指硬盘。如果控制器没有搭配超级电容时出现异常掉电，可能会使写入 Cache 中的数据还未写入至硬盘，就因掉电而丢失；
+ Write Through：控制器在将请求写入的数据写入至硬盘后，才向上层软件反馈写操作完成；
+ Write Back：条件使用写回策略。与 Always Write Back 不同， Write Back 策略在控制器使用超级电容时，会一直开启写回功能，而当检测到没有超级电容、电容正在充放电或电容损坏时，会自动切换至 Write Through 策略，从而保证数据不被丢失。

#### 1.2.17 掉电保护
&emsp;&emsp;当控制器搭配超级电容时，如果遇到异常掉电的情形，超级电容模块会利用电容中存储的电量，将Cache 中的数据写入至模块中的 Nand Flash 中进行保存，等到控制器下次上电时，再将存储在 NandFlash 中的数据写回 Cache，进而保证数据不会因掉电而丢失。

#### 1.2.18 硬盘节能
&emsp;&emsp;RAID 控制器具有硬盘节能的功能。当该功能开启时，控制器下 Unconfigured Good 状态的硬盘、热备盘都将处于节能状态。控制器允许节能状态下的硬盘暂时停转，当出现需要使用这些硬盘的操作时（如 RAID 组建、 RAID 组列出现故障盘等），将硬盘从节能状态唤醒。
&emsp;&emsp;硬盘节能状态适用于所有旋转式 SAS 和 SATA 硬盘。

## 2. RAID-HBA 卡类型及芯片技术规格
### 2.1 RAID-HBA卡类型
&emsp;&emsp;RAID-HBA 卡的类型及基本信息如表 2-1 所示。</font> <br/>
<font size=2> 表2-1 RAID-HBA卡类型及基本信息</font> 
<font size=3 face="宋体">
|物料描述|厂商|芯片|固件类型|芯片厂商|
|-|-|-|-|-|
|LSI 9400-16i Tri-mode HBA卡|博通|SAS3416|IT|博通|
|Leap-io xxx|LeapIO|xxxx|HBA|LeapIO|
|Leap-io xxx|LeapIO|xxxx|RAID|LeapIO|

缺真实的产品信息，要么编。

### 2.2 RAID-HBA卡芯片技术规格

xxxx  需要芯片具体信息  现在应该无法写

## 3. LSI SAS3416  HBA
这需要我们的产品信息填写上去，配上操作截图。  现在应该无法写

## 4. LSI SAS3516  RAID
这需要我们的产品信息填写上去，配上操作截图。  现在应该无法写

## 5. LSI SAS3416  HBA最新产品

这需要我们的产品信息填写上去，配上操作截图。  现在应该无法写
## 6. LSI SAS3516  RAID最新产品 
这需要我们的产品信息填写上去，配上操作截图。  现在应该无法写

## 7. FAQ
这个可以慢慢补充出来一版
</font> 