# K230 Linux SDK

Compared with CanMV K230, the memory of Canaan K230D AIoT chip has been changed from K230's external 256MB/512MB to internal 128MB. The software memory overhead of K230 based on 64LP64 ABI is too large, and Xuantie's 64ILP32 technology can simultaneously improve the storage density of memory and cache, and can play a greater performance advantage in memory, taking into account both cost and performance.

Based on the SDK of K230 64LP64 ABI, K230D Linux SDK adds the SDK of 64ILP32 ABI, which can support the construction of 64ILP32 and 64LP64 systems. After preliminary performance comparison, the memory overhead of the 64ILP32 RuyiSDK has decreased by 30%, and the traversal performance of Doubly linked list has increased by 20%. The SDK provides relevant kernel module files.

At the recently held RISC-V Day Tokyo 2024 Summer, we showcased this open-source work and engaged with the community. 64ILP32 effectively reduces memory overhead and enhances cache performance, which can improve the product competitiveness of the widely available RV64 small-memory chips.

![](image/1.jpg)
![](image/2.jpg)

> You can find the pre-built images in the [release](https://github.com/ruyisdk/k230_linux_sdk/releases)

> This document uses the k230d_canmv_ilp32_defconfig configuration as an example. If you use other configurations, replace k230d_canmv_ilp32_defconfig with correct names. All the configurations supported by this SDK,  can be found in  buildroot-overlay/configs directory.

## Build Preparation

1. Download the toolchain:

download  `Xuantie-900-gcc-linux-6.6.0-glibc-x86_64-V2.10.1-20240712.tar.gz` toolchain from  `https://www.xrvm.cn/community/download?id=4333581795569242112` and uncompress the toolchain to the `/opt/toolchain` :

```bash
mkdir -p /opt/toolchain
tar -zxvf Xuantie-900-gcc-linux-6.6.0-glibc-x86_64-V2.10.1-20240712.tar.gz -C /opt/toolchain
```

2. Install dependencies:

```bash
apt-get install -y   git sed make binutils build-essential diffutils gcc  g++ bash patch gzip \
        bzip2 perl  tar cpio unzip rsync file  bc findutils wget  libncurses-dev python3  \
        libssl-dev gawk cmake bison flex  bash-completion
```

3. Prepare a dedicated toolchain `riscv-gnu-toolchain-rv64ilp32` toolchain for 64ILP32 builds:

> k230d_canmv_ilp32_defconfig required  ubuntu 22.04 or 24.04 and install rv64ilp32 toolchain:

```bash
wget -c https://github.com/ruyisdk/riscv-gnu-toolchain-rv64ilp32/releases/download/2024.06.25/riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.06.25-nightly.tar.gz
mkdir -p /opt/toolchain/riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.06.25/
tar -xvf riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.06.25-nightly.tar.gz -C /opt/toolchain/riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.06.25/
```

## Build

Before building, please download or clone the source code of this repository:

```bash
git clone https://github.com/ruyisdk/k230_linux_sdk.git
cd k230_linux_sdk
```

Build 64ILP32 ：

```bash
#build k230d canmv (64ilp32 kernel and 32bit rootfs)
make CONF=k230d_canmv_ilp32_defconfig
```

Build 64LP64 ：

```bash
#build k230d canmv image (64lp64 kernel and 64bit rootfs)
make CONF=k230d_canmv_lp64_defconfig 
```

> you can execute the `make help` command to view help


## output

The build output is in the `output` directory, and is stored separately according to the executed defconfig. The image file paths for 64ILP32 and 64LP64 are:

```bash
# 64ILP32
output/k230d_canmv_ilp32_defconfig/images/sysimage-sdcard.img.gz

# 64LP64
output/k230d_canmv_lp64_defconfig/images/sysimage-sdcard.img.gz
```

After getting the image,please uncompress file , [burn to tf card](https://developer.canaan-creative.com/k230/zh/main/CanMV_K230_%E6%95%99%E7%A8%8B.html#id11),insert tf to device, and poweron device to start using it.

For more SDK usage instructions, please refer to the upstream [kendryte k230_linux_sdk](https://github.com/kendryte/k230_linux_sdk) repository instructions.
