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

### Copy to a USB drive

    dd if=archlinux.img of=/dev/sdX bs=16M && sync # on linux

### Boot from USB drive

If the usb fails to boot, make sure that secure boot is disabled in the BIOS configuration.

## This assumes a wifi only system...

    wifi-menu

## Create partitions

    cgdisk /dev/sda
    1 512MB EFI partition # Hex code ef00
    2 100% size partiton # (to be encrypted) Hex code 8300
    You might have to use hexcode: 8e00
    8300 causes errors sometimes.
   
### Create EFI partition

    mkfs.vfat -F32 -n EFI /dev/sda1

### Setup the encryption of the system with 256 bit effective size

Note: Many NVMe drives can exceed 2GB/s, consider your crypto algorithm wisely, review `cryptsetup benchmark`, the defaults are viewable end of  `cryptsetup --help`, defaults are commonly the fastest with good security from my experience with cryptsetup (AES 256, sha256, 2000ms)
  
    cryptsetup --use-random luksFormat /dev/sda2
    
    or if you want better encryption like me: Use cryptsetup -c aes-xts-plain64 -s 512 -h sha384 -i 2500 --use-random luksFormat /dev/sdXX
    
    cryptsetup luksOpen /dev/sda2 luks
    
        Also when it asks "are you sure, make sure you type captial yes. example: YES


### Create encrypted partitions

This creates one partions for root, modify if /home or other partitions should be on separate partitions

    pvcreate /dev/mapper/luks
    vgcreate vg0 /dev/mapper/luks
    lvcreate --size 16G vg0 --name swap
    lvcreate -l +100%FREE vg0 --name root

### Create filesystems on encrypted partitions
  
    mkfs.ext4 -L root /dev/mapper/vg0-root
    mkswap /dev/mapper/vg0-swap

## Mount the new system

    mount /dev/mapper/vg0-root /mnt # /mnt is the installed system
    swapon /dev/mapper/vg0-swap # Not needed but a good thing to test
    mkdir /mnt/boot
    mount /dev/sda1 /mnt/boot

## Install the system 

Also includes stuff needed for starting wifi when first booting into the newly installed system
Unless vim and zsh are desired these can be removed from the command. Dialog is needed by wifi-menu

    pacstrap /mnt base base-devel git sudo efibootmgr dialog wpa_supplicant tmux intel-ucode networkmanager net-tools

Note: I removed "zsh" and "neovim", because I like 'bash' and 'vi'. I also added networkmanager so ethernet will work on boot. 
May 27 EDIT: Added net-tools so you can use ifconfig on boot to find your IPv4 for SSH.

## Generate fstab

    genfstab -pU /mnt | tee -a /mnt/etc/fstab

### Make /tmp a ramdisk (add the following line to /mnt/etc/fstab)
tmpfs	/tmp	tmpfs	defaults,noatime,mode=1777	0	0

Also change relatime on all non-boot partitions to noatime (reduces wear if using an SSD)

## Enter the new system

    arch-chroot /mnt /bin/bash

## Setup system clock

    ln -s /usr/share/zoneinfo/America/Chicago /etc/localtime
    hwclock --systohc --utc

## Set the hostname

    echo MYHOSTNAME > /etc/hostname

## Generate locale

Uncomment wanted locales in /etc/locale.gen

    vim /etc/locale.gen
    locale-gen
    localectl set-locale LANG=en_US.UTF-8

To avoid problems with gnome-terminal set locale system wide
Do NOT set LC_ALL=C. It overrides all the locale vars and messes up special characters
Pay attention to the UTF-8. Capital letters !

    echo LANG=en_US.UTF-8 >> /etc/locale.conf
    echo LC_ALL= >> /etc/locale.conf


## Set password for root

    passwd

## Add user

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
