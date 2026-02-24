# V. network management
# 22. interface configuration
## 22.1. to gui or not to gui 
Recent Linux distributions often include a graphical application to configure the network. Some people complain that these applications mess networking configurations up when used simultaneously with command line configurations. Notably Network Manager (often replaced by wicd) and yast are known to not care about configuration changes via the command line. Since the goal of this course is server administration, we will assume our Linux servers are always administered through the command line. This chapter only focuses on using the command line for network interface configuration! Unfortunately there is no single combination of Linux commands and /etc files that works on all Linux distributions. We discuss networking on two (large but distinct) Linux distribution families. We start with Debian (this should also work on Ubuntu and Mint), then continue with RHEL (which is identical to CentOS and Fedora).
## 22.2. Debian nic configuration 
### 22.2.1. /etc/network/interfaces 
The /etc/network/interfaces file is a core network interface card configuration file on debian. 
**dhcp client** 
The screenshot below shows that our computer is configured for dhcp on eth0 (the first network interface card or nic).
```
paul@debian8:~$ cat /etc/network/interfaces 
# This file describes the network interfaces available on your system 
# and how to activate them. For more information, see interfaces(5). 
# The loopback network interface 
auto lo 
iface lo inet loopback 
auto eth0 
iface eth0 inet dhcp
```
Configuring network cards for dhcp is good practice for clients, but servers usually require a fixed ip address.
**fixed ip**
The screenshot below shows /etc/network/interfaces configured with a fixed ip address.
```
root@debian7~# cat /etc/network/interfaces 
auto lo 
iface lo inet loopback 
auto eth0 
iface eth0 inet static 
address 10.42.189.198 
broadcast 10.42.189.207 
netmask 255.255.255.240 
gateway 10.42.189.193
```
The screenshot above also shows that you can provide more configuration than just the ip address. See interfaces(5) for help on setting a gateway, netmask or any of the other options.
### 22.2.2. /sbin/ifdown 
It is adviced (but not mandatory) to down an interface before changing its configuration. This can be done with the ifdown command. The command will not give any output when downing an interface with a fixed ip address. However ifconfig will no longer show the interface.
```
root@ubu1104srv:~# ifdown eth0 
root@ubu1104srv:~# ifconfig 
lo Link encap:Local Loopback 
inet addr:127.0.0.1 Mask:255.0.0.0 
inet6 addr: ::1/128 Scope:Host 
UP LOOPBACK RUNNING MTU:16436 Metric:1 
RX packets:106 errors:0 dropped:0 overruns:0 frame:0 
TX packets:106 errors:0 dropped:0 overruns:0 carrier:0 collisions:0 txqueuelen:0 
RX bytes:11162 (11.1 KB) TX bytes:11162 (11.1 KB) 
```
An interface that is down cannot be used to connect to the network.
### 22.2.3. /sbin/ifup 
Below a screenshot of ifup bringing the eth0 ethernet interface up using dhcp. (Note that this is a Ubuntu 10.10 screenshot, Ubuntu 11.04 omits ifup output by default.)
```
root@ubu1010srv:/etc/network# ifup eth0 
Internet Systems Consortium DHCP Client V3.1.3 
Copyright 2004-2009 Internet Systems Consortium. 
All rights reserved. 
For info, please visit https://www.isc.org/software/dhcp/ 
Listening on LPF/eth0/08:00:27:cd:7f:fc 
Sending on LPF/eth0/08:00:27:cd:7f:fc 
Sending on Socket/fallback 
DHCPREQUEST of 192.168.1.34 on eth0 to 255.255.255.255 port 67 
DHCPNAK from 192.168.33.100 
DHCPDISCOVER on eth0 to 255.255.255.255 port 67 interval 3 
DHCPOFFER of 192.168.33.77 from 192.168.33.100 
DHCPREQUEST of 192.168.33.77 on eth0 to 255.255.255.255 port 67 
DHCPACK of 192.168.33.77 from 192.168.33.100 
bound to 192.168.33.77 -- renewal in 95 seconds. ssh stop/waiting 
ssh start/running, process 1301 
root@ubu1010srv:/etc/network# 
```
## 22.3. RHEL nic configuration 
### 22.3.1. /etc/sysconfig/network
The /etc/sysconfig/network file is a global (across all network cards) configuration file. It allows us to define whether we want networking (NETWORKING=yes|no), what the hostname should be (HOSTNAME=) and which gateway to use (GATEWAY=).
```
[root@rhel6 ~]# cat /etc/sysconfig/network 
NETWORKING=yes 
HOSTNAME=rhel6
GATEWAY=192.168.1.1 
```
There are a dozen more options settable in this file, details can be found in /usr/share/doc/ initscripts-`*`/sysconfig.txt. Note that this file contains no settings at all in a default RHEL7 install (with networking en`abled). 
```
[root@rhel71 ~]# cat /etc/sysconfig/network 
# Created by anaconda
```
### 22.3.2. /etc/sysconfig/network-scripts/ifcfg
Each network card can be configured individually using the /etc/sysconfig/network-scripts/ ifcfg-* files. When you have only one network card, then this will probably be /etc/ sysconfig/network-scripts/ifcfg-eth0. 
**dhcp client** 
Below a screenshot of /etc/sysconfig/network-scripts/ifcfg-eth0 configured for dhcp (BOOTPROTO="dhcp"). Note also the NM_CONTROLLED paramater to disable control of this nic by Network Manager. This parameter is not explained (not even mentioned) in /usr/share/doc/initscripts-`*`/sysconfig.txt, but many others are.
```
[root@rhel6 ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0 
DEVICE="eth0" HWADDR="08:00:27:DD:0D:5C" 
NM_CONTROLLED="no" 
BOOTPROTO="dhcp" 
ONBOOT="yes"
```
The BOOTPROTO variable can be set to either dhcp or bootp, anything else will be considered static meaning there should be no protocol used at boot time to set the interface values. RHEL7 adds ipv6 variables to this file
```
[root@rhel71 network-scripts]# cat ifcfg-enp0s3 
TYPE="Ethernet" 
BOOTPROTO="dhcp" 
DEFROUTE="yes" 
PEERDNS="yes" 
PEERROUTES="yes" 
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes" IPV6_AUTOCONF="yes" 
IPV6_DEFROUTE="yes" 
IPV6_PEERDNS="yes" 
IPV6_PEERROUTES="yes" 
IPV6_FAILURE_FATAL="no" 
NAME="enp0s3" 
UUID="9fa6a83a-2f8e-4ecc-962c-5f614605f4ee" 
DEVICE="enp0s3" 
ONBOOT="yes" 
[root@rhel71 network-scripts]#
```
**fixed ip**
Below a screenshot of a fixed ip configuration in /etc/sysconfig/network-scripts/ifcfgeth0.
```
[root@rhel6 ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0 DEVICE="eth0" HWADDR="08:00:27:DD:0D:5C" 
NM_CONTROLLED="no" 
BOOTPROTO="none" 
IPADDR="192.168.1.99" 
NETMASK="255.255.255.0" 
GATEWAY="192.168.1.1" 
ONBOOT="yes"
``` 
The HWADDR can be used to make sure that each network card gets the correct name when multiple network cards are present in the computer. It can not be used to assign a mac address to a network card. For this, you need to specify the MACADDR variable. Do not use HWADDR and MACADDR in the same ifcfg-ethx file. The BROADCAST= and NETWORK= parameters from previous RHEL/Fedora versions are obsoleted.
### 22.3.3. nmcli 
On RHEL7 you should run nmcli connection reload if you changed configuration files in /etc/sysconfig/ to enable your changes. The nmcli tool has many options to configure networking on the command line in RHEL7/ CentOS7 man nmcli 
### 22.3.4. nmtui
Another recommendation for RHEL7/CentOS7 is to use nmtui. This tool will use a 'windowed' interface in command line to manage network interfaces. nmtui
### 22.3.5. /sbin/ifup and /sbin/ifdown
The ifup and ifdown commands will set an interface up or down, using the configuration discussed above. This is identical to their behaviour in Debian and Ubuntu.
```
[root@rhel6 ~]# ifdown eth0 && ifup eth0 
[root@rhel6 ~]# ifconfig eth0 
eth0 Link encap:Ethernet HWaddr 08:00:27:DD:0D:5C 
inet addr:192.168.1.99 Bcast:192.168.1.255 Mask:255.255.255.0 
inet6 addr: fe80::a00:27ff:fedd:d5c/64 Scope:Link 
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1 
RX packets:2452 errors:0 dropped:0 overruns:0 frame:0 
TX packets:1881 errors:0 dropped:0 overruns:0 carrier:0 
collisions:0 txqueuelen:1000 
RX bytes:257036 (251.0 KiB) TX bytes:184767 (180.4 KiB) 
```
## 22.4. ifconfig
The use of /sbin/ifconfig without any arguments will present you with a list of all active network interface cards, including wireless and the loopback interface. In the screenshot below eth0 has no ip address.
```
root@ubu1010:~# ifconfig 
eth0 Link encap:Ethernet HWaddr 00:26:bb:5d:2e:52 
UP BROADCAST MULTICAST MTU:1500 Metric:1 
RX packets:0 errors:0 dropped:0 overruns:0 frame:0 
TX packets:0 errors:0 dropped:0 overruns:0 carrier:0 
collisions:0 txqueuelen:1000 
RX bytes:0 (0.0 B) TX bytes:0 (0.0 B) 
Interrupt:43 Base address:0xe000 

eth1 Link encap:Ethernet HWaddr 00:26:bb:12:7a:5e 
inet addr:192.168.1.30 Bcast:192.168.1.255 Mask:255.255.255.0 
inet6 addr: fe80::226:bbff:fe12:7a5e/64 Scope:Link 
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1 
RX packets:11141791 errors:202 dropped:0 overruns:0 frame:11580126 
TX packets:6473056 errors:3860 dropped:0 overruns:0 carrier:0 
collisions:0 txqueuelen:1000 
RX bytes:3476531617 (3.4 GB) TX bytes:2114919475 (2.1 GB) 
Interrupt:23 

lo Link encap:Local Loopback 
inet addr:127.0.0.1 Mask:255.0.0.0 
inet6 addr: ::1/128 Scope:Host 
UP LOOPBACK RUNNING MTU:16436 Metric:1 
RX packets:2879 errors:0 dropped:0 overruns:0 frame:0 
TX packets:2879 errors:0 dropped:0 overruns:0 carrier:0 
collisions:0 txqueuelen:0 
RX bytes:486510 (486.5 KB) TX bytes:486510 (486.5 KB)
```
You can also use ifconfig to obtain information about just one network card.
```
[root@rhel6 ~]# ifconfig eth0 
eth0 Link encap:Ethernet HWaddr 08:00:27:DD:0D:5C 
inet addr:192.168.1.99 Bcast:192.168.1.255 Mask:255.255.255.0 
inet6 addr: fe80::a00:27ff:fedd:d5c/64 Scope:Link 
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1 
RX packets:2969 errors:0 dropped:0 overruns:0 frame:0 
TX packets:1918 errors:0 dropped:0 overruns:0 carrier:0 
collisions:0 txqueuelen:
RX bytes:335942 (328.0 KiB) TX bytes:190157 (185.7 KiB)
```
When /sbin is not in the `$`PATH of a normal user you will have to type the full path, as seen here on Debian. 
```
paul@debian5:~$ /sbin/ifconfig eth3 
eth3 Link encap:Ethernet HWaddr 08:00:27:ab:67:30 
inet addr:192.168.1.29 Bcast:192.168.1.255 Mask:255.255.255.0 inet6 addr: fe80::a00:27ff:feab:6730/64 Scope:Link 
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1 
RX packets:27155 errors:0 dropped:0 overruns:0 frame:0 
TX packets:30527 errors:0 dropped:0 overruns:0 carrier:0 
collisions:0 txqueuelen:1000 RX bytes:13095386 (12.4 MiB) TX bytes:25767221 (24.5 MiB)
```
### 22.4.1. up and down 
You can also use ifconfig to bring an interface up or down. The difference with ifup is that ifconfig eth0 up will re-activate the nic keeping its existing (current) configuration, whereas ifup will read the correct file that contains a (possibly new) configuration and use this config file to bring the interface up.
```
[root@rhel6 ~]# ifconfig eth0 down 
[root@rhel6 ~]# ifconfig eth0 up 
[root@rhel6 ~]# ifconfig eth0 eth0 Link encap:Ethernet HWaddr 08:00:27:DD:0D:5C inet addr:192.168.1.99 Bcast:192.168.1.255 Mask:255.255.255.0 inet6 addr: fe80::a00:27ff:fedd:d5c/64 Scope:Link UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1 RX packets:2995 errors:0 dropped:0 overruns:0 frame:0 TX packets:1927 errors:0 dropped:0 overruns:0 carrier:0 collisions:0 txqueuelen:1000 RX bytes:339030 (331.0 KiB) TX bytes:191583 (187.0 KiB)
```
### 22.4.2. setting ip address
You can temporary set an ip address with ifconfig. This ip address is only valid until the next ifup/ifdown cycle or until the next reboot.
```
[root@rhel6 ~]# ifconfig eth0 | grep 192 
inet addr:192.168.1.99 Bcast:192.168.1.255 Mask:255.255.255.0 
[root@rhel6 ~]# ifconfig eth0 192.168.33.42 netmask 255.255.0.0 
[root@rhel6 ~]# ifconfig eth0 | grep 192 
inet addr:192.168.33.42 Bcast:192.168.255.255 Mask:255.255.0.0 
[root@rhel6 ~]# ifdown eth0 && ifup eth0 
[root@rhel6 ~]# ifconfig eth0 | grep 192 
inet addr:192.168.1.99 Bcast:192.168.1.255 Mask:255.255.255.0
```
### 22.4.3. setting mac address 
You can also use ifconfig to set another mac address than the one hard coded in the network card. This screenshot shows you how. 
```
[root@rhel6 ~]# ifconfig eth0 | grep HWaddr 
eth0 Link encap:Ethernet HWaddr 08:00:27:DD:0D:5C 
[root@rhel6 ~]# ifconfig eth0 hw ether 00:42:42:42:42:42 
[root@rhel6 ~]# ifconfig eth0 | grep HWaddr 
eth0 Link encap:Ethernet HWaddr 00:42:42:42:42:42
```
## 22.5. ip 
The ifconfig tool is deprecated on some systems. Use the ip tool instead. To see ip addresses on RHEL7 for example, use this command:
```
[root@rhel71 ~]# ip a 
1: lo: mtu 65536 qdisc noqueue state UNKNOWN 
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 
inet 127.0.0.1/8 scope host lo 
valid_lft forever preferred_lft forever 
inet6 ::1/128 scope host 
valid_lft forever preferred_lft forever 
2: enp0s3: mtu 1500 qdisc pfifo_fast state UP qlen 1000 link/ether 08:00:27:89:22:33 brd ff:ff:ff:ff:ff:ff 
inet 192.168.1.135/24 brd 192.168.1.255 scope global dynamic enp0s3 
valid_lft 6173sec preferred_lft 6173sec 
inet6 fe80::a00:27ff:fe89:2233/64 scope link 
valid_lft forever preferred_lft forever 
[root@rhel71 ~]#
```
## 22.6. dhclient 
Home and client Linux desktops often have /sbin/dhclient running. This is a daemon that enables a network interface to lease an ip configuration from a dhcp server. When your adapter is configured for dhcp or bootp, then /sbin/ifup will start the dhclient daemon. When a lease is renewed, dhclient will override your ifconfig set ip address! 
## 22.7. hostname
Every host receives a hostname, often placed in a DNS name space forming the fqdn or Fully Qualified Domain Name. This screenshot shows the hostname command and the configuration of the hostname on Red Hat/Fedora.
```
[root@rhel6 ~]# grep HOSTNAME /etc/sysconfig/network 
HOSTNAME=rhel6 
[root@rhel6 ~]# hostname rhel6 
```
Starting with RHEL7/CentOS7 this file is empty. The hostname is configured in the standard /etc/hostname file.
```
[root@rhel71 ~]# cat /etc/hostname 
rhel71.linux-training.be 
[root@rhel71 ~]# 
```
Ubuntu/Debian uses the /etc/hostname file to configure the hostname.
```
paul@debian8:~$ cat /etc/hostname 
server42 
paul@debian8:~$ hostname 
server42 
```
On all Linux distributions you can change the hostname using the hostname $newname command. This is not a permanent change.
```
[root@rhel6 ~]# hostname server42 
[root@rhel6 ~]# hostname 
server42
```
On any Linux you can use sysctl to display and set the hostname.
```
[root@rhel6 ~]# sysctl kernel.hostname 
kernel.hostname = server42 
[root@rhel6 ~]# sysctl kernel.hostname=rhel6 
kernel.hostname = rhel6 
[root@rhel6 ~]# sysctl kernel.hostname 
kernel.hostname = rhel6 
[root@rhel6 ~]# hostname rhel6
```
## 22.8. arp 
The ip to mac resolution is handled by the layer two broadcast protocol arp. The arp table can be displayed with the arp tool. The screenshot below shows the list of computers that this computer recently communicated with.
```
root@barry:~# arp -a 
? (192.168.1.191) at 00:0C:29:3B:15:80 [ether] on eth1 
agapi (192.168.1.73) at 00:03:BA:09:7F:D2 [ether] on eth1 
anya (192.168.1.1) at 00:12:01:E2:87:FB [ether] on eth1 
faith (192.168.1.41) at 00:0E:7F:41:0D:EB [ether] on eth1 
kiss (192.168.1.49) at 00:D0:E0:91:79:95 [ether] on eth1 
laika (192.168.1.40) at 00:90:F5:4E:AE:17 [ether] on eth1 
pasha (192.168.1.71) at 00:03:BA:02:C3:82 [ether] on eth1 
shaka (192.168.1.72) at 00:03:BA:09:7C:F9 [ether] on eth1 
root@barry:~#
```
Anya is a Cisco Firewall, faith is a laser printer, kiss is a Kiss DP600, laika is a laptop and Agapi, Shaka and Pasha are SPARC servers. The question mark is a Red Hat Enterprise Linux server running on a virtual machine. You can use arp -d to remove an entry from the arp table.
```
[root@rhel6 ~]# arp 
Address HWtype HWaddress Flags Mask Iface 
ubu1010 ether 00:26:bb:12:7a:5e C eth0 
anya ether 00:02:cf:aa:68:f0 C eth0 
[root@rhel6 ~]# arp -d anya 
[root@rhel6 ~]# arp 
Address HWtype HWaddress Flags Mask Iface 
ubu1010 ether 00:26:bb:12:7a:5e C eth0 anya (incomplete) eth0 
[root@rhel6 ~]# ping anya 
PING anya (192.168.1.1) 56(84) bytes of data. 
64 bytes from anya (192.168.1.1): icmp_seq=1 ttl=254 time=10.2 ms 
... 
[root@rhel6 ~]# arp 
Address HWtype HWaddress Flags Mask Iface 
ubu1010 ether 00:26:bb:12:7a:5e C eth0 
anya ether 00:02:cf:aa:68:f0 C eth0
```
## 22.9. route 
You can see the computer's local routing table with the /sbin/route command (and also with netstat -r ).
```
root@RHEL4b ~]# netstat -r 
Kernel IP routing table 
Destination Gateway Genmask Flags MSS Window irtt Iface 
192.168.1.0 * 255.255.255.0 U 0 0 0 eth0 
[root@RHEL4b ~]# route Kernel IP routing table 
Destination Gateway Genmask Flags Metric Ref Use Iface 
192.168.1.0 * 255.255.255.0 U 0 0 0 eth0 
[root@RHEL4b ~]#
```
It appears this computer does not have a gateway configured, so we use route add default gw to add a default gateway on the fly.
```
[root@RHEL4b ~]# route add default gw 192.168.1.1 
[root@RHEL4b ~]# route 
Kernel IP routing table 
Destination Gateway Genmask Flags Metric Ref Use Iface 
192.168.1.0 * 255.255.255.0 U 0 0 0 eth0 
default 192.168.1.1 0.0.0.0 UG 0 0 0 eth0 
[root@RHEL4b ~]#
```
Unless you configure the gateway in one of the /etc/ file from the start of this chapter, your computer will forget this gateway after a reboot. 
## 22.10. ping
If you can ping to another host, then tcp/ip is configured.
```
[root@RHEL4b ~]# ping 192.168.1.5 
PING 192.168.1.5 (192.168.1.5) 56(84) bytes of data. 
64 bytes from 192.168.1.5: icmp_seq=0 ttl=64 time=1004 ms 
64 bytes from 192.168.1.5: icmp_seq=1 ttl=64 time=1.19 ms 
64 bytes from 192.168.1.5: icmp_seq=2 ttl=64 time=0.494 ms 
64 bytes from 192.168.1.5: icmp_seq=3 ttl=64 time=0.419 ms 
--- 192.168.1.5 ping statistics --- 
4 packets transmitted, 4 received, 0% packet loss, time 3009ms 
rtt min/avg/max/mdev = 0.419/251.574/1004.186/434.520 ms, pipe 2 
[root@RHEL4b ~]#
```
# Chapter 24. binding and bonding 
Sometimes a server needs more than one ip address on the same network card, we call this binding ip addresses. Linux can also activate multiple network cards behind the same ip address, this is called bonding.
## 24.1. binding on Redhat/Fedora 
### 24.1.1. binding extra ip addresses
To bind more than one ip address to the same interface, use ifcfg-eth0:0, where the last zero can be anything else. Only two directives are required in the files.
```
[root@rhel6 ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0:0
DEVICE="eth0:0" 
IPADDR="192.168.1.133" 
[root@rhel6 ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0:1 
DEVICE="eth0:0" 
IPADDR="192.168.1.142"
```
### 24.1.2. enabling extra ip-addresses 
To activate a virtual network interface, use ifup, to deactivate it, use ifdown.
```
[root@rhel6 ~]# ifup eth0:0 
[root@rhel6 ~]# ifconfig | grep 'inet ' 
inet addr:192.168.1.99 Bcast:192.168.1.255 Mask:255.255.255.0 
inet addr:192.168.1.133 Bcast:192.168.1.255 Mask:255.255.255.0 
inet addr:127.0.0.1 Mask:255.0.0.0 
[root@rhel6 ~]# ifup eth0:1 
[root@rhel6 ~]# ifconfig | grep 'inet ' 
inet addr:192.168.1.99 Bcast:192.168.1.255 Mask:255.255.255.0 
inet addr:192.168.1.133 Bcast:192.168.1.255 Mask:255.255.255.0 
inet addr:192.168.1.142 Bcast:192.168.1.255 Mask:255.255.255.0 
inet addr:127.0.0.1 Mask:255.0.
```
### 24.1.3. verifying extra ip-addresses
Use ping from another computer to check the activation, or use ifconfig like in this screenshot.
```
[root@rhel6 ~]# ifconfig 
eth0 Link encap:Ethernet HWaddr 08:00:27:DD:0D:5C 
inet addr:192.168.1.99 Bcast:192.168.1.255 Mask:255.255.255.0 
inet6 addr: fe80::a00:27ff:fedd:d5c/64 Scope:Link UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1 
RX packets:1259 errors:0 dropped:0 overruns:0 frame:0 
TX packets:545 errors:0 dropped:0 overruns:0 carrier:0 collisions:0 txqueuelen:1000 
RX bytes:115260 (112.5 KiB) TX bytes:84293 (82.3 KiB) 
eth0:0 Link encap:Ethernet HWaddr 08:00:27:DD:0D:5C inet addr:192.168.1.133 Bcast:192.168.1.255 Mask:255.255.255.0 UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1 
eth0:1 Link encap:Ethernet HWaddr 08:00:27:DD:0D:5C inet addr:192.168.1.142 Bcast:192.168.1.255 Mask:255.255.255.0 UP BROADCAST RUN
```
## 24.2. binding on Debian/Ubuntu
### 24.2.1. binding extra ip addresses
The configuration of multiple ip addresses on the same network card is done in /etc/network/ interfaces by adding eth0:x devices. Adding the netmask is mandatory.
```
debian5:~# cat /etc/network/interfaces 
# This file describes the network interfaces available on your system 
# and how to activate them. For more information, see interfaces(5). 

# The loopback network interface 
auto lo 
iface lo inet loopback 
# The primary network interface 
iface eth0 inet static 
address 192.168.1.34 network 192.168.1.0 
netmask 255.255.255.0 
gateway 192.168.1.1 auto eth0 
auto eth0:0 
iface eth0:0 inet static 
address 192.168.1.233 
netmask 255.255.255.0 
auto eth0:1 
iface eth0:1 inet static 
address 192.168.1.242 
netmask 255.255.255.0
```
### 24.2.2. enabling extra ip-addresses 
Use ifup to enable the extra addresses.
```
debian5:~# ifup eth0:0 
debian5:~# ifup eth0:1
```
### 24.2.3. verifying extra ip-addresses
Use ping from another computer to check the activation, or use ifconfig like in this screenshot.
```
debian5:~# ifconfig | grep 'inet ' 
inet addr:192.168.1.34 Bcast:192.168.1.255 Mask:255.255.255.0 
inet addr:192.168.1.233 Bcast:192.168.1.255 Mask:255.255.255.0 
inet addr:192.168.1.242 Bcast:192.168.1.255 Mask:255.255.255.0 
inet addr:127.0.0.1 Mask:255.0.0.0
```
## 24.4. bonding on Debian/Ubuntu 
We start with ifconfig -a to get a list of all the network cards on our system.
```
debian5:~# ifconfig -a | grep Ethernet 
eth0 Link encap:Ethernet HWaddr 08:00:27:bb:18:a4 
eth1 Link encap:Ethernet HWaddr 08:00:27:63:9a:95 
eth2 Link encap:Ethernet HWaddr 08:00:27:27:a4:92
```
In this demo we decide to bond eth1 and eth2. We also need to install the ifenslave package.
```
debian5:~# aptitude search ifenslave 
p ifenslave - Attach and detach slave interfaces to a bonding device 
p ifenslave-2.6 - Attach and detach slave interfaces to a bonding device debian5:~# aptitude install ifenslave 
Reading package lists... Done ...
```
Next we update the /etc/network/interfaces file with information about the bond0 interface.
```
debian5:~# tail -7 /etc/network/interfaces 
iface bond0 inet static 
address 192.168.1.42 
netmask 255.255.255.0 
gateway 192.168.1.1 
slaves eth1 eth2 
bond-mode active-backup 
bond_primar eth1
```
On older version of Debian/Ubuntu you needed to modprobe bonding, but this is no longer required. Use ifup to bring the interface up, then test that it works.
```
debian5:~# ifup bond0 
debian5:~# ifconfig bond0 
bond0 Link encap:Ethernet HWaddr 08:00:27:63:9a:95 
inet addr:192.168.1.42 Bcast:192.168.1.255 Mask:255.255.255.0 
inet6 addr: fe80::a00:27ff:fe63:9a95/64 Scope:Link 
UP BROADCAST RUNNING MASTER MULTICAST MTU:1500 Metric:1 
RX packets:212 errors:0 dropped:0 overruns:0 frame:0 
TX packets:39 errors:0 dropped:0 overruns:0 carrier:0 collisions:0 txqueuelen:0 
RX bytes:31978 (31.2 KiB) TX bytes:6709 (6.5 KiB)
```
The bond should also be visible in /proc/net/bonding.
```
debian5:~# cat /proc/net/bonding/bond0 
Ethernet Channel Bonding Driver: v3.2.5 (March 21, 2008) 
Bonding Mode: fault-tolerance (active-backup) 
Primary Slave: eth1 Currently Active Slave: eth1 
MII Status: up MII Polling Interval (ms): 0 
Up Delay (ms): 0 
Down Delay (ms): 0 
Slave Interface: eth1 
MII Status: up 
Link Failure Count: 0
Permanent HW addr: 08:00:27:63:9a:95 
Slave Interface: eth2 MII Status: up 
Link Failure Count: 0 
Permanent HW addr: 08:00:27:27:a4:92
```
# Chapter 25. ssh client and server 
The secure shell or ssh is a collection of tools using a secure protocol for communications with remote Linux computers.
## 25.1. about ssh 
### 25.1.1. secure shell 
Avoid using telnet, rlogin and rsh to remotely connect to your servers. These older protocols do not encrypt the login session, which means your user id and password can be sniffed by tools like wireshark or tcpdump. To securely connect to your servers, use ssh. The ssh protocol is secure in two ways. Firstly the connection is encrypted and secondly the connection is authenticated both ways. An ssh connection always starts with a cryptographic handshake, followed by encryption of the transport layer using a symmetric cypher. In other words, the tunnel is encrypted before you start typing anything. Then authentication takes place (using user id/password or public/private keys) and communication can begin over the encrypted connection. The ssh protocol will remember the servers it connected to (and warn you in case something suspicious happened). The openssh package is maintained by the OpenBSD people and is distributed with a lot of operating systems (it may even be the most popular package in the world).
### 25.1.2. /etc/ssh/ 
Configuration of ssh client and server is done in the /etc/ssh directory. In the next sections we will discuss most of the files found in /etc/ssh/. 
### 25.1.3. ssh protocol versions 
The ssh protocol has two versions (1 and 2). Avoid using version 1 anywhere, since it contains some known vulnerabilities. You can control the protocol version via /etc/ssh/ ssh_config for the client side and /etc/ssh/sshd_config for the openssh-server daemon.
```
paul@ubu1204:/etc/ssh$ grep Protocol ssh_config 
# Protocol 2,1 
paul@ubu1204:/etc/ssh$ grep Protocol sshd_config 
Protocol 2
```
### 25.1.4. public and private keys
The ssh protocol uses the well known system of public and private keys. The below explanation is succinct, more information can be found on wikipedia.
```
http://en.wikipedia.org/wiki/Public-key_cryptography 
```
Imagine Alice and Bob, two people that like to communicate with each other. Using public and private keys they can communicate with encryption and with authentication. When Alice wants to send an encrypted message to Bob, she uses the public key of Bob. Bob shares his public key with Alice, but keeps his private key private! Since Bob is the only one to have Bob's private key, Alice is sure that Bob is the only one that can read the encrypted message. When Bob wants to verify that the message came from Alice, Bob uses the public key of Alice to verify that Alice signed the message with her private key. Since Alice is the only one to have Alice's private key, Bob is sure the message came from Alice. 
### 25.1.5. rsa and dsa algorithms
This chapter does not explain the technical implementation of cryptographic algorithms, it only explains how to use the ssh tools with rsa and dsa. More information about these algorithms can be found here:
```
http://en.wikipedia.org/wiki/RSA_(algorithm) http://en.wikipedia.org/wiki/Digital_Signature_Algorithm
```
## 25.2. log on to a remote server 
The following screenshot shows how to use ssh to log on to a remote computer running Linux. The local user is named paul and he is logging on as user admin42 on the remote system.
```
paul@ubu1204:~$ ssh admin42@192.168.1.30 
The authenticity of host '192.168.1.30 (192.168.1.30)' can't be established. 
RSA key fingerprint is b5:fb:3c:53:50:b4:ab:81:f3:cd:2e:bb:ba:44:d3:75. 
Are you sure you want to continue connecting (yes/no)?
```
As you can see, the user paul is presented with an rsa authentication fingerprint from the remote system. The user can accepts this bu typing yes. We will see later that an entry will be added to the ~/.ssh/known_hosts file.
```
paul@ubu1204:~$ ssh admin42@192.168.1.30 
The authenticity of host '192.168.1.30 (192.168.1.30)' can't be established. 
RSA key fingerprint is b5:fb:3c:53:50:b4:ab:81:f3:cd:2e:bb:ba:44:d3:75. 
Are you sure you want to continue connecting (yes/no)? yes 
Warning: Permanently added '192.168.1.30' (RSA) to the list of known hosts. 
admin42@192.168.1.30's password: 
Welcome to Ubuntu 12.04 LTS (GNU/Linux 3.2.0-26-generic-pae i686) 
* Documentation: https://help.ubuntu.com/ 
1 package can be updated. 
0 updates are security updates. 
Last login: Wed Jun 6 19:25:57 2012 from 172.28.0.131 
admin42@ubuserver:~$
```
The user can get log out of the remote server by typing exit or by using Ctrl-d.
```
admin42@ubuserver:~$ exit 
logout
Connection to 192.168.1.30 closed. 
paul@ubu1204:~$
```
## 25.3. executing a command in remote
This screenshot shows how to execute the pwd command on the remote server. There is no need to exit the server manually.
```
paul@ubu1204:~$ ssh admin42@192.168.1.30 pwd 
admin42@192.168.1.30's password: 
/home/admin42 
paul@ubu1204:~$
```
## 25.4. scp 
The scp command works just like cp, but allows the source and destination of the copy to be behind ssh. Here is an example where we copy the /etc/hosts file from the remote server to the home directory of user paul.
```
paul@ubu1204:~$ scp admin42@192.168.1.30:/etc/hosts /home/paul/serverhosts admin42@192.168.1.30's password: 
hosts 100% 809 0.8KB/s 00:00 
```
Here is an example of the reverse, copying a local file to a remote server. 
```
paul@ubu1204:~$ scp ~/serverhosts admin42@192.168.1.30:/etc/hosts.new admin42@192.168.1.30's password: 
serverhosts 100% 809 0.8KB/s 00:00
```
## 25.5. setting up passwordless ssh 
To set up passwordless ssh authentication through public/private keys, use ssh-keygen to generate a key pair without a passphrase, and then copy your public key to the destination server. Let's do this step by step. In the example that follows, we will set up ssh without password between Alice and Bob. Alice has an account on a Red Hat Enterprise Linux server, Bob is using Ubuntu on his laptop. Bob wants to give Alice access using ssh and the public and private key system. This means that even if Bob changes his password on his laptop, Alice will still have access.
### 25.5.1. ssh-keygen 
The example below shows how Alice uses ssh-keygen to generate a key pair. Alice does not enter a passphrase.
```
[alice@RHEL5 ~]$ ssh-keygen -t rsa 
Generating public/private rsa key pair. 
Enter file in which to save the key (/home/alice/.ssh/id_rsa): 
Created directory '/home/alice/.ssh'. 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/alice/.ssh/id_rsa. 
Your public key has been saved in /home/alice/.ssh/id_rsa.pub. 
The key fingerprint is: 
9b:ac:ac:56:c2:98:e5:d9:18:c4:2a:51:72:bb:45:eb alice@RHEL5 
[alice@RHEL5 ~]$
```
You can use ssh-keygen -t dsa in the same way. 
### 25.5.2. ~/.ssh 
While ssh-keygen generates a public and a private key, it will also create a hidden .ssh directory with proper permissions. If you create the .ssh directory manually, then you need to chmod 700 it! Otherwise ssh will refuse to use the keys (world readable private keys are not secure!). As you can see, the .ssh directory is secure in Alice's home directory.
```
[alice@RHEL5 ~]$ ls -ld .ssh 
drwx------ 2 alice alice 4096 May 1 07:38 .ssh 
[alice@RHEL5 ~]$
```
Bob is using Ubuntu at home. He decides to manually create the .ssh directory, so he needs to manually secure it.
```
bob@laika:~$ mkdir .ssh 
bob@laika:~$ ls -ld .ssh 
drwxr-xr-x 2 bob bob 4096 2008-05-14 16:53 .ssh 
bob@laika:~$ chmod 700 .ssh/ 
bob@laika:~$
```
### 25.5.3. id_rsa and id_rsa.pub 
The ssh-keygen command generate two keys in .ssh. The public key is named ~/.ssh/ id_rsa.pub. The private key is named ~/.ssh/id_rsa.
```
[alice@RHEL5 ~]$ ls -l .ssh/ 
total 16 
-rw------- 1 alice alice 1671 May 1 07:38 id_rsa 
-rw-r--r-- 1 alice alice 393 May 1 07:38 id_rsa.pub
```
The files will be named id_dsa and id_dsa.pub when using dsa instead of rsa. 
### 25.5.4. copy the public key to the other computer 
To copy the public key from Alice's server tot Bob's laptop, Alice decides to use scp.
```
[alice@RHEL5 .ssh]$ scp id_rsa.pub bob@192.168.48.92:~/.ssh/authorized_keys bob@192.168.48.92's password: 
id_rsa.pub 100% 393 0.4KB/s 00:00
```
Be careful when copying a second key! Do not overwrite the first key, instead append the key to the same ~/.ssh/authorized_keys file!
```
cat id_rsa.pub >> ~/.ssh/authorized_keys
```
Alice could also have used ssh-copy-id like in this example.
```
ssh-copy-id -i .ssh/id_rsa.pub bob@192.168.48.92
```
### 25.5.5. authorized_keys 
In your ~/.ssh directory, you can create a file called authorized_keys. This file can contain one or more public keys from people you trust. Those trusted people can use their private keys to prove their identity and gain access to your account via ssh (without password). The example shows Bob's authorized_keys file containing the public key of Alice.
```
bob@laika:~$ cat .ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEApCQ9xzyLzJes1sR+hPyqW2vyzt1D4zTLqk\ MDWBR4mMFuUZD/O583I3Lg/Q+JIq0RSksNzaL/BNLDou1jMpBe2Dmf/u22u4KmqlJBfDhe\ yTmGSBzeNYCYRSMq78CT9l9a+y6x/shucwhaILsy8A2XfJ9VCggkVtu7XlWFDL2cum08/0\ mRFwVrfc/uPsAn5XkkTscl4g21mQbnp9wJC40pGSJXXMuFOk8MgCb5ieSnpKFniAKM+tEo\ /vjDGSi3F/bxu691jscrU0VUdIoOSo98HUfEf7jKBRikxGAC7I4HLa+/zX73OIvRFAb2hv\ tUhn6RHrBtUJUjbSGiYeFTLDfcTQ== alice@RHEL5
```
### 25.5.6. passwordless ssh
Alice can now use ssh to connect passwordless to Bob's laptop. In combination with ssh's capability to execute commands on the remote host, this can be useful in pipes across different machines.
```
[alice@RHEL5 ~]$ ssh bob@192.168.48.92 "ls -l .ssh" 
total 4 
-rw-r--r-- 1 bob bob 393 2008-05-14 17:03 authorized_keys 
[alice@RHEL5 ~]$
```
## 25.6. X forwarding via ssh 
Another popular feature of ssh is called X11 forwarding and is implemented with ssh -X. Below an example of X forwarding: user paul logs in as user greet on her computer to start the graphical application mozilla-thunderbird. Although the application will run on the remote computer from greet, it will be displayed on the screen attached locally to paul's computer.
```
paul@debian5:~/PDF$ ssh -X greet@greet.dyndns.org -p 55555 
Warning: Permanently added the RSA host key for IP address \ 
'81.240.174.161' to the list of known hosts. 
Password: 
Linux raika 2.6.8-2-686 #1 Tue Aug 16 13:22:48 UTC 2005 i686 GNU/Linux 
Last login: Thu Jan 18 12:35:56 2007 
greet@raika:~$ ps fax | grep thun 
greet@raika:~$ mozilla-thunderbird & 
[1] 30336
```
## 25.7. troubleshooting ssh 
Use ssh -v to get debug information about the ssh connection attempt.
```
paul@debian5:~$ ssh -v bert@192.168.1.192 
OpenSSH_4.3p2 Debian-8ubuntu1, OpenSSL 0.9.8c 05 Sep 2006 
debug1: Reading configuration data /home/paul/.ssh/config 
debug1: Reading configuration data /etc/ssh/ssh_config 
debug1: Applying options for * 
debug1: Connecting to 192.168.1.192 [192.168.1.192] port 22. 
debug1: Connection established. 
debug1: identity file /home/paul/.ssh/identity type -1 
debug1: identity file /home/paul/.ssh/id_rsa type 1 
debug1: identity file /home/paul/.ssh/id_dsa type -1 
debug1: Remote protocol version 1.99, remote software version OpenSSH_3 
debug1: match: OpenSSH_3.9p1 pat OpenSSH_3.* 
debug1: Enabling compatibility mode for protocol 2.0 ...
```
## 25.8. sshd 
The ssh server is called sshd and is provided by the openssh-server package.
```
root@ubu1204~# dpkg -l openssh-server | tail -1 
ii openssh-server 1:5.9p1-5ubuntu1 secure shell (SSH) server,...
```
## 25.9. sshd keys
The public keys used by the sshd server are located in /etc/ssh and are world readable. The private keys are only readable by root.
```
root@ubu1204~# ls -l /etc/ssh/ssh_host_* 
-rw------- 1 root root 668 Jun 7 2011 /etc/ssh/ssh_host_dsa_key 
-rw-r--r-- 1 root root 598 Jun 7 2011 /etc/ssh/ssh_host_dsa_key.pub 
-rw------- 1 root root 1679 Jun 7 2011 /etc/ssh/ssh_host_rsa_key 
-rw-r--r-- 1 root root 390 Jun 7 2011 /etc/ssh/ssh_host_rsa_key.pub
```
## 25.10. ssh-agent 
When generating keys with ssh-keygen, you have the option to enter a passphrase to protect access to the keys. To avoid having to type this passphrase every time, you can add the key to ssh-agent using ssh-add. Most Linux distributions will start the ssh-agent automatically when you log on.
```
root@ubu1204~# ps -ef | grep ssh-agent 
paul 2405 2365 0 08:13 ? 00:00:00 /usr/bin/ssh-agent...
```
This clipped screenshot shows how to use ssh-add to list the keys that are currently added to the ssh-agent
```
paul@debian5:~$ ssh-add -L 
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAvgI+Vx5UrIsusZPl8da8URHGsxG7yivv3/\ 
...
wMGqa48Kelwom8TGb4Sgcwpp/VO/ldA5m+BGCw== paul@deb503
```
# 26. introduction to nfs 
The network file system (or simply nfs) enables us since the Eighties to share a directory with other computers on the network. In this chapter we see how to setup an nfs server and an nfs client computer.
## 26.1. nfs protocol versions 
The older nfs versions 2 and 3 are stateless (udp) by default (but they can use tcp). The more recent nfs version 4 brings a stateful protocol with better performance and stronger security. NFS version 4 was defined in rfc 3010 in 2000 and rfc 3530 in 2003 and requires tcp (port 2049). It also supports Kerberos user authentication as an option when mounting a share. NFS versions 2 and 3 authenticate only the host.
## 26.2. rpcinfo
Clients connect to the server using rpc (on Linux this can be managed by the portmap daemon). Look at rpcinfo to verify that nfs and its related services are running.
```
root@RHELv4u2:~# /etc/init.d/portmap status 
portmap (pid 1920) is running... 
root@RHELv4u2:~# rpcinfo -p 
program vers proto port 
100000 2 tcp 111 portmapper 
100000 2 udp 111 portmapper 
100024 1 udp 32768 status 
100024 1 tcp 32769 status 
root@RHELv4u2:~# service nfs start 
Starting NFS services: [ OK ] 
Starting NFS quotas: [ OK ] 
Starting NFS daemon: [ OK ] 
Starting NFS mountd: [ OK ] 
```
The same rpcinfo command when nfs is started.
```
root@RHELv4u2:~# rpcinfo -p 
program vers proto port 
100000 2 tcp 111 portmapper 
100000 2 udp 111 portmapper 
100024 1 udp 32768 status 
100024 1 tcp 32769 status 
100011 1 udp 985 rquotad 
100011 2 udp 985 rquotad 
100011 1 tcp 988 rquotad 
100011 2 tcp 988 rquotad 
100003 2 udp 2049 nfs 
100003 3 udp 2049 nfs 
100003 4 udp 2049 nfs 
100003 2 tcp 2049 nfs 
100003 3 tcp 2049 nfs 
100003 4 tcp 2049 nfs 
100021 1 udp 32770 nlockmgr 
100021 3 udp 32770 nlockmgr 
100021 4 udp 32770 nlockmgr 
100021 1 tcp 32789 nlockmgr 
100021 3 tcp 32789 nlockmgr 
100021 4 tcp 32789 nlockmgr 
100005 1 udp 1004 mountd 
100005 1 tcp 1007 mountd 
100005 2 udp 1004 mountd 
100005 2 tcp 1007 mountd 
100005 3 udp 1004 mountd 
100005 3 tcp 1007 mountd
```
## 26.3. server configuration 
nfs is configured in /etc/exports. You might want some way (ldap?) to synchronize userid's across computers when using nfs a lot. The rootsquash option will change UID 0 to the UID of a nobody (or similar) user account. The sync option will write writes to disk before completing the client request. 
## 26.4. /etc/exports 
Here is a sample /etc/exports to explain the syntax:
```
paul@laika:~$ cat /etc/exports 
# Everyone can read this share 
/mnt/data/iso *(ro) 

# Only the computers named pasha and barry can readwrite this one 
/var/www pasha(rw) barry(rw) 

# same, but without root squashing for barry 
/var/ftp pasha(rw) barry(rw,no_root_squash) 

# everyone from the netsec.local domain gets access 
/var/backup *.netsec.local(rw) 

# ro for one network, rw for the other 
/var/upload 192.168.1.0/24(ro) 192.168.5.0/24(rw) 
```
More recent incarnations of nfs require the subtree_check option to be explicitly set (or unset with no_subtree_check). The /etc/exports file then looks like this:
```
root@debian6 ~# cat /etc/exports 
# Everyone can read this share 
/srv/iso *(ro,no_subtree_check) 

# Only the computers named pasha and barry can readwrite this one 
/var/www pasha(rw,no_subtree_check) barry(rw,no_subtree_check) 

# same, but without root squashing for barry 
/var/ftp pasha(rw,no_subtree_check) barry(rw,no_root_squash,no_subtree_check) 
```
## 26.5. exportfs 
You don't need to restart the nfs server to start exporting your newly created exports. You can use the exportfs -va command to do this. It will write the exported directories to /var/ lib/nfs/etab, where they are immediately applied.
```
root@debian6 ~# exportfs -va 
exporting pasha:/var/ftp 
exporting barry:/var/ftp 
exporting pasha:/var/www 
exporting barry:/var/www 
exporting *:/srv/iso
```
26.6. client configuration 
We have seen the mount command and the /etc/fstab file before. 
```
root@RHELv4u2:~# mount -t nfs barry:/mnt/data/iso /home/project55/ root@RHELv4u2:~# cat /etc/fstab | grep nfs 
barry:/mnt/data/iso /home/iso nfs defaults 0 0 
root@RHELv4u2:~#
```
Here is another simple example. Suppose the project55 people tell you they only need a couple of CD-ROM images, and you already have them available on an nfs server. You could issue the following command to mount this storage on their /home/project55 mount point.
```
root@RHELv4u2:~# mount -t nfs 192.168.1.40:/mnt/data/iso /home/project55/ root@RHELv4u2:~# ls -lh /home/project55/ 
total 3.6G 
drwxr-xr-x 2 1000 1000 4.0K Jan 16 17:55 RHELv4u1 
drwxr-xr-x 2 1000 1000 4.0K Jan 16 14:14 RHELv4u2 
drwxr-xr-x 2 1000 1000 4.0K Jan 16 14:54 RHELv4u3 
drwxr-xr-x 2 1000 1000 4.0K Jan 16 11:09 RHELv4u4 
-rw-r--r-- 1 root root 1.6G Oct 13 15:22 sled10-vmwarews5-vm.zip 
root@RHELv4u2:~#
```
# 27. introduction to networking
## 27.1. introduction to iptables
### 27.1.1. iptables firewall 
The Linux kernel has a built-in stateful firewall named iptables. To stop the iptables firewall on Red Hat, use the service command.
```
root@RHELv4u4:~# service iptables stop 
Flushing firewall rules: [ OK ] 
Setting chains to policy ACCEPT: filter [ OK ] 
Unloading iptables modules: [ OK ] 
root@RHELv4u4:~#
```
The easy way to configure iptables, is to use a graphical tool like KDE's kmyfirewall or Security Level Configuration Tool. You can find the latter in the graphical menu, somewhere in System Tools - Security, or you can start it by typing system-configsecuritylevel in bash. These tools allow for some basic firewall configuration. You can decide whether to enable or disable the firewall, and what typical standard ports are allowed when the firewall is active. You can even add some custom ports. When you are done, the configuration is written to /etc/sysconfig/iptables on Red Hat.
```
root@RHELv4u4:~# cat /etc/sysconfig/iptables 
# Firewall configuration written by system-config-securitylevel 
# Manual customization of this file is not recommended. 
*filter 
:INPUT ACCEPT [0:0] 
:FORWARD ACCEPT [0:0] 
:OUTPUT ACCEPT [0:0] 
:RH-Firewall-1-INPUT - [0:0] 
-A INPUT -j RH-Firewall-1-INPUT 
-A FORWARD -j RH-Firewall-1-INPUT 
-A RH-Firewall-1-INPUT -i lo -j ACCEPT 
-A RH-Firewall-1-INPUT -p icmp --icmp-type any -j ACCEPT 
-A RH-Firewall-1-INPUT -p 50 -j ACCEPT 
-A RH-Firewall-1-INPUT -p 51 -j ACCEPT 
-A RH-Firewall-1-INPUT -p udp --dport 5353 -d 224.0.0.251 -j ACCEPT 
-A RH-Firewall-1-INPUT -p udp -m udp --dport 631 -j ACCEPT 
-A RH-Firewall-1-INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT 
-A RH-F...NPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT 
-A RH-F...NPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT 
-A RH-F...NPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT 
-A RH-F...NPUT -m state --state NEW -m tcp -p tcp --dport 25 -j ACCEPT 
-A RH-Firewall-1-INPUT -j REJECT --reject-with icmp-host-prohibited 
COMMIT 
root@RHELv4u4:~#
```
To start the service, issue the service iptables start command. You can configure iptables to start at boot time with chkconfig.
```
root@RHELv4u4:~# service iptables start 
Applying iptables firewall rules: [ OK ] 
root@RHELv4u4:~# chkconfig iptables on 
root@RHELv4u4:~#
```
One of the nice features of iptables is that it displays extensive status information when queried with the service iptables status command.
```
root@RHELv4u4:~# service iptables status 
Table: filter 
Chain INPUT (policy ACCEPT) 
target prot opt source destination 
RH-Firewall-1-INPUT all -- 0.0.0.0/0 0.0.0.0/0 

Chain FORWARD (policy ACCEPT) 
target prot opt source destination 
RH-Firewall-1-INPUT all -- 0.0.0.0/0 0.0.0.0/0 

Chain OUTPUT (policy ACCEPT) 
target prot opt source destination 

Chain RH-Firewall-1-INPUT (2 references) 
target prot opt source destination ACCEPT all -- 0.0.0.0/0 0.0.0.0/0 
ACCEPT icmp -- 0.0.0.0/0 0.0.0.0/0 icmp type 255 
ACCEPT esp -- 0.0.0.0/0 0.0.0.0/0 
ACCEPT ah -- 0.0.0.0/0 0.0.0.0/0 
ACCEPT udp -- 0.0.0.0/0 224.0.0.251 udp dpt:5353 
ACCEPT udp -- 0.0.0.0/0 0.0.0.0/0 udp dpt:631 
ACCEPT all -- 0.0.0.0/0 0.0.0.0/0 state RELATED,ESTABLISHED 
ACCEPT tcp -- 0.0.0.0/0 0.0.0.0/0 state NEW tcp dpt:22 
ACCEPT tcp -- 0.0.0.0/0 0.0.0.0/0 state NEW tcp dpt:80 
ACCEPT tcp -- 0.0.0.0/0 0.0.0.0/0 state NEW tcp dpt:21 
ACCEPT tcp -- 0.0.0.0/0 0.0.0.0/0 state NEW tcp dpt:25 
REJECT all -- 0.0.0.0/0 0.0.0.0/0 reject-with icmp-host-prohibited 
root@RHELv4u4:~#
```
## 27.4. xinetd and inetd 
### 27.4.1. the superdaemon
Back when resources like RAM memory were limited, a super-server was devised to listen to all sockets and start the appropriate daemon only when needed. Services like swat, telnet and ftp are typically served by such a super-server. The xinetd superdaemon is more recent than inetd. We will discuss the configuration both daemons. Recent Linux distributions like RHEL5 and Ubuntu10.04 do not activate inetd or xinetd by default, unless an application requires it.
### 27.4.2. inetd or xinetd 
First verify whether your computer is running inetd or xinetd. This Debian 4.0 Etch is running inetd.
```
root@barry:~# ps fax | grep inet 
3870 ? Ss 0:00 /usr/sbin/inetd 
```
This Red Hat Enterprise Linux 4 update 4 is running xinetd.
```
[root@RHEL4b ~]# ps fax | grep inet 
3003 ? Ss 0:00 xinetd -stayalive -pidfile /var/run/xinetd.pid
```
Both daemons have the same functionality (listening to many ports, starting other daemons when they are needed), but they have different configuration files.
### 27.4.3. xinetd superdaemon 
The xinetd daemon is often called a superdaemon because it listens to a lot of incoming connections, and starts other daemons when they are needed. When a connection request is received, xinetd will first check TCP wrappers (/etc/hosts.allow and /etc/hosts.deny) and then give control of the connection to the other daemon. This superdaemon is configured through /etc/xinetd.conf and the files in the directory /etc/xinetd.d. Let's first take a look at /etc/xinetd.conf.
```
paul@RHELv4u2:~$ cat /etc/xinetd.conf 
# 
# Simple configuration file for xinetd 
# 
# Some defaults, and include /etc/xinetd.d/ 
defaults 
{ 
instances = 60 
log_type = SYSLOG authpriv 
log_on_success = HOST PID 
log_on_failure = HOST 
cps = 25 30
} 
includedir /etc/xinetd.d 
paul@RHELv4u2:~$
```
According to the settings in this file, xinetd can handle 60 client requests at once. It uses the authpriv facility to log the host ip-address and pid of successful daemon spawns. When a service (aka protocol linked to daemon) gets more than 25 cps (connections per second), it holds subsequent requests for 30 seconds. The directory /etc/xinetd.d contains more specific configuration files. Let's also take a look at one of them.
```
paul@RHELv4u2:~$ ls /etc/xinetd.d 
amanda chargen-udp echo klogin rexec talk amandaidx cups-lpd echo-udp krb5-telnet rlogin telnet amidxtape daytime eklogin kshell rsh tftp auth daytime-udp finger ktalk rsync time chargen dbskkd-cdb gssftp ntalk swat  time-udp 
paul@RHELv4u2:~$ cat /etc/xinetd.d/swat 
# default: off 
# description: SWAT is the Samba Web Admin Tool. Use swat \ 
# to configure your Samba server. To use SWAT, \ 
# connect to port 901 with your favorite web browser. service swat 
{
port = 901 
socket_type = stream 
wait = no 
only_from = 127.0.0.1 
user = root 
server = /usr/sbin/swat 
log_on_failure += USERID 
disable = yes 
} 
paul@RHELv4u2:~$
```
The services should be listed in the /etc/services file. Port determines the service port, and must be the same as the port specified in /etc/services. The socket_type should be set to stream for tcp services (and to dgram for udp). The log_on_failure += concats the userid to the log message formatted in /etc/xinetd.conf. The last setting disable can be set to yes or no. Setting this to no means the service is enabled! Check the xinetd and xinetd.conf manual pages for many more configuration options. 
### 27.4.4. inetd superdaemon 
This superdaemon has only one configuration file /etc/inetd.conf. Every protocol or daemon that it is listening for, gets one line in this file.
```
root@barry:~# grep ftp /etc/inetd.conf 
tftp dgram udp wait nobody /usr/sbin/tcpd /usr/sbin/in.tftpd /boot/tftp root@barry:~#
```
You can disable a service in inetd.conf above by putting a # at the start of that line. Here an example of the disabled vmware web interface (listening on tcp port 902). 
```
paul@laika:~$ grep vmware /etc/inetd.conf 
#902 stream tcp nowait root /usr/sbin/vmware-authd vmware-authd
```
## 27.6. network file system 
### 27.6.1. protocol versions 
The older nfs versions 2 and 3 are stateless (udp) by default, but they can use tcp. Clients connect to the server using rpc (on Linux this is controlled by the portmap daemon. Look at rpcinfo to verify that nfs and its related services are running.
```
root@RHELv4u2:~# /etc/init.d/portmap status 
portmap (pid 1920) is running... 
root@RHELv4u2:~# rpcinfo -p 
program vers proto port 
100000 2 tcp 111 portmapper 
100000 2 udp 111 portmapper 
100024 1 udp 32768 status 
100024 1 tcp 32769 status 
root@RHELv4u2:~# service nfs start 
Starting NFS services: [ OK ] 
Starting NFS quotas: [ OK ] 
Starting NFS daemon: [ OK ] 
Starting NFS mountd: [ OK ] 
```
The same rpcinfo command when nfs is started
```
root@RHELv4u2:~# rpcinfo -p 
program vers proto port 
100000 2 tcp 111 portmapper 
100000 2 udp 111 portmapper 
100024 1 udp 32768 status 
100024 1 tcp 32769 status 
100011 1 udp 985 rquotad 
100011 2 udp 985 rquotad 
100011 1 tcp 988 rquotad 
100011 2 tcp 988 rquotad 
100003 2 udp 2049 nfs 
100003 3 udp 2049 nfs 
100003 4 udp 2049 nfs 
100003 2 tcp 2049 nfs 
100003 3 tcp 2049 nfs 
100003 4 tcp 2049 nfs 
100021 1 udp 32770 nlockmgr 
100021 3 udp 32770 nlockmgr 
100021 4 udp 32770 nlockmgr 
100021 1 tcp 32789 nlockmgr 
100021 3 tcp 32789 nlockmgr 
100021 4 tcp 32789 nlockmgr 
100005 1 udp 1004 mountd 
100005 1 tcp 1007 mountd 
100005 2 udp 1004 mountd 
100005 2 tcp 1007 mountd 
100005 3 udp 1004 mountd 
100005 3 tcp 1007 mountd 
root@RHELv4u2:~#
```
nfs version 4 requires tcp (port 2049) and supports Kerberos user authentication as an option. nfs authentication only takes place when mounting the share. nfs versions 2 and 3 authenticate only the host.
### 27.6.2. server configuration 
nfs is configured in /etc/exports. Here is a sample /etc/exports to explain the syntax. You need some way (NIS domain or LDAP) to synchronize userid's across computers when using nfs a lot. The rootsquash option will change UID 0 to the UID of the nfsnobody user account. The sync option will write writes to disk before completing the client request.
```
paul@laika:~$ cat /etc/exports 

# Everyone can read this share 
/mnt/data/iso *(ro) 

# Only the computers barry and pasha can readwrite this one 
/var/www pasha(rw) barry(rw) 

# same, but without root squashing for barry 
/var/ftp pasha(rw) barry(rw,no_root_squash) 

# everyone from the netsec.lan domain gets access 
/var/backup *.netsec.lan(rw) 

# ro for one network, rw for the other 
/var/upload 192.168.1.0/24(ro) 192.168.5.0/24(rw)
```
You don't need to restart the nfs server to start exporting your newly created exports. You can use the exportfs -va command to do this. It will write the exported directories to /var/ lib/nfs/etab, where they are immediately applied.
### 27.6.3. client configuration
We have seen the mount command and the /etc/fstab file before.
```
root@RHELv4u2:~# mount -t nfs barry:/mnt/data/iso /home/project55/ root@RHELv4u2:~# cat /etc/fstab | grep nfs 
barry:/mnt/data/iso /home/iso nfs defaults 0 0 
root@RHELv4u2:~#
```
Here is another simple example. Suppose the project55 people tell you they only need a couple of CD-ROM images, and you already have them available on an nfs server. You could issue the following command to mount this storage on their /home/project55 mount point.
```
root@RHELv4u2:~# mount -t nfs 192.168.1.40:/mnt/data/iso /home/project55/ root@RHELv4u2:~# ls -lh /home/project55/ 
total 3.6G 
drwxr-xr-x 2 1000 1000 4.0K Jan 16 17:55 RHELv4u1 
drwxr-xr-x 2 1000 1000 4.0K Jan 16 14:14 RHELv4u2 
drwxr-xr-x 2 1000 1000 4.0K Jan 16 14:54 RHELv4u3 
drwxr-xr-x 2 1000 1000 4.0K Jan 16 11:09 RHELv4u4 
-rw-r--r-- 1 root root 1.6G Oct 13 15:22 sled10-vmwarews5-vm.zip 
root@RHELv4u2:~#
```
