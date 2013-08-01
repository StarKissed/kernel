Linux Kernel for SHIELD
=======================
This is a temporary project that maintains a version of the Linux kernel that can boot in NVIDIA's SHIELD. It will be removed once all necessary patches have been approved and merged upstream.

WARNING: this branch will be regularly rebased to the latest upstream and to update the set of patches in progress. Be aware of this if you plan to base your work on it.

What's here
-----------
Not much really - this is just the (hopefully) latest Linux kernel with a few extra patches that add SHIELD's device tree, custom kernel configuration, and a few features/hacks.

What's (not yet) supported
--------------------------
Display: currently uses simplefb. tegra-drm support should appear in the future.

Wifi, bluetooth: not working yet.

All the rest should be working as expected. You can access eMMC and SD, although SD is very slow.

How to compile and boot
-----------------------
Use the tegra\_roth\_defconfig configuration and build zImage and dtbs. The kernel configuration uses appended DTB so you can boot the kernel without altering any system partition. To append the DTB to your kernel image:

    $ cat arch/arm/boot/zImage arch/arm/boot/dts/tegra114-roth.dtb >zImage_dtb

The kernel boots into a ramdisk by default, which will be provided by fastboot. You can obtain a basic Busybox ramdisk here: https://github.com/Gnurou/bbfs

The go into the bootloader to boot the image using fastboot. Your bootloader must be unlocked for this to work:

    $ fastboot boot zImage_dtb /path/to/ramfs.img.gz

Note that doing this will not alter your Android system.

How to boot into a loopback-mounted root filesystem
---------------------------------------------------

While you want to boot into a full-fledged Linux, you probably also want to preserve your Android installation and still be able to use it. It is possible to do so by storing your root filesystem into a disk image file, and mounting it as the root filesystem.

The creation of the filesystem image depends on your target distribution and is outside the scope of this document ; we will just assume you have a disk image named `linux_root.img` copied under `/sdcard`. You can then use a different Busybox initramfs that will look for this image file, mount it as root, and continue booting from it:

https://github.com/linux-shield/bbinitramfs

Build the ramdisk, and boot it as usual:

    $ fastboot boot zImage_dtb /path/to/ramfs.img.gz

If everything goes well, the distribution in your image file should boot after a few seconds. If it doesn't, check that the `init` script is doing things the way it should.

Disclaimer
----------
Tampering with your SHIELD is dangerous and you should only do this if you perfectly understand what you are doing. Unlocking your SHIELD will void its warranty and exposes it to being permanently broken, so be wise.
