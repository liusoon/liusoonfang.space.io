---
title: Macbookpro2015升级SSD
subtitle: 通过更换nvme协议固态对旧款MacBookpro进行升级
date: 2020-10-26T13:04:20.000Z
lastmod: 2020-10-26T13:04:20.000Z
draft: false
author: "Liusoon"
authorLink: "liusoonfang.space"
description: "井字型少年"  

tags: ["MacBook","SSD","捡垃圾"]
categories: ["修个硬件"]  

toc : true
math:
    enable: true
lightgallery: true
license: ""

---

&emsp;自从老乔不在了之后，苹果的产品随说还是业界标杆，但是在我心里或多或少还是有点拉垮。搭载了最新的t2安全芯片的MacBook pro已经多次出现休眠问题，突然觉得自己手里的2015款mbp又开始香了。后悔当年买的时候没有买顶配导致现在多导入几个项目文件就几乎要占满容量。虽然苹果的笔记本系列一如既往的焊死了内存，好在手里的2015款还能够支持ssd的更换，于是准备自己动手升级ssd，毕竟自己动手丰衣足食。同时把升级过程以及遇到的问题记录下来，以供准备自己动手升级的朋友参考。  

## 准备工作

&emsp;首先在准备动手升级前需要明确两个问题，自己的[笔记本型号是否支持更换NVMEssd](https://forums.macrumors.com/threads/upgrading-2013-2014-macbook-pro-ssd-to-m-2-nvme.2034976/ )以及自己更换ssd是出于对容量的需求还是对读取速度的需求。我自己使用的是2015款的MacBook pro 13.3 256g，出于对硬盘存储量的需求，所以准备直接上1tb的ssd。对读取速度有需求的应该考虑一下自己笔记本本身的最大读取速度支持，2015款13寸的mbp最大读取是在1500MB/s,所以购买再高读取速度的ssd在这个本子上也只能发挥到1500MB/s。  

### 型号选择

&emsp;目前已知的能够支持[MacBookpro2015款的NVMEssd](https://forums.macrumors.com/threads/upgrading-2013-2014-macbook-pro-ssd-to-m-2-nvme.2034976/ )有：  

> - Adata NVMe SSD : SX6000, SX7000, SX8200, SX8200 Pro etc
>
> - Corsair NVMe SSD : MP500, MP510  
>
> - Crucial NVMe SSD : P1
>
> - HP NVMe SSD : ex900, ex920, ex950
>
> - OCZ RD400
>
> - Toshiba XG3, XG4,XG5, XG5p, XG6
>
> - Intel NVMe SSD : 600p, 660p, 760p
>
> - MyDigital NVMe SSDs : SBX - BPX
>
> - Kingston NVMe SSD : A1000, A2000, KC1000
>
> - Sabrent Rocket (Phison E12 and E16 based)  
>
> - Samsung NVMe SSD : 960 Evo, 960 Pro, 970 Evo, 970 Pro, 970 Evo plus (with latest firmware)  
>
> - WD Black NVMe SSD v1, v2 and v3, WD Blue SN550  
>
> - Inland Premium (not Professional)  

&emsp;明确不支持的ssd型号：  

> - Samsung PM981  
>
> - Samsung 950 Pro  
>
> - SK hynix Gold P31

&emsp;考虑性价比的话可以选择Intel760p，或者西数（个人不推荐）如果单考虑性能可以选择Samsung 970evo，喜欢mlc颗粒选择970pro。考虑到后续更换电脑还能够继续使用，我选择的是970evoplus。虽然本子最大只支持1500M/s，但是还是选择Samsung信仰加成。选择970evoplus需要最新的固件支持，如果购买时没有升级固件可以自行升级[970evoplus固件版本](https://www.samsung.com/semiconductor/minisite/ssd/download/tools/ )2B2XXX，根据提示下载升级即可。 不考虑使用各种软件的话，工装包也是不错的选择，例如pm981a对于的产品就是970evoplus，附上Samsung各工装ssd版本对应的产品型号：![image-20201029190843130](https://cdn.jsdelivr.net/gh/liusoon/images/image-Samsungddpng )

## 需要的工具  

> - 六角螺丝刀  
>
> - 普通十字螺丝刀
>
> - NVMEssd一块（Samsung 970evoplus）
>
> - 能够格式化的空白u盘一个（容量大于笔记本已用存储量）用来进行系统备份  
>
> - NVME 转接卡（因为苹果的ssd接口特殊，需要购买转接卡）

## 更换步骤  

### 系统备份  

  + 备份前可以先测试一下现有的ssd读取速度，方便更换后进行对比读取速度是否有异常。![image-20201029184246397](https://cdn.jsdelivr.net/gh/liusoon/images/image-applessd.png )测试发现我的MacBook Pro读取速度还行，但毕竟是15年的老笔记本了，用到现在写入速度能达到400也是很正常的事情。![image-20201029202829018](https://cdn.jsdelivr.net/gh/liusoon/images/image-20201029202829018.png )
  
  + 插入u盘，利用时间机器对系统进行备份。注意的是最好是将设置中的[文件保险箱解锁](https://cdn.jsdelivr.net/gh/liusoon/images/image-%E6%96%87%E4%BB%B6%E4%BF%9D%E9%99%A9%E7%AE%B1.png )，否则在恢复备份后ssd读取速度会受到影响。![image-20201029184006699](https://cdn.jsdelivr.net/gh/liusoon/images/image-%E6%96%87%E4%BB%B6%E4%BF%9D%E9%99%A9%E7%AE%B1.png )
  
+ 利用时间机器对系统进行备份![image-20201029184533977](https://cdn.jsdelivr.net/gh/liusoon/images/image-backup.png )
  
   因为macOS的限流措施，Time Machine在备份时硬盘读写速度被限制，如果备份时间过长，可以尝试使用终端命令解除限制。
  
   `sudo sysctl debug.lowpri_throttle_enabled=0 //解除Time Machine读取速度限制`
  
   &emsp;系统备份不只一种方式，利用时间机器是我个人认为后期恢复起来最快的。系统备份完成后，就可以开始动手进行硬件的更换了。  

### 硬件更换  

+ 首先将MacBook关机，断开电源线，将背部的10颗螺丝拧下按顺序摆好（因为螺丝的长度不一样，建议按拆下的位置摆好）![](https://cdn.jsdelivr.net/gh/liusoon/images/256g.jpeg )  
&emsp;螺丝拆下后，从笔记本的黑条处拆开，用巧劲就可以不要相信大力出奇迹。拆开后的背板可以很清晰的看到两个卡扣，内部的结构也很精密工整。想起我拆过的hp的笔记本，内部胶贴到处都是，缝缝补补的感觉就很廉价。![](https://cdn.jsdelivr.net/gh/liusoon/images/backcase.jpeg )![](https://cdn.jsdelivr.net/gh/liusoon/images/backcase2.jpeg )
  
+ 为了安全起见将电池卡扣拔出，进行断电操作。带电操作有一定几率会造成主板烧坏，得不偿失，虽说也有头铁的带电进行操作的，拒绝做铁头娃从我做起。电池卡扣拔出需要一定的技巧，图中翘起的是电池的卡扣。两个手指卡住该卡扣的左上角及右上角两个位置，向上顶开，即可打开卡扣。![打开后的电池卡扣](https://cdn.jsdelivr.net/gh/liusoon/images/powerout.jpeg )
  
+ 断开电池卡扣之后就可以进行ssd的更换了，使用螺丝刀将ssd的固定螺丝卸下，然后将ssd平着推出就能很轻易的拆下原装的固态硬盘了。![](https://cdn.jsdelivr.net/gh/liusoon/images/ssdchange.jpeg )使用准备好的新的ssd连接上转接头，听到卡扣卡入的声音代表卡紧。然后将新的ssd连着转接头插入MacBook的ssd凹槽卡紧，并拧上用来固定ssd的螺丝。接着将电池的卡扣卡入，装上电脑的后壳，先测试一下新装固态是否能够正确识别，不用急着将外壳的螺丝拧入。此时电脑可能出现不识别电池的问题，所以需要连接外接电源线进行测试。  
  
   &emsp;如果无法被读取，排查连接处是否插紧以及硬盘和转接头是否支持；==有部分ssd需要在开机后使用磁盘工具进行格式化==；如果新装固态没有进行格式可能造成不识别或者备份恢复失败，正确读取之后进行下步操作。  
+ 点击开机按钮，按住**option+R**出现地球界面，选择网络设置（推荐有线连接)。![地球界面](https://cdn.jsdelivr.net/gh/liusoon/images/earth.png )进入界面后，选择磁盘工具，对ssd进行[格式化和分区设置](https://support.apple.com/zh-cn/guide/disk-utility/dskutl14027/mac )。设置完成后，可以正确识别ssd。![](https://cdn.jsdelivr.net/gh/liusoon/images/970evoplus.jpeg )然后选择时间机器，插入之前备份的u盘，选择从该u盘恢复。此时可以看到恢复程序已经启动，等待备份恢复就可以了。(如果恢复过程出现失败，建议格式化新装的固态，重新进行恢复。)![image-20201029201845801](https://cdn.jsdelivr.net/gh/liusoon/images/image-timemachine.png )  
  
## 固态读写对比

&emsp;此时开机点亮，系统文件以及各个数据都在，点开系统信息查看固态信息。可以看到新的固态硬盘已经在进行工作，并且从之前的sata协议成功的更换到nvme协议。![image-20201029202303212](https://cdn.jsdelivr.net/gh/liusoon/images/image-ssd970evoplus.png )

&emsp;对此时的硬盘读取速度进行测试，读取速度因为本身硬件的限制只能达到1500MB/s; 写入速度在更换了硬件之后从400MB/s直接到1288.9MB/s,起飞了。后续的使用中也没有发现过热之类的现象，休眠状态也正常，可以再战5年了。  
![image-20201029202434665](https://cdn.jsdelivr.net/gh/liusoon/images/image-newssd.png )  
最后不要忘记在终端里输入以下命令行，恢复时间机器的限制，以免日常使用中因为时间机器对资源对过度占用而引起卡顿。  
&emsp;`sudo sysctl debug.lowpri_throttle_enabled=1 //恢复Time Machine读取速度限制`
