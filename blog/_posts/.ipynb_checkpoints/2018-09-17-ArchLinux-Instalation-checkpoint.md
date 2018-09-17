---
title: "This is ArchLinux - Installation Guide"
subtitle: "A quick introduction"
layout: post
tags : [archlinux, installation, guide]
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

My intention here is provide a simple installation guide that will help you to understand how Arch philosophy works. The [official installation guide](https://wiki.archlinux.org/index.php/Installation_Guide) is the better way to understand the full installation of Arch.

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

## Setup the environment

### Internet connection

The ArchLinux installation enables the `dhcpcd` daemon for wired network devices on boot. The connection may be verified with ping:

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

We finished the environment to create the ArchLinux installation.

## Installing the system 

ArchLinux is an rolling release distribution which means that we don't have an specific version. So, to install the entire system with a minimal base package we can do

    pacstrap /mnt base base-devel

In this moment we're downloading the entire system and create the tree of system, with all directories as `\home`, `\etc`, `...`, `\var`.

### Generate fstab
We now have to specify to our computer the mount order, for example, which partition has to be mounted as `\boot` partition or `\`. It's can be done running:

    genfstab -U /mnt >> /mnt/etc/fstab

After this command the installation it's almost finished, we have just to install the bootloader, to boot the system properly.

### Enter the new system
Once installed, we have to setup the bootloader, but we can configure the entire installation as hostname, system clock, locale, new users, etc. I'll put this as optional to keep the installation simple.

    arch-chroot /mnt 

If you wanna do it, following the next section, if not, you can skip to setup the `mkinitcpio`.

#### Optional configuration
This is some simple configurations that can be done after the installation, but you can do right now.

##### Setup system clock
In my case, I live in Fortaleza/CE - Brazil, here is the way that I set my time:

    ln -s /usr/share/zoneinfo/America/Fortaleza /etc/localtime
    hwclock --systohc --utc

You can see the right locale to your place [here](https://wiki.archlinux.org/index.php/Time#Time_zone).

##### Set the hostname
The hostname is your machine name's, for example `belchior`, and you can set as follows

    echo belchior > /etc/hostname

##### Generate locale
This is how your computer will setup the language and characters. Uncomment wanted locales in /etc/locale.gen

    vim /etc/locale.gen
    locale-gen
    localectl set-locale LANG=en_US.UTF-8

To avoid problems with `gnome-terminal` set locale system wide

    echo LANG=en_US.UTF-8 >> /etc/locale.conf
    echo LC_ALL= >> /etc/locale.conf

Pay attention to the UTF-8. Capital letters!

##### Add user
Here you can add differents users to your machine

    useradd -m -g users -G wheel <username>
    passwd <username>
    
You can setup the new user to install as administrator

    EDITOR=nano visudo
    
Inside visudo, find the line that says *"Uncomment to allow members of group wheel to execute any command"*. Just remove the comment from **#%wheel ALL=(ALL) ALL**. It should now look like this:
    
    %wheel ALL=(ALL) ALL 

Then CTRL + X, now it should prompt you if you want to save, press Y. Now wheel has proper permissions.

##### Set password for root

    passwd
    
##### Starting `dhcpcd` service on boot
This will provide an dynamic internet IP to your machine

    systemctl enable dhcpcd.service
    
#### Installing bootloader
I chose the most common bootloader, [**grub**](https://wiki.archlinux.org/index.php/GRUB), as always you can chose whatever you want, this is ArchLinux philosophy. To install `grub` you can just type

    pacman -S  grub efibootmgr

Once `grub` was installed you have to install grub directories on your `\boot` partition

    grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub 
    
Now you can create the configuration file the will boot your system
    
    grub-mkconfig -o /boot/grub/grub.cfg

### You've finished
Yeah, congrats! You've finished the ArchLinux installation, you can exit from the installed system just typing

    exit

Reboot into the new system, don't forget to remove the cd/usb.

    reboot

## After installation

If you finished all this tutorial, probabily you want to install some interface as [Gnome](https://wiki.archlinux.org/index.php/GNOME). To do this you have to install some packages as `X-org`

    pacman -S xorg xorg-xinit gnome gnome-shell
    
After the installation just start the [GDM](https://wiki.archlinux.org/index.php/GDM) service that's provides the graphical managements of users.

    systemctl enable gdm.service
    
Now, reboot one more time and the installation will be finished.

Best regards.