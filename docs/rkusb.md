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



