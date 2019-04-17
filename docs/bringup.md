# rkdeveloptool
rkdeveloptool是rockchip是提供USB烧录工具
以下是安装 rkdeveloptool 的流程

```
git clone https://github.com/rockchip-linux/rkdeveloptool.git
cd rkdeveloptool
sudo apt-get install libudev-dev libusb-1.0-0-dev dh-autoreconf
autoreconf -i
./configure
make
make install
```

# 固件
完整的烧录需要以下7个文件，均可在github下载[下载链接](https://github.com/wisnuc/rk3328-bringup/releases)
其中a.img与p.img需要从a.tar.xz和p.tar.xz解压

```
rk3328_loader_v1.14.249.bin # loader文件
part.img        # MBR/DOS分区表
idbloader.img   # spl
uboot.img       # uboot
trust.img       # atf
p.img           # partition p (persistent)
a.img           # partition a (rootfs a)
```

# 枚举rkusb设备
使用USB连接设备，然后以下命令可看到当前连接的设备
```
sudo rkdeveloptool ld
```

连接成功的打印信息 `DevNo=1	Vid=0x2207,Pid=0x320c,LocationID=101	Maskrom`

# 下载rkusb loader
```
sudo rkdeveloptool db rk3328_loader_v1.14.249.bin # loader
```

可在串口看到loader加载的打印
```
DDR version 1.14 20190328
ID:0x805 Y
In
DDR3
333MHz
Bus Width=32 Col=10 Bank=8 Row=15/15 CS=2 Die Bus-Width=16 Size=2048MB
ddrconfig:6
OUT
Boot1 Release Time: Sep  7 2018 15:48:25, version: 2.49
SdmmcInit=0 NOT PRESENT
mmc Ext_csd, ret=0 ,
 Ext[226]=20, bootSize=2000,
                        Ext[215]=0, Ext[214]=e9, Ext[213]=0, Ext[212]=0,cap =e90000 REL=1f
mmc2:cmd19,100
SdmmcInit=2 0
BootCapSize=2000
UserCapSize=7456MB
FwPartOffset=2000 , 2000
UsbHook 898122
powerOn 898195
```

# 烧录固件
依次烧录以下固件
```
sudo rkdeveloptool wl 0x00     part.img        # MBR/DOS分区表
sudo rkdeveloptool wl 0x40     idbloader.img   # spl
sudo rkdeveloptool wl 0x4000   uboot.img       # uboot
sudo rkdeveloptool wl 0x6000   trust.img       # atf
sudo rkdeveloptool wl 0x10000  p.img           # partition p (persistent)
sudo rkdeveloptool wl 0x210000 a.img           # partition a (rootfs a)
```

# 重启设备

可在串口看到设备重启，进入系统
```
sudo rkdeveloptool rd
```
