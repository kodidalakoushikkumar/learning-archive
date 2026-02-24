# IV. system management
# Chapter 16. scheduling
Linux administrators use the at to schedule one time jobs. Recurring jobs are better
scheduled with cron. The next two sections will discuss both tools.
## 16.1. one time jobs with at
### 16.1.1. at
Simple scheduling can be done with the at command. This screenshot shows the scheduling of the date command at 22:01 and the sleep command at 22:03.
```
root@laika:~# at 22:01
at> date
at> <EOT>
job 1 at Wed Aug 1 22:01:00 2007
root@laika:~# at 22:03
at> sleep 10
at> <EOT>
job 2 at Wed Aug 1 22:03:00 2007
root@laika:~#
```
### 16.1.2. atq
It is easy to check when jobs are scheduled with the atq or at -l commands.
```
root@laika:~# atq
1 Wed Aug 1 22:01:00 2007 a root
2 Wed Aug 1 22:03:00 2007 a root
root@laika:~# at -l
1 Wed Aug 1 22:01:00 2007 a root
2 Wed Aug 1 22:03:00 2007 a root
root@laika:~#
```
The at command understands English words like tomorrow and teatime to schedule
commands the next day and at four in the afternoon.
```
root@laika:~# at 10:05 tomorrow
at> sleep 100
at> <EOT>
job 5 at Thu Aug 2 10:05:00 2007
root@laika:~# at teatime tomorrow
at> tea
at> <EOT>
job 6 at Thu Aug 2 16:00:00 2007
root@laika:~# atq
6 Thu Aug 2 16:00:00 2007 a root
5 Thu Aug 2 10:05:00 2007 a root
root@laika:~#
```
### 16.1.3. atrm
Jobs in the at queue can be removed with atrm.
```
root@laika:~# atq
6 Thu Aug 2 16:00:00 2007 a root
5 Thu Aug 2 10:05:00 2007 a root
root@laika:~# atrm 5
root@laika:~# atq
6 Thu Aug 2 16:00:00 2007 a root
root@laika:~#
```
### 16.1.4. at.allow and at.deny
You can also use the /etc/at.allow and /etc/at.deny files to manage who can schedule jobs with at. The /etc/at.allow file can contain a list of users that are allowed to schedule at jobs. When /etc/at.allow does not exist, then everyone can use at unless their username is listed in /etc/at.deny. If none of these files exist, then everyone can use at.
## 16.2. cron
### 16.2.1. crontab file
The crontab(1) command can be used to maintain the crontab(5) file. Each user can have
their own crontab file to schedule jobs at a specific time. This time can be specified with
five fields in this order: minute, hour, day of the month, month and day of the week. If a
field contains an asterisk `(*),` then this means all values of that field.
The following example means : run script42 eight minutes after two, every day of the month,
every month and every day of the week.
`8 14 * * * script42`
Run script8472 every month on the first of the month at 25 past midnight.
`25 0 1 * * script8472`
Run this script33 every two minutes on Sunday (both 0 and 7 refer to Sunday).
`*/2 * * * 0`
Instead of these five fields, you can also type one of these: @reboot, @yearly or @annually, @monthly, @weekly, @daily or @midnight, and @hourly.
### 16.2.2. crontab command
Users should not edit the crontab file directly, instead they should type crontab -e which
will use the editor defined in the EDITOR or VISUAL environment variable. Users can
display their cron table with crontab -l.
### 16.2.3. cron.allow and cron.deny
The cron daemon crond is reading the cron tables, taking into account the /etc/cron.allow and /etc/cron.deny files. These files work in the same way as at.allow and at.deny. When the cron.allow file exists, then your username has to be in it, otherwise you cannot use cron. When the cron.allow file does not exists, then your username cannot be in the cron.deny file if you want to use cron.
### 16.2.4. /etc/crontab
The /etc/crontab file contains entries for when to run hourly/daily/weekly/monthly tasks.
It will look similar to this output.
```
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
20 3 * * * root run-parts --report /etc/cron.daily
40 3 * * 7 root run-parts --report /etc/cron.weekly
55 3 1 * * root run-parts --report /etc/cron.monthly
```
### 16.2.5. /etc/cron.*
The directories shown in the next screenshot contain the tasks that are run at the times
scheduled in /etc/crontab. The /etc/cron.d directory is for special cases, to schedule jobs
that require finer control than hourly/daily/weekly/monthly.
```
paul@laika:~$ ls -ld /etc/cron.*
drwxr-xr-x 2 root root 4096 2008-04-11 09:14 /etc/cron.d
drwxr-xr-x 2 root root 4096 2008-04-19 15:04 /etc/cron.daily
drwxr-xr-x 2 root root 4096 2008-04-11 09:14 /etc/cron.hourly
drwxr-xr-x 2 root root 4096 2008-04-11 09:14 /etc/cron.monthly
drwxr-xr-x 2 root root 4096 2008-04-11 09:14 /etc/cron.weekly
```
### 16.2.6. /etc/cron.*
Note that Red Hat uses anacron to schedule daily, weekly and monthly cron jobs.
```
root@rhel65:/etc# cat anacrontab
# /etc/anacrontab: configuration file for anacron
# See anacron(8) and anacrontab(5) for details.
SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22
#period in days delay in minutes job-identifier command
1 5 cron.daily nice run-parts /etc/cron.daily
7 25 cron.weekly nice run-parts /etc/cron.weekly
@monthly 45 cron.monthly nice run-parts /etc/cron.monthly
root@rhel65:/etc#
```
# Chapter 17. logging
## 17.1. login logging

To keep track of who is logging into the system, Linux can maintain the /var/log/wtmp, /
var/log/btmp, /var/run/utmp and /var/log/lastlog files.
### 17.1.1. /var/run/utmp (who)

Use the who command to see the /var/run/utmp file. This command is showing you all the currently logged in users. Notice that the utmp file is in /var/run and not in /var/log .
```
[root@rhel4 ~]# who
paul pts/1 Feb 14 18:21 (192.168.1.45)
sandra pts/2 Feb 14 18:11 (192.168.1.42)
inge pts/3 Feb 14 12:01 (192.168.1.33)
els pts/4 Feb 14 14:33 (192.168.1.19)
```
### 17.1.2. /var/log/wtmp (last)

The /var/log/wtmp file is updated by the login program. Use last to see the /var/run/wtmp file.
```
[root@rhel4a ~]# last | head
paul pts/1 192.168.1.45 Wed Feb 14 18:39 still logged in
reboot system boot 2.6.9-42.0.8.ELs Wed Feb 14 18:21 (01:15)
nicolas pts/5 pc-dss.telematic Wed Feb 14 12:32 - 13:06 (00:33)
stefaan pts/3 pc-sde.telematic Wed Feb 14 12:28 - 12:40 (00:12)
nicolas pts/3 pc-nae.telematic Wed Feb 14 11:36 - 12:21 (00:45)
nicolas pts/3 pc-nae.telematic Wed Feb 14 11:34 - 11:36 (00:01)
dirk pts/5 pc-dss.telematic Wed Feb 14 10:03 - 12:31 (02:28)
nicolas pts/3 pc-nae.telematic Wed Feb 14 09:45 - 11:34 (01:48)
dimitri pts/5 rhel4 Wed Feb 14 07:57 - 08:38 (00:40)
stefaan pts/4 pc-sde.telematic Wed Feb 14 07:16 - down (05:50)
[root@rhel4a ~]#
```

The last command can also be used to get a list of last reboots.
```
[paul@rekkie ~]$ last reboot
reboot system boot 2.6.16-rekkie Mon Jul 30 05:13 (370+08:42)
wtmp begins Tue May 30 23:11:45 2006
[paul@rekkie ~]
```
### 17.1.3. /var/log/lastlog (lastlog)

Use lastlog to see the /var/log/lastlog file.
```
 [root@rhel4a ~]# lastlog | tail
tim pts/5 10.170.1.122 Tue Feb 13 09:36:54 +0100 2007
rm pts/6 rhel4 Tue Feb 13 10:06:56 +0100 2007
henk **Never logged in**
stefaan pts/3 pc-sde.telematic Wed Feb 14 12:28:38 +0100 2007
dirk pts/5 pc-dss.telematic Wed Feb 14 10:03:11 +0100 2007
arsene **Never logged in**
nicolas pts/5 pc-dss.telematic Wed Feb 14 12:32:18 +0100 2007
dimitri pts/5 rhel4 Wed Feb 14 07:57:19 +0100 2007
bashuserrm pts/7 rhel4 Tue Feb 13 10:35:40 +0100 2007
kornuserrm pts/5 rhel4 Tue Feb 13 10:06:17 +0100 2007
[root@rhel4a ~]#
```
### 17.1.4. /var/log/btmp (lastb)

There is also the lastb command to display the /var/log/btmp file. This file is updated by
the login program when entering the wrong password, so it contains failed login attempts. Many computers will not have this file, resulting in no logging of failed login attempts.
```
[root@RHEL4b ~]# lastb
lastb: /var/log/btmp: No such file or directory
Perhaps this file was removed by the operator to prevent logging lastb\
 info.
[root@RHEL4b ~]#
```
The reason given for this is that users sometimes type their password by mistake instead
of their login, so this world readable file poses a security risk. You can enable bad login
logging by simply creating the file. Doing a chmod o-r /var/log/btmp improves security.
```
[root@RHEL4b ~]# touch /var/log/btmp
[root@RHEL4b ~]# ll /var/log/btmp
-rw-r--r-- 1 root root 0 Jul 30 06:12 /var/log/btmp
[root@RHEL4b ~]# chmod o-r /var/log/btmp
[root@RHEL4b ~]# lastb
btmp begins Mon Jul 30 06:12:19 2007
[root@RHEL4b ~]#
```
Failed logins via ssh, rlogin or su are not registered in /var/log/btmp. Failed logins via tty are.
```
[root@RHEL4b ~]# lastb
HalvarFl tty3 Mon Jul 30 07:10 - 07:10 (00:00)
Maria tty1 Mon Jul 30 07:09 - 07:09 (00:00)
Roberto tty1 Mon Jul 30 07:09 - 07:09 (00:00)
btmp begins Mon Jul 30 07:09:32 2007
[root@RHEL4b ~]#
```
### 17.1.5. su and ssh logins

Depending on the distribution, you may also have the /var/log/secure file being filled with messages from the auth and/or authpriv syslog facilities. This log will include su and/or ssh failed login attempts. Some distributions put this in /var/log/auth.log, verify the syslog configuration.
```
[root@RHEL4b ~]# cat /var/log/secure
Jul 30 07:09:03 sshd[4387]: Accepted publickey for paul from ::ffff:19\
2.168.1.52 port 33188 ssh2
Jul 30 05:09:03 sshd[4388]: Accepted publickey for paul from ::ffff:19\
2.168.1.52 port 33188 ssh2
Jul 30 07:22:27 sshd[4655]: Failed password for Hermione from ::ffff:1\
92.168.1.52 port 38752 ssh2
Jul 30 05:22:27 sshd[4656]: Failed password for Hermione from ::ffff:1\
92.168.1.52 port 38752 ssh2
Jul 30 07:22:30 sshd[4655]: Failed password for Hermione from ::ffff:1\
92.168.1.52 port 38752 ssh2
Jul 30 05:22:30 sshd[4656]: Failed password for Hermione from ::ffff:1\
92.168.1.52 port 38752 ssh2
Jul 30 07:22:33 sshd[4655]: Failed password for Hermione from ::ffff:1\
92.168.1.52 port 38752 ssh2
Jul 30 05:22:33 sshd[4656]: Failed password for Hermione from ::ffff:1\
92.168.1.52 port 38752 ssh2
Jul 30 08:27:33 sshd[5018]: Invalid user roberto from ::ffff:192.168.1\
.52
Jul 30 06:27:33 sshd[5019]: input_userauth_request: invalid user rober\
to
Jul 30 06:27:33 sshd[5019]: Failed none for invalid user roberto from \
::ffff:192.168.1.52 port 41064 ssh2
Jul 30 06:27:33 sshd[5019]: Failed publickey for invalid user roberto \
from ::ffff:192.168.1.52 port 41064 ssh2
Jul 30 08:27:36 sshd[5018]: Failed password for invalid user roberto f\
rom ::ffff:192.168.1.52 port 41064 ssh2
Jul 30 06:27:36 sshd[5019]: Failed password for invalid user roberto f\
rom ::ffff:192.168.1.52 port 41064 ssh2
[root@RHEL4b ~]#
```
You can enable this yourself, with a custom log file by adding the following line tot
syslog.conf.
`auth.*,authpriv.* /var/log/customsec.log`
## 17.2. syslogd
### 17.2.1. about syslog

The standard method of logging on Linux was through the syslogd daemon. Syslog was
developed by Eric Allman for sendmail, but quickly became a standard among many
Unix applications and was much later written as rfc 3164. The syslog daemon can receive
messages on udp port 514 from many applications (and appliances), and can append to log files, print, display messages on terminals and forward logs to other syslogd daemons on other machines. The syslogd daemon is configured in /etc/syslog.conf.
### 17.2.2. about rsyslog

The new method is called reliable and extended syslogd and uses the rsyslogd daemon
and the /etc/rsyslogd.conf configuration file. The syntax is backwards compatible.
Each line in the configuration file uses a facility to determine where the message is coming from. It also contains a priority for the severity of the message, and an action to decide on what to do with the message.
### 17.2.3. modules

The new rsyslog has many more features that can be expanded by using modules. Modules allow for example exporting of syslog logging to a database.
Se the manuals for more information (when you are done with this chapter).
```
root@rhel65:/etc# man rsyslog.conf
root@rhel65:/etc# man rsyslogd
root@rhel65:/etc#
```
### 17.2.4. facilities

The man rsyslog.conf command will explain the different default facilities for certain
daemons, such as mail, lpr, news and kern(el) messages. The local0 to local7 facility can
be used for appliances (or any networked device that supports syslog). Here is a list of all
facilities for rsyslog.conf version 1.3. The security keyword is deprecated.
```
auth (security)
authpriv
cron
daemon
ftp
kern
lpr mail
mark (internal use only)
news
syslog
user
uucp
local0-7
```
### 17.2.5. priorities

The worst severity a message can have is emerg followed by alert and crit. Lowest priority
should go to info and debug messages. Specifying a severity will also log all messages with a higher severity. You can prefix the severity with = to obtain only messages that match that severity. You can also specify .none to prevent a specific action from any message from a certain facility. Here is a list of all priorities, in ascending order. The keywords warn, error and panic are deprecated.
```
debug
info
notice
warning (warn)
err (error)
crit
alert
emerg (panic)
```
### 17.2.6. actions

The default action is to send a message to the username listed as action. When the action is prefixed with a / then rsyslog will send the message to the file (which can be a regular file, but also a printer or terminal). The @ sign prefix will send the message on to another syslog server. Here is a list of all possible actions.
```
root,user1 list of users, separated by comma's
* message to all logged on users
/ file (can be a printer, a console, a tty, ...)
-/ file, but don't sync after every write
| named pipe
@ other syslog hostname
```
In addition, you can prefix actions with a - to omit syncing the file after every logging.
### 17.2.7. configuration

Below a sample configuration of custom local4 messages in /etc/rsyslog.conf.
```
local4.crit /var/log/critandabove
local4.=crit /var/log/onlycrit
local4.* /var/log/alllocal4
```
### 17.2.8. restarting rsyslogd

Don't forget to restart the server after changing its configuration.
```
root@rhel65:/etc# service rsyslog restart
Shutting down system logger: [ OK ]
Starting system logger: [ OK ]
root@rhel65:/etc#
```
## 17.3. logger

The logger command can be used to generate syslog test messages. You can aslo use it in scripts. An example of testing syslogd with the logger tool.
```
[root@rhel4a ~]# logger -p local4.debug "l4 debug"
[root@rhel4a ~]# logger -p local4.crit "l4 crit"
[root@rhel4a ~]# logger -p local4.emerg "l4 emerg"
[root@rhel4a ~]#
```
The results of the tests with logger.
```
[root@rhel4a ~]# cat /var/log/critandabove
Feb 14 19:55:19 rhel4a paul: l4 crit
Feb 14 19:55:28 rhel4a paul: l4 emerg
[root@rhel4a ~]# cat /var/log/onlycrit
Feb 14 19:55:19 rhel4a paul: l4 crit
[root@rhel4a ~]# cat /var/log/alllocal4
Feb 14 19:55:11 rhel4a paul: l4 debug
Feb 14 19:55:19 rhel4a paul: l4 crit
Feb 14 19:55:28 rhel4a paul: l4 emerg
[root@rhel4a ~]#
```
## 17.4. watching logs

You might want to use the tail -f command to look at the last lines of a log file. The -f option will dynamically display lines that are appended to the log.
```
paul@ubu1010:~$ tail -f /var/log/udev
SEQNUM=1741
SOUND_INITIALIZED=1
ID_VENDOR_FROM_DATABASE=nVidia Corporation
ID_MODEL_FROM_DATABASE=MCP79 High Definition Audio
ID_BUS=pci
ID_VENDOR_ID=0x10de
ID_MODEL_ID=0x0ac0
ID_PATH=pci-0000:00:08.0
SOUND_FORM_FACTOR=internal
```
You can automatically repeat commands by preceding them with the watch command.
When executing the following:
`[root@rhel6 ~]# watch who`
Something similar to this, repeating the output of the who command every two seconds,
will appear on the screen.
```
Every 2.0s: who Sun Jul 17 15:31:03 2011
root tty1 2011-07-17 13:28
paul pts/0 2011-07-17 13:31 (192.168.1.30)
paul pts/1 2011-07-17 15:19 (192.168.1.30)
```
## 17.5. rotating logs

A lot of log files are always growing in size. To keep this within bounds, you may
want to use logrotate to rotate, compress, remove and mail log files. More info on the
logrotate command in /etc/logrotate.conf.. Individual configurations can be found in the /etc/logrotate.d/ directory.
Below a screenshot of the default Red Hat logrotate.conf file.
```
root@rhel65:/etc# cat logrotate.conf
# see "man logrotate" for details
# rotate log files weekly
weekly
# keep 4 weeks worth of backlogs
rotate 4
# create new (empty) log files after rotating old ones
create
# use date as a suffix of the rotated file
dateext
# uncomment this if you want your log files compressed
#compress
# RPM packages drop log rotation information into this directory
include /etc/logrotate.d
# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
 monthly
 create 0664 root utmp
 minsize 1M
 rotate 1
}
/var/log/btmp {
 missingok
 monthly
 create 0600 root utmp
 rotate 1
}
# system-specific logs may be also be configured here.
root@rhel65:/etc#
```
# Chapter 18. memory management
## 18.1. displaying memory and cache
### 18.1.1. /proc/meminfo

Displaying /proc/meminfo will tell you a lot about the memory on your Linux computer.
```
paul@ubu1010:~$ cat /proc/meminfo
MemTotal: 3830176 kB
MemFree: 244060 kB
Buffers: 41020 kB
Cached: 2035292 kB
SwapCached: 9892 kB
...
```
The first line contains the total amount of physical RAM, the second line is the unused RAM. Buffers is RAM used for buffering files, cached is the amount of RAM used as cache and SwapCached is the amount of swap used as cache. The file gives us much more information outside of the scope of this course.
### 18.1.2. free

The free tool can display the information provided by /proc/meminfo in a more readable
format. The example below displays brief memory information in megabytes.
```
paul@ubu1010:~$ free -om
 total used free shared buffers cached
Mem: 3740 3519 221 0 42 1994
Swap: 6234 82 6152
```
### 18.1.3. top

The top tool is often used to look at processes consuming most of the cpu, but it also displays memory information on line four and five (which can be toggled by pressing m).
Below a screenshot of top on the same ubu1010 from above.
```
top - 10:44:34 up 16 days, 9:56, 6 users, load average: 0.13, 0.09, 0.12
Tasks: 166 total, 1 running, 165 sleeping, 0 stopped, 0 zombie
Cpu(s): 5.1%us, 4.6%sy, 0.6%ni, 88.7%id, 0.8%wa, 0.0%hi, 0.3%si, 0.0%st
Mem: 3830176k total, 3613720k used, 216456k free, 45452k buffers
Swap: 6384636k total, 84988k used, 6299648k free, 2050948k cached
```
## 18.2. managing swap space
### 18.2.1. about swap space

When the operating system needs more memory than physically present in RAM, it can use swap space. Swap space is located on slower but cheaper memory. Notice that, although hard disks are commonly used for swap space, their access times are one hundred thousand times slower. The swap space can be a file, a partition, or a combination of files and partitions. You can see the swap space with the free command, or with cat /proc/swaps.
```
paul@ubu1010:~$ free -o | grep -v Mem
 total used free shared buffers cached
Swap: 6384636 84988 6299648
paul@ubu1010:~$ cat /proc/swaps
Filename Type Size Used Priority
/dev/sda3 partition 6384636 84988 -1
```
The amount of swap space that you need depends heavily on the services that the computer provides.
### 18.2.2. creating a swap partition

You can activate or deactivate swap space with the swapon and swapoff commands. New
swap space can be created with the mkswap command. The screenshot below shows the
creation and activation of a swap partition.
```
root@RHELv4u4:~# fdisk -l 2> /dev/null | grep hda
Disk /dev/hda: 536 MB, 536870912 bytes
/dev/hda1 1 1040 524128+ 83 Linux
root@RHELv4u4:~# mkswap /dev/hda1
Setting up swapspace version 1, size = 536702 kB
root@RHELv4u4:~# swapon /dev/hda1
```
Now you can see that /proc/swaps displays all swap spaces separately, whereas the free -
om command only makes a human readable summary.
```
root@RHELv4u4:~# cat /proc/swaps
Filename Type Size Used Priority
/dev/mapper/VolGroup00-LogVol01 partition 1048568 0 -1
/dev/hda1 partition 524120 0 -2
root@RHELv4u4:~# free -om
 total used free shared buffers cached
Mem: 249 245 4 0 125 54
Swap: 1535 0 1535
```
### 18.2.3. creating a swap file

Here is one more example showing you how to create a swap file. On Solaris you can use
mkfile instead of dd.
```
root@RHELv4u4:~# dd if=/dev/zero of=/smallswapfile bs=1024 count=4096
4096+0 records in
4096+0 records out
root@RHELv4u4:~# mkswap /smallswapfile
Setting up swapspace version 1, size = 4190 kB
root@RHELv4u4:~# swapon /smallswapfile
root@RHELv4u4:~# cat /proc/swaps
Filename Type Size Used Priority
/dev/mapper/VolGroup00-LogVol01 partition 1048568 0 -1
/dev/hda1 partition 524120 0 -2
/smallswapfile file 4088 0 -3
```
### 18.2.4. swap space in /etc/fstab

If you like these swaps to be permanent, then don't forget to add them to /etc/fstab. The
lines in /etc/fstab will be similar to the following.
```
/dev/hda1 swap swap defaults 0 0
/smallswapfile swap swap defaults 0 0
```
## 18.3. monitoring memory with vmstat

You can find information about swap usage using vmstat.
Below a simple vmstat displaying information in megabytes.
```
paul@ubu1010:~$ vmstat -S m
procs ---------memory-------- ---swap-- -----io---- -system- ----cpu----
 r b swpd free buff cache si so bi bo in cs us sy id wa
 0 0 87 225 46 2097 0 0 2 5 14 8 6 5 89 1
```
Below a sample vmstat when (in another terminal) root launches a find /. It generates a lot of disk i/o (bi and bo are disk blocks in and out). There is no need for swapping here.
```
paul@ubu1010:~$ vmstat 2 100
procs ----------memory---------- ---swap-- -----io---- -system-- ----cpu----
 r b swpd free buff cache si so bi bo in cs us sy id wa
 0 0 84984 1999436 53416 269536 0 0 2 5 2 10 6 5 89 1
 0 0 84984 1999428 53416 269564 0 0 0 0 1713 2748 4 4 92 0
 0 0 84984 1999552 53416 269564 0 0 0 0 1672 1838 4 6 90 0
 0 0 84984 1999552 53424 269560 0 0 0 14 1587 2526 5 7 87 2
 0 0 84984 1999180 53424 269580 0 0 0 100 1748 2193 4 6 91 0
 1 0 84984 1997800 54508 269760 0 0 610 0 1836 3890 17 10 68 4
 1 0 84984 1994620 55040 269748 0 0 250 168 1724 4365 19 17 56 9
 0 1 84984 1978508 55292 269704 0 0 126 0 1957 2897 19 18 58 4
 0 0 84984 1974608 58964 269784 0 0 1826 478 2605 4355 7 7 44 41
 0 2 84984 1971260 62268 269728 0 0 1634 756 2257 3865 7 7 47 39
```
Below a sample vmstat when executing (on RHEL6) a simple memory leaking program.
Now you see a lot of memory being swapped (si is 'swapped in').
```
[paul@rhel6c ~]$ vmstat 2 100
procs ----------memory-------- ---swap-- ----io---- --system-- -----cpu-----
 r b swpd free buff cache si so bi bo in cs us sy id wa st
 0 3 245208 5280 232 1916 261 0 0 42 27 21 0 1 98 1 0
 0 2 263372 4800 72 908 143840 128 0 1138 462 191 2 10 0 88 0
 1 3 350672 4792 56 992 169280 256 0 1092 360 142 1 13 0 86 0
 1 4 449584 4788 56 1024 95880 64 0 606 471 191 2 13 0 85 0
 0 4 471968 4828 56 1140 44832 80 0 390 235 90 2 12 0 87 0
 3 5 505960 4764 56 1136 68008 16 0 538 286 109 1 12 0 87 0
```
The code below was used to simulate a memory leak (and force swapping). This code was
found on wikipedia without author.
```
paul@mac:~$ cat memleak.c
#include <stdlib.h>
int main(void)
{
 while (malloc(50));
 return 0;
}
```
# Chapter 19. resource monitoring

Monitoring is the process of obtaining information about the utilization of memory, cpu,
bandwidth and storage. You should start monitoring your system as soon as possible, to be able to create a baseline. Make sure that you get to know your system! This baseline is
important because it allows you to see a steady or sudden growth in resource utilization
and likewise steady (or sudden) decline in resource availability. It will allow you to plan
for scaling up or scaling out.
Let us look at some tools that go beyond ps fax, df -h, free -om and du -sh.
## 19.1. four basic resources

The four basic resources to monitor are:
- cpu
- network
- ram memory
- storage
## 19.2. top

To start monitoring, you can use top. This tool will monitor ram memory, cpu and swap. Top will automatically refresh. Inside top you can use many commands, like k to kill processes, or t and m to toggle displaying task and memory information, or the number 1 to have one line per cpu, or one summary line for all cpu's.
```
top - 12:23:16 up 2 days, 4:01, 2 users, load average: 0.00, 0.00, 0.00
Tasks: 61 total, 1 running, 60 sleeping, 0 stopped, 0 zombie
Cpu(s): 0.3% us, 0.5% sy, 0.0% ni, 98.9% id, 0.2% wa, 0.0% hi, 0.0% si
Mem: 255972k total, 240952k used, 15020k free, 59024k buffers
Swap: 524280k total, 144k used, 524136k free, 112356k cached
PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
 1 root 16 0 2816 560 480 S 0.0 0.2 0:00.91 init
 2 root 34 19 0 0 0 S 0.0 0.0 0:00.01 ksoftirqd/0
 3 root 5 -10 0 0 0 S 0.0 0.0 0:00.57 events/0
 4 root 5 -10 0 0 0 S 0.0 0.0 0:00.00 khelper
 5 root 15 -10 0 0 0 S 0.0 0.0 0:00.00 kacpid
16 root 5 -10 0 0 0 S 0.0 0.0 0:00.08 kblockd/0
26 root 15 0 0 0 0 S 0.0 0.0 0:02.86 pdflush
...
```
You can customize top to display the columns of your choice, or to display only the processes
that you find interesting.
`[paul@RHELv4u3 ~]$ top p 3456 p 8732 p 9654`
## 19.3. free

The free command is common on Linux to monitor free memory. You can use free to display information every x seconds, but the output is not ideal.
```
[paul@RHELv4u3 gen]$ free -om -s 10
total used free shared buffers cached
Mem: 249 222 27 0 50 109
Swap: 511 0 511

total used free shared buffers cached
Mem: 249 222 27 0 50 109
Swap: 511 0 511

[paul@RHELv4u3 gen]$
```
## 19.4. watch

It might be more interesting to combine free with the watch program. This program can run commands with a delay, and can highlight changes (with the -d switch).
```
[paul@RHELv4u3 ~]$ watch -d -n 3 free -om
...
Every 3.0s: free -om Sat Jan 27 12:13:03 2007
total used free shared buffers cached
Mem: 249 230 19 0 56 109
Swap: 511 0 511
```
## 19.5. vmstat

To monitor CPU, disk and memory statistics in one line there is vmstat. The screenshot
below shows vmstat running every two seconds 100 times (or until the Ctrl-C). Below the
r, you see the number of processes waiting for the CPU, sleeping processes go below b.
Swap usage (swpd) stayed constant at 144 kilobytes, free memory dropped from 16.7MB
to 12.9MB. See man vmstat for the rest.
```
[paul@RHELv4u3 ~]$ vmstat 2 100
procs ----------memory--------- --swap-- ---io--- --system-- ---cpu----
r b swpd free buff cache si so bi bo in cs us sy id wa
0 0 144 16708 58212 111612 0 0 3 4 75 62 0 1 99 0
0 0 144 16708 58212 111612 0 0 0 0 976 22 0 0 100 0
0 0 144 16708 58212 111612 0 0 0 0 958 14 0 1 99 0
1 0 144 16528 58212 111612 0 0 0 18 1432 7417 1 32 66 0
1 0 144 16468 58212 111612 0 0 0 0 2910 20048 4 95 1 0
1 0 144 16408 58212 111612 0 0 0 0 3210 19509 4 97 0 0
1 0 144 15568 58816 111612 0 0 300 1632 2423 10189 2 62 0 36
0 1 144 13648 60324 111612 0 0 754 0 1910 2843 1 27 0 72
0 0 144 12928 60948 111612 0 0 312 418 1346 1258 0 14 57 29
0 0 144 12928 60948 111612 0 0 0 0 977 19 0 0 100 0
0 0 144 12988 60948 111612 0 0 0 0 977 15 0 0 100 0
0 0 144 12988 60948 111612 0 0 0 0 978 18 0 0 100 0

[paul@RHELv4u3 ~]$
```
## 19.6. iostat

The iostat tool can display disk and cpu statistics. The -d switch below makes iostat only
display disk information (500 times every two seconds). The first block displays statistics
since the last reboot.
```
[paul@RHELv4u3 ~]$ iostat -d 2 500
Linux 2.6.9-34.EL (RHELv4u3.localdomain) 01/27/2007

Device: tps Blk_read/s Blk_wrtn/s Blk_read Blk_wrtn
hdc 0.00 0.01 0.00 1080 0
sda 0.52 5.07 7.78 941798 1445148
sda1 0.00 0.01 0.00 968 4
sda2 1.13 5.06 7.78 939862 1445144
dm-0 1.13 5.05 7.77 939034 1444856
dm-1 0.00 0.00 0.00 360 288

Device: tps Blk_read/s Blk_wrtn/s Blk_read Blk_wrtn
hdc 0.00 0.00 0.00 0 0
sda 0.00 0.00 0.00 0 0
sda1 0.00 0.00 0.00 0 0
sda2 0.00 0.00 0.00 0 0
dm-0 0.00 0.00 0.00 0 0
dm-1 0.00 0.00 0.00 0 0
...
[paul@RHELv4u3 ~]$
```
You can have more statistics using iostat -d -x, or display only cpu statistics with iostat -c.
```
[paul@RHELv4u3 ~]$ iostat -c 5 500
Linux 2.6.9-34.EL (RHELv4u3.localdomain) 01/27/2007

avg-cpu: %user %nice %sys %iowait %idle
0.31 0.02 0.52 0.23 98.92

avg-cpu: %user %nice %sys %iowait %idle
0.62 0.00 52.16 47.23 0.00

avg-cpu: %user %nice %sys %iowait %idle
2.92 0.00 36.95 60.13 0.00

avg-cpu: %user %nice %sys %iowait %idle
0.63 0.00 36.63 62.32 0.42

avg-cpu: %user %nice %sys %iowait %idle
0.00 0.00 0.20 0.20 99.59

 [paul@RHELv4u3
```
## 19.7. mpstat

On multi-processor machines, mpstat can display statistics for all, or for a selected cpu.
```
paul@laika:~$ mpstat -P ALL
Linux 2.6.20-3-generic (laika) 02/09/2007

CPU %user %nice %sys %iowait %irq %soft %steal %idle intr/s
all 1.77 0.03 1.37 1.03 0.02 0.39 0.00 95.40 1304.91
 0 1.73 0.02 1.47 1.93 0.04 0.77 0.00 94.04 1304.91
 1 1.81 0.03 1.27 0.13 0.00 0.00 0.00 96.76 0.00
paul@laika:~$
```
## 19.8. sadc and sar

The sadc tool writes system utilization data to /var/log/sa/sa??, where ?? is replaced with
the current day of the month. By default, cron runs the sal script every 10 minutes, the sal
script runs sadc for one second. Just before midnight every day, cron runs the sa2 script,
which in turn invokes sar. The sar tool will read the daily data generated by sadc and put it in /var/log/sa/sar??. These sar reports contain a lot of statistics.
You can also use sar to display a portion of the statistics that were gathered. Like this example for cpu statistics.
```
[paul@RHELv4u3 sa]$ sar -u | head
Linux 2.6.9-34.EL (RHELv4u3.localdomain) 01/27/2007

12:00:01 AM CPU %user %nice %system %iowait %idle
12:10:01 AM all 0.48 0.01 0.60 0.04 98.87
12:20:01 AM all 0.49 0.01 0.60 0.06 98.84
12:30:01 AM all 0.49 0.01 0.64 0.25 98.62
12:40:02 AM all 0.44 0.01 0.62 0.07 98.86
12:50:01 AM all 0.42 0.01 0.60 0.10 98.87
01:00:01 AM all 0.47 0.01 0.65 0.08 98.80
01:10:01 AM all 0.45 0.01 0.68 0.08 98.78
[paul@RHELv4u3 sa]$
```
There are other useful sar options, like sar -I PROC to display interrupt activity per interrupt and per CPU, or sar -r for memory related statistics. Check the manual page of sar for more
## 19.9. ntop

The ntop tool is not present in default Red Hat installs. Once run, it will generate a very
extensive analysis of network traffic in html on http://localhost:3000 .
## 19.10. iftop

The iftop tool will display bandwidth by socket statistics for a specific network device. Not
available on default Red Hat servers.
```
1.91Mb 3.81Mb 5.72Mb 7.63Mb 9.54Mb
--------------|-------------|--------------|-------------|--------|----
laika.local => barry 4.94Kb 6.65Kb 69.9Kb
 <= 7.41Kb 16.4Kb 766Kb
laika.local => ik-in-f19.google.com 0b 1.58Kb 14.4Kb
 <= 0b 292b 41.0Kb
laika.local => ik-in-f99.google.com 0b 83b 4.01Kb
 <= 0b 83b 39.8Kb
laika.local => ug-in-f189.google.com 0b 42b 664b
 <= 0b 42b 406b
laika.local => 10.0.0.138 0b 0b 149b
 <= 0b 0b 256b
laika.local => 224.0.0.251 0b 0b 86b
 <= 0b 0b 0b
laika.local => ik-in-f83.google.com 0b 0b 39b
 <= 0b 0b 21b
```
## 19.11. iptraf

Use iptraf for a colourful display of ip traffic over the network cards.
```
[root@centos65 ~]# iptraf
[root@centos65 ~]# iptraf -i eth0
```

# Chapter 20. package management

Most Linux distributions have a package management system with online repositories
containing thousands of packages. This makes it very easy to install and remove applications, operating system components, documentation and much more.
We first discuss the Debian package format .deb and its tools dpkg, apt-get and aptitude.
This should be similar on Debian, Ubuntu, Mint and all derived distributions.
Then we look at the Red Hat package format .rpm and its tools rpm and yum. This should
be similar on Red Hat, Fedora, CentOS and all derived distributions.
## 20.1. package terminology
### 20.1.1. repository

A lot of software and documentation for your Linux distribution is available as packages
in one or more centrally distributed repositories. These packages in such a repository are
tested and very easy to install (or remove) with a graphical or command line installer.
### 20.1.2. .deb packages

Debian, Ubuntu, Mint and all derivatives from Debian and Ubuntu use .deb packages. To
manage software on these systems, you can use aptitude or apt-get, both these tools are
a front end for dpkg.
### 20.1.3. .rpm packages

Red Hat, Fedora, CentOS, OpenSUSE, Mandriva, Red Flag and others use .rpm packages.
The tools to manage software packages on these systems are yum and rpm.
### 20.1.4. dependency

Some packages need other packages to function. Tools like apt-get, aptitude and yum will install all dependencies you need. When using dpkg or rpm, or when building from source, you will need to install dependencies yourself.
### 20.1.5. open source

These repositories contain a lot of independent open source software. Often the source code is customized to integrate better with your distribution. Most distributions also offer this modified source code as a package in one or more source repositories.
You are free to go to the project website itself (samba.org, apache.org, github.com, ...) an
download the vanilla (= without the custom distribution changes) source code.
### 20.1.6. GUI software management

End users have several graphical applications available via the desktop (look for 'add/remove software' or something similar).
## 20.2. deb package management
### 20.2.1. about deb

Most people use aptitude or apt-get to manage their Debian/Ubuntu family of Linux
distributions. Both are a front end for dpkg and are themselves a back end for synaptic and other graphical tools.
### 20.2.2. dpkg -l

The low level tool to work with .deb packages is dpkg. Here you see how to obtain a list
of all installed packages on a Debian server.
```
root@debian6:~# dpkg -l | wc -l
265
```
Compare this to the same list on a Ubuntu Desktop computer.
```
root@ubu1204~# dpkg -l | wc -l
2527
```
### 20.2.3. dpkg -l $package

Here is an example on how to get information on an individual package. The ii at the
beginning means the package is installed.
```
root@debian6:~# dpkg -l rsync | tail -1 | tr -s ' '
ii rsync 3.0.7-2 fast remote file copy program (like rcp)
```
### 20.2.4. dpkg -S

You can find the package that installed a certain file on your computer with dpkg -S. This
example shows how to find the package for three files on a typical Debian server.
```
root@debian6:~# dpkg -S /usr/share/doc/tmux/ /etc/ssh/ssh_config /sbin/ifconfig
tmux: /usr/share/doc/tmux/
openssh-client: /etc/ssh/ssh_config
net-tools: /sbin/ifconfig
```
### 20.2.5. dpkg -L

You can also get a list of all files that are installed by a certain program. Below is the list
for the tmux package.
```
root@debian6:~# dpkg -L tmux
/.
/etc
/etc/init.d
/etc/init.d/tmux-cleanup
/usr
/usr/share
/usr/share/lintian
/usr/share/lintian/overrides
/usr/share/lintian/overrides/tmux
/usr/share/doc
/usr/share/doc/tmux
/usr/share/doc/tmux/TODO.gz
/usr/share/doc/tmux/FAQ.gz
/usr/share/doc/tmux/changelog.Debian.gz
/usr/share/doc/tmux/NEWS.Debian.gz
/usr/share/doc/tmux/changelog.gz
/usr/share/doc/tmux/copyright
/usr/share/doc/tmux/examples
/usr/share/doc/tmux/examples/tmux.vim.gz
/usr/share/doc/tmux/examples/h-boetes.conf
/usr/share/doc/tmux/examples/n-marriott.conf
/usr/share/doc/tmux/examples/screen-keys.conf
/usr/share/doc/tmux/examples/t-williams.conf
/usr/share/doc/tmux/examples/vim-keys.conf
/usr/share/doc/tmux/NOTES
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/tmux.1.gz
/usr/bin
/usr/bin/tmux
```
### 20.2.6. dpkg

You could use dpkg -i to install a package and dpkg -r to remove a package, but you'd have to manually keep track of dependencies. Using apt-get or aptitude is much easier.
## 20.3. apt-get

Debian has been using apt-get to manage packages since 1998. Today Debian and
many Debian-based distributions still actively support apt-get, though some experts claim aptitude is better at handling dependencies than apt-get.
Both commands use the same configuration files and can be used alternately; whenever you see apt-get in documentation, feel free to type aptitude.
We will start with apt-get and discuss aptitude in the next section.
### 20.3.1. apt-get update

When typing apt-get update you are downloading the names, versions and short description of all packages available on all configured repositories for your system.
In the example below you can see some repositories at the url be.archive.ubuntu.com
because this computer was installed in Belgium. This url can be different for you.
```
root@ubu1204~# apt-get update
Ign http://be.archive.ubuntu.com precise InRelease
Ign http://extras.ubuntu.com precise InRelease
Ign http://security.ubuntu.com precise-security InRelease
Ign http://archive.canonical.com precise InRelease
Ign http://be.archive.ubuntu.com precise-updates InRelease
...
Hit http://be.archive.ubuntu.com precise-backports/main Translation-en
Hit http://be.archive.ubuntu.com precise-backports/multiverse Translation-en
Hit http://be.archive.ubuntu.com precise-backports/restricted Translation-en
Hit http://be.archive.ubuntu.com precise-backports/universe Translation-en
Fetched 13.7 MB in 8s (1682 kB/s)
Reading package lists... Done
root@mac~#
```
Run apt-get update every time before performing other package operations.
### 20.3.2. apt-get upgrade

One of the nicest features of apt-get is that it allows for a secure update of all software
currently installed on your computer with just one command.
```
root@debian6:~# apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
root@debian6:~#
```
The above screenshot shows that all software is updated to the latest version available for
my distribution.
### 20.3.3. apt-get clean

apt-get keeps a copy of downloaded packages in /var/cache/apt/archives, as can be seen
in this screenshot.
```
root@ubu1204~# ls /var/cache/apt/archives/ | head
accountsservice_0.6.15-2ubuntu9.4_i386.deb
apport_2.0.1-0ubuntu14_all.deb
apport-gtk_2.0.1-0ubuntu14_all.deb
apt_0.8.16~exp12ubuntu10.3_i386.deb
apt-transport-https_0.8.16~exp12ubuntu10.3_i386.deb
apt-utils_0.8.16~exp12ubuntu10.3_i386.deb
bind9-host_1%3a9.8.1.dfsg.P1-4ubuntu0.4_i386.deb
chromium-browser_20.0.1132.47~r144678-0ubuntu0.12.04.1_i386.deb
chromium-browser-l10n_20.0.1132.47~r144678-0ubuntu0.12.04.1_all.deb
chromium-codecs-ffmpeg_20.0.1132.47~r144678-0ubuntu0.12.04.1_i386.deb
```
Running apt-get clean removes all .deb files from that directory.
```
root@ubu1204~# apt-get clean
root@ubu1204~# ls /var/cache/apt/archives/*.deb
ls: cannot access /var/cache/apt/archives/*.deb: No such file or directory
```
### 20.3.4. apt-cache search

Use apt-cache search to search for availability of a package. Here we look for rsync.
```
root@ubu1204~# apt-cache search rsync | grep ^rsync
rsync - fast, versatile, remote (and local) file-copying tool
rsyncrypto - rsync friendly encryption
```
### 20.3.5. apt-get install

You can install one or more applications by appending their name behind apt-get install.
The screenshot shows how to install the rsync package.
```
root@ubu1204~# apt-get install rsync
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
 rsync
0 upgraded, 1 newly installed, 0 to remove and 8 not upgraded.
Need to get 299 kB of archives.
After this operation, 634 kB of additional disk space will be used.
Get:1 http://be.archive.ubuntu.com/ubuntu/ precise/main rsync i386 3.0.9-1ubuntu1 [299 kB]
Fetched 299 kB in 0s (740 kB/s)
Selecting previously unselected package rsync.
(Reading database ... 323649 files and directories currently installed.)
Unpacking rsync (from .../rsync_3.0.9-1ubuntu1_i386.deb) ...
Processing triggers for man-db ...
Processing triggers for ureadahead ...
Setting up rsync (3.0.9-1ubuntu1) ...
 Removing any system startup links for /etc/init.d/rsync ...
root@ubu1204~#
```
### 20.3.6. apt-get remove

You can remove one or more applications by appending their name behind apt-get remove.
The screenshot shows how to remove the rsync package.
```
root@ubu1204~# apt-get remove rsync
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages will be REMOVED:
 rsync ubuntu-standard
0 upgraded, 0 newly installed, 2 to remove and 8 not upgraded.
After this operation, 692 kB disk space will be freed.
Do you want to continue [Y/n]?
(Reading database ... 323681 files and directories currently installed.)
Removing ubuntu-standard ...
Removing rsync ...
 * Stopping rsync daemon rsync Processing triggers for ureadahead ...
Processing triggers for man-db ...
root@ubu1204~#
```
Note however that some configuration information is not removed.
```
root@ubu1204~# dpkg -l rsync | tail -1 | tr -s ' '
rc rsync 3.0.9-1ubuntu1 fast, versatile, remote (and local) file-copying tool
```
### 20.3.7. apt-get purge

You can purge one or more applications by appending their name behind apt-get purge.
Purging will also remove all existing configuration files related to that application. The
screenshot shows how to purge the rsync package.
```
root@ubu1204~# apt-get purge rsync
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages will be REMOVED:
 rsync*
0 upgraded, 0 newly installed, 1 to remove and 8 not upgraded.
After this operation, 0 B of additional disk space will be used.
Do you want to continue [Y/n]?
(Reading database ... 323651 files and directories currently installed.)
Removing rsync ...
Purging configuration files for rsync ...
Processing triggers for ureadahead ...
root@ubu1204~#
```
Note that dpkg has no information about a purged package, except that it is uninstalled and no configuration is left on the system.
```
root@ubu1204~# dpkg -l rsync | tail -1 | tr -s ' '
un rsync <none> (no description available)
```
## 20.4. aptitude

Most people use aptitude for package management on Debian, Mint and Ubuntu systems.
To synchronize with the repositories.
`aptitude update`
To patch and upgrade all software to the latest version on Debian.
`aptitude upgrade`
To patch and upgrade all software to the latest version on Ubuntu and Mint.
`aptitude safe-upgrade`
To install an application with all dependencies.
`aptitude install $package`
To search the repositories for applications that contain a certain string in their name or
description.
`aptitude search $string`
To remove an application.
`aptitude remove $package`
To remove an application and all configuration files.
`aptitude purge $package`
## 20.5. apt

Both apt-get and aptitude use the same configuration information in /etc/apt/. Thus adding a repository for one of them, will automatically add it for both.
### 20.5.1. /etc/apt/sources.list

The resource list used by apt-get and aptitude is located in /etc/apt/sources.list. This file
contains a list of http or ftp sources where packages for the distribution can be downloaded.
This is what that list looks like on my Debian server.
```
root@debian6:~# cat /etc/apt/sources.list
deb http://ftp.be.debian.org/debian/ squeeze main
deb-src http://ftp.be.debian.org/debian/ squeeze main
deb http://security.debian.org/ squeeze/updates main
deb-src http://security.debian.org/ squeeze/updates main
# squeeze-updates, previously known as 'volatile'
deb http://ftp.be.debian.org/debian/ squeeze-updates main
deb-src http://ftp.be.debian.org/debian/ squeeze-updates main
```
On my Ubuntu there are four times as many online repositories in use.
```
root@ubu1204~# wc -l /etc/apt/sources.list
63 /etc/apt/sources.list
```
There is much more to learn about apt, explore commands like add-apt-repository, aptkey and apropos apt.
## 20.9. downloading software outside the repository

First and most important, whenever you download software, start by reading the README file! Normally the readme will explain what to do after download. You will probably receive a .tar.gz or a .tgz file. Read the documentation, then put the compressed file in a directory.
You can use the following to find out where the package wants to install.
`tar tvzpf $downloadedFile.tgz`
You unpack them like with tar xzf, it will create a directory called applicationName-1.2.3
`tar xzf $applicationName.tgz`
Replace the z with a j when the file ends in .tar.bz2. The tar, gzip and bzip2 commands are explained in detail in the Linux Fundamentals course.
If you download a .deb file, then you'll have to use dpkg to install it, .rpm's can be installed with the rpm command.
## 20.10. compiling software

First and most important, whenever you download source code for installation, start by
reading the README file!
Usually the steps are always the same three : running ./configure followed by make (which is the actual compiling) and then by make install to copy the files to their proper location.
```
./configure
make
make install
```
