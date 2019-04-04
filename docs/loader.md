# Loader

一共有4个loader的二进制文件需要生成。

* rkusb loader
* idbloader
* uboot
* trust

## rkusb loader

rkusb loader用于使用rkusb工具（rkdeveloptool或upgrade_tool）时下载到cpu上执行rkusb的协议；使用rkbin repo合成该loader，需三个二进制文件：

- ddr
- usbplug
- miniloader

使用rkbin的`tools`目录下的`boot_merger`命令，需提供一个配置文件作为其参数，对于RK3328使用的配置文件是`RKBOOT`目录下的`RK3328MINIALL.ini`；

在独立使用rkbin repo时（而不是完整的SDK目录结构），ini文件内的路径需要做修改，修改后的文件内容如下：

```
[CHIP_NAME]
NAME=RK322H
[VERSION]
MAJOR=2
MINOR=49
[CODE471_OPTION]
NUM=1
Path1=bin/rk33/rk3328_ddr_333MHz_v1.14.bin
Sleep=1
[CODE472_OPTION]
NUM=1
Path1=bin/rk33/rk322xh_usbplug_v2.49.bin
[LOADER_OPTION]
NUM=2
LOADER1=FlashData
LOADER2=FlashBoot
FlashData=bin/rk33/rk3328_ddr_333MHz_v1.14.bin
FlashBoot=bin/rk33/rk322xh_miniloader_v2.49.bin
[OUTPUT]
PATH=rk3328_loader_v1.14.249.bin
```

将上述内容在rkbin目录下保存为`RK3328MINIALL.ini`，在该目录下执行：

```
tools/boot_merger RK3328MINIALL.ini
```

即可获得rkusb loader所需二进制文件，`rk3328_loader_v1.14.249.bin`；该文件的命名规则在rkbin的README中有说明，其中1.14是ddr bin的版本，249为miniloader的版本2.49。

预编译的二进制发布在本软件池的release中。

> 注意：在bin/rk33目录下没有命名对应rk3328的usbplug和miniloader文件，需使用rk322xh前缀的对应文件。

## idbloader, uboot and trust

- idbloader是瑞芯的spl，secondary program loader（相对于rom里的primary program loader而言）；
- uboot
- trust是arm trusted firmware, atf

### 源代码

rockchip官方的linux sdk使用repo管理（和android一致），其manifest文件位于：

https://github.com/rockchip-linux/manifests

```
$ tree -L 1
.
├── build
├── kernel
├── out
├── README.md
├── rkbin
├── rootfs
└── u-boot
```

仅仅编译上述文件无须sync整个repo，只需要建立所需的目录结构和clone所需的代码池。

```
mkdir out
git clone https://github.com/rockchip-linux/build
git clone https://github.com/rockchip-linux/u-boot
git clone https://github.com/rockchip-linux/rkbin
```

其中rkbin也可以不全部clone，项目所需文件包括：
```
rkbin/bin/rk33/rk3328_ddr_333MHz_v1.14.bin
rkbin/bin/rk33/rk322xh_miniloader_v2.49.bin
rkbin/bin/rk33/rk322xh_bl31_v1.40.elf
```

编译u-boot和合成所需文件的脚本是`build/mk-uboot.sh`，其中合成二进制部分需要根据rkbin的最新二进制进行修改：

```bash
elif [ "${CHIP}" == "rk3328" ]; then
        $TOOLPATH/loaderimage --pack --uboot ./u-boot-dtb.bin uboot.img 0x200000

        # tools/mkimage -n rk3328 -T rksd -d ../rkbin/rk33/rk3328_ddr_786MHz_v1.06.bin idbloader.img
        tools/mkimage -n rk3328 -T rksd -d ../rkbin/bin/rk33/rk3328_ddr_333MHz_v1.14.bin idbloader.img
        # cat ../rkbin/rk33/rk3328_miniloader_v2.43.bin >> idbloader.img
        cat ../rkbin/bin/rk33/rk322xh_miniloader_v2.49.bin >> idbloader.img
        cp idbloader.img ${OUT}/u-boot/
        # cp ../rkbin/rk33/rk3328_loader_ddr786_v1.06.243.bin ${OUT}/u-boot/
        # cp ../rkbin/bin/rk33/rk3328_loader_ddr333_v1.14.249.bin ${OUT}/u-boot/ <- not in rkbin

        # PATH=../rkbin/rk33/rk3328_bl31_v1.34.bin
        cat >trust.ini <<EOF
[VERSION]
MAJOR=1
MINOR=2
[BL30_OPTION]
SEC=0
[BL31_OPTION]
SEC=1
PATH=../rkbin/bin/rk33/rk322xh_bl31_v1.40.elf
ADDR=0x10000
[BL32_OPTION]
SEC=0
[BL33_OPTION]
SEC=0
[OUTPUT]
PATH=trust.img
EOF

        $TOOLPATH/trust_merger trust.ini

        cp uboot.img ${OUT}/u-boot/
        mv trust.img ${OUT}/u-boot/
elif [ "${CHIP}" == "rk3399" ]; then
```

执行修改后的脚本，使用`rk3328-evb`作为开发板信息。

```
build/mk-uboot.sh rk3328-evb
```

编译成功完成后得到所需二进制文件。

```
$ tree out/u-boot
out/u-boot
├── idbloader.img
├── trust.img
└── uboot.img
```

```
curl -H "Authorization: token $GITHUB_TOKEN" -H "Content-Type: $(file -b --mime-type $FILE)" --data-binary @$FILE "https://uploads.github.com/repos/hubot/singularity/releases/123/assets?name=$(basename $FILE)"
```

