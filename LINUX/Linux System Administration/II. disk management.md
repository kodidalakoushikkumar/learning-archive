# II. disk management
## 4.1. terminology

### 4.1.1. platter, head, track, cylinder, sector

Data is commonly stored on magnetic or optical disk platters. The platters are rotated (at
high speeds). Data is read by heads, which are very close to the surface of the platter, without
touching it! The heads are mounted on an arm (sometimes called a comb or a fork).
Data is written in concentric circles called tracks. Track zero is (usually) on the outside.
The time it takes to position the head over a certain track is called the seek time. Often
the platters are stacked on top of each other, hence the set of tracks accessible at a certain
position of the comb forms a cylinder. Tracks are divided into 512 byte sectors, with more
unused space (gap) between the sectors on the outside of the platter.
When you break down the advertised access time of a hard drive, you will notice that most
of that time is taken by movement of the heads (about 65%) and rotational latency (about
30%).

### 4.1.2. ide or scsi

Actually, the title should be ata or scsi, since ide is an ata compatible device. Most desktops
use ata devices, most servers use scsi.

### 4.1.3. ata

An ata controller allows two devices per bus, one master and one slave. Unless your
controller and devices support cable select, you have to set this manually with jumpers.
With the introduction of sata (serial ata), the original ata was renamed to parallel ata.
Optical drives often use atapi, which is an ATA interface using the SCSI communication
protocol.

### 4.1.4. scsi

A scsi controller allows more than two devices. When using SCSI (small computer system
interface), each device gets a unique scsi id. The scsi controller also needs a scsi id, do not
use this id for a scsi-attached device.
Older 8-bit SCSI is now called narrow, whereas 16-bit is wide. When the bus speeds was
doubled to 10Mhz, this was known as fast SCSI. Doubling to 20Mhz made it ultra SCSI.
Take a look at http://en.wikipedia.org/wiki/SCSI for more SCSI standards.

### 4.1.5. block device

Random access hard disk devices have an abstraction layer called block device to enable
formatting in fixed-size (usually 512 bytes) blocks. Blocks can be accessed independent of
access to other blocks.
```
[root@centos65 ~]# lsblk
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda 8:0 0 40G 0 disk
--sda1 8:1 0 500M 0 part /boot
--sda2 8:2 0 39.5G 0 part
  --VolGroup-lv_root (dm-0) 253:0 0 38.6G 0 lvm /
  --VolGroup-lv_swap (dm-1) 253:1 0 928M 0 lvm [SWAP]
sdb 8:16 0 72G 0 disk
sdc 8:32 0 144G 0 disk
```
A block device has the letter b to denote the file type in the output of ls -l.
```
[root@centos65 ~]# ls -l /dev/sd*
brw-rw----. 1 root disk 8, 0 Apr 19 10:12 /dev/sda
brw-rw----. 1 root disk 8, 1 Apr 19 10:12 /dev/sda1
brw-rw----. 1 root disk 8, 2 Apr 19 10:12 /dev/sda2
brw-rw----. 1 root disk 8, 16 Apr 19 10:12 /dev/sdb
brw-rw----. 1 root disk 8, 32 Apr 19 10:12 /dev/sdc
```
Note that a character device is a constant stream of characters, being denoted by a c in ls -
l. Note also that the ISO 9660 standard for cdrom uses a 2048 byte block size.
Old hard disks (and floppy disks) use cylinder-head-sector addressing to access a sector
on the disk. Most current disks use LBA (Logical Block Addressing).

### 4.1.6. solid state drive

A solid state drive or ssd is a block device without moving parts. It is comparable to flash
memory. An ssd is more expensive than a hard disk, but it typically has a much faster access
time.

## 4.2. device naming

### 4.2.1. ata (ide) device naming

All ata drives on your system will start with /dev/hd followed by a unit letter. The master
hdd on the first ata controller is /dev/hda, the slave is /dev/hdb. For the second controller,
the names of the devices are /dev/hdc and /dev/hdd.

# TABLE

It is possible to have only /dev/hda and /dev/hdd. The first one is a single ata hard disk, the
second one is the cdrom (by default configured as slave).

### 4.2.2. scsi device naming

scsi drives follow a similar scheme, but all start with /dev/sd. When you run out of letters
(after /dev/sdz), you can continue with /dev/sdaa and /dev/sdab and so on. (We will see later
on that lvm volumes are commonly seen as /dev/md0, /dev/md1 etc.)
Below a sample of how scsi devices on a Linux can be named. Adding a scsi disk or raid
controller with a lower scsi address will change the naming scheme (shifting the higher scsi
addresses one letter further in the alphabet).

# TABLE

A modern Linux system will use /dev/sd* for scsi and sata devices, and also for sd-cards,
usb-sticks, (legacy) ATA/IDE devices and solid state drives.

## 4.3. discovering disk devices

### 4.3.1. fdisk

You can start by using /sbin/fdisk to find out what kind of disks are seen by the kernel.
Below the result on old Debian desktop, with two ata-ide disks present.
```
root@barry:~# fdisk -l | grep Disk
Disk /dev/hda: 60.0 GB, 60022480896 bytes
Disk /dev/hdb: 81.9 GB, 81964302336 bytes
```
And here an example of sata and scsi disks on a server with CentOS. Remember that sata
disks are also presented to you with the scsi /dev/sd* notation.
```
[root@centos65~]# fdisk -l | grep 'Disk /dev/sd'
Disk /dev/sda:42.9 GB, 42949672960 bytes
Disk /dev/sdb:77.3 GB, 77309411328 bytes
Disk /dev/sdc:154.6 GB, 154618822656 bytes
Disk /dev/sdd:154.6 GB, 154618822656 bytes
```
Here is an overview of disks on a RHEL4u3 server with two real 72GB scsi disks. This
server is attached to a NAS with four NAS disks of half a terabyte. On the NAS disks, four
LVM (/dev/mdx) software RAID devices are configured.
```
[root@tsvtl1 ~]# fdisk -l | grep Disk
Disk /dev/sda: 73.4 GB, 73407488000 bytes
Disk /dev/sdb: 73.4 GB, 73407488000 bytes
Disk /dev/sdc: 499.0 GB, 499036192768 bytes
Disk /dev/sdd: 499.0 GB, 499036192768 bytes
Disk /dev/sde: 499.0 GB, 499036192768 bytes
Disk /dev/sdf: 499.0 GB, 499036192768 bytes
Disk /dev/md0: 271 MB, 271319040 bytes
Disk /dev/md2: 21.4 GB, 21476081664 bytes
Disk /dev/md3: 21.4 GB, 21467889664 bytes
Disk /dev/md1: 21.4 GB, 21476081664 bytes
```
You can also use fdisk to obtain information about one specific hard disk device.
```
[root@centos65 ~]# fdisk -l /dev/sdc
Disk /dev/sdc: 154.6 GB, 154618822656 bytes
255 heads, 63 sectors/track, 18798 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
```
Later we will use fdisk to do dangerous stuff like creating and deleting partitions.

### 4.3.2. dmesg

Kernel boot messages can be seen after boot with dmesg. Since hard disk devices are
detected by the kernel during boot, you can also use dmesg to find information about disk
devices.
```
[root@centos65 ~]# dmesg | grep 'sd[a-z]' | head
sd 0:0:0:0: [sda] 83886080 512-byte logical blocks: (42.9 GB/40.0 GiB)
sd 0:0:0:0: [sda] Write Protect is off
sd 0:0:0:0: [sda] Mode Sense: 00 3a 00 00
sd 0:0:0:0: [sda] Write cache: enabled, read cache: enabled, doesn't support \
DPO or FUA
sda: sda1 sda2
sd 0:0:0:0: [sda] Attached SCSI disk
sd 3:0:0:0: [sdb] 150994944 512-byte logical blocks: (77.3 GB/72.0 GiB)
sd 3:0:0:0: [sdb] Write Protect is off
sd 3:0:0:0: [sdb] Mode Sense: 00 3a 00 00
sd 3:0:0:0: [sdb] Write cache: enabled, read cache: enabled, doesn't support \
DPO or FUA
```
Here is another example of dmesg on a computer with a 200GB ata disk.
```
paul@barry:~$ dmesg | grep -i "ata disk"
[ 2.624149] hda: ST360021A, ATA DISK drive
[ 2.904150] hdb: Maxtor 6Y080L0, ATA DISK drive
[ 3.472148] hdd: WDC WD2000BB-98DWA0, ATA DISK drive
```
Third and last example of dmesg running on RHEL5.3.
```
root@rhel53~# dmesg | grep -i "scsi disk"
sd 0:0:2:0:Attached scsi disk sda
sd 0:0:3:0:Attached scsi disk sdb
sd 0:0:6:0:Attached scsi disk sdc
```

### 4.3.3. /sbin/lshw

The lshw tool will list hardware. With the right options lshw can show a lot of information
about disks (and partitions).
Below a truncated screenshot on Debian 6:
```
root@debian6~# lshw -class volume | grep -A1 -B2 scsi
description: Linux raid autodetect partition
physical id: 1
bus info: scsi@1:0.0.0,1
logical name: /dev/sdb1
--
description: Linux raid autodetect partition
physical id: 1
bus info: scsi@2:0.0.0,1
logical name: /dev/sdc1
--
description: Linux raid autodetect partition
physical id: 1
bus info: scsi@3:0.0.0,1
logical name: /dev/sdd1
--
description: Linux raid autodetect partition
physical id: 1
bus info: scsi@4:0.0.0,1
logical name: /dev/sde1
--
vendor: Linux
physical id: 1
bus info: scsi@0:0.0.0,1
logical name: /dev/sda1
--
vendor: Linux
physical id: 2
bus info: scsi@0:0.0.0,2
logical name: /dev/sda2
--
description: Extended partition
physical id: 3
bus info: scsi@0:0.0.0,3
logical name: /dev/sda3
```
Redhat and CentOS do not have this tool (unless you add a repository).

### 4.3.4. /sbin/lsscsi

The lsscsi command provides a nice readable output of all scsi (and scsi emulated devices).
This first screenshot shows lsscsi on a SPARC system.
```
root@shaka:~# lsscsi
[0:0:0:0] disk Adaptec RAID5 V1.0 /dev/sda
[1:0:0:0] disk SEAGATE ST336605FSUN36G 0438 /dev/sdb
root@shaka:~#
```
Below a screenshot of lsscsi on a QNAP NAS (which has four 750GB disks and boots from
a usb stick).
```
lroot@debian6~# lsscsi 
[0:0:0:0] disk SanDisk Cruzer Edge 1.19 /dev/sda
[1:0:0:0] disk ATA ST3750330AS SD04 /dev/sdb
[2:0:0:0] disk ATA ST3750330AS SD04 /dev/sdc
[3:0:0:0] disk ATA ST3750330AS SD04 /dev/sdd
[4:0:0:0] disk ATA ST3750330AS SD04 /dev/sde
```
This screenshot shows the classic output of lsscsi.
```
root@debian6~# lsscsi -c
Attached devices:

Host: scsi0 Channel: 00 Target: 00 Lun:00
Vendor: SanDisk Model: Cruzer Edge Rev: 1.19
Type: Direct-Access ANSI SCSI revision: 02

Host: scsi1 Channel: 00 Target: 00 Lun:00
Vendor: ATA Model: ST3750330AS Rev: SD04
Type: Direct-Access ANSI SCSI revision: 05

Host: scsi2 Channel: 00 Target: 00 Lun:00
Vendor: ATA Model: ST3750330AS Rev: SD04
Type: Direct-Access ANSI SCSI revision: 05

Host: scsi3 Channel: 00 Target: 00 Lun:00
Vendor: ATA Model: ST3750330AS Rev: SD04
Type: Direct-Access ANSI SCSI revision: 05

Host: scsi4 Channel: 00 Target: 00 Lun:00
Vendor: ATA Model: ST3750330AS Rev: SD04
Type:  Direct-Access  ANSI SCSI revision: 05
38
00
```

### 4.3.5. /proc/scsi/scsi

Another way to locate scsi (or sd) devices is via /proc/scsi/scsi.
This screenshot is from a sparc computer with adaptec RAID5.
```
root@shaka:~# cat /proc/scsi/scsi
Attached devices:
Host: scsi0 Channel: 00 Id: 00 Lun: 00
Vendor: Adaptec Model: RAID5 Rev: V1.0
Type: Direct-Access ANSI SCSI revision: 02
Host: scsi1 Channel: 00 Id: 00 Lun: 00 
Vendor: SEAGATE Model: ST336605FSUN36G Rev: 0438
Type: Direct-Access ANSI SCSI revision: 03
root@shaka:~#
```
Here we run cat /proc/scsi/scsi on the QNAP from above (with Debian Linux).
```
root@debian6~# cat /proc/scsi/scsi
Attached devices:
Host: scsi0 Channel: 00 Id: 00 Lun: 00
Vendor: SanDisk Model: Cruzer Edge Rev: 1.19
Type: Direct-Access ANSI SCSI revision: 02
Host: scsi1 Channel: 00 Id: 00 Lun: 00
Vendor: ATA Model: ST3750330AS Rev: SD04
Type: Direct-Access ANSI SCSI revision: 05
Host: scsi2 Channel: 00 Id: 00 Lun: 00
Vendor: ATA Model: ST3750330AS Rev: SD04
Type: Direct-Access ANSI SCSI revision: 05
Host: scsi3 Channel: 00 Id: 00 Lun: 00
Vendor: ATA Model: ST3750330AS Rev: SD04
Type: Direct-Access ANSI SCSI revision: 05
Host: scsi4 Channel: 00 Id: 00 Lun: 00 
Vendor: ATA Model: ST3750330AS Rev: SD04
Type: Direct-Access ANSI SCSI revision: 05
```
Note that some recent versions of Debian have this disabled in the kernel. You can enable
it (after a kernel compile) using this entry:
`# CONFIG_SCSI_PROC_FS is not set`
Redhat and CentOS have this by default (if there are scsi devices present).
```
[root@centos65 ~]# cat /proc/scsi/scsi
Attached devices:
Host: scsi0 Channel: 00 Id: 00 Lun: 00
Vendor: ATA Model: VBOX HARDDISK Rev: 1.0
Type: Direct-Access ANSI SCSI revision: 05
Host: scsi3 Channel: 00 Id: 00 Lun: 00
Vendor: ATA Model: VBOX HARDDISK Rev: 1.0
Type: Direct-Access ANSI SCSI revision: 05
Host: scsi4 Channel: 00 Id: 00 Lun: 00
Vendor: ATA Model: VBOX HARDDISK Rev: 1.0
Type: Direct-Access ANSI SCSI revision: 05
39
```

## 4.4. erasing a hard disk

Before selling your old hard disk on the internet, it may be a good idea to erase it. By simply
repartitioning, or by using the Microsoft Windows format utility, or even after an mkfs
command, some people will still be able to read most of the data on the disk.
```
root@debian6~# aptitude search foremost autopsy sleuthkit | tr -s ' '
p autopsy - graphical interface to SleuthKit
p foremost - Forensics application to recover data
p sleuthkit - collection of tools for forensics analysis
```
Although technically the /sbin/badblocks tool is meant to look for bad blocks, you can use
it to completely erase all data from a disk. Since this is really writing to every sector of the
disk, it can take a long time!
```
root@RHELv4u2:~# badblocks -ws /dev/sdb
Testing with pattern 0xaa: done
Reading and comparing: done
Testing with pattern 0x55: done
Reading and comparing: done
Testing with pattern 0xff: done
Reading and comparing: done
Testing with pattern 0x00: done
Reading and comparing: done
```
The previous screenshot overwrites every sector of the disk four times. Erasing once with
a tool like dd is enough to destroy all data.
Warning, this screenshot shows how to permanently destroy all data on a block device.
`[root@rhel65 ~]# dd if=/dev/zero of=/dev/sdb`

## 4.5. advanced hard disk settings

Tweaking of hard disk settings (dma, gap, ...) are not covered in this course. Several tools
exists, hdparm and sdparm are two of them.
hdparm can be used to display or set information and parameters about an ATA (or SATA)
hard disk device. The -i and -I options will give you even more information about the
physical properties of the device.
```
root@laika:~# hdparm /dev/sdb
/dev/sdb:
IO_support = 0 (default 16-bit)
readonly = 0 (off)
readahead = 256 (on)
geometry = 12161/255/63, sectors = 195371568, start = 0
```
Below hdparm info about a 200GB IDE disk.
```
root@barry:~# hdparm /dev/hdd
/dev/hdd:
multcount = 0 (off)
IO_support = 0 (default)
unmaskirq = 0 (off)
using_dma = 1 (on)
keepsettings = 0 (off)
readonly = 0 (off)
readahead = 256 (on)
geometry = 24321/255/63, sectors = 390721968, start = 0
```
Here a screenshot of sdparm on Ubuntu 10.10.
```
root@ubu1010:~# aptitude install sdparm
...
root@ubu1010:~# sdparm /dev/sda | head -1
/dev/sda: ATA FUJITSU MJA2160B 0081
root@ubu1010:~# man sdparm
```
Use hdparm and sdparm with care.

# Chapter 5. disk partitions

## 5.1. about partitions

### 5.1.1. primary, extended and logical

Linux requires you to create one or more partitions. The next paragraphs will explain how
to create and use partitions.
A partition's geometry and size is usually defined by a starting and ending cylinder
(sometimes by sector). Partitions can be of type primary (maximum four), extended
(maximum one) or logical (contained within the extended partition). Each partition has a
type field that contains a code. This determines the computers operating system or the
partitions file system.

# (TABLE)

### 5.1.2. partition naming

We saw before that hard disk devices are named /dev/hdx or /dev/sdx with x depending on
the hardware configuration. Next is the partition number, starting the count at 1. Hence the
four (possible) primary partitions are numbered 1 to 4. Logical partition counting always
starts at 5. Thus /dev/hda2 is the second partition on the first ATA hard disk device, and /
dev/hdb5 is the first logical partition on the second ATA hard disk device. Same for SCSI, /
dev/sdb3 is the third partition on the second SCSI disk.
# (TABLE)

## 5.2. discovering partitions

### 5.2.1. fdisk -l

In the fdisk -l example below you can see that two partitions exist on /dev/sdb. The first
partition spans 31 cylinders and contains a Linux swap partition. The second partition is
much bigger.
```
root@laika:~# fdisk -l /dev/sdb
Disk /dev/sdb: 100.0 GB, 100030242816 bytes
255 heads, 63 sectors/track, 12161 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Device Boot Start End Blocks Id System
/dev/sdb1 1 31 248976 82 Linux swap / Solaris
/dev/sdb2 32 12161 97434225 83 Linux
root@laika:~# 
```

### 5.2.2. /proc/partitions

The /proc/partitions file contains a table with major and minor number of partitioned
devices, their number of blocks and the device name in /dev. Verify with /proc/devices to
link the major number to the proper device.

```
paul@RHELv4u4:~$ cat /proc/partitions
major minor #blocks name
```

The major number corresponds to the device type (or driver) and can be found in /proc/
devices. In this case 3 corresponds to ide and 8 to sd. The major number determines the
device driver to be used with this device.
The minor number is a unique identification of an instance of this device type. The
devices.txt file in the kernel tree contains a full list of major and minor numbers.

### 5.2.3. parted and others

You may be interested in alternatives to fdisk like parted, cfdisk, sfdisk and gparted. This
course mainly uses fdisk to partition hard disks.
parted is recommended by some Linux distributions for handling storage with gpt instead
of mbr.
Below a screenshot of parted on CentOS.

```
[root@centos65 ~]# rpm -q parted
parted-2.1-21.el6.x86_64
[root@centos65 ~]# parted /dev/sda
GNU Parted 2.1
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted) print
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sda: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
...
```

## 5.3. partitioning new disks

In the example below, we bought a new disk for our system. After the new hardware is
properly attached, you can use fdisk and parted to create the necessary partition(s). This
example uses fdisk, but there is nothing wrong with using parted.

### 5.3.1. recognising the disk

First, we check with fdisk -l whether Linux can see the new disk. Yes it does, the new disk
is seen as /dev/sdb, but it does not have any partitions yet.

```
root@RHELv4u2:~# fdisk -l
Disk /dev/sda: 12.8 GB, 12884901888 bytes
255 heads, 63 sectors/track, 1566 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
...
Disk /dev/sdb: 1073 MB, 1073741824 bytes
255 heads, 63 sectors/track, 130 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Disk /dev/sdb doesn't contain a valid partition table
```

### 5.3.2. opening the disk with fdisk

Then we create a partition with fdisk on /dev/sdb. First we start the fdisk tool with /dev/sdb
as argument. Be very very careful not to partition the wrong disk!!
```
root@RHELv4u2:~# fdisk /dev/sdb
Device contains neither a valid DOS partition table, nor Sun, SGI...
Building a new DOS disklabel. Changes will remain in memory only,
until you decide to write them. After that, of course, the previous
content won't be recoverable.
Warning: invalid flag 0x0000 of partition table 4 will be corrected...

```

### 5.3.3. empty partition table

Inside the fdisk tool, we can issue the p command to see the current disks partition table.

```
Command (m for help): p
Disk /dev/sdb: 1073 MB, 1073741824 bytes
255 heads, 63 sectors/track, 130 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
...
```

### 5.3.4. create a new partition

No partitions exist yet, so we issue n to create a new partition. We choose p for primary, 1
for the partition number, 1 for the start cylinder and 14 for the end cylinder.
```
Command (m for help): n
Command action
e extended
p primary partition (1-4)
p 
Partition number (1-4): 1
First cylinder (1-130, default 1):
Using default value 1
Last cylinder or +size or +sizeM or +sizeK (1-130, default 130): 14
```
We can now issue p again to verify our changes, but they are not yet written to disk. This
means we can still cancel this operation! But it looks good, so we use w to write the changes
to disk, and then quit the fdisk tool.
```
Command (m for help): p
Disk /dev/sdb: 1073 MB, 1073741824 bytes
255 heads, 63 sectors/track, 130 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
...
Command (m for help): w
The partition table has been altered!
Calling ioctl() to re-read partition table.
Syncing disks.
root@RHELv4u2:~#
```

### 5.3.5. display the new partition

Let's verify again with fdisk -l to make sure reality fits our dreams. Indeed, the screenshot
below now shows a partition on /dev/sdb.
```
root@RHELv4u2:~# fdisk -l
Disk /dev/sda: 12.8 GB, 12884901888 bytes
255 heads, 63 sectors/track, 1566 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
...
Disk /dev/sdb: 1073 MB, 1073741824 bytes
255 heads, 63 sectors/track, 130 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
...
```

## 5.4. about the partition table

### 5.4.1. master boot record

The partition table information (primary and extended partitions) is written in the master
boot record or mbr. You can use dd to copy the mbr to a file.
This example copies the master boot record from the first SCSI hard disk.
`dd if=/dev/sda of=/SCSIdisk.mbr bs=512 count=1`
The same tool can also be used to wipe out all information about partitions on a disk. This
example writes zeroes over the master boot record.
`dd if=/dev/zero of=/dev/sda bs=512 count=1`
Or to wipe out the whole partition or disk.
`dd if=/dev/zero of=/dev/sda`

### 5.4.2. partprobe

Don't forget that after restoring a master boot record with dd, that you need to force the
kernel to reread the partition table with partprobe. After running partprobe, the partitions
can be used again.
```
[root@RHEL5 ~]# partprobe
[root@RHEL5 ~]#
```
### 5.4.3. logical drives

The partition table does not contain information about logical drives. So the dd backup
of the mbr only works for primary and extended partitions. To backup the partition table
including the logical drives, you can use sfdisk.
This example shows how to backup all partition and logical drive information to a file.
`sfdisk -d /dev/sda > parttable.sda.sfdisk`
The following example copies the mbr and all logical drive info from /dev/sda to /dev/sdb.
`sfdisk -d /dev/sda | sfdisk /dev/sdb`

## 5.5. GUID partition table

gpt was developed because of the limitations of the 1980s mbr partitioning scheme (for
example only four partitions can be defined, and they have a maximum size two terabytes).
Since 2010 gpt is a part of the uefi specification, but it is also used on bios systems.
Newer versions of fdisk work fine with gpt, but most production servers today (mid 2015)
still have an older fdisk.. You can use parted instead.

## 5.6. labeling with parted

parted is an interactive tool, just like fdisk. Type help in parted for a list of commands
and options.
This screenshot shows how to start parted to manage partitions on /dev/sdb.
```
[root@rhel71 ~]# parted /dev/sdb
GNU Parted 3.1
Using /dev/sdb
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted)
```
Each command also has built-in help. For example help mklabel will list all supported
labels. Note that we only discussed mbr(msdos) and gpt in this book.
```
(parted) help mklabel
mklabel,mktable LABEL-TYPE
create a new disklabel (partition table)
LABEL-TYPE is one of: aix, amiga, bsd, dvh, gpt, mac, msdos, pc98, sun, loop
(parted)
```
We create an mbr label.
```
(parted) mklabel msdos>
Warning: The existing disk
this disk will be lost. Do
Yes/No? yes
(parted) mklabel gpt
Warning: The existing disk
this disk will be lost. Do
Yes/No? Y
(parted)
```

### 5.6.1. partitioning with parted

Once labeled it is easy to create partitions with parted. This screenshot starts with an
unpartitioned (but gpt labeled) disk.
```
(parted) print
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sdb: 8590MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:
Number Start End Size File system Name Flags
(parted)
```
This example shows how to create two primary partitions of equal size.
```
(parted) mkpart primary 0 50%
Warning: The resulting partition is not properly aligned for best performance.
Ignore/Cancel? I
(parted) mkpart primary 50% 100%
(parted)
```
Verify with print and exit with quit. Since parted works directly on the disk, there is no
need to w(rite) like in fdisk.
```
(parted) print
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sdb: 8590MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
...
(parted) quit
Information: You may need to update /etc/fstab.
[root@rhel71 ~]#
```

# Chapter 6. file systems
When you are finished partitioning the hard disk, you can put a file system on each partition.
## 6.1. about file systems
A file system is a way of organizing files on your partition. Besides file-based storage, file systems usually include directories and access control, and contain meta information about files like access times, modification times and file ownership. The properties (length, character set, ...) of filenames are determined by the file system you choose. Directories are usually implemented as files, you will have to learn how this is implemented! Access control in file systems is tracked by user ownership (and group ownerand membership) in combination with one or more access control lists.
### 6.1.1. man fs 
The manual page about filesystems is accessed by typing man fs.
```
[root@rhel65 ~]# man fs
``` 
### 6.1.2. /proc/filesystems 
The Linux kernel will inform you about currently loaded file system drivers in /proc/ filesystems. 
```
root@rhel53 ~# cat /proc/filesystems | grep -v nodev 
ext2
iso9660
ext3
``` 
### 6.1.3. /etc/filesystems 
The /etc/filesystems file contains a list of autodetected filesystems (in case the mount command is used without the -t option. Help for this file is provided by man mount. 
```
[root@rhel65 ~]# man mount
```
## 6.2. common file systems 
### 6.2.1. ext2 and ext3
Once the most common Linux file systems is the ext2 (the second extended) file system. A disadvantage is that file system checks on ext2 can take a long time. ext2 was being replaced by ext3 on most Linux machines. They are essentially the same, except for the journaling which is only present in ext3. Journaling means that changes are first written to a journal on the disk. The journal is flushed regularly, writing the changes in the file system. Journaling keeps the file system in a consistent state, so you don't need a file system check after an unclean shutdown or power failure. 
### 6.2.2. creating ext2 and ext3 
You can create these file systems with the /sbin/mkfs or /sbin/mke2fs commands. Use mke2fs -j to create an ext3 file system. You can convert an ext2 to ext3 with tune2fs -j. You can mount an ext3 file system as ext2, but then you lose the journaling. Do not forget to run mkinitrd if you are booting from this device. 
### 6.2.3. ext4 
The newest incarnation of the ext file system is named ext4 and is available in the Linux kernel since 2008. ext4 supports larger files (up to 16 terabyte) and larger file systems than ext3 (and many more features). Development started by making ext3 fully capable for 64-bit. When it turned out the changes were significant, the developers decided to name it ext4. 
### 6.2.4. xfs
Redhat Enterprise Linux 7 will have XFS as the default file system. This is a highly scalable high-performance file system. xfs was created for Irix and for a couple of years it was also used in FreeBSD. It is supported by the Linux kernel, but rarely used in dsitributions outside of the Redhat/CentOS realm.
### 6.2.5. vfat 
The vfat file system exists in a couple of forms : fat12 for floppy disks, fat16 on ms-dos, and fat32 for larger disks. The Linux vfat implementation supports all of these, but vfat lacks a lot of features like security and links. fat disks can be read by every operating system, and are used a lot for digital cameras, usb sticks and to exchange data between different OS'ses on a home user's computer. 
### 6.2.6. iso 9660 
iso 9660 is the standard format for cdroms. Chances are you will encounter this file system also on your hard disk in the form of images of cdroms (often with the .iso extension). The iso 9660 standard limits filenames to the 8.3 format. The Unix world didn't like this, and thus added the rock ridge extensions, which allows for filenames up to 255 characters and Unixstyle file-modes, ownership and symbolic links. Another extensions to iso 9660 is joliet, which adds 64 unicode characters to the filename. The el torito standard extends iso 9660 to be able to boot from CD-ROM's. 
### 6.2.7. udf 
Most optical media today (including cd's and dvd's) use udf, the Universal Disk Format. 
### 6.2.8. swap 
All things considered, swap is not a file system. But to use a partition as a swap partition it must be formatted and mounted as swap space. 
### 6.2.9. gfs
Linux clusters often use a dedicated cluster filesystem like GFS, GFS2, ClusterFS, ... 
### 6.2.10. and more... 
You may encounter reiserfs on older Linux systems. Maybe you will see Sun's zfs or the open source btrfs. This last one requires a chapter on itself.
### 6.2.11. /proc/filesystems
The /proc/filesystems file displays a list of supported file systems. When you mount a file system without explicitly defining one, then mount will first try to probe /etc/filesystems and then probe /proc/filesystems for all the filesystems without the nodev label. If /etc/ filesystems ends with a line containing only an asterisk (*) then both files are probed.

```
paul@RHELv4u4:~$ cat /proc/filesystems
nodev sysfs
nodev rootfs
nodev bdev
nodev proc
nodev sockfs
nodev binfmt_misc
nodev usbfs
nodev usbdevfs
nodev futexfs
nodev tmpfs
nodev pipefs
nodev eventpollfs
nodev devpts
 ext2
nodev ramfs
nodev hugetlbfs
 iso9660
nodev relayfs
nodev mqueue
nodev selinuxfs
 ext3
nodev rpc_pipefs
nodev vmware-hgfs
nodev autofs
paul@RHELv4u4:~$
```

## 6.3. putting a file system on a partition
We now have a fresh partition. The system binaries to make file systems can be found with ls.

```
[root@RHEL4b ~]# ls -lS /sbin/mk*
-rwxr-xr-x 3 root root 34832 Apr 24 2006 /sbin/mke2fs
-rwxr-xr-x 3 root root 34832 Apr 24 2006 /sbin/mkfs.ext2
-rwxr-xr-x 3 root root 34832 Apr 24 2006 /sbin/mkfs.ext3
-rwxr-xr-x 3 root root 28484 Oct 13 2004 /sbin/mkdosfs
-rwxr-xr-x 3 root root 28484 Oct 13 2004 /sbin/mkfs.msdos
-rwxr-xr-x 3 root root 28484 Oct 13 2004 /sbin/mkfs.vfat
-rwxr-xr-x 1 root root 20313 Apr 10 2006 /sbin/mkinitrd
-rwxr-x--- 1 root root 15444 Oct 5 2004 /sbin/mkzonedb
-rwxr-xr-x 1 root root 15300 May 24 2006 /sbin/mkfs.cramfs
-rwxr-xr-x 1 root root 13036 May 24 2006 /sbin/mkswap
-rwxr-xr-x 1 root root 6912 May 24 2006 /sbin/mkfs
-rwxr-xr-x 1 root root 5905 Aug 3 2004 /sbin/mkbootdisk
[root@RHEL4b ~]#
```

It is time for you to read the manual pages of mkfs and mke2fs. In the example below, you see the creation of an ext2 file system on /dev/sdb1. In real life, you might want to use options like -m0 and -j.

```
root@RHELv4u2:~# mke2fs /dev/sdb1
mke2fs 1.35 (28-Feb-2004)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
28112 inodes, 112420 blocks
5621 blocks (5.00%) reserved for the super user
First data block=1
Maximum filesystem blocks=67371008
14 block groups
8192 blocks per group, 8192 fragments per group
2008 inodes per group
Superblock backups stored on blocks:
8193, 24577, 40961, 57345, 73729

Writing inode tables: done
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 37 mounts or
180 days, whichever comes first. Use tune2fs -c or -i to override.
```

## 6.4. tuning a file system 
You can use tune2fs to list and set file system settings. The first screenshot lists the reserved space for root (which is set at five percent).

```
[root@rhel4 ~]# tune2fs -l /dev/sda1 | grep -i "block count"
Block count: 104388
Reserved block count: 5219
[root@rhel4 ~]#
```
This example changes this value to ten percent. You can use tune2fs while the file system is active, even if it is the root file system (as in this example).

```
[root@rhel4 ~]# tune2fs -m10 /dev/sda1
tune2fs 1.35 (28-Feb-2004)
Setting reserved blocks percentage to 10 (10430 blocks)
[root@rhel4 ~]# tune2fs -l /dev/sda1 | grep -i "block count"
Block count: 104388
Reserved block count: 10430
[root@rhel4 ~]#
```

## 6.5. checking a file system 
The fsck command is a front end tool used to check a file system for errors.
```
[root@RHEL4b ~]# ls /sbin/*fsck*
/sbin/dosfsck /sbin/fsck /sbin/fsck.ext2 /sbin/fsck.msdos
/sbin/e2fsck /sbin/fsck.cramfs /sbin/fsck.ext3 /sbin/fsck.vfat
[root@RHEL4b ~]#
```
The last column in /etc/fstab is used to determine whether a file system should be checked
at boot-up.
```
[paul@RHEL4b ~]$ grep ext /etc/fstab
/dev/VolGroup00/LogVol00 / ext3 defaults 1 1
LABEL=/boot /boot ext3 defaults 1 2
[paul@RHEL4b ~]$
```
Manually checking a mounted file system results in a warning from fsck.
```
[root@RHEL4b ~]# fsck /boot
fsck 1.35 (28-Feb-2004)
e2fsck 1.35 (28-Feb-2004)
/dev/sda1 is mounted.
WARNING!!! Running e2fsck on a mounted filesystem may cause
SEVERE filesystem damage.
Do you really want to continue (y/n)? no
check aborted.
```
But after unmounting fsck and e2fsck can be used to check an ext2 file system.
```
[root@RHEL4b ~]# fsck /boot
fsck 1.35 (28-Feb-2004)
e2fsck 1.35 (28-Feb-2004)
/boot: clean, 44/26104 files, 17598/104388 blocks
[root@RHEL4b ~]# fsck -p /boot
fsck 1.35 (28-Feb-2004)
/boot: clean, 44/26104 files, 17598/104388 blocks
[root@RHEL4b ~]# e2fsck -p /dev/sda1
/boot: clean, 44/26104 files, 17598/104388 blocks
```

# Chapter 7. mounting 
Once you've put a file system on a partition, you can mount it. Mounting a file system makes it available for use, usually as a directory. We say mounting a file system instead of mounting a partition because we will see later that we can also mount file systems that do not exists on partitions. On all Unix systems, every file and every directory is part of one big file tree. To access a file, you need to know the full path starting from the root directory. When adding a file system to your computer, you need to make it available somewhere in the file tree. The directory where you make a file system available is called a mount point.
## 7.1. mounting local file systems
### 7.1.1. mkdir
This example shows how to create a new mount point with mkdir.
```
root@RHELv4u2:~# mkdir /home/project42
```
### 7.1.2. mount
When the mount point is created, and a file system is present on the partition, then mount
can mount the file system on the mount point directory.
```
root@RHELv4u2:~# mount -t ext2 /dev/sdb1 /home/project42/
```
Once mounted, the new file system is accessible to users.
### 7.1.3. /etc/filesystems
Actually the explicit -t ext2 option to set the file system is not always necessary. The mount
command is able to automatically detect a lot of file systems.
When mounting a file system without specifying explicitly the file system, then mount will
first probe /etc/filesystems. Mount will skip lines with the nodev directive.
```
paul@RHELv4u4:~$ cat /etc/filesystems
ext3
ext2
nodev proc
nodev devpts
iso9660
vfat
hfs
```
### 7.1.4. /proc/filesystems
When /etc/filesystems does not exist, or ends with a single * on the last line, then mount
will read /proc/filesystems.
```
[root@RHEL52 ~]# cat /proc/filesystems | grep -v ^nodev
 ext2
 iso9660
 ext3
```
### 7.1.5. umount
You can unmount a mounted file system using the umount command.
```
root@pasha:~# umount /home/reet
```
## 7.2. displaying mounted file systems
To display all mounted file systems, issue the mount command. Or look at the files /proc/
mounts and /etc/mtab.
### 7.2.1. mount
The simplest and most common way to view all mounts is by issuing the mount command
without any arguments.
```
root@RHELv4u2:~# mount | grep /dev/sdb
/dev/sdb1 on /home/project42 type ext2 (rw)
```
### 7.2.2. /proc/mounts
The kernel provides the info in /proc/mounts in file form, but /proc/mounts does not exist
as a file on any hard disk. Looking at /proc/mounts is looking at information that comes
directly from the kernel.
```
root@RHELv4u2:~# cat /proc/mounts | grep /dev/sdb
/dev/sdb1 /home/project42 ext2 rw 0 0
```
### 7.2.3. /etc/mtab
The /etc/mtab file is not updated by the kernel, but is maintained by the mount command.
Do not edit /etc/mtab manually.
```
root@RHELv4u2:~# cat /etc/mtab | grep /dev/sdb
/dev/sdb1 /home/project42 ext2 rw 0 0
```
### 7.2.4. df
A more user friendly way to look at mounted file systems is df. The df (diskfree) command
has the added benefit of showing you the free space on each mounted disk. Like a lot of
Linux commands, df supports the -h switch to make the output more human readable.
```
root@RHELv4u2:~# df
Filesystem 1K-blocks Used Available Use% Mounted on
/dev/mapper/VolGroup00-LogVol00
11707972 6366996 4746240 58% /
/dev/sda1 101086 9300 86567 10% /boot
none 127988 0 127988 0% /dev/shm
/dev/sdb1 108865 1550 101694 2% /home/project42
root@RHELv4u2:~# df -h
Filesystem Size Used Avail Use% Mounted on
/dev/mapper/VolGroup00-LogVol00
12G 6.1G 4.6G 58% /
/dev/sda1 99M 9.1M 85M 10% /boot
none 125M 0 125M 0% /dev/shm
/dev/sdb1 107M 1.6M 100M 2% /home/project42
```
### 7.2.5. df -h
In the df -h example below you can see the size, free space, used gigabytes and percentage
and mount point of a partition.
```
root@laika:~# df -h | egrep -e "(sdb2|File)"
Filesystem Size Used Avail Use% Mounted on
/dev/sdb2 92G 83G 8.6G 91% /media/sdb2
```
### 7.2.6. du
The du command can summarize disk usage for files and directories. By using du on a
mount point you effectively get the disk space used on a file system.
While du can go display each subdirectory recursively, the -s option will give you a total
summary for the parent directory. This option is often used together with -h. This means du
-sh on a mount point gives the total amount used by the file system in that partition.
```
root@debian6~# du -sh /boot /srv/wolf
6.2M /boot
1.1T /srv/wolf
```
## 7.3. from start to finish
Below is a screenshot that show a summary roadmap starting with detection of the hardware
(/dev/sdb) up until mounting on /mnt.
```
[root@centos65 ~]# dmesg | grep '\[sdb\]'
sd 3:0:0:0: [sdb] 150994944 512-byte logical blocks: (77.3 GB/72.0 GiB)
sd 3:0:0:0: [sdb] Write Protect is off
sd 3:0:0:0: [sdb] Mode Sense: 00 3a 00 00
sd 3:0:0:0: [sdb] Write cache: enabled, read cache: enabled, doesn't support \
DPO or FUA
sd 3:0:0:0: [sdb] Attached SCSI disk
[root@centos65 ~]# parted /dev/sdb
(parted) mklabel msdos
(parted) mkpart primary ext4 1 77000
(parted) print
Model: ATA VBOX HARDDISK (scsi)
Disk /dev/sdb: 77.3GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number Start End Size Type File system Flags
 1 1049kB 77.0GB 77.0GB primary
(parted) quit
[root@centos65 ~]# mkfs.ext4 /dev/sdb1
mke2fs 1.41.12 (17-May-2010)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
4702208 inodes, 18798592 blocks
939929 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=4294967296
574 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
( output truncated )
...
[root@centos65 ~]# mount /dev/sdb1 /mnt
[root@centos65 ~]# mount | grep mnt
/dev/sdb1 on /mnt type ext4 (rw)
[root@centos65 ~]# df -h | grep mnt
/dev/sdb1 71G 180M 67G 1% /mnt
[root@centos65 ~]# du -sh /mnt
20K /mnt
[root@centos65 ~]# umount /mnt
```
## 7.4. permanent mounts
Until now, we performed all mounts manually. This works nice, until the next reboot.
Luckily there is a way to tell your computer to automatically mount certain file systems
during boot.
### 7.4.1. /etc/fstab
The file system table located in /etc/fstab contains a list of file systems, with an option to
automtically mount each of them at boot time.
Below is a sample /etc/fstab file.
```
root@RHELv4u2:~# cat /etc/fstab
/dev/VolGroup00/LogVol00 / ext3 defaults 1 1
LABEL=/boot /boot ext3 defaults 1 2
none /dev/pts devpts gid=5,mode=620 0 0
none /dev/shm tmpfs defaults 0 0
none /proc proc defaults 0 0
none /sys sysfs defaults 0 0
/dev/VolGroup00/LogVol01 swap swap defaults 0 0
```
By adding the following line, we can automate the mounting of a file system.
```
/dev/sdb1 /home/project42 ext2 defaults 0 0
```
### 7.4.2. mount /mountpoint
Adding an entry to /etc/fstab has the added advantage that you can simplify the mount
command. The command in the screenshot below forces mount to look for the partition
info in /etc/fstab.
```
root@rhel65:~# mount /home/project42
```
## 7.5. securing mounts
File systems can be secured with several mount options. Here are some examples.
### 7.5.1. ro
The ro option will mount a file system as read only, preventing anyone from writing.
```
root@rhel53 ~# mount -t ext2 -o ro /dev/hdb1 /home/project42
root@rhel53 ~# touch /home/project42/testwrite
touch: cannot touch `/home/project42/testwrite': Read-only file system
```
### 7.5.2. noexec
The noexec option will prevent the execution of binaries and scripts on the mounted file
system.
```
root@rhel53 ~# mount -t ext2 -o noexec /dev/hdb1 /home/project42
root@rhel53 ~# cp /bin/cat /home/project42
root@rhel53 ~# /home/project42/cat /etc/hosts
-bash: /home/project42/cat: Permission denied
root@rhel53 ~# echo echo hello > /home/project42/helloscript
root@rhel53 ~# chmod +x /home/project42/helloscript
root@rhel53 ~# /home/project42/helloscript
-bash: /home/project42/helloscript: Permission denied
```
### 7.5.3. nosuid
The nosuid option will ignore setuid bit set binaries on the mounted file system.
Note that you can still set the setuid bit on files.
```
root@rhel53 ~# mount -o nosuid /dev/hdb1 /home/project42
root@rhel53 ~# cp /bin/sleep /home/project42/
root@rhel53 ~# chmod 4555 /home/project42/sleep
root@rhel53 ~# ls -l /home/project42/sleep
-r-sr-xr-x 1 root root 19564 Jun 24 17:57 /home/project42/sleep
```

But users cannot exploit the setuid feature.
```
root@rhel53 ~# su - paul
[paul@rhel53 ~]$ /home/project42/sleep 500 &
[1] 2876
[paul@rhel53 ~]$ ps -f 2876
UID PID PPID C STIME TTY STAT TIME CMD
paul 2876 2853 0 17:58 pts/0 S 0:00 /home/project42/sleep 500
[paul@rhel53 ~]$
```
### 7.5.4. noacl
To prevent cluttering permissions with acl's, use the noacl option.
```
root@rhel53 ~# mount -o noacl /dev/hdb1 /home/project42
```
More mount options can be found in the manual page of mount.
## 7.6. mounting remote file systems
### 7.6.1. smb/cifs
The Samba team (samba.org) has a Unix/Linux service that is compatible with the SMB/
CIFS protocol. This protocol is mainly used by networked Microsoft Windows computers.
Connecting to a Samba server (or to a Microsoft computer) is also done with the mount
command.
This example shows how to connect to the 10.0.0.42 server, to a share named data2.
```
[root@centos65 ~]# mount -t cifs -o user=paul //10.0.0.42/data2 /home/data2
Password:
[root@centos65 ~]# mount | grep cifs
//10.0.0.42/data2 on /home/data2 type cifs (rw)
```
The above requires yum install cifs-client.
### 7.6.2. nfs
Unix servers often use nfs (aka the network file system) to share directories over the network.
Setting up an nfs server is discussed later. Connecting as a client to an nfs server is done
with mount, and is very similar to connecting to local storage.
This command shows how to connect to the nfs server named server42, which is sharing
the directory /srv/data. The mount point at the end of the command (/home/data) must
already exist.
```
[root@centos65 ~]# mount -t nfs server42:/srv/data /home/data
[root@centos65 ~]#
```
If this server42 has ip-address 10.0.0.42 then you can also write:
```
[root@centos65 ~]# mount -t nfs 10.0.0.42:/srv/data /home/data
[root@centos65 ~]# mount | grep data
10.0.0.42:/srv/data on /home/data type nfs (rw,vers=4,addr=10.0.0.42,clienta\
ddr=10.0.0.33)
```
### 7.6.3. nfs specific mount options
```
bg If mount fails, retry in background.
fg (default)If mount fails, retry in foreground.
soft Stop trying to mount after X attempts.
hard (default)Continue trying to mount.
```
The soft+bg options combined guarantee the fastest client boot if there are NFS problems.
```
retrans=X Try X times to connect (over udp).
tcp Force tcp (default and supported)
udp Force udp (unsupported)
```
# Chapter 8. troubleshooting tools
## 8.1. lsof
List open files with lsof.
When invoked without options, lsof will list all open files. You can see the command (init in
this case), its PID (1) and the user (root) has openend the root directory and /sbin/init. The
FD (file descriptor) columns shows that / is both the root directory (rtd) and current working
directory (cwd) for the /sbin/init command. The FD column displays rtd for root directory,
cwd for current directory and txt for text (both including data and code).
```
root@debian7:~# lsof | head -4
COMMAND PID TID USER FD TYPE DEVICE SIZE/OFF NODE NAME
init 1 root cwd DIR 254,0 4096 2 /
init 1 root rtd DIR 254,0 4096 2 /
init 1 root txt REG 254,0 36992 130856 /sbin/init
```
Other options in the FD column besides w for writing, are r for reading and u for both reading
and writing. You can look at open files for a process id by typing lsof -p PID. For init this
would look like this:
```
lsof -p 1
```
The screenshot below shows basic use of lsof to prove that vi keeps a .swp file open (even
when stopped in background) on our freshly mounted file system.
```
[root@RHEL65 ~]# df -h | grep sdb
/dev/sdb1 541M 17M 497M 4% /srv/project33
[root@RHEL65 ~]# vi /srv/project33/busyfile.txt
[1]+ Stopped vi /srv/project33/busyfile.txt
[root@RHEL65 ~]# lsof /srv/*
COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
vi 3243 root 3u REG 8,17 4096 12 /srv/project33/.busyfile.txt.swp
```
Here we see that rsyslog has a couple of log files open for writing (the FD column).
```
root@debian7:~# lsof /var/log/*
COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
rsyslogd 2013 root 1w REG 254,0 454297 1308187 /var/log/syslog
rsyslogd 2013 root 2w REG 254,0 419328 1308189 /var/log/kern.log
rsyslogd 2013 root 5w REG 254,0 116725 1308200 /var/log/debug
rsyslogd 2013 root 6w REG 254,0 309847 1308201 /var/log/messages
rsyslogd 2013 root 7w REG 254,0 17591 1308188 /var/log/daemon.log
rsyslogd 2013 root 8w REG 254,0 101768 1308186 /var/log/auth.log
```
You can specify a specific user with lsof -u. This example shows the current working
directory for a couple of command line programs.
```
[paul@RHEL65 ~]$ lsof -u paul | grep home
bash 3302 paul cwd DIR 253,0 4096 788024 /home/paul
lsof 3329 paul cwd DIR 253,0 4096 788024 /home/paul
grep 3330 paul cwd DIR 253,0 4096 788024 /home/paul
lsof 3331 paul cwd DIR 253,0 4096 788024 /home/paul
```
The -u switch of lsof also supports the ^ character meaning 'not'. To see all open files, but
not those open by root:
```
lsof -u^root
```

## 8.2. fuser
The fuser command will display the 'user' of a file system.
In this example we still have a vi process in background and we use fuser to find the process
id of the process using this file system.
```
[root@RHEL65 ~]# jobs
[1]+ Stopped vi /srv/project33/busyfile.txt
[root@RHEL65 ~]# fuser -m /srv/project33/
/srv/project33/: 3243
```
Adding the -u switch will also display the user name.
```
[root@RHEL65 ~]# fuser -m -u /srv/project33/
/srv/project33/: 3243(root)
```
You can quickly kill all processes that are using a specific file (or directory) with the -k
switch.
```
[root@RHEL65 ~]# fuser -m -k -u /srv/project33/
/srv/project33/: 3243(root)
[1]+ Killed vi /srv/project33/busyfile.txt
[root@RHEL65 ~]# fuser -m -u /srv/project33/
[root@RHEL65 ~]#
```
This example shows all processes that are using the current directory (bash and vi in this
case).
```
root@debian7:~/test42# vi file42
[1]+ Stopped vi file42
root@debian7:~/test42# fuser -v .
 USER PID ACCESS COMMAND
/root/test42: root 2909 ..c.. bash
 root 3113 ..c.. vi
```
This example shows that the vi command actually accesses /usr/bin/vim.basic as an
executable file.
```
root@debian7:~/test42# fuser -v $(which vi)
 USER PID ACCESS COMMAND
/usr/bin/vim.basic: root 3113 ...e. vi
```
The last example shows how to find the process that is accessing a specific file.
```
[root@RHEL65 ~]# vi /srv/project33/busyfile.txt
[1]+ Stopped vi /srv/project33/busyfile.txt
[root@RHEL65 ~]# fuser -v -m /srv/project33/busyfile.txt
 USER PID ACCESS COMMAND
/srv/project33/busyfile.txt:
 root 13938 F.... vi
[root@RHEL65 ~]# ps -fp 13938
UID PID PPID C STIME TTY TIME CMD
root 13938 3110 0 15:47 pts/0 00:00:00 vi /srv/project33/busyfile.txt
```

## 8.3. chroot
The chroot command creates a shell with an alternate root directory. It effectively hides
anything outside of this directory.
In the example below we assume that our system refuses to start (maybe because there is a
problem with /etc/fstab or the mounting of the root file system).
We start a live system (booted from cd/dvd/usb) to troubleshoot our server. The live system
will not use our main hard disk as root device
```
root@livecd:~# df -h | grep root
rootfs 186M 11M 175M 6% /
/dev/loop0 807M 807M 0 100% /lib/live/mount/rootfs/filesystem.squashfs
root@livecd:~# mount | grep root
/dev/loop0 on /lib/live/mount/rootfs/filesystem.squashfs type squashfs (ro)
```
We create some test file on the current rootfs.
```
root@livecd:~# touch /file42
root@livecd:~# mkdir /dir42
root@livecd:~# ls /
bin dir42 home lib64 opt run srv usr
boot etc initrd.img media proc sbin sys var
dev file42 lib mnt root selinux tmp vmlinuz
```
First we mount the root file system from the disk (which is on lvm so we use /dev/mapper
instead of /dev/sda5).
```
root@livecd:~# mount /dev/mapper/packer--debian--7-root /mnt
```
We are now ready to chroot into the rootfs on disk.
```
root@livecd:~# cd /mnt
root@livecd:/mnt# chroot /mnt
root@livecd:/# ls /
bin dev initrd.img lost+found opt run srv usr vmlinuz
boot etc lib media proc sbin sys vagrant
data home lib64 mnt root selinux tmp var
```
Our test files (file42 and dir42) are not visible because they are out of the chrooted
environment.
Note that the hostname of the chrooted environment is identical to the existing hostname.
To exit the chrooted file system:
```
root@livecd:/# exit
exit
root@livecd:~# ls /
bin dir42 home lib64 opt run srv usr
boot etc initrd.img media proc sbin sys var
dev file42 lib mnt root selinux tmp vmlinuz
```
## 8.4. iostat
iostat reports IO statitics every given period of time. It also includes a small cpu usage
summary. This example shows iostat running every ten seconds with /dev/sdc and /dev/sde
showing a lot of write activity.
```
[root@RHEL65 ~]# iostat 10 3
Linux 2.6.32-431.el6.x86_64 (RHEL65) 06/16/2014 _x86_64_ (1 CPU)
avg-cpu: %user %nice %system %iowait %steal %idle
 5.81 0.00 3.15 0.18 0.00 90.85
Device: tps Blk_read/s Blk_wrtn/s Blk_read Blk_wrtn
sda 42.08 1204.10 1634.88 1743708 2367530
sdb 1.20 7.69 45.78 11134 66292
sdc 0.92 5.30 45.82 7672 66348
sdd 0.91 5.29 45.78 7656 66292
sde 1.04 6.28 91.49 9100 132496
sdf 0.70 3.40 91.46 4918 132440
sdg 0.69 3.40 91.46 4918 132440
dm-0 191.68 1045.78 1362.30 1514434 1972808
dm-1 49.26 150.54 243.55 218000 352696
avg-cpu: %user %nice %system %iowait %steal %idle
 56.11 0.00 16.83 0.10 0.00 26.95
Device: tps Blk_read/s Blk_wrtn/s Blk_read Blk_wrtn
sda 257.01 10185.97 76.95 101656 768
sdb 0.00 0.00 0.00 0 0
sdc 3.81 1.60 2953.11 16 29472
sdd 0.00 0.00 0.00 0 0
sde 4.91 1.60 4813.63 16 48040
sdf 0.00 0.00 0.00 0 0
sdg 0.00 0.00 0.00 0 0
dm-0 283.77 10185.97 76.95 101656 768
dm-1 0.00 0.00 0.00 0 0
avg-cpu: %user %nice %system %iowait %steal %idle
 67.65 0.00 31.11 0.11 0.00 1.13
Device: tps Blk_read/s Blk_wrtn/s Blk_read Blk_wrtn
sda 466.86 26961.09 178.28 238336 1576
sdb 0.00 0.00 0.00 0 0
sdc 31.45 0.90 24997.29 8 220976
sdd 0.00 0.00 0.00 0 0
sde 0.34 0.00 5.43 0 48
sdf 0.00 0.00 0.00 0 0
sdg 0.00 0.00 0.00 0 0
dm-0 503.62 26938.46 178.28 238136 1576
dm-1 2.83 22.62 0.00 200 0
[root@RHEL65 ~]#
```
Other options are to specify the disks you want to monitor (every 5 seconds here):
```
iostat sdd sde sdf 5
```
Or to show statistics per partition:
```
iostat -p sde -p sdf 5
```
## 8.5. iotop
iotop works like the top command but orders processes by input/output instead of by CPU.
By default iotop will show all processes. This example uses iotop -o to only display
processes with actual I/O.
```
[root@RHEL65 ~]# iotop -o
Total DISK READ: 8.63 M/s | Total DISK WRITE: 0.00 B/s
 TID PRIO USER DISK READ DISK WRITE SWAPIN IO> COMMAND
15000 be/4 root 2.43 M/s 0.00 B/s 0.00 % 14.60 % tar cjf /srv/di...
25000 be/4 root 6.20 M/s 0.00 B/s 0.00 % 6.15 % tar czf /srv/di...
24988 be/4 root 0.00 B/s 7.21 M/s 0.00 % 0.00 % gzip
25003 be/4 root 0.00 B/s 1591.19 K/s 0.00 % 0.00 % gzip
25004 be/4 root 0.00 B/s 193.51 K/s 0.00 % 0.00 % bzip2
```
Use the -b switch to create a log of iotop output (instead of the default interactive view).
```
[root@RHEL65 ~]# iotop -bod 10
Total DISK READ: 12.82 M/s | Total DISK WRITE: 5.69 M/s
 TID PRIO USER DISK READ DISK WRITE SWAPIN IO COMMAND
25153 be/4 root 2.05 M/s 0.00 B/s 0.00 % 7.81 % tar cjf /srv/di...
25152 be/4 root 10.77 M/s 0.00 B/s 0.00 % 2.94 % tar czf /srv/di...
25144 be/4 root 408.54 B/s 0.00 B/s 0.00 % 0.05 % python /usr/sbi...
12516 be/3 root 0.00 B/s 1491.33 K/s 0.00 % 0.04 % [jbd2/sdc1-8]
12522 be/3 root 0.00 B/s 45.48 K/s 0.00 % 0.01 % [jbd2/sde1-8]
25158 be/4 root 0.00 B/s 0.00 B/s 0.00 % 0.00 % [flush-8:64]
25155 be/4 root 0.00 B/s 493.12 K/s 0.00 % 0.00 % bzip2
25156 be/4 root 0.00 B/s 2.81 M/s 0.00 % 0.00 % gzip
25159 be/4 root 0.00 B/s 528.63 K/s 0.00 % 0.00 % [flush-8:32]
```
This is an example of iotop to track disk I/O every ten seconds for one user named vagrant
(and only one process of this user, but this can be omitted). The -a switch accumulates I/
O over time.
```
[root@RHEL65 ~]# iotop -q -a -u vagrant -b -p 5216 -d 10 -n 10
Total DISK READ: 0.00 B/s | Total DISK WRITE: 0.00 B/s
 TID PRIO USER DISK READ DISK WRITE SWAPIN IO COMMAND
 5216 be/4 vagrant 0.00 B 0.00 B 0.00 % 0.00 % gzip
Total DISK READ: 818.22 B/s | Total DISK WRITE: 20.78 M/s
 5216 be/4 vagrant 0.00 B 213.89 M 0.00 % 0.00 % gzip
Total DISK READ: 2045.95 B/s | Total DISK WRITE: 23.16 M/s
 5216 be/4 vagrant 0.00 B 430.70 M 0.00 % 0.00 % gzip
Total DISK READ: 1227.50 B/s | Total DISK WRITE: 22.37 M/s
 5216 be/4 vagrant 0.00 B 642.02 M 0.00 % 0.00 % gzip
Total DISK READ: 818.35 B/s | Total DISK WRITE: 16.44 M/s
 5216 be/4 vagrant 0.00 B 834.09 M 0.00 % 0.00 % gzip
Total DISK READ: 6.95 M/s | Total DISK WRITE: 8.74 M/s
 5216 be/4 vagrant 0.00 B 920.69 M 0.00 % 0.00 % gzip
Total DISK READ: 21.71 M/s | Total DISK WRITE: 11.99 M/s
```
## 8.6. vmstat
While vmstat is mainly a memory monitoring tool, it is worth mentioning here for its
reporting on summary I/O data for block devices and swap space.
This example shows some disk activity (underneath the -----io---- column), without
swapping.
```
[root@RHEL65 ~]# vmstat 5 10
procs ----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
 r b swpd free buff cache si so bi bo in cs us sy id wa st
 0 0 5420 9092 14020 340876 7 12 235 252 77 100 2 1 98 0 0
 2 0 5420 6104 13840 338176 0 0 7401 7812 747 1887 38 12 50 0 0
 2 0 5420 10136 13696 336012 0 0 11334 14 1725 4036 76 24 0 0 0
 0 0 5420 14160 13404 341552 0 0 10161 9914 1174 1924 67 15 18 0 0
 0 0 5420 14300 13420 341564 0 0 0 16 28 18 0 0 100 0 0
 0 0 5420 14300 13420 341564 0 0 0 0 22 16 0 0 100 0 0
...
[root@RHEL65 ~]#
```
You can benefit from vmstat's ability to display memory in kilobytes, megabytes or even
kibibytes and mebibytes using -S (followed by k K m or M).
```
[root@RHEL65 ~]# vmstat -SM 5 10
procs ----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
 r b swpd free buff cache si so bi bo in cs us sy id wa st
 0 0 5 14 11 334 0 0 259 255 79 107 2 1 97 0 0
 0 0 5 14 11 334 0 0 0 2 21 18 0 0 100 0 0
 0 0 5 15 11 334 0 0 6 0 35 31 0 0 100 0 0
 2 0 5 6 11 336 0 0 17100 7814 1378 2945 48 21 31 0 0
 2 0 5 6 11 336 0 0 13193 14 1662 3343 78 22 0 0 0
 2 0 5 13 11 330 0 0 11656 9781 1419 2642 82 18 0 0 0
 2 0 5 9 11 334 0 0 10705 2716 1504 2657 81 19 0 0 0
 1 0 5 14 11 336 0 0 6467 3788 765 1384 43 9 48 0 0
 0 0 5 14 11 336 0 0 0 13 28 24 0 0 100 0 0
 0 0 5 14 11 336 0 0 0 0 20 15 0 0 100 0 0
[root@RHEL65 ~]#
```

# Chapter 9. introduction to uuid's
A uuid or universally unique identifier is used to uniquely identify objects. This 128bit standard allows anyone to create a unique uuid.
## 9.1. about unique objects
Older versions of Linux have a vol_id utility to display the uuid of a file system.
```
root@debian5:~# vol_id --uuid /dev/sda1
193c3c9b-2c40-9290-8b71-4264ee4d4c82
```
Red Hat Enterprise Linux 5 puts vol_id in /lib/udev/vol_id, which is not in the $PATH. The
syntax is also a bit different from Debian/Ubuntu/Mint.
```
root@rhel53 ~# /lib/udev/vol_id -u /dev/hda1
48a6a316-9ca9-4214-b5c6-e7b33a77e860
```
This utility is not available in standard installations of RHEL6 or Debian6.
## 9.2. tune2fs
Use tune2fs to find the uuid of a file system.
```
[root@RHEL5 ~]# tune2fs -l /dev/sda1 | grep UUID
Filesystem UUID: 11cfc8bc-07c0-4c3f-9f64-78422ef1dd5c
[root@RHEL5 ~]# /lib/udev/vol_id -u /dev/sda1
11cfc8bc-07c0-4c3f-9f64-78422ef1dd5c
```
## 9.3. uuid
There is more information in the manual of uuid, a tool that can generate uuid's.
```
[root@rhel65 ~]# yum install uuid
(output truncated)
[root@rhel65 ~]# man uuid
```
## 9.4. uuid in /etc/fstab
You can use the uuid to make sure that a volume is universally uniquely identified in /etc/
fstab. The device name can change depending on the disk devices that are present at boot
time, but a uuid never changes.
First we use tune2fs to find the uuid.
```
[root@RHEL5 ~]# tune2fs -l /dev/sdc1 | grep UUID
Filesystem UUID: 7626d73a-2bb6-4937-90ca-e451025d64e8
```
Then we check that it is properly added to /etc/fstab, the uuid replaces the variable
devicename /dev/sdc1.
```
[root@RHEL5 ~]# grep UUID /etc/fstab
UUID=7626d73a-2bb6-4937-90ca-e451025d64e8 /home/pro42 ext3 defaults 0 0
```
Now we can mount the volume using the mount point defined in /etc/fstab.
```
[root@RHEL5 ~]# mount /home/pro42
[root@RHEL5 ~]# df -h | grep 42
/dev/sdc1 397M 11M 366M 3% /home/pro42
```
The real test now, is to remove /dev/sdb from the system, reboot the machine and see what
happens. After the reboot, the disk previously known as /dev/sdc is now /dev/sdb.
```
[root@RHEL5 ~]# tune2fs -l /dev/sdb1 | grep UUID
Filesystem UUID: 7626d73a-2bb6-4937-90ca-e451025d64e8
```
And thanks to the uuid in /etc/fstab, the mountpoint is mounted on the same disk as before.
```
[root@RHEL5 ~]# df -h | grep sdb
/dev/sdb1 397M 11M 366M 3% /home/pro42
```
## 9.5. uuid as a boot device
Recent Linux distributions (Debian, Ubuntu, ...) use grub with a uuid to identify the root
file system.
This example shows how a root=/dev/sda1 is replaced with a uuid.
```
title Ubuntu 9.10, kernel 2.6.31-19-generic
uuid f001ba5d-9077-422a-9634-8d23d57e782a
kernel /boot/vmlinuz-2.6.31-19-generic \
root=UUID=f001ba5d-9077-422a-9634-8d23d57e782a ro quiet splash
initrd /boot/initrd.img-2.6.31-19-generic
```
The screenshot above contains only four lines. The line starting with root= is the
continuation of the kernel line.
RHEL and CentOS boot from LVM after a default install.
# Chapter 10. introduction to raid
## 10.1. hardware or software
Redundant Array of Independent (originally Inexpensive) Disks or RAID can be set up using
hardware or software. Hardware RAID is more expensive, but offers better performance.
Software RAID is cheaper and easier to manage, but it uses your CPU and your memory.
Where ten years ago nobody was arguing about the best choice being hardware RAID, this
has changed since technologies like mdadm, lvm and even zfs focus more on managability.
The workload on the cpu for software RAID used to be high, but cpu's have gotten a lot
faster.
## 10.2. raid levels
### 10.2.1. raid 0
raid 0 uses two or more disks, and is often called striping (or stripe set, or striped volume).
Data is divided in chunks, those chunks are evenly spread across every disk in the array.
The main advantage of raid 0 is that you can create larger drives. raid 0 is the only raid
without redundancy.
### 10.2.2. jbod
jbod uses two or more disks, and is often called concatenating (spanning, spanned set, or
spanned volume). Data is written to the first disk, until it is full. Then data is written to the
second disk... The main advantage of jbod (Just a Bunch of Disks) is that you can create
larger drives. JBOD offers no redundancy.
### 10.2.3. raid 1
raid 1 uses exactly two disks, and is often called mirroring (or mirror set, or mirrored
volume). All data written to the array is written on each disk. The main advantage of raid 1
is redundancy. The main disadvantage is that you lose at least half of your available disk
space (in other words, you at least double the cost).
### 10.2.4. raid 2, 3 and 4 ?
raid 2 uses bit level striping, raid 3 byte level, and raid 4 is the same as raid 5, but with a
dedicated parity disk. This is actually slower than raid 5, because every write would have
to write parity to this one (bottleneck) disk. It is unlikely that you will ever see these raid
levels in production.
### 10.2.5. raid 5
raid 5 uses three or more disks, each divided into chunks. Every time chunks are written
to the array, one of the disks will receive a parity chunk. Unlike raid 4, the parity chunk
will alternate between all disks. The main advantage of this is that raid 5 will allow for full
data recovery in case of one hard disk failure.
### 10.2.6. raid 6
raid 6 is very similar to raid 5, but uses two parity chunks. raid 6 protects against two hard
disk failures. Oracle Solaris zfs calls this raidz2 (and also had raidz3 with triple parity).
### 10.2.7. raid 0+1
raid 0+1 is a mirror(1) of stripes(0). This means you first create two raid 0 stripe sets, and
then you set them up as a mirror set. For example, when you have six 100GB disks, then
the stripe sets are each 300GB. Combined in a mirror, this makes 300GB total. raid 0+1
will survive one disk failure. It will only survive the second disk failure if this disk is in the
same stripe set as the previous failed disk.
### 10.2.8. raid 1+0
raid 1+0 is a stripe(0) of mirrors(1). For example, when you have six 100GB disks, then
you first create three mirrors of 100GB each. You then stripe them together into a 300GB
drive. In this example, as long as not all disks in the same mirror fail, it can survive up to
three hard disk failures.
### 10.2.9. raid 50
raid 5+0 is a stripe(0) of raid 5 arrays. Suppose you have nine disks of 100GB, then you
can create three raid 5 arrays of 200GB each. You can then combine them into one large
stripe set.
### 10.2.10. many others
There are many other nested raid combinations, like raid 30, 51, 60, 100, 150, ...
## 10.3. building a software raid5 array
### 10.3.1. do we have three disks?
First, you have to attach some disks to your computer. In this scenario, three brand new disks
of eight gigabyte each are added. Check with fdisk -l that they are connected.
```
[root@rhel6c ~]# fdisk -l 2> /dev/null | grep MB
Disk /dev/sdb: 8589 MB, 8589934592 bytes
Disk /dev/sdc: 8589 MB, 8589934592 bytes
Disk /dev/sdd: 8589 MB, 8589934592 bytes
```
### 10.3.2. fd partition type
The next step is to create a partition of type fd on every disk. The fd type is to set the partition
as Linux RAID autodetect. See this (truncated) screenshot:
```
[root@rhel6c ~]# fdisk /dev/sdd
...
Command (m for help): n
Command action
 e extended
 p primary partition (1-4)
p
Partition number (1-4): 1
First cylinder (1-1044, default 1):
Using default value 1
Last cylinder, +cylinders or +size{K,M,G} (1-1044, default 1044):
Using default value 1044
Command (m for help): t
Selected partition 1
Hex code (type L to list codes): fd
Changed system type of partition 1 to fd (Linux raid autodetect)
Command (m for help): w
The partition table has been altered!
Calling ioctl() to re-read partition table.
Syncing disks.
```
### 10.3.3. verify all three partitions
Now all three disks are ready for raid 5, so we have to tell the system what to do with these
disks.
```
[root@rhel6c ~]# fdisk -l 2> /dev/null | grep raid
/dev/sdb1 1 1044 8385898+ fd Linux raid autodetect
/dev/sdc1 1 1044 8385898+ fd Linux raid autodetect
/dev/sdd1 1 1044 8385898+ fd Linux raid autodetect
```
### 10.3.4. create the raid5
The next step used to be create the raid table in /etc/raidtab. Nowadays, you can just issue
the command mdadm with the correct parameters.
The command below is split on two lines to fit this print, but you should type it on one line,
without the backslash (\).
```
[root@rhel6c ~]# mdadm --create /dev/md0 --chunk=64 --level=5 --raid-\
devices=3 /dev/sdb1 /dev/sdc1 /dev/sdd1
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
```
Below a partial screenshot how fdisk -l sees the raid 5.
```
[root@rhel6c ~]# fdisk -l /dev/md0
Disk /dev/md0: 17.2 GB, 17172135936 bytes
2 heads, 4 sectors/track, 4192416 cylinders
Units = cylinders of 8 * 512 = 4096 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 65536 bytes / 131072 bytes
Disk identifier: 0x00000000
Disk /dev/md0 doesn't contain a valid partition table
```
We could use this software raid 5 array in the next topic: lvm.
### 10.3.5. /proc/mdstat
The status of the raid devices can be seen in /proc/mdstat. This example shows a raid 5
in the process of rebuilding.
```
[root@rhel6c ~]# cat /proc/mdstat
Personalities : [raid6] [raid5] [raid4]
md0 : active raid5 sdd1[3] sdc1[1] sdb1[0]
 16769664 blocks super 1.2 level 5, 64k chunk, algorithm 2 [3/2] [UU_]
 [============>........] recovery = 62.8% (5266176/8384832) finish=0\
.3min speed=139200K/sec
```
This example shows an active software raid 5.
```
[root@rhel6c ~]# cat /proc/mdstat
Personalities : [raid6] [raid5] [raid4]
md0 : active raid5 sdd1[3] sdc1[1] sdb1[0]
 16769664 blocks super 1.2 level 5, 64k chunk, algorithm 2 [3/3] [UUU]
```
### 10.3.6. mdadm --detail
Use mdadm --detail to get information on a raid device.
```
[root@rhel6c ~]# mdadm --detail /dev/md0
/dev/md0:
 Version : 1.2
 Creation Time : Sun Jul 17 13:48:41 2011
 Raid Level : raid5
 Array Size : 16769664 (15.99 GiB 17.17 GB)
 Used Dev Size : 8384832 (8.00 GiB 8.59 GB)
 Raid Devices : 3
 Total Devices : 3
 Persistence : Superblock is persistent
 Update Time : Sun Jul 17 13:49:43 2011
 State : clean
 Active Devices : 3
Working Devices : 3
 Failed Devices : 0
 Spare Devices : 0
 Layout : left-symmetric
 Chunk Size : 64K
 Name : rhel6c:0 (local to host rhel6c)
 UUID : c10fd9c3:08f9a25f:be913027:999c8e1f
 Events : 18
 Number Major Minor RaidDevice State
 0 8 17 0 active sync /dev/sdb1
 1 8 33 1 active sync /dev/sdc1
 3 8 49 2 active sync /dev/sdd1
```
### 10.3.7. removing a software raid
The software raid is visible in /proc/mdstat when active. To remove the raid completely so
you can use the disks for other purposes, you stop (de-activate) it with mdadm.
```
[root@rhel6c ~]# mdadm --stop /dev/md0
mdadm: stopped /dev/md0
```
The disks can now be repartitioned.
### 10.3.8. further reading
Take a look at the man page of mdadm for more information. Below an example command
to add a new partition while removing a faulty one.
```
mdadm /dev/md0 --add /dev/sdd1 --fail /dev/sdb1 --remove /dev/sdb1
```
# Chapter 11. logical volume management
Most lvm implementations support physical storage grouping, logical volume resizing
and data migration.
Physical storage grouping is a fancy name for grouping multiple block devices (hard disks,
but also iSCSI etc) into a logical mass storage device. To enlarge this physical group, block
devices (including partitions) can be added at a later time.
The size of lvm volumes on this physical group is independent of the individual size of the
components. The total size of the group is the limit.
One of the nice features of lvm is the logical volume resizing. You can increase the size of
an lvm volume, sometimes even without any downtime. Additionally, you can migrate data
away from a failing hard disk device, create mirrors and create snapshots.
## 11.1. introduction to lvm
### 11.1.1. problems with standard partitions
There are some problems when working with hard disks and standard partitions. Consider
a system with a small and a large hard disk device, partitioned like this. The first disk (/
dev/sda) is partitioned in two, the second disk (/dev/sdb) has two partitions and some empty
space.
In the example above, consider the options when you want to enlarge the space available
for /srv/project42. What can you do ? The solution will always force you to unmount the
file system, take a backup of the data, remove and recreate partitions, and then restore the
data and remount the file system.
### 11.1.2. solution with lvm
Using lvm will create a virtual layer between the mounted file systems and the hardware
devices. This virtual layer will allow for an administrator to enlarge a mounted file system in
use. When lvm is properly used, then there is no need to unmount the file system to enlarge it.
## 11.2. lvm terminology
### 11.2.1. physical volume (pv)
A physical volume is any block device (a disk, a partition, a RAID device or even an iSCSI
device). All these devices can become a member of a volume group.
The commands used to manage a physical volume start with pv.
```
[root@centos65 ~]# pv
pvchange pvck pvcreate pvdisplay pvmove pvremove
pvresize pvs pvscan
```
### 11.2.2. volume group (vg)
A volume group is an abstraction layer between block devices and logical volumes.
The commands used to manage a volume group start with vg.
```
[root@centos65 ~]# vg
vgcfgbackup vgconvert vgextend vgmknodes vgs
vgcfgrestore vgcreate vgimport vgreduce vgscan
vgchange vgdisplay vgimportclone vgremove vgsplit
vgck vgexport vgmerge vgrename
```
### 11.2.3. logical volume (lv)
A logical volume is created in a volume group. Logical volumes that contain a file system
can be mounted. The use of logical volumes is similar to the use of partitions and is
accomplished with the same standard commands (mkfs, mount, fsck, df, ...).
The commands used to manage a logical volume start with lv.
```
[root@centos65 ~]# lv
lvchange lvextend lvmdiskscan lvmsar lvresize
lvconvert lvm lvmdump lvreduce lvs
lvcreate lvmchange lvmetad lvremove lvscan
lvdisplay lvmconf lvmsadc lvrename
```
## 11.3. example: using lvm
This example shows how you can use a device (in this case /dev/sdc, but it could have been /
dev/sdb or any other disk or partition) with lvm, how to create a volume group (vg) and how
to create and use a logical volume (vg/lvol0).
First thing to do, is create physical volumes that can join the volume group with pvcreate.
This command makes a disk or partition available for use in Volume Groups. The screenshot
shows how to present the SCSI Disk device to LVM.
```
root@RHEL4:~# pvcreate /dev/sdc
Physical volume "/dev/sdc" successfully created
```
Note: lvm will work fine when using the complete device, but another operating system on the
same computer (or on the same SAN) will not recognize lvm and will mark the block device
as being empty! You can avoid this by creating a partition that spans the whole device, then
run pvcreate on the partition instead of the disk.
Then vgcreate creates a volume group using one device. Note that more devices could be
added to the volume group.
```
root@RHEL4:~# vgcreate vg /dev/sdc
Volume group "vg" successfully created
```
The last step lvcreate creates a logical volume.
```
root@RHEL4:~# lvcreate --size 500m vg
Logical volume "lvol0" created
```
The logical volume /dev/vg/lvol0 can now be formatted with ext3, and mounted for normal
use.
```
root@RHELv4u2:~# mke2fs -m0 -j /dev/vg/lvol0
mke2fs 1.35 (28-Feb-2004)
Filesystem label=
OS type: Linux
Block size=1024 (log=0)
Fragment size=1024 (log=0)
128016 inodes, 512000 blocks
0 blocks (0.00%) reserved for the super user
First data block=1
Maximum filesystem blocks=67633152
63 block groups
8192 blocks per group, 8192 fragments per group
2032 inodes per group
Superblock backups stored on blocks:
8193, 24577, 40961, 57345, 73729, 204801, 221185, 401409

Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done

This filesystem will be automatically checked every 37 mounts or
180 days, whichever comes first. Use tune2fs -c or -i to override.
root@RHELv4u2:~# mkdir /home/project10
root@RHELv4u2:~# mount /dev/vg/lvol0 /home/project10/
root@RHELv4u2:~# df -h | grep proj
/dev/mapper/vg-lvol0 485M 11M 474M 3% /home/project10
```
A logical volume is very similar to a partition, it can be formatted with a file system, and
can be mounted so users can access it.
## 11.4. example: extend a logical volume
A logical volume can be extended without unmounting the file system. Whether or not a
volume can be extended depends on the file system it uses. Volumes that are mounted as
vfat or ext2 cannot be extended, so in the example here we use the ext3 file system.
The fdisk command shows us newly added scsi-disks that will serve our lvm volume. This
volume will then be extended. First, take a look at these disks.
```
[root@RHEL5 ~]# fdisk -l | grep sd[bc]
Disk /dev/sdb doesn't contain a valid partition table
Disk /dev/sdc doesn't contain a valid partition table
Disk /dev/sdb: 1181 MB, 1181115904 bytes
Disk /dev/sdc: 429 MB, 429496320 bytes
```
You already know how to partition a disk, below the first disk is partitioned (in one big
primary partition), the second disk is left untouched.
```
[root@RHEL5 ~]# fdisk -l | grep sd[bc]
Disk /dev/sdc doesn't contain a valid partition table
Disk /dev/sdb: 1181 MB, 1181115904 bytes
/dev/sdb1 1 143 1148616 83 Linux
Disk /dev/sdc: 429 MB, 429496320 bytes
```
You also know how to prepare disks for lvm with pvcreate, and how to create a volume
group with vgcreate. This example adds both the partitioned disk and the untouched disk
to the volume group named vg2.
```
[root@RHEL5 ~]# pvcreate /dev/sdb1
 Physical volume "/dev/sdb1" successfully created
[root@RHEL5 ~]# pvcreate /dev/sdc
 Physical volume "/dev/sdc" successfully created
[root@RHEL5 ~]# vgcreate vg2 /dev/sdb1 /dev/sdc
 Volume group "vg2" successfully created
```
You can use pvdisplay to verify that both the disk and the partition belong to the volume
group.
```
[root@RHEL5 ~]# pvdisplay | grep -B1 vg2
 PV Name /dev/sdb1
 VG Name vg2
--
 PV Name /dev/sdc
 VG Name vg2
```
And you are familiar both with the lvcreate command to create a small logical volume and
the mke2fs command to put ext3 on it.
```
[root@RHEL5 ~]# lvcreate --size 200m vg2
 Logical volume "lvol0" created
[root@RHEL5 ~]# mke2fs -m20 -j /dev/vg2/lvol0
...
```

As you see, we end up with a mounted logical volume that according to df is almost 200
megabyte in size.
```
[root@RHEL5 ~]# mkdir /home/resizetest
[root@RHEL5 ~]# mount /dev/vg2/lvol0 /home/resizetest/
[root@RHEL5 ~]# df -h | grep resizetest
 194M 5.6M 149M 4% /home/resizetest
```
Extending the volume is easy with lvextend.
```
[root@RHEL5 ~]# lvextend -L +100 /dev/vg2/lvol0
 Extending logical volume lvol0 to 300.00 MB
 Logical volume lvol0 successfully resized
```
But as you can see, there is a small problem: it appears that df is not able to display the
extended volume in its full size. This is because the filesystem is only set for the size of the
volume before the extension was added.
```
[root@RHEL5 ~]# df -h | grep resizetest
 194M 5.6M 149M 4% /home/resizetest
```
With lvdisplay however we can see that the volume is indeed extended.
```
[root@RHEL5 ~]# lvdisplay /dev/vg2/lvol0 | grep Size
 LV Size 300.00 MB
```
To finish the extension, you need resize2fs to span the filesystem over the full size of the
logical volume.
```
[root@RHEL5 ~]# resize2fs /dev/vg2/lvol0
resize2fs 1.39 (29-May-2006)
Filesystem at /dev/vg2/lvol0 is mounted on /home/resizetest; on-line re\
sizing required
Performing an on-line resize of /dev/vg2/lvol0 to 307200 (1k) blocks.
The filesystem on /dev/vg2/lvol0 is now 307200 blocks long.
```
Congratulations, you just successfully expanded a logical volume.
```
[root@RHEL5 ~]# df -h | grep resizetest
 291M 6.1M 225M 3% /home/resizetest
[root@RHEL5 ~]#
```
## 11.5. example: resize a physical Volume
This is a humble demonstration of how to resize a physical Volume with lvm (after you
resize it with fdisk). The demonstration starts with a 100MB partition named /dev/sde1. We
used fdisk to create it, and to verify the size.
```
[root@RHEL5 ~]# fdisk -l 2>/dev/null | grep sde1
/dev/sde1 1 100 102384 83 Linux
[root@RHEL5 ~]#
```
Now we can use pvcreate to create the Physical Volume, followed by pvs to verify the
creation.
```
[root@RHEL5 ~]# pvcreate /dev/sde1
 Physical volume "/dev/sde1" successfully created
[root@RHEL5 ~]# pvs | grep sde1
 /dev/sde1 lvm2 -- 99.98M 99.98M
[root@RHEL5 ~]#
```
The next step is to use fdisk to enlarge the partition (actually deleting it and then recreating /
dev/sde1 with more cylinders).
```
[root@RHEL5 ~]# fdisk /dev/sde
Command (m for help): p
Disk /dev/sde: 858 MB, 858993152 bytes
64 heads, 32 sectors/track, 819 cylinders
Units = cylinders of 2048 * 512 = 1048576 bytes
 Device Boot Start End Blocks Id System
/dev/sde1 1 100 102384 83 Linux
Command (m for help): d
Selected partition 1
Command (m for help): n
Command action
 e extended
 p primary partition (1-4)
p
Partition number (1-4):
Value out of range.
Partition number (1-4): 1
First cylinder (1-819, default 1):
Using default value 1
Last cylinder or +size or +sizeM or +sizeK (1-819, default 819): 200
Command (m for help): w
The partition table has been altered!
Calling ioctl() to re-read partition table.
Syncing disks.
[root@RHEL5 ~]#
```
When we now use fdisk and pvs to verify the size of the partition and the Physical Volume,
then there is a size difference. LVM is still using the old size.
```
[root@RHEL5 ~]# fdisk -l 2>/dev/null | grep sde1
/dev/sde1 1 200 204784 83 Linux
[root@RHEL5 ~]# pvs | grep sde1
 /dev/sde1 lvm2 -- 99.98M 99.98M
[root@RHEL5 ~]#
```
Executing pvresize on the Physical Volume will make lvm aware of the size change of the
partition. The correct size can be displayed with pvs.
```
[root@RHEL5 ~]# pvresize /dev/sde1
 Physical volume "/dev/sde1" changed
 1 physical volume(s) resized / 0 physical volume(s) not resized
[root@RHEL5 ~]# pvs | grep sde1
 /dev/sde1 lvm2 -- 199.98M 199.98M
[root@RHEL5 ~]#
```
## 11.6. example: mirror a logical volume
We start by creating three physical volumes for lvm. Then we verify the creation and the
size with pvs. Three physical disks because lvm uses two disks for the mirror and a third
disk for the mirror log!
```
[root@RHEL5 ~]# pvcreate /dev/sdb /dev/sdc /dev/sdd
 Physical volume "/dev/sdb" successfully created
 Physical volume "/dev/sdc" successfully created
 Physical volume "/dev/sdd" successfully created
[root@RHEL5 ~]# pvs
 PV VG Fmt Attr PSize PFree
 /dev/sdb lvm2 -- 409.60M 409.60M
 /dev/sdc lvm2 -- 409.60M 409.60M
 /dev/sdd lvm2 -- 409.60M 409.60M
```
Then we create the Volume Group and verify again with pvs. Notice how the three physical
volumes now belong to vg33, and how the size is rounded down (in steps of the extent size,
here 4MB).
```
[root@RHEL5 ~]# vgcreate vg33 /dev/sdb /dev/sdc /dev/sdd
 Volume group "vg33" successfully created
[root@RHEL5 ~]# pvs
 PV VG Fmt Attr PSize PFree
 /dev/sda2 VolGroup00 lvm2 a- 15.88G 0
 /dev/sdb vg33 lvm2 a- 408.00M 408.00M
 /dev/sdc vg33 lvm2 a- 408.00M 408.00M
 /dev/sdd vg33 lvm2 a- 408.00M 408.00M
[root@RHEL5 ~]#
```
The last step is to create the Logical Volume with lvcreate. Notice the -m 1 switch to create
one mirror. Notice also the change in free space in all three Physical Volumes!
```
[root@RHEL5 ~]# lvcreate --size 300m -n lvmir -m 1 vg33
 Logical volume "lvmir" created
[root@RHEL5 ~]# pvs
 PV VG Fmt Attr PSize PFree
 /dev/sda2 VolGroup00 lvm2 a- 15.88G 0
 /dev/sdb vg33 lvm2 a- 408.00M 108.00M
 /dev/sdc vg33 lvm2 a- 408.00M 108.00M
 /dev/sdd vg33 lvm2 a- 408.00M 404.00M
```
You can see the copy status of the mirror with lvs. It currently shows a 100 percent copy.
```
[root@RHEL5 ~]# lvs vg33/lvmir
 LV VG Attr LSize Origin Snap% Move Log Copy%
 lvmir vg33 mwi-ao 300.00M 
```
## 11.7. example: snapshot a logical volume
A snapshot is a virtual copy of all the data at a point in time on a volume. A snapshot Logical
Volume will retain a copy of all changed files of the snapshotted Logical Volume.
The example below creates a snapshot of the bigLV Logical Volume.
```
[root@RHEL5 ~]# lvcreate -L100M -s -n snapLV vg42/bigLV
 Logical volume "snapLV" created
[root@RHEL5 ~]#
```
You can see with lvs that the snapshot snapLV is indeed a snapshot of bigLV. Moments
after taking the snapshot, there are few changes to bigLV (0.02 percent).
```
[root@RHEL5 ~]# lvs
 LV VG Attr LSize Origin Snap% Move Log Copy%
 bigLV vg42 owi-a- 200.00M
 snapLV vg42 swi-a- 100.00M bigLV 0.02
[root@RHEL5 ~]#
```
But after using bigLV for a while, more changes are done. This means the snapshot volume
has to keep more original data (10.22 percent).
```
[root@RHEL5 ~]# lvs | grep vg42
 bigLV vg42 owi-ao 200.00M
 snapLV vg42 swi-a- 100.00M bigLV 10.22
[root@RHEL5 ~]#
```
You can now use regular backup tools (dump, tar, cpio, ...) to take a backup of the snapshot
Logical Volume. This backup will contain all data as it existed on bigLV at the time the
snapshot was taken. When the backup is done, you can remove the snapshot.
```
[root@RHEL5 ~]# lvremove vg42/snapLV
Do you really want to remove active logical volume "snapLV"? [y/n]: y
 Logical volume "snapLV" successfully removed
[root@RHEL5 ~]#
```
## 11.8. verifying existing physical volumes
### 11.8.1. lvmdiskscan
To get a list of block devices that can be used with LVM, use lvmdiskscan. The example
below uses grep to limit the result to SCSI devices.
```
[root@RHEL5 ~]# lvmdiskscan | grep sd
 /dev/sda1 [ 101.94 MB]
 /dev/sda2 [ 15.90 GB] LVM physical volume
 /dev/sdb [ 409.60 MB]
 /dev/sdc [ 409.60 MB]
 /dev/sdd [ 409.60 MB] LVM physical volume
 /dev/sde1 [ 95.98 MB]
 /dev/sde5 [ 191.98 MB]
 /dev/sdf [ 819.20 MB] LVM physical volume
 /dev/sdg1 [ 818.98 MB]
[root@RHEL5 ~]#
```
### 11.8.2. pvs
The easiest way to verify whether devices are known to lvm is with the pvs command. The
screenshot below shows that only /dev/sda2 is currently known for use with LVM. It shows
that /dev/sda2 is part of Volgroup00 and is almost 16GB in size. It also shows /dev/sdc and /
dev/sdd as part of vg33. The device /dev/sdb is knwon to lvm, but not linked to any Volume
Group.
```
[root@RHEL5 ~]# pvs
 PV VG Fmt Attr PSize PFree
 /dev/sda2 VolGroup00 lvm2 a- 15.88G 0
 /dev/sdb lvm2 -- 409.60M 409.60M
 /dev/sdc vg33 lvm2 a- 408.00M 408.00M
 /dev/sdd vg33 lvm2 a- 408.00M 408.00M
[root@RHEL5 ~]#
```
### 11.8.3. pvscan
The pvscan command will scan all disks for existing Physical Volumes. The information is
similar to pvs, plus you get a line with total sizes.
```
[root@RHEL5 ~]# pvscan
 PV /dev/sdc VG vg33 lvm2 [408.00 MB / 408.00 MB free]
 PV /dev/sdd VG vg33 lvm2 [408.00 MB / 408.00 MB free]
 PV /dev/sda2 VG VolGroup00 lvm2 [15.88 GB / 0 free]
 PV /dev/sdb lvm2 [409.60 MB]
 Total: 4 [17.07 GB] / in use: 3 [16.67 GB] / in no VG: 1 [409.60 MB]
[root@RHEL5 ~]#
```
### 11.8.4. pvdisplay
Use pvdisplay to get more information about physical volumes. You can also use pvdisplay
without an argument to display information about all physical (lvm) volumes.
```
[root@RHEL5 ~]# pvdisplay /dev/sda2
 --- Physical volume ---
 PV Name /dev/sda2
 VG Name VolGroup00
 PV Size 15.90 GB / not usable 20.79 MB
 Allocatable yes (but full)
 PE Size (KByte) 32768
 Total PE 508
 Free PE 0
 Allocated PE 508
 PV UUID TobYfp-Ggg0-Rf8r-xtLd-5XgN-RSPc-8vkTHD

[root@RHEL5 ~]#
```
## 11.9. verifying existing volume groups
### 11.9.1. vgs
Similar to pvs is the use of vgs to display a quick overview of all volume groups. There
is only one volume group in the screenshot below, it is named VolGroup00 and is almost
16GB in size.
```
[root@RHEL5 ~]# vgs
 VG #PV #LV #SN Attr VSize VFree
 VolGroup00 1 2 0 wz--n- 15.88G 0
[root@RHEL5 ~]#
```
### 11.9.2. vgscan
The vgscan command will scan all disks for existing Volume Groups. It will also update the
/etc/lvm/.cache file. This file contains a list of all current lvm devices.
```
[root@RHEL5 ~]# vgscan
 Reading all physical volumes. This may take a while...
 Found volume group "VolGroup00" using metadata type lvm2
[root@RHEL5 ~]#
```
LVM will run the vgscan automatically at boot-up, so if you add hot swap devices, then you
will need to run vgscan to update /etc/lvm/.cache with the new devices.
### 11.9.3. vgdisplay
The vgdisplay command will give you more detailed information about a volume group (or
about all volume groups if you omit the argument).
```
[root@RHEL5 ~]# vgdisplay VolGroup00
 --- Volume group ---
 VG Name VolGroup00
 System ID
 Format lvm2
 Metadata Areas 1
 Metadata Sequence No 3
 VG Access read/write
 VG Status resizable
 MAX LV 0
 Cur LV 2
 Open LV 2
 Max PV 0
 Cur PV 1
 Act PV 1
 VG Size 15.88 GB
 PE Size 32.00 MB
 Total PE 508
 Alloc PE / Size 508 / 15.88 GB
 Free PE / Size 0 / 0
 VG UUID qsXvJb-71qV-9l7U-ishX-FobM-qptE-VXmKIg

[root@RHEL5 ~]#
```
## 11.10. verifying existing logical volumes
### 11.10.1. lvs
Use lvs for a quick look at all existing logical volumes. Below you can see two logical
volumes named LogVol00 and LogVol01.
```
[root@RHEL5 ~]# lvs
 LV VG Attr LSize Origin Snap% Move Log Copy%
 LogVol00 VolGroup00 -wi-ao 14.88G
 LogVol01 VolGroup00 -wi-ao 1.00G
[root@RHEL5 ~]#
```
### 11.10.2. lvscan
The lvscan command will scan all disks for existing Logical Volumes.
```
[root@RHEL5 ~]# lvscan
 ACTIVE '/dev/VolGroup00/LogVol00' [14.88 GB] inherit
 ACTIVE '/dev/VolGroup00/LogVol01' [1.00 GB] inherit
[root@RHEL5 ~]#
```
### 11.10.3. lvdisplay
More detailed information about logical volumes is available through the lvdisplay(1)
command.
```
[root@RHEL5 ~]# lvdisplay VolGroup00/LogVol01
 --- Logical volume ---
 LV Name /dev/VolGroup00/LogVol01
 VG Name VolGroup00
 LV UUID RnTGK6-xWsi-t530-ksJx-7cax-co5c-A1KlDp
 LV Write Access read/write
 LV Status available
 # open 1
 LV Size 1.00 GB
 Current LE 32
 Segments 1
 Allocation inherit
 Read ahead sectors 0
 Block device 253:1

[root@RHEL5 ~]#
```
## 11.11. manage physical volumes
### 11.11.1. pvcreate
Use the pvcreate command to add devices to lvm. This example shows how to add a disk
(or hardware RAID device) to lvm.
```
[root@RHEL5 ~]# pvcreate /dev/sdb
 Physical volume "/dev/sdb" successfully created
[root@RHEL5 ~]#
```
This example shows how to add a partition to lvm.
```
[root@RHEL5 ~]# pvcreate /dev/sdc1
 Physical volume "/dev/sdc1" successfully created
[root@RHEL5 ~]#
```
You can also add multiple disks or partitions as target to pvcreate. This example adds three
disks to lvm.
```
[root@RHEL5 ~]# pvcreate /dev/sde /dev/sdf /dev/sdg
 Physical volume "/dev/sde" successfully created
 Physical volume "/dev/sdf" successfully created
 Physical volume "/dev/sdg" successfully created
[root@RHEL5 ~]#
```
### 11.11.2. pvremove
Use the pvremove command to remove physical volumes from lvm. The devices may not
be in use.
```
[root@RHEL5 ~]# pvremove /dev/sde /dev/sdf /dev/sdg
 Labels on physical volume "/dev/sde" successfully wiped
 Labels on physical volume "/dev/sdf" successfully wiped
 Labels on physical volume "/dev/sdg" successfully wiped
[root@RHEL5 ~]#
```
### 11.11.3. pvresize
When you used fdisk to resize a partition on a disk, then you must use pvresize to make lvm
recognize the new size of the physical volume that represents this partition.
```
[root@RHEL5 ~]# pvresize /dev/sde1
 Physical volume "/dev/sde1" changed
 1 physical volume(s) resized / 0 physical volume(s) not resized
```
### 11.11.4. pvchange
With pvchange you can prevent the allocation of a Physical Volume in a new Volume Group
or Logical Volume. This can be useful if you plan to remove a Physical Volume.
```
[root@RHEL5 ~]# pvchange -xn /dev/sdd
 Physical volume "/dev/sdd" changed
 1 physical volume changed / 0 physical volumes not changed
[root@RHEL5 ~]#
```
To revert your previous decision, this example shows you how te re-enable the Physical
Volume to allow allocation.
```
[root@RHEL5 ~]# pvchange -xy /dev/sdd
 Physical volume "/dev/sdd" changed
 1 physical volume changed / 0 physical volumes not changed
[root@RHEL5 ~]#
```
### 11.11.5. pvmove
With pvmove you can move Logical Volumes from within a Volume Group to another
Physical Volume. This must be done before removing a Physical Volume.
```
[root@RHEL5 ~]# pvs | grep vg1
 /dev/sdf vg1 lvm2 a- 816.00M 0
 /dev/sdg vg1 lvm2 a- 816.00M 816.00M
[root@RHEL5 ~]# pvmove /dev/sdf
 /dev/sdf: Moved: 70.1%
 /dev/sdf: Moved: 100.0%
[root@RHEL5 ~]# pvs | grep vg1
 /dev/sdf vg1 lvm2 a- 816.00M 816.00M
 /dev/sdg vg1 
```
## 11.12. manage volume groups
### 11.12.1. vgcreate
Use the vgcreate command to create a volume group. You can immediately name all the
physical volumes that span the volume group.
```
[root@RHEL5 ~]# vgcreate vg42 /dev/sde /dev/sdf
 Volume group "vg42" successfully created
[root@RHEL5 ~]#
```
### 11.12.2. vgextend
Use the vgextend command to extend an existing volume group with a physical volume.
```
[root@RHEL5 ~]# vgextend vg42 /dev/sdg
 Volume group "vg42" successfully extended
[root@RHEL5 ~]#
```
### 11.12.3. vgremove
Use the vgremove command to remove volume groups from lvm. The volume groups may
not be in use.
```
[root@RHEL5 ~]# vgremove vg42
 Volume group "vg42" successfully removed
[root@RHEL5 ~]#
```
### 11.12.4. vgreduce
Use the vgreduce command to remove a Physical Volume from the Volume Group.
The following example adds Physical Volume /dev/sdg to the vg1 Volume Group using
vgextend. And then removes it again using vgreduce.
```
[root@RHEL5 ~]# pvs | grep sdg
 /dev/sdg lvm2 -- 819.20M 819.20M
[root@RHEL5 ~]# vgextend vg1 /dev/sdg
 Volume group "vg1" successfully extended
[root@RHEL5 ~]# pvs | grep sdg
 /dev/sdg vg1 lvm2 a- 816.00M 816.00M
[root@RHEL5 ~]# vgreduce vg1 /dev/sdg
 Removed "/dev/sdg" from volume group "vg1"
[root@RHEL5 ~]# pvs | grep sdg
 /dev/sdg lvm2 -- 819.20M 819.20M
```
### 11.12.5. vgchange
Use the vgchange command to change parameters of a Volume Group.
This example shows how to prevent Physical Volumes from being added or removed to the
Volume Group vg1.
```
[root@RHEL5 ~]# vgchange -xn vg1
 Volume group "vg1" successfully changed
[root@RHEL5 ~]# vgextend vg1 /dev/sdg
 Volume group vg1 is not resizable.
```
You can also use vgchange to change most other properties of a Volume Group. This
example changes the maximum number of Logical Volumes and maximum number of
Physical Volumes that vg1 can serve.
```
[root@RHEL5 ~]# vgdisplay vg1 | grep -i max
 MAX LV 0
 Max PV 0
[root@RHEL5 ~]# vgchange -l16 vg1
 Volume group "vg1" successfully changed
[root@RHEL5 ~]# vgchange -p8 vg1
 Volume group "vg1" successfully changed
[root@RHEL5 ~]# vgdisplay vg1 | grep -i max
 MAX LV 16
 Max PV 8
```
### 11.12.6. vgmerge
Merging two Volume Groups into one is done with vgmerge. The following example merges
vg2 into vg1, keeping all the properties of vg1.
```
[root@RHEL5 ~]# vgmerge vg1 vg2
 Volume group "vg2" successfully merged into "vg1"
[root@RHEL5 ~]#
```
## 11.13. manage logical volumes
### 11.13.1. lvcreate
Use the lvcreate command to create Logical Volumes in a Volume Group. This example
creates an 8GB Logical Volume in Volume Group vg42.
```
[root@RHEL5 ~]# lvcreate -L5G vg42
 Logical volume "lvol0" created
[root@RHEL5 ~]#
```
As you can see, lvm automatically names the Logical Volume lvol0. The next example
creates a 200MB Logical Volume named MyLV in Volume Group vg42.
```
[root@RHEL5 ~]# lvcreate -L200M -nMyLV vg42
 Logical volume "MyLV" created
[root@RHEL5 ~]#
```
The next example does the same thing, but with different syntax.
```
[root@RHEL5 ~]# lvcreate --size 200M -n MyLV vg42
 Logical volume "MyLV" created
[root@RHEL5 ~]#
```
This example creates a Logical Volume that occupies 10 percent of the Volume Group.
```
[root@RHEL5 ~]# lvcreate -l 10%VG -n MyLV2 vg42
 Logical volume "MyLV2" created
[root@RHEL5 ~]#
```
This example creates a Logical Volume that occupies 30 percent of the remaining free space
in the Volume Group.
```
[root@RHEL5 ~]# lvcreate -l 30%FREE -n MyLV3 vg42
 Logical volume "MyLV3" created
[root@RHEL5 ~]#
```
### 11.13.2. lvremove
Use the lvremove command to remove Logical Volumes from a Volume Group. Removing
a Logical Volume requires the name of the Volume Group.
```
[root@RHEL5 ~]# lvremove vg42/MyLV
Do you really want to remove active logical volume "MyLV"? [y/n]: y
 Logical volume "MyLV" successfully removed
[root@RHEL5 ~]#
```
Removing multiple Logical Volumes will request confirmation for each individual volume.
```
[root@RHEL5 ~]# lvremove vg42/MyLV vg42/MyLV2 vg42/MyLV3
Do you really want to remove active logical volume "MyLV"? [y/n]: y
 Logical volume "MyLV" successfully removed
Do you really want to remove active logical volume "MyLV2"? [y/n]: y
 Logical volume "MyLV2" successfully removed
Do you really want to remove active logical volume "MyLV3"? [y/n]: y
 Logical volume "MyLV3" successfully removed
[root@RHEL5 ~]#
```
### 11.13.3. lvextend
Extending the volume is easy with lvextend. This example extends a 200MB Logical
Volume with 100 MB.
```
[root@RHEL5 ~]# lvdisplay /dev/vg2/lvol0 | grep Size
 LV Size 200.00 MB
[root@RHEL5 ~]# lvextend -L +100 /dev/vg2/lvol0
 Extending logical volume lvol0 to 300.00 MB
 Logical volume lvol0 successfully resized
[root@RHEL5 ~]# lvdisplay /dev/vg2/lvol0 | grep Size
 LV Size 300.00 MB
```
The next example creates a 100MB Logical Volume, and then extends it to 500MB.
```
[root@RHEL5 ~]# lvcreate --size 100M -n extLV vg42
 Logical volume "extLV" created
[root@RHEL5 ~]# lvextend -L 500M vg42/extLV
 Extending logical volume extLV to 500.00 MB
 Logical volume extLV successfully resized
[root@RHEL5 ~]#
```
This example doubles the size of a Logical Volume.
```
[root@RHEL5 ~]# lvextend -l+100%LV vg42/extLV
 Extending logical volume extLV to 1000.00 MB
 Logical volume extLV successfully resized
[root@RHEL5 ~]#
```
### 11.13.4. lvrename
Renaming a Logical Volume is done with lvrename. This example renames extLV to bigLV
in the vg42 Volume Group.
```
[root@RHEL5 ~]# lvrename vg42/extLV vg42/bigLV
 Renamed "extLV" to "bigLV" in volume group "vg42"
[root@RHEL5 ~]#
```
