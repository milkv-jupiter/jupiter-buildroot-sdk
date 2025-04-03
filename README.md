# jupiter-build

This document will guide you to compile and generate a buildroot-based system image that can run on jupiter.

## Prepare the build environment

Ubuntu 20.04 or later LTS versions are recommended.

### Ubuntu 16.04 and 18.04

```
sudo apt-get install git build-essential cpio unzip rsync file bc wget python3 libncurses5-dev libssl-dev dosfstools mtools u-boot-tools flex bison python3-pip
sudo pip3 install pyyaml
```

### Ubuntu 20.04 or higher

```
sudo apt-get install git build-essential cpio unzip rsync file bc wget python3 python-is-python3 libncurses5-dev libssl-dev dosfstools mtools u-boot-tools flex bison python3-pip
sudo pip3 install pyyaml
```

## Download source code

```
mkdir jupiter-linux
cd jupiter-linux

repo init -u https://github.com/milkv-jupiter/manifests.git -b main -m k1-bl-v2.1.y.xml
repo sync
repo start k1-bl-v2.1.y --all
```

It is recommended to download the third-party software packages that buildroot depends on in advance to avoid failure in downloading dependent packages during the compilation process.

```
wget -c -r -nv -np -nH -R "index.html*" http://archive.spacemit.com/buildroot/dl
```

Directory Structure:
```
├── bsp-src               # Linux kernel，uboot and opensbi source code
│   ├── linux-6.6
│   ├── opensbi
│   └── uboot-2022.10
├── buildroot             # Buildroot main directory
│   ├── dl                # Buildroot dependent packages
├── buildroot-ext         # Customized ext, such as board, configs, package, and patches
├── Makefile              # Top Makefile
├── package-src           # Locally deployed application or library source directory
│   ├── ai-support
│   ├── drm-test
│   ├── factorytest
│   ├── glmark2
│   ├── img-gpu-powervr
│   ├── jetson-utils
│   ├── k1x-cam
│   ├── k1x-jpu
│   ├── k1x-vpu-firmware
│   ├── k1x-vpu-test
│   ├── mesa3d
│   ├── mpp
│   ├── rtk_hciattach
│   ├── usb-gadget
│   └── v2d-test
└── scripts               # Scripts used during compilation
```

## Compilation

For the first compilation, it is recommended to use `make envconfig` to compile completely. If `buildroot-ext/configs/spacemit_k1_defconfig` has been modified, use `make envconfig` to compile. In other cases, just use `make` to compile.

```
cd jupiter-linux

make envconfig
Available configs in buildroot-ext/configs/:
  1. spacemit_k1_minimal_defconfig
  2. spacemit_k1_plt_defconfig
  3. spacemit_k1_rt_defconfig
  4. spacemit_k1_upstream_defconfig
  5. spacemit_k1_v2_defconfig
\n
your choice (1-5):
```

Enter `5` and press Enter to start compiling.

The compilation process may require downloading some third-party software packages, and the specific time required depends on the network environment. If you download the third-party software packages that buildroot depends on in advance, the recommended hardware configuration compilation takes about 1 hour.

After the compilation is completed, you can see the following results:
```
Images successfully packed into /build/jupiter-sdkv2/local/output/k1_v2/images/bianbu-linux-k1_v2.zip


Generating sdcard image...................................
INFO: cmd: "mkdir -p "/build/jupiter-sdkv2/local/output/k1_v2/build/genimage.tmp"" (stderr):
INFO: cmd: "rm -rf "/build/jupiter-sdkv2/local/output/k1_v2/build/genimage.tmp"/*" (stderr):
INFO: cmd: "mkdir -p "/build/jupiter-sdkv2/local/output/k1_v2/images"" (stderr):
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition 'bootinfo' from 'factory/bootinfo_sd.bin' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition 'fsbl' (in MBR) from 'factory/FSBL.bin' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition 'env' (in MBR) from 'env.bin' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition 'opensbi' (in MBR) from 'fw_dynamic.itb' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition 'uboot' (in MBR) from 'u-boot.itb' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition 'bootfs' (in MBR) from 'bootfs.img' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition 'rootfs' (in MBR) from 'rootfs.ext4' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition '[MBR]' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition '[GPT header]' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition '[GPT array]' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): adding partition '[GPT backup]' ...
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): writing GPT
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): writing protective MBR
INFO: hdimage(bianbu-linux-k1_v2-sdcard.img): writing MBR
Successfully generated at /build/jupiter-sdkv2/local/output/k1_v2/images/bianbu-linux-k1_v2-sdcard.img
make[1]: Leaving directory '/build/jupiter-sdkv2/local/output/k1_v2'
```

`bianbu-linux-k1_v2.zip` is suitable for titanflasher, or can be decompressed and flashed with fastboot, `bianbu-linux-k1_v2-sdcard.img` is the sdcard firmware, which can be written to the sdcard with the dd command or balenaEtcher after decompression.

## Ref

[https://bianbu-linux.spacemit.com/source](https://bianbu-linux.spacemit.com/source)
