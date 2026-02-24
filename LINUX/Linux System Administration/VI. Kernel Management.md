# VI. Kernel Management
# Chapter 28. the Linux kernel
## 28.1. about the Linux kernel 
### 28.1.1. kernel versions 
In 1991 Linux Torvalds wrote (the first version of) the Linux kernel. He put it online, and other people started contributing code. Over 4000 individuals contributed source code to the latest kernel release (version 2.6.27 in November 2008). Major Linux kernel versions used to come in even and odd numbers. Versions 2.0, 2.2, 2.4 and 2.6 are considered stable kernel versions. Whereas 2.1, 2.3 and 2.5 were unstable (read development) versions. Since the release of 2.6.0 in January 2004, all development has been done in the 2.6 tree. There is currently no v2.7.x and according to Linus the even/stable vs odd/development scheme is abandoned forever. 
### 28.1.2. uname -r 
To see your current Linux kernel version, issue the uname -r command as shown below. This first example shows Linux major version 2.6 and minor version 24. The rest -22-generic is specific to the distribution (Ubuntu in this case). 
```
paul@laika:~$ uname -r 
2.6.24-22-generic 
```
The same command on Red Hat Enterprise Linux shows an older kernel (2.6.18) with -92.1.17.el5 being specific to the distribution. 
```
[paul@RHEL52 ~]$ uname -r 
2.6.18-92.1.17.el5 
```
### 28.1.3. /proc/cmdline 
The parameters that were passed to the kernel at boot time are in /proc/cmdline. 
```
paul@RHELv4u4:~$ cat /proc/cmdline 
ro root=/dev/VolGroup00/LogVol00 rhgb quiet
```
### 28.1.4. single user mode 
When booting the kernel with the single parameter, it starts in single user mode. Linux can start in a bash shell with the root user logged on (without password). Some distributions prevent the use of this feature (at kernel compile time). 
### 28.1.5. init=/bin/bash 
Normally the kernel invokes init as the first daemon process. Adding init=/bin/bash to the kernel parameters will instead invoke bash (again with root logged on without providing a password).
### 28.1.6. /var/log/messages 
The kernel reports during boot to syslog which writes a lot of kernel actions in /var/log/ messages. Looking at this file reveals when the kernel was started, including all the devices that were detected at boot time.
```
[root@RHEL53 ~]# grep -A16 "syslogd 1.4.1:" /var/log/messages|cut -b24- syslogd 1.4.1: restart. 
kernel: klogd 1.4.1, log source = /proc/kmsg started. 
kernel: Linux version 2.6.18-128.el5 (mockbuild@hs20-bc1-5.build.red... 
kernel: BIOS-provided physical RAM map: 
kernel: BIOS-e820: 0000000000000000 - 000000000009f800 (usable) 
kernel: BIOS-e820: 000000000009f800 - 00000000000a0000 (reserved) 
kernel: BIOS-e820: 00000000000ca000 - 00000000000cc000 (reserved) 
kernel: BIOS-e820: 00000000000dc000 - 0000000000100000 (reserved) 
kernel: BIOS-e820: 0000000000100000 - 000000001fef0000 (usable) 
kernel: BIOS-e820: 000000001fef0000 - 000000001feff000 (ACPI data) 
kernel: BIOS-e820: 000000001feff000 - 000000001ff00000 (ACPI NVS) 
kernel: BIOS-e820: 000000001ff00000 - 0000000020000000 (usable) 
kernel: BIOS-e820: 00000000fec00000 - 00000000fec10000 (reserved) 
kernel: BIOS-e820: 00000000fee00000 - 00000000fee01000 (reserved) 
kernel: BIOS-e820: 00000000fffe0000 - 0000000100000000 (reserved) 
kernel: 0MB HIGHMEM available. kernel: 512MB LOWMEM available. 
```
This example shows how to use /var/log/messages to see kernel information about /dev/sda.
```
[root@RHEL53 ~]# grep sda /var/log/messages | cut -b24- 
kernel: SCSI device sda: 41943040 512-byte hdwr sectors (21475 MB) 
kernel: sda: Write Protect is off 
kernel: sda: cache data unavailable 
kernel: sda: assuming drive cache: write through 
kernel: SCSI device sda: 41943040 512-byte hdwr sectors (21475 MB) 
kernel: sda: Write Protect is off 
kernel: sda: cache data unavailable 
kernel: sda: assuming drive cache: write through 
kernel: sda: sda1 sda2 
kernel: sd 0:0:0:0: Attached scsi disk sda kernel: EXT3 FS on sda1, internal journal
```
### 28.1.7. dmesg 
The dmesg command prints out all the kernel bootup messages (from the last boot). 
```
[root@RHEL53 ~]# dmesg | head Linux version 2.6.18-128.el5 (mockbuild@hs20-bc1-5.build.redhat.com) BIOS-provided physical RAM map: 
BIOS-e820: 0000000000000000 - 000000000009f800 (usable) 
BIOS-e820: 000000000009f800 - 00000000000a0000 (reserved) 
BIOS-e820: 00000000000ca000 - 00000000000cc000 (reserved) 
BIOS-e820: 00000000000dc000 - 0000000000100000 (reserved) 
BIOS-e820: 0000000000100000 - 000000001fef0000 (usable) 
BIOS-e820: 000000001fef0000 - 000000001feff000 (ACPI data) 
BIOS-e820: 000000001feff000 - 000000001ff00000 (ACPI NVS) 
BIOS-e820: 000000001ff00000 - 0000000020000000 (usable) 
```
Thus to find information about /dev/sda, using dmesg will yield only kernel messages from the last boot.
```
[root@RHEL53 ~]# dmesg | grep sda 
SCSI device sda: 41943040 512-byte hdwr sectors (21475 MB) 
sda: Write Protect is off 
sda: Mode Sense: 5d 00 00 00 
sda: cache data unavailable 
sda: assuming drive cache: write through SCSI device 
sda: 41943040 512-byte hdwr sectors (21475 MB) 
sda: Write Protect is off 
sda: Mode Sense: 5d 00 00 00 
sda: cache data unavailable 
sda: assuming drive cache: write through 
sda: sda1 sda2 
sd 0:0:0:0: Attached scsi disk sda 
EXT3 FS on sda1, internal journal
```
## 28.2. Linux kernel source 
### 28.2.1. ftp.kernel.org 
The home of the Linux kernel source is ftp.kernel.org. It contains all official releases of the Linux kernel source code from 1991. It provides free downloads over http, ftp and rsync of all these releases, as well as changelogs and patches. More information can be otained on the website www.kernel.org. Anyone can anonymously use an ftp client to access ftp.kernel.org
```
paul@laika:~$ ftp ftp.kernel.org 
Connected to pub3.kernel.org. 
220 Welcome to ftp.kernel.org. 
Name (ftp.kernel.org:paul): anonymous 
331 Please specify the password. 
Password: 
230- Welcome to the 
230- 230- LINUX KERNEL ARCHIVES 
230- ftp.kernel.org 
```
All the Linux kernel versions are located in the pub/linux/kernel/ directory. 
```
ftp> ls pub/linux/kernel/v* 
200 PORT command successful. Consider using PASV. 
150 Here comes the directory listing. 
drwxrwsr-x 2 536 536 4096 Mar 20 2003 v1.0 
drwxrwsr-x 2 536 536 20480 Mar 20 2003 v1.1 
drwxrwsr-x 2 536 536 8192 Mar 20 2003 v1.2 
drwxrwsr-x 2 536 536 40960 Mar 20 2003 v1.3 
drwxrwsr-x 3 536 536 16384 Feb 08 2004 v2.0 
drwxrwsr-x 2 536 536 53248 Mar 20 2003 v2.1 
drwxrwsr-x 3 536 536 12288 Mar 24 2004 v2.2 
drwxrwsr-x 2 536 536 24576 Mar 20 2003 v2.3 
drwxrwsr-x 5 536 536 28672 Dec 02 08:14 v2.4 
drwxrwsr-x 4 536 536 32768 Jul 14 2003 v2.5 
drwxrwsr-x 7 536 536 110592 Dec 05 22:36 v2.6 
226 Directory send OK. 
ftp>
```
### 28.2.2. /usr/src 
On your local computer, the kernel source is located in /usr/src. Note though that the structure inside /usr/src might be different depending on the distribution that you are using. 
First let's take a look at /usr/src on Debian. There appear to be two versions of the complete Linux source code there. Looking for a specific file (e1000_main.c) with find reveals it's exact location. 
```
paul@barry:~$ ls -l /usr/src/ 
drwxr-xr-x 20 root root 4096 2006-04-04 22:12 linux-source-2.6.15 
drwxr-xr-x 19 root root 4096 2006-07-15 17:32 linux-source-2.6.16 
paul@barry:~$ find /usr/src -name e1000_main.c 
/usr/src/linux-source-2.6.15/drivers/net/e1000/e1000_main.c 
/usr/src/linux-source-2.6.16/drivers/net/e1000/e1000_main.c 
```
This is very similar to /usr/src on Ubuntu, except there is only one kernel here (and it is newer). 
```
paul@laika:~$ ls -l /usr/src/ 
drwxr-xr-x 23 root root 4096 2008-11-24 23:28 linux-source-2.6.24 
paul@laika:~$ find /usr/src -name "e1000_main.c" 
/usr/src/linux-source-2.6.24/drivers/net/e1000/e1000_main.c
``` 
Now take a look at /usr/src on Red Hat Enterprise Linux.
```
[paul@RHEL52 ~]$ ls -l /usr/src/ 
drwxr-xr-x 5 root root 4096 Dec 5 19:23 kernels 
drwxr-xr-x 7 root root 4096 Oct 11 13:22 redhat 
```
We will have to dig a little deeper to find the kernel source on Red Hat! 
```
[paul@RHEL52 ~]$ cd /usr/src/redhat/BUILD/ 
[paul@RHEL52 BUILD]$ find . -name "e1000_main.c" 
./kernel-2.6.18/linux-2.6.18.i686/drivers/net/e1000/e1000_main.c
```
### 28.2.3. downloading the kernel source 
### Debian 
Installing the kernel source on Debian is really simple with aptitude install linux-source. You can do a search for all linux-source packeges first, like in this screenshot. 
```
root@barry:~# aptitude search linux-source 
v linux-source - 
v linux-source-2.6 - 
id linux-source-2.6.15 - Linux kernel source for version 2.6.15 
i linux-source-2.6.16 - Linux kernel source for version 2.6.16 
p linux-source-2.6.18 - Linux kernel source for version 2.6.18 
p linux-source-2.6.24 - Linux kernel source for version 2.6.24 
```
And then use aptitude install to download and install the Debian Linux kernel source code. 
```
root@barry:~# aptitude install linux-source-2.6.24 
```
When the aptitude is finished, you will see a new file named /usr/src/linux-source- .tar.bz2 
```
root@barry:/usr/src# ls -lh 
drwxr-xr-x 20 root root 4.0K 2006-04-04 22:12 linux-source-2.6.15 
drwxr-xr-x 19 root root 4.0K 2006-07-15 17:32 linux-source-2.6.16 
-rw-r--r-- 1 root root 45M 2008-12-02 10:56 linux-source-2.6.24.tar.bz2 
```
### Ubuntu 
Ubuntu is based on Debian and also uses aptitude, so the task is very similar. 
```
root@laika:~# aptitude search linux-source 
i linux-source - Linux kernel source with Ubuntu patches 
v linux-source-2.6 - 
i A linux-source-2.6.24 - Linux kernel source for version 2.6.24 
root@laika:~# aptitude install linux-source
```
And when aptitude finishes, we end up with a /usr/src/linux-source-.tar.bz file.
```
oot@laika:~# ll /usr/src total 45M 
-rw-r--r-- 1 root root 45M 2008-11-24 23:30 linux-source-2.6.24.tar.bz2
```
## 28.3. kernel boot files 
### 28.3.1. vmlinuz 
The vmlinuz file in /boot is the compressed kernel. 
```
paul@barry:~$ ls -lh /boot | grep vmlinuz 
-rw-r--r-- 1 root root 1.2M 2006-03-06 16:22 vmlinuz-2.6.15-1-486 
-rw-r--r-- 1 root root 1.1M 2006-03-06 16:30 vmlinuz-2.6.15-1-686 
-rw-r--r-- 1 root root 1.3M 2008-02-11 00:00 vmlinuz-2.6.18-6-686 
paul@barry:~$ 
```
### 28.3.2. initrd 
The kernel uses initrd (an initial RAM disk) at boot time. The initrd is mounted before the kernel loads, and can contain additional drivers and modules. It is a compressed cpio archive, so you can look at the contents in this way.
```
root@RHELv4u4:/boot# mkdir /mnt/initrd 
root@RHELv4u4:/boot# cp initrd-2.6.9-42.0.3.EL.img TMPinitrd.gz root@RHELv4u4:/boot# gunzip TMPinitrd.gz 
root@RHELv4u4:/boot# file TMPinitrd 
TMPinitrd: ASCII cpio archive (SVR4 with no CRC) 
root@RHELv4u4:/boot# cd /mnt/initrd/ 
root@RHELv4u4:/mnt/initrd# cpio -i | /boot/TMPinitrd 4985 blocks root@RHELv4u4:/mnt/initrd# ls -l total 76 
drwxr-xr-x 2 root root 4096 Feb 5 08:36 bin 
drwxr-xr-x 2 root root 4096 Feb 5 08:36 dev 
drwxr-xr-x 4 root root 4096 Feb 5 08:36 etc 
-rwxr-xr-x 1 root root 1607 Feb 5 08:36 init 
drwxr-xr-x 2 root root 4096 Feb 5 08:36 lib 
drwxr-xr-x 2 root root 4096 Feb 5 08:36 loopfs 
drwxr-xr-x 2 root root 4096 Feb 5 08:36 proc 
lrwxrwxrwx 1 root root 3 Feb 5 08:36 sbin -> bin 
drwxr-xr-x 2 root root 4096 Feb 5 08:36 sys 
drwxr-xr-x 2 root root 4096 Feb 5 08:36 sysroot 
root@RHELv4u4:/mnt/initrd#
```
### 28.3.3. System.map 
The System.map contains the symbol table and changes with every kernel compile. The symbol table is also present in /proc/kallsyms (pre 2.6 kernels name this file /proc/ksyms). 
```
root@RHELv4u4:/boot# head System.map-`uname -r` 
00000400 A __kernel_vsyscall 
0000041a A SYSENTER_RETURN_OFFSET 
00000420 A __kernel_sigreturn 
00000440 A __kernel_rt_sigreturn 
c0100000 A _text 
c0100000 T startup_32 
c01000c6 t checkCPUtype 
c0100147 t is486 
c010014e t is386 
c010019f t L6 
root@RHELv4u4:/boot# head /proc/kallsyms 
c0100228 t _stext 
c0100228 t calibrate_delay_direct 
c0100228 t stext 
c0100337 t calibrate_delay 
c01004db t rest_init 
c0100580 t do_pre_smp_initcalls 
c0100585 t run_init_process 
c01005ac t init 
c0100789 t early_param_test 
c01007ad t early_setup_test 
root@RHELv4u4:/boot# 
```
### 28.3.4. .config 
The last file copied to the /boot directory is the kernel configuration used for compilation. This file is not necessary in the /boot directory, but it is common practice to put a copy there. It allows you to recompile a kernel, starting from the same configuration as an existing working one.
## 28.4. Linux kernel modules 
### 28.4.1. about kernel modules 
The Linux kernel is a monolithic kernel with loadable modules. These modules contain parts of the kernel used typically for device drivers, file systems and network protocols. Most of the time the necessary kernel modules are loaded automatically and dynamically without administrator interaction. 
### 28.4.2. /lib/modules 
The modules are stored in the /lib/modules/ directory. There is a separate directory for each kernel that was compiled for your system. 
```
paul@laika:~$ ll /lib/modules/ total 12K 
drwxr-xr-x 7 root root 4.0K 2008-11-10 14:32 2.6.24-16-generic 
drwxr-xr-x 8 root root 4.0K 2008-12-06 15:39 2.6.24-21-generic 
drwxr-xr-x 8 root root 4.0K 2008-12-05 12:58 2.6.24-22-generic 
```
### 28.4.3. .ko 
The file containing the modules usually ends in .ko. This screenshot shows the location of the isdn module files. 
```
paul@laika:~$ find /lib/modules -name isdn.ko 
/lib/modules/2.6.24-21-generic/kernel/drivers/isdn/i4l/isdn.ko 
/lib/modules/2.6.24-22-generic/kernel/drivers/isdn/i4l/isdn.ko 
/lib/modules/2.6.24-16-generic/kernel/drivers/isdn/i4l/isdn.ko 
```
### 28.4.4. lsmod 
To see a list of currently loaded modules, use lsmod. You see the name of each loaded module, the size, the use count, and the names of other modules using this one.
```
[root@RHEL52 ~]# lsmod | head -5 
Module Size Used by 
autofs4 24517 2 
hidp 23105 2 
rfcomm 42457 0 
 l2cap 29505 10 hidp,rfcomm
```
### 28.4.5. /proc/modules 
/proc/modules lists all modules loaded by the kernel. The output would be too long to display here, so lets grep for the vm module. We see that vmmon and vmnet are both loaded. You can display the same information with lsmod. Actually lsmod only reads and reformats the output of /proc/modules. 
```
paul@laika:~$ cat /proc/modules | grep vm 
vmnet 36896 13 - Live 0xffffffff88b21000 (P) 
vmmon 194540 0 - Live 0xffffffff88af0000 (P) 
paul@laika:~$ lsmod | grep vm 
vmnet 36896 13 
vmmon 194540 0 
paul@laika:~$ 
```
### 28.4.6. module dependencies 
Some modules depend on others. In the following example, you can see that the nfsd module is used by exportfs, lockd and sunrpc. 
```
paul@laika:~$ cat /proc/modules | grep nfsd 
nfsd 267432 17 - Live 0xffffffff88a40000 
exportfs 7808 1 nfsd, Live 0xffffffff88a3d000 
lockd 73520 3 nfs,nfsd, Live 0xffffffff88a2a000 
sunrpc 185032 12 nfs,nfsd,lockd, Live 0xffffffff889fb000 
paul@laika:~$ lsmod | grep nfsd 
nfsd 267432 17 
exportfs 7808 1 nfsd 
lockd 73520 3 nfs,nfsd 
sunrpc 185032 12 nfs,nfsd,lockd 
paul@laika:~$
```
### 28.4.7. insmod 
Kernel modules can be manually loaded with the insmod command. This is a very simple (and obsolete) way of loading modules. The screenshot shows insmod loading the fat module (for fat file system support). 
```
root@barry:/lib/modules/2.6.17-2-686# lsmod | grep fat root@barry:/lib/modules/2.6.17-2-686# insmod kernel/fs/fat/fat.ko root@barry:/lib/modules/2.6.17-2-686# lsmod | grep fat 
fat 46588 0 
```
insmod is not detecting dependencies, so it fails to load the isdn module (because the isdn module depends on the slhc module).
```
[root@RHEL52 drivers]# pwd 
/lib/modules/2.6.18-92.1.18.el5/kernel/drivers 
[root@RHEL52 kernel]# insmod isdn/i4l/isdn.ko 
insmod: error inserting 'isdn/i4l/isdn.ko': -1 Unknown symbol in module 
```
### 28.4.8. modinfo 
As you can see in the screenshot of modinfo below, the isdn module depends in the slhc module.
```
[root@RHEL52 drivers]# modinfo isdn/i4l/isdn.ko | head -6 
filename: isdn/i4l/isdn.ko 
license: GPL 
author: Fritz Elfert 
description: ISDN4Linux: link layer 
srcversion: 99650346E708173496F6739 
depends: slhc 
```
### 28.4.9. modprobe 
The big advantage of modprobe over insmod is that modprobe will load all necessary modules, whereas insmod requires manual loading of dependencies. Another advantage is that you don't need to point to the filename with full path. This screenshot shows how modprobe loads the isdn module, automatically loading slhc in background.
```
[root@RHEL52 kernel]# lsmod | grep isdn 
[root@RHEL52 kernel]# modprobe isdn 
[root@RHEL52 kernel]# lsmod | grep isdn 
isdn 122433 0 
slhc 10561 1 isdn 
[root@RHEL52 kernel]#
```
### 28.4.10. /lib/modules//modules.dep 
Module dependencies are stored in modules.dep. 
```
[root@RHEL52 2.6.18-92.1.18.el5]# pwd 
/lib/modules/2.6.18-92.1.18.el5 
[root@RHEL52 2.6.18-92.1.18.el5]# head -3 modules.dep 
/lib/modules/2.6.18-92.1.18.el5/kernel/drivers/net/tokenring/3c359.ko: /lib/modules/2.6.18-92.1.18.el5/kernel/drivers/net/pcmcia/3c574_cs.ko: /lib/modules/2.6.18-92.1.18.el5/kernel/drivers/net/pcmcia/3c589_cs.ko: 
```
### 28.4.11. depmod 
The modules.dep file can be updated (recreated) with the depmod command. In this screenshot no modules were added, so depmod generates the same file.
```
root@barry:/lib/modules/2.6.17-2-686# ls -l modules.dep 
-rw-r--r-- 1 root root 310676 2008-03-01 16:32 modules.dep root@barry:/lib/modules/2.6.17-2-686# depmod 
root@barry:/lib/modules/2.6.17-2-686# ls -l modules.dep 
-rw-r--r-- 1 root root 310676 2008-12-07 13:54 modules.dep 
```
### 28.4.12. rmmod 
Similar to insmod, the rmmod command is rarely used anymore. 
```
[root@RHELv4u3 ~]# modprobe isdn 
[root@RHELv4u3 ~]# rmmod slhc 
ERROR: Module slhc is in use by isdn 
[root@RHELv4u3 ~]# rmmod isdn 
[root@RHELv4u3 ~]# rmmod slhc 
[root@RHELv4u3 ~]# lsmod | grep isdn 
[root@RHELv4u3 ~]# 
```
### 28.4.13. modprobe -r 
Contrary to rmmod, modprobe will automatically remove unneeded modules. 
```
[root@RHELv4u3 ~]# modprobe isdn 
[root@RHELv4u3 ~]# lsmod | grep isdn 
isdn 133537 0 
slhc 7233 1 isdn 
[root@RHELv4u3 ~]# modprobe -r isdn 
[root@RHELv4u3 ~]# lsmod | grep isdn 
[root@RHELv4u3 ~]# lsmod | grep slhc 
[root@RHELv4u3 ~]# 
```
### 28.4.14. /etc/modprobe.conf 
The /etc/modprobe.conf file and the /etc/modprobe.d directory can contain aliases (used by humans) and options (for dependent modules) for modprobe. 
```
[root@RHEL52 ~]# cat /etc/modprobe.conf 
alias scsi_hostadapter mptbase 
alias scsi_hostadapter1 mptspi 
alias scsi_hostadapter2 ata_piix
alias eth0 pcnet32 
alias eth2 pcnet32
alias eth1 pcnet32
```
## 28.5. compiling a kernel 
### 28.5.1. extraversion 
Enter into /usr/src/redhat/BUILD/kernel-2.6.9/linux-2.6.9/ and change the extraversion in the Makefile. 
```
[root@RHEL52 linux-2.6.18.i686]# pwd 
/usr/src/redhat/BUILD/kernel-2.6.18/linux-2.6.18.i686 
[root@RHEL52 linux-2.6.18.i686]# vi Makefile 
[root@RHEL52 linux-2.6.18.i686]# head -4 Makefile 
VERSION = 2 
PATCHLEVEL = 6 
SUBLEVEL = 18 
EXTRAVERSION = -paul2008 
```
### 28.5.2. make mrproper 
Now clean up the source from any previous installs with make mrproper. If this is your first after downloading the source code, then this is not needed. 
```
[root@RHEL52 linux-2.6.18.i686]# make mrproper 
CLEAN scripts/basic 
CLEAN scripts/kconfig 
CLEAN include/config 
CLEAN .config .config.old 
```
### 28.5.3. .config 
Now copy a working .config from /boot to our kernel directory. This file contains the configuration that was used for your current working kernel. It determines whether modules are included in compilation or not. 
```
[root@RHEL52 linux-2.6.18.i686]# cp /boot/config-2.6.18-92.1.18.el5 .config
```
### 28.5.4. make menuconfig
Now run make menuconfig (or the graphical make xconfig). This tool allows you to select whether to compile stuff as a module (m), as part of the kernel `(*),` or not at all (smaller kernel size). If you remove too much, your kernel will not work. The configuration will be stored in the hidden .config file.
```
[root@RHEL52 linux-2.6.18.i686]# make menuconfig 
```
### 28.5.5. make clean 
Issue a make clean to prepare the kernel for compile. make clean will remove most generated files, but keeps your kernel configuration. Running a make mrproper at this point would destroy the .config file that you built with make menuconfig. 
```
[root@RHEL52 linux-2.6.18.i686]# make clean
```
### 28.5.6. make bzImage 
And then run make bzImage, sit back and relax while the kernel compiles. You can use time make bzImage to know how long it takes to compile, so next time you can go for a short walk. 
```
[root@RHEL52 linux-2.6.18.i686]# time make bzImage 
HOSTCC scripts/basic/fixdep 
HOSTCC scripts/basic/docproc 
HOSTCC scripts/kconfig/conf.o 
HOSTCC scripts/kconfig/kxgettext.o 
... 
```
This command will end with telling you the location of the bzImage file (and with time info if you also specified the time command. 
```
Kernel: arch/i386/boot/bzImage is ready (#1) 
real 13m59.573s 
user 1m22.631s 
sys 11m51.034s 
[root@RHEL52 linux-2.6.18.i686]# 
```
You can already copy this image to /boot with cp arch/i386/boot/bzImage /boot/vmlinuz- . 
### 28.5.7. make modules 
Now run make modules. It can take 20 to 50 minutes to compile all the modules. 
```
[root@RHEL52 linux-2.6.18.i686]# time make modules 
CHK include/linux/version.h 
CHK include/linux/utsrelease.h 
CC [M] arch/i386/kernel/msr.o 
CC [M] arch/i386/kernel/cpuid.o 
CC [M] arch/i386/kernel/microcode.o 
```
### 28.5.8. make modules_install 
To copy all the compiled modules to /lib/modules just run make modules_install (takes about 20 seconds). Here's a screenshot from before the command. 
```
[root@RHEL52 linux-2.6.18.i686]# ls -l /lib/modules/ 
total 20 
drwxr-xr-x 6 root root 4096 Oct 15 13:09 2.6.18-92.1.13.el5 
drwxr-xr-x 6 root root 4096 Nov 11 08:51 2.6.18-92.1.17.el5 
drwxr-xr-x 6 root root 4096 Dec 6 07:11 2.6.18-92.1.18.el5 
[root@RHEL52 linux-2.6.18.i686]# make modules_install 
```
And here is the same directory after. Notice that make modules_install created a new directory for the new kernel. 
```
[root@RHEL52 linux-2.6.18.i686]# ls -l /lib/modules/ 
total 24 
drwxr-xr-x 6 root root 4096 Oct 15 13:09 2.6.18-92.1.13.el5 
drwxr-xr-x 6 root root 4096 Nov 11 08:51 2.6.18-92.1.17.el5 
drwxr-xr-x 6 root root 4096 Dec 6 07:11 2.6.18-92.1.18.el5 
drwxr-xr-x 3 root root 4096 Dec 6 08:50 2.6.18-paul2008
```
### 28.5.9. /boot 
We still need to copy the kernel, the System.map and our configuration file to /boot. Strictly speaking the .config file is not obligatory, but it might help you in future compilations of the kernel.
```
[root@RHEL52 ]# pwd 
/usr/src/redhat/BUILD/kernel-2.6.18/linux-2.6.18.i686 
[root@RHEL52 ]# cp System.map /boot/System.map-2.6.18-paul2008 
[root@RHEL52 ]# cp .config /boot/config-2.6.18-paul2008 
[root@RHEL52 ]# cp arch/i386/boot/bzImage /boot/vmlinuz-2.6.18-paul2008 
```
### 28.5.10. mkinitrd 
The kernel often uses an initrd file at bootup. We can use mkinitrd to generate this file. Make sure you use the correct kernel name! 
```
[root@RHEL52 ]# pwd 
/usr/src/redhat/BUILD/kernel-2.6.18/linux-2.6.18.i686 
[root@RHEL52 ]# mkinitrd /boot/initrd-2.6.18-paul2008 2.6.18-paul2008 
```
### 28.5.11. bootloader 
Compilation is now finished, don't forget to create an additional stanza in grub or lilo.
## 28.6. compiling one module 
### 28.6.1. hello.c 
A little C program that will be our module.
```
[root@rhel4a kernel_module]# cat hello.c 
#include  <linux/module.h>
#include  <section>
int init_module(void) 
{ 
printk(KERN_INFO "Start Hello World...\n");
return 0; 
} 
void cleanup_module(void) 
{ 
printk(KERN_INFO "End Hello World... \n"); 
} 
```
### 28.6.2. Makefile 
The make file for this module.
```
[root@rhel4a kernel_module]# cat Makefile obj-m += hello.o all: make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules clean: make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean 
```
These are the only two files needed.
```
[root@rhel4a kernel_module]# ll 
total 16 
-rw-rw-r-- 1 paul paul 250 Feb 15 19:14 hello.c 
-rw-rw-r-- 1 paul paul 153 Feb 15 19:15 Makefile
```
### 28.6.3. make 
The running of the make command. 
```
[root@rhel4a kernel_module]# make 
make -C /lib/modules/2.6.9-paul-2/build M=~/kernel_module modules 
make[1]: Entering dir... `/usr/src/redhat/BUILD/kernel-2.6.9/linux-2.6.9' 
CC [M] /home/paul/kernel_module/hello.o 
Building modules, stage 2. 
MODPOST 
CC /home/paul/kernel_module/hello.mod.o 
LD [M] /home/paul/kernel_module/hello.ko 
make[1]: Leaving dir... `/usr/src/redhat/BUILD/kernel-2.6.9/linux-2.6.9' 
[root@rhel4a kernel_module]# 
```
Now we have more files.
```
[root@rhel4a kernel_module]# ll 
total 172 
-rw-rw-r-- 1 paul paul 250 Feb 15 19:14 hello.c 
-rw-r--r-- 1 root root 64475 Feb 15 19:15 hello.ko 
-rw-r--r-- 1 root root 632 Feb 15 19:15 hello.mod.c 
-rw-r--r-- 1 root root 37036 Feb 15 19:15 hello.mod.o 
-rw-r--r-- 1 root root 28396 Feb 15 19:15 hello.o 
-rw-rw-r-- 1 paul paul 153 Feb 15 19:15 Makefile 
[root@rhel4a kernel_module]# 
```
### 28.6.4. hello.ko
Use modinfo to verify that it is really a module. \
```
[root@rhel4a kernel_module]# modinfo hello.ko 
filename: hello.ko 
vermagic: 2.6.9-paul-2 SMP 686 REGPARM 4KSTACKS gcc-3.4 
depends: 
[root@rhel4a kernel_module]# 
```
Good, so now we can load our hello module. 
```
[root@rhel4a kernel_module]# lsmod | grep hello 
[root@rhel4a kernel_module]# insmod ./hello.ko 
[root@rhel4a kernel_module]# lsmod | grep hello 
hello 5504 0 
[root@rhel4a kernel_module]# tail -1 /var/log/messages 
Feb 15 19:16:07 rhel4a kernel: Start Hello World... 
[root@rhel4a kernel_module]# rmmod hello 
[root@rhel4a kernel_module]# 
```
Finally /var/log/messages has a little surprise. 
```
[root@rhel4a kernel_module]# tail -2 /var/log/messages 
Feb 15 19:16:07 rhel4a kernel: Start Hello World... 
Feb 15 19:16:35 rhel4a kernel: End Hello World...
[root@rhel4a kernel_module]#
```
# Chapter 29. library management
## 29.1. introduction 
With libraries we are talking about dynamically linked libraries (aka shared objects). These are binaries that contain functions and are not started themselves as programs, but are called by other binaries. 
Several programs can use the same library. The name of the library file usually starts with lib, followed by the actual name of the library, then the chracters .so and finally a version number. 
## 29.2. /lib and /usr/lib 
When you look at the /lib or the /usr/lib directory, you will see a lot of symbolic links. Most libraries have a detailed version number in their name, but receive a symbolic link from a filename which only contains the major version number. 
```
root@rhel53 ~# ls -l /lib/libext* 
lrwxrwxrwx 1 root root 16 Feb 18 16:36 /lib/libext2fs.so.2 -> libext2fs.so.2.4 
-rwxr-xr-x 1 root root 113K Jun 30 2009 /lib/libext2fs.so.2.4 
```
## 29.3. ldd 
Many programs have dependencies on the installation of certain libraries. You can display these dependencies with ldd. 
This example shows the dependencies of the su command.
```
paul@RHEL5 ~$ ldd /bin/su linux-gate.so.1 => (0x003f7000) 
libpam.so.0 => /lib/libpam.so.0 (0x00d5c000) 
libpam_misc.so.0 => /lib/libpam_misc.so.0 (0x0073c000) 
libcrypt.so.1 => /lib/libcrypt.so.1 (0x00aa4000) 
libdl.so.2 => /lib/libdl.so.2 (0x00800000) 
libc.so.6 => /lib/libc.so.6 (0x00ec1000) 
libaudit.so.0 => /lib/libaudit.so.0 (0x0049f000) 
/lib/ld-linux.so.2 (0x4769c000)
```
## 29.4. ltrace 
The ltrace program allows to see all the calls made to library functions by a program. The example below uses the -c option to get only a summary count (there can be many calls), and the -l option to only show calls in one library file. All this to see what calls are made when executing su - serena as root.
```
root@deb503:~# ltrace -c -l /lib/libpam.so.0 su - serena 
serena@deb503:~$ exit 
logout % time seconds usecs/call calls function 
------ ----------- ----------- --------- -------------------- 
70.31 0.014117 14117 1 pam_start 
12.36 0.002482 2482 1 pam_open_session 
5.17 0.001039 1039 1 pam_acct_mgmt 
4.36 0.000876 876 1 pam_end 
3.36 0.000675 675 1 pam_close_session 
3.22 0.000646 646 1 pam_authenticate 
0.48 0.000096 48 2 pam_set_item 
0.27 0.000054 54 1 pam_setcred 
0.25 0.000050 50 1 pam_getenvlist 
0.22 0.000044 44 1 pam_get_item 
------ ----------- ----------- --------- -------------------- 
100.00 0.020079 11 total
```
## 29.5. dpkg -S and debsums 
Find out on Debian/Ubuntu to which package a library belongs. 
```
paul@deb503:/lib$ dpkg -S libext2fs.so.2.4 e2fslibs: /lib/libext2fs.so.2.4 
```
You can then verify the integrity of all files in this package using debsums.

```
paul@deb503:~$ debsums e2fslibs 
/usr/share/doc/e2fslibs/changelog.Debian.gz OK 
/usr/share/doc/e2fslibs/copyright OK 
/lib/libe2p.so.2.3 OK 
/lib/libext2fs.so.2.4 OK  
```
Should a library be broken, then reinstall it with aptitude reinstall $package.
```
root@deb503:~# aptitude reinstall e2fslibs 
Reading package lists... Done 
Building dependency tree Reading state information... Done 
Reading extended state information Initializing package states... Done 
Reading task descriptions... Done 
The following packages will be REINSTALLED: 
e2fslibs ...
```
## 29.6. rpm -qf and rpm -V 
Find out on Red Hat/Fedora to which package a library belongs.
```
paul@RHEL5 ~$ rpm -qf /lib/libext2fs.so.2.4 
e2fsprogs-libs-1.39-8.el5 
```
You can then use rpm -V to verify all files in this package. In the example below the output shows that the Size and the Time stamp of the file have changed since installation. 
```
root@rhel53 ~# rpm -V e2fsprogs-libs prelink: /lib/libext2fs.so.2.4: 
prelinked file size differs 
S.?....T /lib/libext2fs.so.2.4 
```
You can then use yum reinstall $package to overwrite the existing library with an original version. ```
```
root@rhel53 lib# yum reinstall e2fsprogs-libs 
Loaded plugins: rhnplugin, security 
Setting up Reinstall Process 
Resolving Dependencies 
--> Running transaction check 
---> Package e2fsprogs-libs.i386 0:1.39-23.el5 set to be erased 
---> Package e2fsprogs-libs.i386 0:1.39-23.el5 set to be updated 
--> Finished Dependency Resolution ... 
```
The package verification now reports no problems with the library.
```
root@rhel53 lib# rpm -V e2fsprogs-libs 
root@rhel53 lib#
```
## 29.7. tracing with strace 
More detailed tracing of all function calls can be done with strace. We start by creating a read only file. 
```
root@deb503:~# echo hello > 42.txt 
root@deb503:~# chmod 400 42.txt 
root@deb503:~# ls -l 42.txt 
-r-------- 1 root root 6 2011-09-26 12:03 42.txt 
```
We open the file with vi, but include the strace command with an output file for the trace before vi. This will create a file with all the function calls done by vi. 
```
root@deb503:~# strace -o strace.txt vi 42.txt 
```
The file is read only, but we still change the contents, and use the :w! directive to write to this file. Then we close vi and take a look at the trace log. 
```
root@deb503:~# grep chmod strace.txt 
chmod("42.txt", 0100600) = -1 ENOENT (No such file or directory) 
chmod("42.txt", 0100400) = 0 
root@deb503:~# ls -l 42.txt 
-r-------- 1 root root 12 2011-09-26 12:04 42.txt 
```
Notice that vi changed the permissions on the file twice. The trace log is too long to show a complete screenshot in this book. 
```
root@deb503:~# wc -l strace.txt 
941 strace.txt
```
