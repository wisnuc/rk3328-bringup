# rkusb

## 源码版本

rockchip官方提供的sdk版本名称为rk3328_linux_v1.00_20170419。

sdk使用了(android) repo管理多个git项目，对应的git项目都在github上。

rkusb首先需要下载一个loader；在rockchip-linux/rkbin项目的README文件中对三种不同的loader说明如下：

> - loader: 由ddrbin, usbplug, miniloader合并而成可用于Windows RK升级工具使用的loader;
> - ubootloader: 由ddrbin, usbplug, U-Boot合并而成可用于Windows RK升级工具使用的loader;
> - idbloader: 由ddrbin, 一级loader(miniloader或uboot)按IDB格式合并直接用于烧写到IDB区的binary;
> 
> 注: miniloader的命名, 仅表示miniloader工程编译输出的bin, 不再延续到合并后的loader中使用;

sdk中提供了预编译的rkdeveloptool，位于rkbin/tools/rkdeveloptool；本文档所述过程未使用该版本，采用了sdk文档描述的办法从rockchip-linux/rkdeveloptool代码池中checkout源码编译和安装。

## Loader

### 合成

sdk中提供的loader在backus项目中下载后hangup。本文档所述流程采用下述方式制作该loader文件。

1. git clone https://github.com/rockchip-linux/rkbin.git
2. 当前commit为`9fc33ae`
3. boot_merger位于tools目录，ini文件使用RKBOOT目录下的RK3328MINIALL.ini，文件内的目录需要做相应修改；

```
./tools/boot_merger RK3328MINIALL.ini
```

生成的loader文件名为`rk3328_loader_v1.13.249.bin`，本项目的`files`目录下有该文件副本。

### 使用

## idbloader

sdk中的idbloader合成使用

- rk3328_ddr_800MHz_v1.00.bin
- rk3328_miniloader_v2.38.bin

无法启动。

```
DDR Version 1.00 20161115_rd_wr_gap_rfc_noc
In
SRX
800MHz
rd addr 0x8000 = 0xFFFFFFFF
rd addr 0x40008000 = 0xFFFFFFFF
ERR
```

对应的rockchip-linux/build的commit为9343881a。

尝试修改为

- rk3328_ddr_333MHz_v1.13.bin
- rk322xh_miniloader_v2.49.bin

rkdeveloptool烧入的gpt有错误，使用的parameter_gpts.txt

```
FIRMWARE_VER: 6.0.0
MACHINE_MODEL: RK3399
MACHINE_ID: 007
MANUFACTURER: RK3399
MAGIC: 0x5041524B
ATAG: 0x00200800
MACHINE: 3399
CHECK_MASK: 0x80
PWR_HLD: 0,0,A,0,1
#KERNEL_IMG: 0x00280000
#FDT_NAME: rk-kernel.dtb
#RECOVER_KEY: 1,1,0,20,0
#in section; per section 512(0x200) bytes
CMDLINE: mtdparts=rk29xxnand:0x00001f40@0x00000040(loader1),0x00000080@0x00001f80(reserved1),0x00002000@0x00002000(reserved2),0x00002000@0x00004000(loader2),0x00002000@0x00006000(atf),0x00038000@0x00008000(boot:bootable),-@0x0040000(rootfs)
```



