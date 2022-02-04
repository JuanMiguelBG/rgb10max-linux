**For OGA/OGS/MAX/MAX2.**

Build
=====
- On Ubuntu desktop

Required packages
```bash
sudo apt-get update && sudo apt-get install -y git lzop build-essential gcc bc libncurses5-dev libc6-i386 lib32stdc++6 zlib1g:i386 p7zip-full p7zip-rar libssl-dev python-is-python3

```
Toolchain 6.3.1
```bash
sudo mkdir -p /opt/toolchains && wget https://releases.linaro.org/components/toolchain/binaries/6.3-2017.05/aarch64-linux-gnu/gcc-linaro-6.3.1-2017.05-x86_64_aarch64-linux-gnu.tar.xz && sudo tar Jxvf gcc-linaro-6.3.1-2017.05-x86_64_aarch64-linux-gnu.tar.xz -C /opt/toolchains/
```
Add lines below to ~/.bashrc
```bash
export ARCH=arm64
export CROSS_COMPILE=aarch64-linux-gnu-
export PATH=/opt/toolchains/gcc-linaro-6.3.1-2017.05-x86_64_aarch64-linux-gnu/bin/:$PATH
```
Run the command below to apply the setting.
```bash
source ~/.bashrc
```

Verify the toolchain version to confirm compatibility.  It should show `gcc version 6.3.1 20170404 (Linaro GCC 6.3-2017.05)` in the last line output below Thread model.
```bash
aarch64-linux-gnu-gcc -v
```

Clone this git and build the kernel
```bash
git clone https://github.com/JuanMiguelBG/rgb10max-linux.git
cd linux
make odroidgoa_defconfig
make
```

Once the kernel build is done, you should have an Image and the following dtb files.  These will go to the /boot partition:

- arch/arm64/boot/Image 
```bash
ls arch/arm64/boot/Image
```

- arch/arm64/boot/dts/rockchip/rk3326-odroidgo2-linux.dtb
- arch/arm64/boot/dts/rockchip/rk3326-odroidgo2-linux-v11.dtb
- arch/arm64/boot/dts/rockchip/rk3326-odroidgo3-linux.dtb
```bash
ls arch/arm64/boot/dts/rockchip/rk3326-odroidgo*.dtb
```


New kernel driver modules will need to be copied to the EXT4 partition:
```bash
sudo make modules_install ARCH=arm64 INSTALL_MOD_PATH=./<ext4 partition location> && sync
```

Make sure to install the compatible libmali video drivers to the EXT4 partition:
```bash
cd video_drivers
7z e rk3326_r13p0_gbm_with_vulkan_and_cl.7z
sudo cp libmali.so_rk3326_gbm_arm64_r13p0_with_vulkan_and_cl  ./<ext4 partition location>/usr/local/lib/aarch64-linux-gnu/libmali-bifrost-g31-rxp0-gbm.so
sudo cp libmali.so_rk3326_gbm_arm32_r13p0_with_vulkan_and_cl  ./<ext4 partition location>/usr/local/lib/arm-linux-gnueabihf/libmali-bifrost-g31-rxp0-gbm.so
```


Thanks
======

Special thanks to DiegoFnz (MAX2 master), [Christian Haitian](https://github.com/christianhaitian/linux) and [Souhtoz](https://github.com/southoz/linux/tree/odroidgoA-4.4.y).

