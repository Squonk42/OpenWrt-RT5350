OpenWrt-RT5350
==============

Patches to compile OpenWrt Linux on Ralink RT5350-based routers

## Introduction

RT5350-based routers are not yet supported in OpenWrt, not even yet in the bleeding edge trunk.

However, here are some experimental patches to the current OpenWrt trunk repository that should work.

These patches were originally developed for the Hame MPR-A1 router, but they also apply to its numerous clones and more generally to a lot of Ralink RT5350-based routers.

This comes from the fact that the RT5350 is a SoC ("System on Chip") that requires only a few external components to provide a working wireless router. So basically, all these designs are very similar, only differing in the SP Flash chip model or the way to control the USB overcurrent protection switch chip.

## Build Instructions

In order to build OpenWrt on an RT5350-based router, you need to:
* download the latest OpenWrt trunk sources from svn
* download the patches
* apply the patches
* choose your target/subtarget/profile for the build
* compile the firmware

This is achieved using the following code snippet:

     mkdir openwrt
     cd openwrt
     svn co svn://svn.openwrt.org/openwrt/trunk
     git clone https://github.com/Squonk42/OpenWrt-RT5350.git
     cd trunk
     patch -p0 <../OpenWrt-RT5350/openwrt_add_gd25q32_gd25q64_pm25lq032_flash_support.patch
     patch -p0 <../OpenWrt-RT5350/openwrt_add_rt3550_wlan_support.patch
     patch -p0 <../OpenWrt-RT5350/openwrt_hame_mpr-a1.patch
     make menuconfig

In the configuration menu, you need to select the following options:
* Target Ssytem: Ralink RT288x/RT3xxx
* Subtarget: RT305x based boards
* Target Profile: HAME MPR-A1

Then proceed to build:
     make -j x

... where "x" is the number of CPU on your PC + 1.

The first time you compile can take hours, since the toolchains is built first. Subsequent build only take a few minutes.

Then copy the the resulting image to your TFTP server root, so you can Flash it from the router's U-Boot bootloader:
     cp bin/ramips/openwrt-ramips-rt305x-mpr-a1-squashfs-sysupgrade.bin /tftpboot/