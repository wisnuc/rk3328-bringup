# Overview

RK3328 board bring-up大体分为两个部分的工作。

第一个部分包括：

1. mmc/emmc分区方案
2. loader的制作，包括
   1. 用于rkusb模式烧录工具的loader；
   2. emmc启动使用的idbloader
   3. emmc启动使用的uboot
   4. emmc启动使用的trust，(atf, arm trusted firmware)
3. loader的烧录

第二个部分包括：

1. 内核，包括配置和dts
2. wifi/bt所用固件
3. rootfs制作

其中内核使用deb格式输出；rootfs基于ubuntu base或debian debootstrap制作；内核、ramdisk和dtb均位于rootfs内；

u-boot使用Linux的标准方式，用boot_distro载入boot.scr启动。

