---
cmake_plat: x86_64
simulation_target: true
SPDX-License-Identifier: CC-BY-SA-4.0
SPDX-FileCopyrightText: 2020 seL4 Project a Series of LF Projects, LLC.
---
# PC99
seL4 runs on 32-bit ia32 and 64-bit x64 machines, on qemu and on
hardware.

## Status
* Works with Dijkstra and window client and [this cable]([url](https://www.amazon.ca/gp/product/B075YHFMC7/)).
* Hemlock: Garbled data
* leap-403: no op on IPMI

## Simulation

Checkout the sel4test project using repo as per seL4Test
```bash
repo init -u https://github.com/seL4/sel4test-manifest.git
repo sync
mkdir cbuild
cd cbuild
../init-build.sh -DPLATFORM=x86_64  && ninja
```
Please substitute `-DPLATFORM=ia32` in place of `-DPLATFORM=x86_64` if you would
prefer to build 32-bit binaries.

## Real Hardware
 When running on real hardware console output will be
over serial. You will need to plug a serial cable into your machine to
see any output.

The build system produces a multiboot compliant image for x86; a grub2
stanza is here, but we usually boot via PXE for convenience.
```
menuentry "Load seL4 VM" --class os {
    load_video
    insmod gzio
    insmod part_msdos
    insmod ext2
    set root='(hd0,msdos2)'
    multiboot /boot/sel4kernel
    module /boot/sel4rootserver
}
```

Booting via PXEBOOT using
[syslinux PXELINUX](http://www.syslinux.org/wiki/index.php?title=PXELINUX) requires setting up a tftp and dhcp server on the network
that the machine you want to boot is connected to. This
[debian page](https://debian-administration.org/article/478/Setting_up_a_server_for_PXE_network_booting) explains how to setup a tftp and dhcp server, and the
[syslinux](http://www.syslinux.org/wiki/index.php?title=Download)
site has a download for pxelinux which we load over PXEBOOT
that then can load seL4. The configuration for pxelinux.cfg/default is
provided below.
```
label seL4
        kernel mboot.c32
        append kernel-ia32-pc99 --- apps-ia32-pc99
```

## Booting off USB with syslinux

Use syslinux to create a bootable USB stick as follows.

Assuming your USB flash drive is at `/dev/sdb` with a FAT partition at
`/dev/sdb1`:
```bash
install-mbr /dev/sdf
syslinux --install /dev/sdf1
mount /dev/sdf1 /mnt
cp images/sel4test-driver-image-ia32-pc99 /mnt/rootserver
cp images/kernel-ia32-pc99 /mnt/sel4kernel
cat > /mnt/syslinux.cfg <<EOF
SERIAL 0 115200
DEFAULT seL4test
LABEL seL4test
    kernel mboot.c32
    append sel4kernel --- rootserver
EOF
cp /usr/lib/syslinux/modules/bios/mboot.c32 /mnt
cp /usr/lib/syslinux/modules/bios/libcom32.c32 /mnt
umount /mnt
```

Use `fdisk` to make sure the first partition is bootable.

And you're done. Output will come on the serial port

The lab machines we have tried this on is:
- hemlock 
- minsky


### hemlock
Spec: i7, 16G ram
MotherBoard: [Manual](https://dlcdnets.asus.com/pub/ASUS/mb/LGA1151/PRIME_H270M-PLUS/E12027_PRIME_H270M-PLUS_UM_WEB.pdf) 

#### Serial

![image](https://user-images.githubusercontent.com/22774472/170778599-517aeb24-2d1c-405b-a7f6-13d0feda4889.png)

Here is zoomed in picture of connector-11.
![image](https://user-images.githubusercontent.com/22774472/170778542-7a25dfa5-5dfd-40b3-98c9-5dfdf8ca9ee0.png)


We are using the [adafruit](https://www.adafruit.com/product/954) serial to USB cable.
| Color | Cable Side | Board Side | 
|-------|------------|------------| 
| Green |     TX     | RXD | 
| White |     RX     | TXD | 
| Black |     GND    | GND | 


### dijkstra
Spec: i5, 10 GB ram

#### Serial
Has a DB9 serial port at the back.
![image](https://user-images.githubusercontent.com/22774472/170782501-d5d1e64f-16bc-4b48-adf2-c585fc4d9c28.png)


We are using the [adafruit](https://www.adafruit.com/product/954) serial to USB cable.
| Color |  Cable Side | Board Side | 
|-----------|-----------| -----------|
| Green |    TX     | RXD - Pin2 | 
| White |     RX     | TXD - Pin3| 
| Black |    GND    | GND - Pin 5 | 


> When using DTECH Cable instead of ADAFRUIT. Remember that Green and white are flipped.
> Finally worked with [this cable]([url](https://www.amazon.ca/gp/product/B075YHFMC7/))
> ![image](https://user-images.githubusercontent.com/22774472/172685941-4a59fc6f-c225-40c8-8776-a38dc606bd62.png)




## Todo
* Try with leap-405: IPMI console seems to reading from ttys0. Tried changing the port via commandline to sel4 for port0 and port1, but there was no op on the ipmi.
* Try PXE boot with leap-405
