# <span style='color:#00ffbf;'>Gentoo Installation 🐧</span>

<span style='background-color:#000000;'><span style='color:#00ffbf;'>**2nd Brain**</span> 🧠 <span style='background-color:#ff465d;'><span style='color:#ffff00;'>**P K N 👁**</span> 

<span style='color:#ffcb74;'>**INT** 🧠 | **XP** 🚀 | **STR** 🦾 | **VIT** 🔥 </span>

<span style='color:#17ffe2;'>

> The **obstacle** is the way...

-  _All it takes is a leap of faith_ **OR** _playful curiosity_

</span>

1. <span style='color:#ffcb2e;'>**Make a bootable USB** 📀</span>
- Download the minimal installation CD (amd64) and flash the .iso using Balena Etcher.

2. <span style='color:#ffcb2e;'>**Choose keyboard layout** ⌨️</span>
- Type `43` for US keyboard layout 🇺🇸. <!-- If you don't make a selection within 15 seconds its automatically selects the US keyboard layout -->

3. <span style='color:#ffcb2e;'>**Network Configuration 🌐**</span>

> Make sure to setup both **Ethernet** and **WiFi** adapters (just in case)

- **Pro Tip 🚀**: Use Ethernet to save time

••..Otherwise..••

`ifconfig` (auto network detection)

**Side Notes 🗒**: Wlan0 (WiFi), Eth0 (Ethernet)

Artix Method, assuming connmanctl is installed

`ip link set wlan0 up`

`connmanctl`

`scan wifi`

`services`

`agent on`

`connect wifi wifi_∆_∆_managed_psk`

`quit`

Handbook method (WiFi)

`ip link set dev wlp9s0`

The wlp9s0 is probably the name of your wlan

`iw dev wlp9s0 connect -w WifiName key 0:password`

<span style='color:#00ff80;'>**Connection test:** `ping -c  3 www.gentoo.org` </span>

4. <span style='color:#ffcb2e;'>**Partitioning 🗃**</span>

`fdisk -l` check the partitions on your system

`wipefs -a /partitionname`

The blank slate of Tabularasa

`parted -a optimal /dir`

The beginning of partition

`mklabel gpt`

GPT superceeds MBR

`unit mib`

MiB is megabaytes, previous commands just enabled us to specify that

`mkpart primary 1 3`

This could be read as. Partition 1-3MB

`name 1 grub`

Assign a name for our first partition and in this case grub

`set 1 bios_grub on`

This is a flag that has been passed on to the first partition. 

`mkpart primary 3 131`

This can be read as. Partition 3-131MB, although it will be approximately 128MB

`name 2 boot`

Second partitioned has been assigned the name boot

`mkpart primary 131 60000`

This can be read as. Partition size 131-60000MB (60GB) .

`name 3 swap`

This is the swap partition and the general rule of thumb is to have to approximately or relatively close to the amount of RAM your system has. In my case thats 64GB

`mkpart primary 60000 -1`

This can be read as. Use the rest of the disk space to make a part. 

`name 4 rootfs`

Rootfs is the file system, the place where our system lies. The Open space usable by the user

`print`

This will simply list all the partitions we created thus far. In our case its 4 followed by their names: grub, boot, swap and rootfs . 

`write`

`quit`

This will exit parted

`lsblk`

This will list all the partitions available on the machine.

`mkfs.fat -F 32 /dev/sda2`

Make a fat32 file system on the boot partition

`mkfs.ext4 /dev/sda4`

Make an ext4 file system on the rootfs partition

`mkswap /dev/sda3`

Make a swap file system on the swap partition

`swapon /dev/sda3`

Turns on the swap partition

`cd /mnt/gentoo`

*if network_devices directory shows up when you `ls` remove it. This only shows up if you are installing this from another Linux distro live CD like Manjaro. 

`mount /dev/sda4 /mnt/gentoo`

⚠️ Before you proceed to the next step, make sure to set the correct time and date.

`date  MMDDhhmmYYYY` 

Month, Day, hour, minute and Year. 24 hour time, if you install in the AM its a breeze.


5. <span style='color:#ffcb2e;'>**Stage 3**</span>

Installing the stage 3 tarball is easy as 

`wget <PASTED_STAGE_URL>`

Installs it into the gentoo directory

`tar xpvf file.tar.xz  --xattrs-include='*.*' --numeric-owner`

After extraction just `rm -rf file.tar.xz`
Because you won't need it anymore.

6. <span style='color:#ffcb2e;'>**Configure Compile Options**</span>

> Its all about editing etc/portage/make.conf

*The equivalent on Arch Linux would be editing the pacman.conf file 

For reference this directory is there for the rescue: mnt/gentoo/usr/share/portage/config/make.conf.example

` nano -w /mnt/gentoo/etc/portage/make.conf`

One of the recommended options for the flags 

``COMMON_FLAGS="-O2 -pipe -march=native" CFLAGS="${COMMON_FLAGS}" CXXFLAGS="${COMMON_FLAGS}"``

7. <span style='color:#ffcb2e;'>**Base System Installation**</span>

``mkdir --parents /mnt/gentoo/etc/portage/repos.conf``

Crete this file with the following directory, if it doenst exist

``cp /mnt/gentoo/usr/share/portage/config/repos.conf /mnt/gentoo/etc/portage/repos.conf/gentoo.conf``

``cp --dereference /etc/resolv.conf /mnt/gentoo/etc/``

``mount --types proc /proc /mnt/gentoo/proc``

``mount --rbind /sys /mnt/gentoo/sys``

``mount --make-rslave /mnt/gentoo/sys``

``mount --rbind /dev /mnt/gentoo/dev``

``mount --make-rslave /mnt/gentoo/dev``

`chroot /mnt/gentoo /bin/bash`

``source /etc/profile``

``export PS1="(chroot) ${PS1}"``


``mount /dev/sda2 /boot``


``emerge-webrsync``

`eselect profile list`

Usually preselected. 

``emerge --ask --verbose --update --deep --newuse @world``

Runs a system update similar to pacman -Syyu

``ls /usr/share/zoneinfo``

Displays all time zones

``echo "Europe/Brussels" > /etc/timezone``

This is how you would change the time zone

``emerge --config sys-libs/timezone-data``


``nano -w /etc/locale.gen``

Proceed to add the following to the file

```
en_US ISO-8859-1 

en_US.UTF-8 UTF-8
```

``locale-gen``

Generates the locale

``eselect locale set #``

The number had to correspond with en_US.utf8

``env-update && source /etc/profile && export PS1="(chroot) ${PS1}"``


Pro Tip 🚀: Using `emerge -q` speeds up the process for isntalling packages because it won't use up your RAM displaying all that compilation info on screen




8. <span style='color:#ffcb2e;'>**Kernel Configuration**</span>

``emerge --ask sys-kernel/gentoo-sources``


``ls -l /usr/src/linux``

`eselect kernel list`

`eselect kernel set #`

``emerge --ask sys-apps/pciutils``

``cd /usr/src/linux``

``make menuconfig``

This is the magic palace of choice. A GUI interface for your kernel needs - manual

``make && make modules_install``

After you are done with your selections run that

`make install`


``emerge --ask sys-kernel/genkernel``

``genkernel --install --kernel-config=/path/to/used/kernel.config initramfs``


``emerge --ask sys-kernel/linux-firmware``


``ls /boot/initramfs*``



9. <span style='color:#ffcb2e;'>**System Configuration**</span>

``nano -w /etc/conf.d/hostname``

``emerge --ask --noreplace net-misc/netifrc``




10. <span style='color:#ffcb2e;'>**System Tool Installation**</span>

11. <span style='color:#ffcb2e;'>**Bootloader Configuration**</span>

12. <span style='color:#ffcb2e;'>**Finalizing**</span>
