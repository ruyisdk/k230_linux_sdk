# K230D Linux SDK

嘉楠勘智 K230D AIoT 芯片相较于 CanMV K230，内存由 K230 的外挂 256MB/512MB 改为内封 128MB。K230 基于 64LP64 ABI 的软件内存开销过大，而玄铁新32位技术可以同时提升内存与缓存的存储密度，能够在内存上发挥更大的性能优势，兼顾成本与性能。

K230D Linux SDK 在 K230 64LP64 ABI 的 SDK 基础上，增加了 64ILP32 ABI 的 SDK，可以支持构建 64ILP32 和 64LP64 两种系统。经过初步的性能对比，新32位 RuyiSDK 内存开销下降 30%，同时双向链表的遍历性能提升 20%，SDK中给出了相关的内核模块文件。

在刚举办的 RISC-V Day Tokyo 2024 Summer 上，我们展示了这一开源工作成果并与大家交流，64ILP32 有效降低内存开销并提升缓存性能，这可以提高已大量上市的 RV64 小内存芯片的产品竞争力。

![](image/1.jpg)
![](image/2.jpg)

> 请优先使用 [预编译镜像](https://github.com/ruyisdk/k230_linux_sdk/releases)

> 本文档以 k230d_canmv_ilp32_defconfig 配置为例，如果使用其他配置，请将 k230d_canmv_ilp32_defconfig 替换为正确的名称。此 SDK 支持的所有配置，都可以在 buildroot-overlay/configs 目录中找到。

## 构建准备

1. 下载工具链：

从 `https://www.xrvm.cn/community/download?id=4333581795569242112` 下载 `Xuantie-900-gcc-linux-6.6.0-glibc-x86_64-V2.10.1-20240712.tar.gz` toolchain 并解压工具链到 `/opt/toolchain` 路径下 :

```bash
mkdir -p /opt/toolchain
tar -zxvf Xuantie-900-gcc-linux-6.6.0-glibc-x86_64-V2.10.1-20240712.tar.gz -C /opt/toolchain
```

2. 安装依赖项：

```bash
apt-get install -y   git sed make binutils build-essential diffutils gcc  g++ bash patch gzip \
        bzip2 perl  tar cpio unzip rsync file  bc findutils wget  libncurses-dev python3  \
        libssl-dev gawk cmake bison flex  bash-completion
```

3. 为 64ILP32 构建准备专用工具链 `riscv-gnu-toolchain-rv64ilp32` 工具链：

> k230d_canmv_ilp32_defconfig 需要 ubuntu 22.04 或 24.04 并安装 rv64ilp32 工具链。

```bash
wget -c https://github.com/ruyisdk/riscv-gnu-toolchain-rv64ilp32/releases/download/2024.06.25/riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.06.25-nightly.tar.gz
mkdir -p /opt/toolchain/riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.06.25/
tar -xvf riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.06.25-nightly.tar.gz -C /opt/toolchain/riscv64ilp32-elf-ubuntu-22.04-gcc-nightly-2024.06.25/
```

## 构建

在构建之前，请先下载或clone本仓库源码：

```bash
git clone https://github.com/ruyisdk/k230_linux_sdk.git
cd k230_linux_sdk
```

构建 64ILP32 ：

```bash
#build k230d canmv (64ilp32 kernel and 32bit rootfs)
make CONF=k230d_canmv_ilp32_defconfig
```

构建 64LP64 ：

```bash
#build k230d canmv image (64lp64 kernel and 64bit rootfs)
make CONF=k230d_canmv_lp64_defconfig 
```

构建帮助 ：

```bash
make help # view help
```

> 如需构建帮助，可以执行 `make help`命令查看帮助

## 输出

构建输出在 `output` 目录下，根据执行的 defconfig 分别存放，64ILP32 和 64LP64 的镜像文件路径分别为：

```bash
# 64ILP32
output/k230d_canmv_ilp32_defconfig/images/sysimage-sdcard.img.gz

# 64LP64
output/k230d_canmv_lp64_defconfig/images/sysimage-sdcard.img.gz
```

获得镜像后，请解压文件，[刻录到 tf 卡](https://developer.canaan-creative.com/k230/zh/main/CanMV_K230_%E6%95%99%E7%A8%8B.html#id11)，将 tf 插入设备，设备上电即可开始使用。


更多SDK使用说明可以参考上游 [kendryte k230_linux_sdk](https://github.com/kendryte/k230_linux_sdk) 仓库说明。
