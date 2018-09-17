---
title: "This is ArchLinux - Instalation Guide"
subtitle: "A quick introduction"
layout: post
tags : [archlinux, instalation, guide]
---

## Welcome to freedom

When I started to work with computers and Linux based systems, I found myself in Ubuntu distributions, Ubuntu 10.04 was one of the bests. With time and experience I tried Debian OS, Elementary OS, LinuxMint OS, RedHat, but all theses systems seems to be the same. I don't know but I can't stop to search for the best distro for me. Until the ArchLinux show up!

<center>
    <figure>
        <img src="/blog/figs/2018-08-17-archlinux-instalation/cover.png"  width="304" height="228" alt="Cover" >
        <br>
        <em> <a href="https://wiki.archlinux.org/index.php/Arch_Linux"> Read more </a>ArchLinux</em> page.
    </figure>
</center>    

My intention here is provide a simple instalation guide that will help you to understand how Arch philosophy works. The [official installation guide](https://wiki.archlinux.org/index.php/Installation_Guide) is the better way to understand the full instalation of Arch.

## Download the Arch ISO
First of all we can download the ISO from Archlinux website

* Image from https://www.archlinux.org/download/

The image can be burned to a CD, mounted as an ISO file, or be directly written to a USB stick using a utility like `dd`.

### Copy to a USB drive

    dd bs=4M if=/path/to/archlinux.iso of=/dev/sdx status=progress oflag=sync
Run the following command, replacing `/dev/sdx` with your drive, e.g. `/dev/sdb`. (Do not append a partition number, so **do not use something like /dev/sdb1**).

### Boot from USB drive

The new common way to install operational systems as Linux Systems or even Microsoft Windows is using [UEFI system](https://wiki.archlinux.org/index.php/Unified_Extensible_Firmware_Interface) (You can read more about it here). Here we'll use UEFI mode, make sure that secure boot is disabled in the BIOS configuration. To see if you are booted on UEFI mode, just list the directory

    ls /sys/firmware/efi/efivars
    
if the directory does not exist, the system may be booted in BIOS or CSM mode.

## Setup the enviromment

### Internet connection

The ArchLinux instalation enables the `dhcpcd` daemon for wired network devices on boot. The connection may be verified with ping:

    ping archlinux.org

If you have a wifi connection, you can try `wifi-menu` and setup a wireless connection.

    wifi-menu

### Create partitions

This part can be a realy trick one. ArchLinux need just to partition, the `\boot` partition (that needs to be format as **FAT32**) and the `\`, the root partition. You can create more as `\home` partition that keeps the users data.

To create the partitions I'll use `fdisk`, a simple manager to create a table partition. You can use `cfdisk` if you want. First, identify the label of yours partitions

    fdisk -l
    
the command above will show the disk attached to your computer such as `/dev/sda` or `/dev/nvme0n1`. Now you can create a new table of partition as GPT partition table, with to partition, `\boot` and `\`.

    fdisk /dev/sda
you can learn about fdisk [here](https://wiki.archlinux.org/index.php/Fdisk). Basically you have to create a 500 Mb partition to that will be a boot partition and the rest to root partition. The result will be something like this:

    Disk /dev/sda: 465.8 GiB, 500107862016 bytes, 976773168 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disklabel type: gpt
    Disk identifier: 7D918645-8068-4E41-AB42-A411242F4546

    Device         Start       End   Sectors   Size Type
    /dev/sda1       2048   1023999   1021952   499M EFI System
    /dev/sda2    1023999 976773119 659331072 464.4G Linux filesystem


### Format the partitions
Once the partitions have been created, each must be formatted with an appropriate file system. For example, to format the boot partition on `/dev/sda1` with `FAT32`, run:

    mkfs.vfat -F32 -n EFI /dev/sda1

and to format the root partition on `/dev/sda2` with `ext4`, run:

    mkfs.ext4 /dev/sda2

### Mount the new system
Now it's time to mount the file system on the root partition to `/mnt`, for example:

    mount /dev/sda2 /mnt
    
Once the file system was mounted, we can create a different folder that will contain the `boot` files

    mkdir /mnt/boot
    mount /dev/sda1 /mnt/boot

We finished the enviromment to create the ArchLinux instalation.

## Installing the system 

ArchLinux is an rolling release distribution which means that we don't have an specific version. So, to install the entire system with a minimal base package we can do

    pacstrap /mnt base base-devel

In this moment we're downloading the entire system and create the tree of system, with all directories as `\home`, `\etc`, `...`, `\var`.

### Generate fstab
We now have to specify to our computer the mount order, for example, which partition has to be mounted as `\boot` partition or `\`. It's can be done running:

    genfstab -U /mnt >> /mnt/etc/fstab

After this command the instalation it's almost finished, we have just to install the bootloader, to boot the system properly.

### Enter the new system
Once installed, we have to setup the bootloader, but we can configure the entire instalation as hostname, system clock, locale, new users, etc. I'll put this as optional to keep the instalation simple.

    arch-chroot /mnt 

#### Optional configuration

##### Setup system clock

    ln -s /usr/share/zoneinfo/America/Chicago /etc/localtime
    hwclock --systohc --utc

##### Set the hostname

    echo MYHOSTNAME > /etc/hostname

##### Generate locale

Uncomment wanted locales in /etc/locale.gen

    vim /etc/locale.gen
    locale-gen
    localectl set-locale LANG=en_US.UTF-8

To avoid problems with gnome-terminal set locale system wide
Do NOT set LC_ALL=C. It overrides all the locale vars and messes up special characters
Pay attention to the UTF-8. Capital letters !

    echo LANG=en_US.UTF-8 >> /etc/locale.conf
    echo LC_ALL= >> /etc/locale.conf

##### Add user

    groupadd MYUSERNAME
    useradd -m -g MYUSERNAME -G wheel,storage,power,network,uucp -s /bin/bash MYUSERNAME
    passwd MYUSERNAME
    
    or
    
    useradd -m -G wheel -s /bin/bash <username>
    passwd <username>
    EDITOR=nano visudo
    
    ###Inside visudo, find the line that says "## Uncomment to allow members of group wheel to execute any command".###
    ###Remove the comment from #%wheel ALL=(ALL) ALL. It should now look like this:###
    
%wheel ALL=(ALL) ALL 


    ###Then CTRL + X, now it should prompt you if you want to save, press Y. Now wheel has proper perms. 
    ###"sudo pacman -Syu" will now work properly when you run it to update with your wheel grouped username.###



##### Set password for root

    passwd

    
## Configure mkinitcpio with modules needed for the initrd image

    vim /etc/mkinitcpio.conf

* Add 'ext4' to MODULES
* Add 'encrypt' and 'lvm2' to HOOKS before filesystems
* Add 'resume' after 'lvm2' (also has to be after 'udev')

## Regenerate initrd image

    mkinitcpio -p linux

## Setup systembootd (grub will not work on nvme at this moment)

    bootctl --path=/boot install

## Create loader.conf

    echo default arch >> /boot/loader/loader.conf
    echo timeout 5 >> /boot/loader/loader.conf

## Create arch.conf (or XYZ.conf for default XYZ in loader.conf)

    nvim /boot/loader/entries/arch.conf

## Add the following content to arch.conf

`<UUID>` is the the one of the raw encrypted device (/dev/sda2). It can be found with the `blkid` command

    title Arch Linux
    linux /vmlinuz-linux
    initrd /intel-ucode.img
    initrd /initramfs-linux.img
    options cryptdevice=UUID=<UUID>:vg0 root=/dev/mapper/vg0-root resume=/dev/mapper/vg0-swap rw intel_pstate=no_hwp

## Exit new system

    exit

## Unmount all partitions

    umount -R /mnt
    swapoff -a

# Reboot into the new system, don't forget to remove the cd/usb

    reboot
