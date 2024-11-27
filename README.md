# 鸣谢 Thanks
* [HandsomeMod](https://github.com/HandsomeMod/HandsomeMod)
* [dreirund/Handsomemod-uf896](https://github.com/dreirund/Handsomemod-uf896)
* [kirdesde/Handsomemod](https://github.com/kirdesde/Handsomemod/)
* [ALbur/HanFI](https://github.com/ALbur/HanFI)

# 本项目 HandsomeMod-uf896-ci
- **用法**
  1. Action构建出boot和system（rootfs）
  2. 替换别的棒子底包的boot.img和rootfs.img
  3. 恢复基带，修复Modem

- **问题**
  1. sim卡切换不正确，默认只能使用板载esim，不能切外置sim，即便去掉esim

- **兼容性**
  1. uf896的构建制品同样适用于 uf629 v1.1
  2. 短接【1，6】测试点进入9008模式

- **参考信息**
  1.  [openstick#edl-recovery-mode](https://github.com/colemickens/mobile-nixos/tree/openstick/devices/openstick#edl-recovery-mode)
  2.  [UF896](https://forum.openwrt.org/t/uf896-qualcomm-msm8916-lte-router-384mib-ram-2-4gib-flash-android-openwrt/131712/59)

- **基带**
  1. 提取基带: modemst1、modemst1、fsc、fsg 四个分区,并替换
  2. 可以用miko备份的文件包xxx.bin文件里面的 0.modem.fat （可以用7z压缩工具打开提取）
  3. 在提取 0.modem.fat （可以用7z压缩工具打开提取）到一个文件夹
  4. 把image下面的所有文件复制到 /lib/firmware/，reboot重启棒子

# 援引 HanFI
* 稳妥起见 刷机前请备份自己的基带4分区及firmware
* 也许你需要自行替换firmware及基带 详见 [大佬文章](https://www.coolapk.com/feed/38895153) 已验证
* tools都不带系统镜像 其中tool.7z已验证 其余需要自行验证 ps:其实刷了大佬们的op后 替换自己编译的 root和boot就行 用高通工具9008更快
* 需要将action生成的OpenWrt_firmware_Handsome_handsome-openstick-ufi001c_xxxx.zip 中的两个img放入你需要的tool解压后目录
* 重命名.bat=>双击.bat/刷入.bat=>刷入 
* 刷入前请自行删除旧版本boot.img及rootfs.img(tool.7z下为system.img)
* firmware 见 [openstick](https://github.com/OpenStick/OpenStick/releases)
* 问题 
  1. mac80211驱动有问题，要编译老网卡需要换，自己添加wcn36xx驱动进去 还望大佬们指导编译方案 也不清楚是否可行
  2. kmod-usb-gadget-eth 编译不进去 使用扩展坞的 若有网卡 或许需要本地编译 或者请指导一下云编译怎么修复
* 其他
  1. 打开主机模式，连接拓展坞 echo host > /sys/kernel/debug/usb/ci_hdrc.0/role
  2. 打开网络共享，关闭主机模式 echo gadget > /sys/kernel/debug/usb/ci_hdrc.0/role

# 援引 Handsomemod-uf896

This repository is a fork of the ["`uf896`"-branch](https://github.com/kirdesde/Handsomemod/tree/uf896) of "[kirdesde/Handsomemod](https://github.com/kirdesde/Handsomemod/)", which itself is based on "[HandsomeMod/HandsomeMod](https://github.com/HandsomeMod/HandsomeMod/)".

It is an operating system based on [OpenWrt](http://openwrt.org/), but with many changes and additions.

As "[kirdesde/Handsomemod](https://github.com/kirdesde/Handsomemod/)", this repository too is especially targeted at supporting the UF896 LTE/WiFi routers which come in the form factor of an USB stick. A discussion about this devices with links to further projects can be found [here on the OpenWrt forum](https://forum.openwrt.org/t/131712).

The purpose of this repository is to have a place for my modifications and fixes I apply for the UF896 support.

It is based on [OpenWrt 21.02](https://openwrt.org/releases/21.02/) and heavily modified. It uses a [Linux kernel](https://kernel.org/) version 5.10.33.


## Building

In general, the [building instructions from the OpenWrt project](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem) apply, but different repositories are used.

To build the firmware for the UF896 from this repository, use the following steps:

1. Make sure you have the [OpenWrt build requirements](https://openwrt.org/docs/guide-developer/toolchain/install-buildsystem) fulfilled.  
   *On [Arch Linux](http://archlinux.org) and Arch based distributions, installing the [AUR package "`openwrt-devel`"](https://aur.archlinux.org/packages/openwrt-devel) does pull in all needed dependencies.*
2. Clone this repository: `git clone https://github.com/dreirund/Handsomemod-uf896.git`.
3. `cd` into the clone: `cd Handsomemod-uf896`.
4. Generate `feeds.conf` from the default: `cp feeds.conf.default feeds.conf`.
5. Make sure that in the `feeds.conf` the following feeds are enabled *(note that the "`packages`" feed comes from HandsomeMod and* not *from OpenWrt, and also note the extra "`handsomefeed`" feed)*:  
   ```
   src-git packages https://github.com/HandsomeMod/packages.git
   src-git luci https://git.openwrt.org/project/luci.git;openwrt-21.02
   src-git routing https://git.openwrt.org/feed/routing.git;openwrt-21.02
   src-git telephony https://git.openwrt.org/feed/telephony.git;openwrt-21.02
   src-git handsomefeed https://github.com/HandsomeMod/handsomefeed.git
   ```  
   *(Other feeds might be available as well.)*
6. Install packages from the feeds: `./scripts/feeds update -a && ./scripts/feeds install -a`.
7. Select the default configuration for the `UF896`:  
   1. `make menuconfig`,
   2. Select the following options:
      1. `Target System`: `Qualcomm Msm89xx Soc` (`CONFIG_TARGET_msm89xx=y`),
      2. `Subtarget`: `Msm8916 Soc based Targets` (`CONFIG_TARGET_msm89xx_msm8916=y`),
      3. `Target Profile`: `Openstick UF896` (`CONFIG_TARGET_msm89xx_msm8916_DEVICE_openstick_uf896=y`),
      4. Select `Save` and then `Exit` the configuration (*do not* do further configurations now).
   3. Generate default configuration for the selected target: `make defconfig`.
8. If you want, configure options to your liking now: `make menuconfig`.
9. Build the firmware image: `make`.

You can also use [example `.config`](sample/.config) (that is my `.config` I use on the device) and [example `feeds.conf`](sample/feeds.conf) from the [`sample/`](sample/) directory.

For more build options and troubleshooting, refer to the [OpenWrt build system usage](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem) and other [documentation at OpenWrt.org](https://openwrt.org/docs/).


## Installing

*(Instructions taken from [&rarr;&hairsp;here](https://github.com/kirdesde/Handsomemod/commit/aec867af57f1212a5ec3c4dea2828463bc31ea29). Not guaranteed to work -- I myself am currently stuck at `fastboot erase rootfs` because my device does not has a partition with that name, see [&rarr;&hairsp;here](https://forum.openwrt.org/t/131712/92).)*

Carry out the following from a computer where the device is connected to via USB.

### Prerequisites:

***Have [OpenStick](https://github.com/OpenStick/OpenStick) installed (or at least the partitions prepared for it)**, the images generate here assume that the device has been prepared for OpenStick operating system before!*

*More detailed instructions on this step hopefully soon.*

### Pre-cautions work:

1. Before conneting the device, make sure that there is currently no other device in fastboot mode connected, otherwise you can damage the other device:  
   `fastboot devices`  
   should return nothing.
2. Connect the device, and check that  
   `fastboot devices`  
   now returns something which looks similar to  
   ```
   0ec38c91 fastboot
   ```  
   (The ID can vary).

If that is given, proceed:

### Install instructions:

1. Erase boot partition:  
   `fastboot erase boot`
2. Write boot image:  
   `fastboot flash boot handsomemod-msm89xx-msm8916-openstick_uf896-squashfs-boot.img`
3. Erase rootfs partition:  
   `fastboot erase rootfs`
4. Write system image:  
   `fastboot flash rootfs handsomemod-msm89xx-msm8916-openstick_uf896-squashfs-system.img`
5. Reboot:  
   `fastboot reboot`
6. "OpenWrt" (HandsomeMod) should boot now (red led is blinking).
7. push the sysupgrade image to the device (this is imported, as this sets up the loop device for the rootfs overlay)  
   *(**Note:** `adbd` is part of the handsome feeds utils package, **Make sure you have `CONFIG_PACKAGE_android-tools-adbd=y`!**)*  
   `adb push handsomemod-msm89xx-msm8916-openstick_uf896-squashfs-sysupgrade.bin /tmp`
8. Execute sysupgrade:  
   `adb shell`  
   `sysupgrade /tmp/handsomemod-msm89xx-msm8916-openstick_uf896-squashfs-sysupgrade.bin`
9. After reboot you should have a working openwrt incl. overlay fs
10. Thanks to the usb gadget mode the device has ethernet connectivity over usb
11. Please note, it's also part of the handsome feeds utils to setup and activate a default wifi AP (unencrypted). You can disable this after the last reboot (or disable it in the build config)

export http_proxy=http://10.77.159.120:808
export https_proxy=http://10.77.159.120:808
export set FORCE_UNSAFE_CONFIGURE=1

export http_proxy=http://192.168.56.2:808
export https_proxy=http://192.168.56.2:808
export set FORCE_UNSAFE_CONFIGURE=1
更新feeds.conf

git reset --hard 560965d582

./scripts/feeds update -a
完成更新后，可修改源码根目录下feeds.conf.default文件实现增加软件源，以添加istore为例

#修改feeds.conf.default文件
nano ./feeds.conf.default
#在最底下增加istore源
src-git istore https://github.com/linkease/istore;main
#完成软件源添加后，执行更新命令
./scripts/feeds update -a
#注：常用软件源
#kiddin9
src-git https://github.com/kiddin9/openwrt-packages.git;master
#haiibo
src-git https://github.com/haiibo/openwrt-packages.git;master
完成所有软件源导入后，执行安装命令

#install symlinks for all obtained packages into package/feeds/
./scripts/feeds install -a

完成配置后，点击save保存，配置文件默认保存在.config文件

#预下载资源
make download -j8 V=s
完成资源下载后，开始编译

#初次编译建议使用单线程编译方便查找错误
make -j1 V=s
#二次编译可选择多线程编译提高效率(j后数字为线程数）
make -j48 V=s

  之前用酷安上下载的苏苏亮亮版友提供的Openwrt，在高通410棒子上刷机成功，但编译一直就没搞定。近期听说又出了个分支版本ImmortalWrt，刷了个版本，感觉界面清爽不少，内核也升级，遂打算搞定编译+刷机

过程记录如下：

1.Ubuntu版本要求
不能用Ubuntu18.04，./scripts/feeds update过程会报错，推荐用20.04

Ubuntu 20.04.6版本pve下安装没问题，hyper-v下iso怎么也无法进入安装界面，后来下载安装了20.04版本就成功在hyper-v下安装完成，特此说明下。

2. 安装依赖
大多数教程都明确需要如下：

sudo apt-get -y install build-essential asciidoc binutils bzip2 gawk gettext git libncurses5-dev libz-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 libc6-dev-i386 subversion flex uglifyjs git-core gcc-multilib p7zip p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils upx libelf-dev autoconf automake libtool autopoint device-tree-compiler g++-multilib antlr3 gperf wget curl swig rsync

实际还缺如下两个：

sudo apt-get -y install mkbootimg img2simg

3. 编译过程
   下载代码命令行：

    git clone https://github.com/lkiuyu/immortalwrt.git

    这个版本编译内核过程中会需要手动操作，所以多线程编译会报错。暂时没有想到办法解决，只能先-j1编译一次后，再-j8编译。

    后续，如果menuconfig里修改了内核模块，也会出现上述确认过程，解决方案一样。

20240720刷新：用最新的lkiuyu/immortalwrt.git编译出来的二进制，modem启动失败，原因未知，替换modem后依旧不能解决，回退到23年8月代码后恢复（酷安苏苏小亮亮最后发布的固件就是23年0802），lkiuyu的代码没有tag，幸运的是有人fork了代码

git clone https://github.com/kennyhn88/immortalwrt.git
https://github.com/obanat/immortalwrt-8916
4.编译配置：
[Targe Images:]
Kernel partition size (in MiB)    50

Root filesystem partition size (in MiB)  200

说明：kernel一般50M足够了，除非编译的kmod模块很多；root分区大小修改后，并不会影响编译出来的system.img文件大小，但会影响刷机的速度，默认的menuconfig编译出来的高通410的system.img(也就是rootfs.img)，大约60M，所以留100M可以了，考虑扩展性，改成200M。200M刷rootfs大约需要2分钟，所以酌情修改


[Protocols]
luci-proto-modemmanager=y

[Utilities]
android-tools-adbd=y

gc=y

注意：这两步可能是不需要的，选择modemmanager后，自动添加了依赖

如果编译过程报modem文件冲突，检查menuconfig中firmware下的配置

1.modem



2.wifi



3.nv项



注意这几个貌似不能多选，多选后编译没问题，打包system.img就会报文件冲突，所以，只能一次编译一个，例如上图编译的是103s，编译完成后，修改menuconfig，再编译001C。

编译完成后，把如下两个文件拷贝到openwrt的刷机包中，改名

immortalwrt-msm89xx-msm8916-openstick-ufi001c-ext4-system.img   ==》 对应rootfs.img

immortalwrt-msm89xx-msm8916-openstick-ufi001c-ext4-boot.img ==》对应boot.img

其他的文件不要动，就用原来的，至此，刷机包准备完毕！
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
                        
原文链接：https://blog.csdn.net/obana/article/details/134213234


In general, the building instructions from the OpenWrt project apply, but different repositories are used.

To build the firmware for the UF896 from this repository, use the following steps:

Make sure you have the OpenWrt build requirements fulfilled.
On Arch Linux and Arch based distributions, installing the AUR package "openwrt-devel" does pull in all needed dependencies.
Clone this repository: git clone https://github.com/dreirund/Handsomemod-uf896.git.
cd into the clone: cd Handsomemod-uf896.
Generate feeds.conf from the default: cp feeds.conf.default feeds.conf.
Make sure that in the feeds.conf the following feeds are enabled (note that the "packages" feed comes from HandsomeMod and not from OpenWrt, and also note the extra "handsomefeed" feed):
src-git packages https://github.com/HandsomeMod/packages.git
src-git luci https://git.openwrt.org/project/luci.git;openwrt-21.02
src-git routing https://git.openwrt.org/feed/routing.git;openwrt-21.02
src-git telephony https://git.openwrt.org/feed/telephony.git;openwrt-21.02
src-git handsomefeed https://github.com/HandsomeMod/handsomefeed.git
(Other feeds might be available as well.)
Install packages from the feeds: ./scripts/feeds update -a && ./scripts/feeds install -a.
Select the default configuration for the UF896:
make menuconfig,
Select the following options:
Target System: Qualcomm Msm89xx Soc (CONFIG_TARGET_msm89xx=y),
Subtarget: Msm8916 Soc based Targets (CONFIG_TARGET_msm89xx_msm8916=y),
Target Profile: Openstick UF896 (CONFIG_TARGET_msm89xx_msm8916_DEVICE_openstick_uf896=y),
Select Save and then Exit the configuration (do not do further configurations now).
Generate default configuration for the selected target: make defconfig.
If you want, configure options to your liking now: make menuconfig.
Build the firmware image: make.
You can also use example .config (that is my .config I use on the device) and example feeds.conf from the sample/ directory.

For more build options and troubleshooting, refer to the OpenWrt build system usage and other documentation at OpenWrt.org.

Installing
