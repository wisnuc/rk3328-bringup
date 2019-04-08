# rkdeveloptool

rockchip提供了两个工具烧录emmc：upgrade_tool和rkdeveloptool。

upgrade_tool是交互式的REPL，脚本使用不方便。

rkdeveloptool对脚本更友好，其gpt功能有bug，本项目暂不使用gpt分区表，不使用rkdeveloptool的gpt功能。

一个完整的烧录过程是：

```
# 枚举rkusb设备，应看到打印信息
$ rkdeveloptool ld

# 下载rkusb loader
$ rkdeveloptool db rk3328_loader_v1.14.249.bin

$ rkdeveloptool wl 0x00     part.img        # MBR/DOS分区表
$ rkdeveloptool wl 0x40     idbloader.img   # spl
$ rkdeveloptool wl 0x4000   uboot.img       # uboot
$ rkdeveloptool wl 0x6000   trust.img       # atf
$ rkdeveloptool wl 0x10000  p.img           # partition p (persistent)
$ rkdeveloptool wl 0x210000 a.img           # partition a (rootfs a)
```

