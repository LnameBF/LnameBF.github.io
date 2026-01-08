---
title: "PVE 直通amd 显示器输出（AMD）"
date: 2026-01-08T16:37:48+08:00
lastmod: 2026-01-08T16:37:48+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "PVE 直通amd 显示器输出（AMD）"
weight:
slug: ""
draft: false # 是否为草稿
comments: true # 本页面是否显示评论
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

# PVE 核显直通Windows10教程 （cpu5800h） 主机Morefine S500+

## 准备工作

在开始之前对pve系统进行一些设置

下载pve_scoure







## 1. 获取 `BIOS` 并提取 `GOP`

>AFUWIN5.12（win10完美运行）版本，下载后并在win10系统（已经测试在winpe也行）上提取bios，运行软件点下存储就可以。因为软件是win10或winpe环境才可以运行，所以你需要在设备上安装个win10或者用winpe系统环境下运行提取。

![image-20241013162952529](https://file.heiok.top/note/202410131629558.png)

在win10上运行这个软件点“储存”就能获取物理机的bios文件

把存储的bios文件命名为**5800h.rom**（以你cpu型号命名）

## 2.从bios.rom中提取AMD核显相关的AMDGopDriver.efi模块

把下载UBU 1.79.17工具解压，把存储的bios.rom拷贝进ubu软件目录下

接着运行UBU.bat，打开你下载的BIOS文件。（如果UBU.bat程序cmd中没找到或者检测到bios，需要手动选择打开bios文件）



![image-20241013163236447](https://file.heiok.top/note/202410131632478.png)

提取完成之后在ubu的Extracted目录下有个GOP文件夹，里面最终目录分别有AMDGopDriver.efi文件和vbt.dat文件。AMDGopDriver.efi文件就是我们需要的。

（5000系列以上的只有一个文件夹，需要额外提取vbios，5000系列及一下的可以直接使用ubu进行提取）

![image-20241013163327964](https://file.heiok.top/note/202410131633001.png)

![image-20241013163442186](https://file.heiok.top/note/202410131634229.png)

## 3. 合并efi并生成rom

将提取出来的AMDGopDriver.efi 放入edk2-BaseTools-win32-master目录，同时这里还会用到核显的厂商与设备ID，ID可以通过在PVE Shell下执行 lspci -nn 命令,会列出有VGA的一行,最后的数字就是ID,如[8086:5917]。

![image-20241013163837396](https://file.heiok.top/note/202410131638428.png)

* AMD核显合成方法：

~~~cmd
.\EfiRom.exe -f 0x1002 -i 0x1638 -b .\vbios_1638.dat -e .\AMDGopDriver.efi -o 5800h.rom
# 命令行中amd核显一定先是-b vbios.bin然后-e gop.efi这个顺序，-f和-i为核显的厂商编号aaaa、设备编号bbbb,比如 -f 0x1002 -i 0x164c，后面-o xxxx.rom是输出文件名。
~~~

将获取到的显卡rom上传到PVE的指定目录中(PVE开机状态),上传路径:/usr/share/kvm，并且使用chmod -R 777 文件名 添加权限

win下推荐上传工具:WinSCP下载地址https://winscp.net/eng/index.php

## 4. PVE开启iommu直通

IOMMU（Input-Output Memory Management Unit）是一种硬件功能，用于管理设备对系统内存的访问。启用 IOMMU 后，可以在虚拟机中直接访问物理设备，并允许虚拟机独立于主机操作系统运行

~~~cmd
nano /etc/default/grub # 将以下行粘贴到其中，并在旧标签前面添加一个注释标签#,以下为常用的一些写法。
# 对于 AMD CPU  (pve8.以下)
GRUB_CMDLINE_LINUX_DEFAULT="quiet iommu=pt initcall_blacklist=sysfb_init pcie_acs_override=downstream,multifunction 
pci=nommconf"
GRUB_CMDLINE_LINUX_DEFAULT="quiet iommu=pt initcall_blacklist=sysfb_init pcie_acs_override=downstream,multifunction pci=nommconf"
或者
# pve8.0以上
GRUB_CMDLINE_LINUX_DEFAULT="quiet initcall_blacklist=sysfb_init"
注意,对于 AMD CPU：initcall_blacklist=sysfb_init 屏蔽掉pve7.2以上的一个bug，方便启动时候就屏蔽核显等设备驱动。pve8的grub里面不需要加入amd_iommu=on pcie_acs_override=downstream,multifunction这些参数，一般默认就开启了，如果后面直通不成功，在加上这些参数。
# 参数释义
1.iommu=pt：启用 Intel VT-d 或 AMD-Vi 的 IOMMU。这是一种硬件功能，用于管理设备对系统内存的访问。在虚拟化环境中，启用 IOMMU 后，可以将物理设备直通到虚拟机中，以便虚拟机可以直接访问硬件设备。“iommu=pt”不是必须的，PT模式只在必要的时候开启设备的IOMMU转换，可以提高未直通设备PCIe的性能，建议添加。
2.initcall_blacklist=sysfb_init：禁用 sysfb_init 内核初始化函数。这个函数通常用于在内核启动过程中初始化系统帧缓冲。在使用 GPU 直通的情况下，这个函数可能会干扰直通操作，因此需要禁用它。
3.i915.enable_gvt=1：启用 Intel GVT-g 虚拟 GPU 技术。这个选项用于创建一个虚拟的 Intel GPU 设备，以便多个虚拟机可以共享物理 GPU 设备。启用 GVT-g 需要在支持虚拟 GPU 的 Intel CPU 和主板上运行，并且需要正确配置内核和虚拟机。想开启GVT-g的就添加这条，显卡直通的就不要添加了。
4.initcall_blacklist=sysfb_init：屏蔽掉pve7.2以上的一个bug，方便启动时候就屏蔽核显等设备驱动；
5.pcie_acs_override=downstream,multifunction：便于iommu每个设备单独分组，以免直通导致物理机卡死等问题
6.pci=nommconf：意思是禁用pci配置空间的内存映射,所有的 PCI 设备都有一个描述该设备的区域（您可以看到lspci -vv），访问该区域的最初方法是通过 I/O 端口，而 PCIe 允许将此空间映射到内存以便更简单地访问。
~~~

**更新grub**

~~~
update-grub
~~~

**主机重新启动并运行后，运行以下命令,验证是否开启iommu**

~~~cmd
dmesg | grep -e DMAR -e IOMMU -e AMD-Vi
# 如果没有输出，则说明有问题。你应该看到这样的东西；“DMAR: IOMMU enabled”
# 另外一种验证方式
dmesg | grep iommu
# 出现如下例子。则代表成功
[ 1.341100] pci 0000:00:00.0: Adding to iommu group 0
[ 1.341116] pci 0000:00:01.0: Adding to iommu group 1
[ 1.341126] pci 0000:00:02.0: Adding to iommu group 2
[ 1.341137] pci 0000:00:14.0: Adding to iommu group 3
[ 1.341146] pci 0000:00:17.0: Adding to iommu group 4
# 此时输入命令
find /sys/kernel/iommu_groups/ -type l
# 出现很多直通组，就代表成功了。如果没有任何东西，就是没有开启
~~~

**启用必要的内核模块，运行以下命令**

~~~cmd
nano /etc/modules # 添加以下行；
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
~~~

**将驱动程序模块列入黑名单，以使虚拟机能够完全访问显卡等。运行以下命令**

~~~cmd
nano /etc/modprobe.d/pve-blacklist.conf # 添加以下行；

blacklist nvidiafb
blacklist amdgpu
blacklist i915
blacklist snd_hda_intel
options vfio_iommu_type1 allow_unsafe_interrupts=1

# 注意不要重复了
~~~

**通过设备ID绑定vfio-pci** 

```
nano /etc/modprobe.d/vifo.conf # 添加以下行； options vfio-pci ids=1002:1638 # 上面获取到的核显的设备id 
```

**更新模块并重新启动主机以应用更改**

~~~cmd
update-initramfs -u -k all
~~~

~~~
reboot  # 重启
~~~

**注意：**

![img](https://diyforfun.cn/wp-content/uploads/2023/09/2024072201110423.png)
在设备黑名单中添加amdgpu，并grub里开启引导自动加载后，pve启动时会卡在以下界面。是正常的！等待一会只要PVE可以通过网页访问到WebUI管理页面就是正常没问题的。因为PVE开机时会按照设备黑名单设置的，释放对核显的控制权

## 5 **关于虚拟机创建配置**

直通AMD核显
所选的虚拟机类型必须为q35
系统类型可选择为推荐使用Linux，Windows或其他类型可能会导致安装核显驱动后，出现43错误，驱动不成功。
固件类型选择ovmf UEFI
TPM选择开启，安全启动随意
CPU类型选HOST，否则可能会出现装不上驱动
光驱选择ide，或者scsi，其他保持默认设置

**直通虚拟机环境设置**

虚拟机创建后先不要启动，为创建的虚拟机添加核显pcie，将显示设置为无 none。并同时添加负责声音的pcie设备。在PVE的SHell节点下执行命令

~~~cmd
nano /etc/pve/qemu-server/100.conf # 100是你创建直通核显虚拟机的ID
~~~

在核显后添加

~~~
hostpci0: 0000:06:00.0,pcie=1,romfile=5800h.rom,x-vga=1  # 刚刚上传到/usr/share/kvm 的文件名
~~~

下面是我的虚拟机配置

~~~
GNU nano 5.4                                                               /etc/pve/qemu-server/101.conf                                                                        
bios: ovmf
boot: order=ide2;scsi0;ide0;net0;hostpci0
cores: 8
cpu: host,hidden=1
efidisk0: local:101/vm-101-disk-0.qcow2,efitype=4m,size=528K
hostpci0: 0000:06:00.0,pcie=1,romfile=5800h.rom,x-vga=1
hostpci1: 0000:06:00.1
ide0: local:101/vm-101-disk-1.qcow2,size=100G
ide2: local:iso/zh-cn_windows_10_business_editions_version_22h2_updated_june_2024_x64_dvd_1139f6a3.iso,media=cdrom,size=6779692K
machine: pc-q35-6.2
memory: 4096
meta: creation-qemu=7.2.0,ctime=1728756712
name: win
net0: e1000=6A:14:B4:A8:37:28,bridge=vmbr0,firewall=1
numa: 0
ostype: win11
scsi0: local:iso/virtio-win.iso,media=cdrom,size=708140K
scsihw: virtio-scsi-single
smbios1: uuid=0bd69c1d-509e-43f4-a66d-bd96d30c2033
sockets: 1
tpmstate0: local:101/vm-101-disk-2.raw,size=4M,version=v2.0
vga: none
vmgenid: 8286c8ff-aff5-4f71-a90b-e0eff7a57e3f
~~~

可以看到已经有GPU了

![image-20241013165201602](https://file.heiok.top/note/202410131652668.png)

启动虚拟机后，正常就可以看到PVE BIOS的开机画面，此时你可直通USB键盘，并按ESC进入UEFI设置，所有直

通的操作就完成了。
