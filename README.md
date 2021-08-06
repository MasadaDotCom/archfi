# archfi Full Disk Encryption

Just a simple bash script wizard to install Arch Linux after you have booted on the official Arch Linux install media.

With this script, you can install Arch Linux with two simple terminal commands.

This wizard is made to install minimum packages (Base, bootloader and optionally archdi).

At the end of this wizard, you can install or launch [archdi](https://github.com/MatMoul/archdi) (Arch Linux Desktop Install) to install and configure desktop packages.

You can watch my videos to see how to use it [here](https://www.youtube.com/playlist?list=PLytHgIKLV1caHlCrcTSkm5OF2WSVI1_Sq).

## How to use

First, boot with the [last Arch Linux image](https://www.archlinux.org/download/) with a [bootable device](https://wiki.archlinux.org/index.php/USB_flash_installation_media).

Then make sure you have Internet connection on the Arch iso. If you have a wireless connection the [`iwctl`](https://wiki.archlinux.org/index.php/Iwd#iwctl) command might be useful to you. You can also read the [Network configuration](https://wiki.archlinux.org/index.php/Network_configuration) from the Arch Linux guide for more detailed instructions


Parted -a optimal /dev/sda

Mklabel gpt

Unit mib

Mkpart.fat -F32 primary 1 513

set 1 BOOT on

set 1 esp on

Mkpart.ext4 primary 513 -1

set 2 lvm on

p

q



Parted -a optimal /dev/sdb

Mklabel gpt

Unit mib

Mkpart.ext4 primary 1 -1

Set 1 lvm on

p

q


Parted -a optimal /dev/sdc

Mklabel gpt

Unit mib

Mkpart.ext4 primary 1 -1

Set 1 lvm on

p

q

(Do this ^ for all disks you wish to merge)



Modprobe dm-crypt


git clone git://git.kernel.org/pub/scm/utils/mdadm/mdadm.git


modprobe mdadm


mdadm --create --verbose /dev/md0 --level=linear --raid-devices=3 /dev/sda2 /dev/sdb1 /dev/sdc1


#cryptsetup -y -v -c serpent-xts-plain64be -h whirlpool -s 512 -i 5000 --use-urandom luksFormat /Dev/md0 (for luks, add "...luks --type luks2 /dev/md0 for the newest luks format) 

#cryptsetup luksOpen /dev/md0 cryptlvm

#pvcreate /dev/mapper/cryptlvm

#vgcreate vg /dev/mapper/cryptlvm

#lvcreate -L +40G vg -n zram0

#lvcreate -L +100G vg -n root

#lvcreate --extents +95%FREE vg -n home


DO NOT ADD A FILESYSTEM TO LOGICAL VOLUMES OR LEAVE LOGICAL MOUNTED WHEN YOU BOOT INTO ARCHFI, ARCHFI WILL NOT BE ABLE TO PROPERLY MOUNT THEM, AND YOU WONT BE ABLE TO GENERATE FILESYSTEM HOOKS!


#modprobe zram

#echo lz4 > /sys/block/zram0/comp_algorithm

#echo 40G > /sys/block/zram0/disksize

#mkswap --label zram0 /dev/mapper/vg-zram0

#swapon --priority 100 /dev/mapper/vg-zram0

#swapoff /dev/mapper/vg-zram0


Then download the script with from the command line:

    curl -LO archfi.sf.net/archfi

If SourceForge is down, use this instead:

    curl -LO matmoul.github.io/archfi

Finally, launch the script:

    sh archfi


--- DO NOT ATTEMPT TO USE BTRFS FILESYSTEM AS IT IS NOT FULLY COMPATIBLE WITH ANY VERSION OF RAID!---

once you reach disk partitioning/mounting, use only /dev/mapper/vg- for all but boot partition (ex. SDA1) otherwise archfi will delete the raid, lvm & luks partitions, and you'll have to start over, as archfi is not locally compatible with lvm, custom encryption or raid filesystems.

#MOUNT /DEV/SDA1 @ /BOOT [FAT32,EXT4]

#MOUNT /DEV/MAPPER/VG-ZRAM0 @ SWAP [SWAP]

#MOUNT /DEV/MAPPER/VG-ROOT @ ROOT [EXT2,EXT3, EXT4]

MOUNT /DEV/MAPPER/VG-HOME @ HOME [EXT2, EXT3, EXT4]


Then follow the on-screen instructions to completion.

If you require extra help, visit the provided video playlist and follow my example.

## More custom install

    sh archfi -cpl {URL of your custom package list}

You can find a sample custom package list file in the samples folder.

## For developers

You can test your script with this command :

    sh archfi -t {githubusername} {branchname}

Alternatively if you wish to install manually, but don't want to install all the packages base post Install...
"archdi.sf.net/archdi"

Example :

    sh archfi -t matmoul master
