# I. Process Management
# Chapter 1. introduction to processes
## 1.1. terminology 
### 1.1.1. process 
A process is compiled source code that is currently running on the system. 
### 1.1.2. PID 
All processes have a process id or PID. 
### 1.1.3. PPID 
Every process has a parent process (with a PPID). The child process is often started by the parent process. 
### 1.1.4. init 
The init process always has process ID 1. The init process is started by the kernel itself so technically it does not have a parent process. init serves as a foster parent for orphaned processes. 
### 1.1.5. kill 
When a process stops running, the process dies, when you want a process to die, you kill it. 
### 1.1.6. daemon 
Processes that start at system startup and keep running forever are called daemon processes or daemons. These daemons never die. 
### 1.1.7. zombie 
When a process is killed, but it still shows up on the system, then the process is referred to as zombie. You cannot kill zombies, because they are already dead.

## 1.2. basic process management 
### 1.2.1.  `$$ and $PPID `
Some shell environment variables contain information about processes. The `$$` variable will hold your current process ID, and `$PPID` contains the parent PID. Actually `$$` is a shell parameter and not a variable, you cannot assign a value to it.
Below we use echo to display the values of `$$` and `$PPID.`
```
[paul@RHEL4b ~]$ echo $$ $PPID 
4224 4223
```
### 1.2.2. pidof 
You can find all process id's by name using the pidof command. 
```
root@rhel53 ~# pidof mingetty 
2819 2798 2797 2796 2795 2794
```
### 1.2.3. parent and child 
Processes have a parent-child relationship. Every process has a parent process. 
When starting a new bash you can use echo to verify that the pid from before is the ppid of the new shell. The child process from above is now the parent process. 
```
[paul@RHEL4b ~]$ bash 
[paul@RHEL4b ~]$ echo $$ $PPID 
4812 4224
```
Typing exit will end the current process and brings us back to our original values for `$$` and `$PPID.`
```
[paul@RHEL4b ~]$ echo $$ $PPID 
4812 4224 
[paul@RHEL4b ~]$ exit 
exit 
[paul@RHEL4b ~]$ echo $$ $PPID 
4224 4223 
[paul@RHEL4b ~]$
```
### 1.2.4. fork and exec 
A process starts another process in two phases. First the process creates a fork of itself, an identical copy. Then the forked process executes an exec to replace the forked process with the target child process.

```
[paul@RHEL4b ~]$ echo $$ 
4224 
[paul@RHEL4b ~]$ bash 
[paul@RHEL4b ~]$ echo $$ $PPID 
5310 4224 
[paul@RHEL4b ~]$
```
### 1.2.5. exec 
With the exec command, you can execute a process without forking a new process. In the following screenshot a Korn shell (ksh) is started and is being replaced with a bash shell using the exec command. The pid of the bash shell is the same as the pid of the Korn shell. Exiting the child bash shell will get me back to the parent bash, not to the Korn shell (which does not exist anymore).
```
[paul@RHEL4b ~]$ echo $$ 4224                                 # PID of bash 
[paul@RHEL4b ~]$ ksh 
$ echo $$ $PPID 
5343 4224                                               # PID of ksh and bash 
$ exec bash 
[paul@RHEL4b ~]$ echo $$ $PPID 
5343 4224                                             # PID of bash and bash 
[paul@RHEL4b ~]$ exit 
exit 
[paul@RHEL4b ~]$ echo $$ 4224
```
### 1.2.6. ps 
One of the most common tools on Linux to look at processes is ps. The following screenshot shows the parent child relationship between three bash processes.

```
[paul@RHEL4b ~]$ echo $$ $PPID 4224 4223 
[paul@RHEL4b ~]$ bash
[paul@RHEL4b ~]$ echo $$ $PPID 4866 4224 
[paul@RHEL4b ~]$ bash 
[paul@RHEL4b ~]$ echo $$ $PPID 4884 4866 
[paul@RHEL4b ~]$ ps fx 
PID TTY STAT TIME COMMAND 
4223 ? S 0:01 sshd: paul@pts/0 
4224 pts/0 Ss 0:00  \_ -bash 
4866 pts/0 S 0:00        \_ bash 
4884 pts/0 S 0:00           \_ bash 
4902 pts/0 R+ 0:00             \_ ps fx 
[paul@RHEL4b ~]$ exit 
exit 
[paul@RHEL4b ~]$ ps fx 
PID TTY STAT TIME COMMAND 
4223 ? S 0:01 sshd: paul@pts/0 
4224 pts/0 Ss 0:00 \_ -bash 
4866 pts/0 S 0:00     \_ bash 
4903 pts/0 R+ 0:00       \_ ps fx 
[paul@RHEL4b ~]$ exit 
exit 
[paul@RHEL4b ~]$ ps fx
PID TTY STAT TIME COMMAND
4223 ? S 0:01 sshd: paul@pts/0 
4224 pts/0 Ss 0:00   \_ -bash 
4904 pts/0 R+ 0:00     \_ ps fx 
[paul@RHEL4b ~]$
```
On Linux, ps fax is often used. On Solaris ps -ef (which also works on Linux) is common. Here is a partial output from ps fax.
```
[paul@RHEL4a ~]$ ps fax 
PID TTY STAT TIME COMMAND 
1 ? S 0:00 init [5] 
... 
3713 ?     Ss 0:00 /usr/sbin/sshd 
5042 ?     Ss 0:00  \_ sshd: paul [priv] 
5044 ?     S  0:00    \_ sshd: paul@pts/1 
5045 pts/1 Ss 0:00      \_ -bash 
5077 pts/1 R+ 0:00       \_ ps fax
```
### 1.2.7. pgrep 
Similar to the ps -C, you can also use pgrep to search for a process by its command name.
```
[paul@RHEL5 ~]$ sleep 1000 & 
[1] 32558 
[paul@RHEL5 ~]$ pgrep sleep 
32558 
[paul@RHEL5 ~]$ ps -C sleep 
PID TTY TIME CMD 
32558 pts/3 00:00:00 sleep
```
You can also list the command name of the process with pgrep. 
```
paul@laika:~$ pgrep -l sleep 
9661 sleep
```
### 1.2.8. top 
Another popular tool on Linux is top. The top tool can order processes according to cpu usage or other properties. You can also kill processes from within top. Press h inside top for help. 
In case of trouble, top is often the first tool to fire up, since it also provides you memory and swap space information.

## 1.3. signaling processes 
### 1.3.1. kill 
The kill command will kill (or stop) a process. The screenshot shows how to use a standard kill to stop the process with pid 1942. 
```
paul@ubuntu910:~$ kill 1942 
paul@ubuntu910:~$
```
By using the kill we are sending a signal to the process.
### 1.3.2. list signals 
Running processes can receive signals from each other or from the users. You can have a list of signals by typing kill -l, that is a letter l, not the number 1.
```
[paul@RHEL4a ~]$ kill -l 
1) SIGHUP 2) SIGINT 3) SIGQUIT 4) SIGILL 5) SIGTRAP 6) SIGABRT 7) SIGBUS 8) SIGFPE 9) SIGKILL 10) SIGUSR1 11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM 17) SIGCHLD 18) SIGCONT 19) SIGSTOP 20) SIGTSTP 21) SIGTTIN 22) SIGTTOU 23) SIGURG 24) SIGXCPU 25) SIGXFSZ 26) SIGVTALRM 27) SIGPROF 28) SIGWINCH 29) SIGIO 30) SIGPWR 31) SIGSYS 34) SIGRTMIN 35) SIGRTMIN+1 36) SIGRTMIN+2 37) SIGRTMIN+3 38) SIGRTMIN+4 39) SIGRTMIN+5 40) SIGRTMIN+6 41) SIGRTMIN+7 42) SIGRTMIN+8 43) SIGRTMIN+9 44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13 48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12 53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9 56) SIGRTMAX-8 57) SIGRTMAX-7 58) SIGRTMAX-6 59) SIGRTMAX-5 60) SIGRTMAX-4 61) SIGRTMAX-3 62) SIGRTMAX-2 63) SIGRTMAX-1 64) SIGRTMAX 
[paul@RHEL4a ~]$
```
### 1.3.3. kill -1 (SIGHUP) 
It is common on Linux to use the first signal SIGHUP (or HUP or 1) to tell a process that it should re-read its configuration file. Thus, the kill -1 1 command forces the init process (init always runs with pid 1) to re-read its configuration file.
```
root@deb503:~# kill -1 1 
root@deb503:~#
```
It is up to the developer of the process to decide whether the process can do this running, or whether it needs to stop and start. It is up to the user to read the documentation of the program.
### 1.3.4. kill -15 (SIGTERM) 
The SIGTERM signal is also called a standard kill. Whenever kill is executed without specifying the signal, a kill -15 is assumed. Both commands in the screenshot below are identical.
```
paul@ubuntu910:~$ kill 1942 
paul@ubuntu910:~$ kill -15 1942
```
### 1.3.5. kill -9 (SIGKILL) 
The SIGKILL is different from most other signals in that it is not being sent to the process, but to the Linux kernel. A kill -9 is also called a sure kill. The kernel will shoot down the process. As a developer you have no means to intercept a kill -9 signal.
```
root@rhel53 ~# kill -9 3342
```
### 1.3.6. SIGSTOP and SIGCONT 
A running process can be suspended when it receives a SIGSTOP signal. This is the same as kill -19 on Linux, but might have a different number in other Unix systems. 
A suspended process does not use any cpu cycles, but it stays in memory and can be reanimated with a SIGCONT signal (kill -18 on Linux). 
Both signals will be used in the section about background processes.
### 1.3.7. pkill 
You can use the pkill command to kill a process by its command name.
```
[paul@RHEL5 ~]$ sleep 1000 & 
[1] 30203 
[paul@RHEL5 ~]$ pkill sleep 
[1]+ Terminated sleep 1000 
[paul@RHEL5 ~]$ 
```
### 1.3.8. killall 
The killall command will send a signal 15 to all processes with a certain name. 
```
paul@rhel65:~$ sleep 8472 & 
[1] 18780 
paul@rhel65:~$ sleep 1201 & 
[2] 187810 
paul@rhel65:~$ jobs 
[1]- Running sleep 8472 & 
[2]+ Running sleep 1201 & 
paul@rhel65:~$ killall sleep 
[1]- Terminated sleep 8472 
[2]+ Terminated sleep 1201 
paul@rhel65:~$ jobs 
paul@rhel65:~$ 
```
###  1.3.9. killall5 
Its SysV counterpart killall5 can by used when shutting down the system. This screenshot shows how Red Hat Enterprise Linux 5.3 uses killall5 when halting the system. 
```
root@rhel53 ~# grep killall /etc/init.d/halt 
action $"Sending all processes the TERM signal..." /sbin/killall5 -15 
action $"Sending all processes the KILL signal..." /sbin/killall5 -9 
```
### 1.3.10. top 
Inside top the k key allows you to select a signal and pid to kill. Below is a partial screenshot of the line just below the summary in top after pressing k. 
```
PID to kill: 1932 
Kill PID 1932 with signal [15]: 9
```
# Chapter 2. process priorities
## 2.1. priority and nice values
### 2.1.1. introduction 
All processes have a priority and a nice value. Higher priority processes will get more cpu time than lower priority processes. You can influence this with the nice and renice commands. 
### 2.1.2. pipes (mkfifo) 
Processes can communicate with each other via pipes. These pipes can be created with the mkfifo command. 
The screenshots shows the creation of four distinct pipes (in a new directory).
```
paul@ubuntu910:~$ mkdir procs 
paul@ubuntu910:~$ cd procs/ 
paul@ubuntu910:~/procs$ mkfifo pipe33a pipe33b pipe42a pipe42b paul@ubuntu910:~/procs$ ls -l 
total 0 prw-r--r-- 1 paul paul 0 2010-04-12 13:21 pipe33a 
prw-r--r-- 1 paul paul 0 2010-04-12 13:21 pipe33b 
prw-r--r-- 1 paul paul 0 2010-04-12 13:21 pipe42a 
prw-r--r-- 1 paul paul 0 2010-04-12 13:21 pipe42b 
paul@ubuntu910:~/procs$
```
### 2.1.3. some fun with cat 
To demonstrate the use of the top and renice commands we will make the cat command use the previously created pipes to generate a full load on the cpu. 

The cat is copied with a distinct name to the current directory. (This enables us to easily recognize the processes within top. You could do the same exercise without copying the cat command, but using different users. Or you could just look at the pid of each process.)
```
paul@ubuntu910:~/procs$ cp /bin/cat proj33 
paul@ubuntu910:~/procs$ cp /bin/cat proj42 
paul@ubuntu910:~/procs$ echo -n x | ./proj33 - pipe33a > pipe33b & 
[1] 1670 paul@ubuntu910:~/procs$ ./proj33 pipe33a & 
[2] 1671 
paul@ubuntu910:~/procs$ echo -n z | ./proj42 - pipe42a > pipe42b & 
[3] 1673 
paul@ubuntu910:~/procs$ ./proj42 pipe42a & 
[4] 1674
```
The commands you see above will create two proj33 processes that use cat to bounce the x character between pipe33a and pipe33b. And ditto for the z character and proj42.
### 2.1.4. top 
Just running top without options or arguments will display all processes and an overview of innformation. The top of the top screen might look something like this.

```
top - 13:59:29 up 48 min, 4 users, load average: 1.06, 0.25, 0.14 
Tasks: 139 total, 3 running, 136 sleeping, 0 stopped, 0 zombie 
Cpu(s): 0.3%us, 99.7%sy, 0.0%ni, 0.0%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st 
Mem: 509352k total, 460040k used, 49312k free, 66752k buffers 
Swap: 746980k total, 0k used, 746980k free, 247324k cached
```

Notice the cpu idle time (0.0%id) is zero. This is because our cat processes are consuming the whole cpu. Results can vary on systems with four or more cpu cores. 
### 2.1.5. top -p 
The top -p 1670,1671,1673,1674 screenshot below shows four processes, all of then using approximately 25 percent of the cpu.
```
paul@ubuntu910:~$ top -p 1670,1671,1673,1674 
PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND 
1674 paul 20 0 2972 616 524 S 26.6 0.1 0:11.92 proj42 
1670 paul 20 0 2972 616 524 R 25.0 0.1 0:23.16 proj33 
1671 paul 20 0 2972 616 524 S 24.6 0.1 0:23.07 proj33 
1673 paul 20 0 2972 620 524 R 23.0 0.1 0:11.48 proj42
```

All four processes have an equal priority (PR), and are battling for cpu time. On some systems the Linux kernel might attribute slightly varying priority values, but the result will still be four processes fighting for cpu time.
### 2.1.6. renice 
Since the processes are already running, we need to use the renice command to change their nice value (NI). 
The screenshot shows how to use renice on both the proj33 processes.
```
paul@ubuntu910:~$ renice +8 1670 
1670: old priority 0, new priority 8 
paul@ubuntu910:~$ renice +8 1671 
1671: old priority 0, new priority 8
```
Normal users can attribute a nice value from zero to 20 to processes they own. Only the root user can use negative nice values. Be very careful with negative nice values, since they can make it impossible to use the keyboard or ssh to a system.
### 2.1.7. impact of nice values 
The impact of a nice value on running processes can vary. The screenshot below shows the result of our renice +8 command. Look at the %CPU values. 
```
PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND 
1674 paul 20 0 2972 616 524 S 46.6 0.1 0:22.37 proj42 
1673 paul 20 0 2972 620 524 R 42.6 0.1 0:21.65 proj42 
1671 paul 28 8 2972 616 524 S 5.7 0.1 0:29.65 proj33 
1670 paul 28 8 2972 616 524 R 4.7 0.1 0:29.82 proj33 
```
Important to remember is to always make less important processes nice to more important processes. Using negative nice values can have a severe impact on a system's usability. 
### 2.1.8. nice 
The nice works identical to the renice but it is used when starting a command. 
The screenshot shows how to start a script with a nice value of five. 
```
paul@ubuntu910:~$ nice -5 ./backup.sh
```
# Chapter 3. background jobs
## 3.1. background processes
### 3.1.1. jobs 
Stuff that runs in background of your current shell can be displayed with the jobs command. By default you will not have any jobs running in background. 
```
root@rhel53 ~# jobs 
root@rhel53 ~# 
```
This jobs command will be used several times in this section. 
### 3.1.2. control-Z 
Some processes can be suspended with the Ctrl-Z key combination. This sends a SIGSTOP signal to the Linux kernel, effectively freezing the operation of the process. When doing this in vi(m), then vi(m) goes to the background. The background vi(m) can be seen with the jobs command. 
```
[paul@RHEL4a ~]$ vi procdemo.txt 
[5]+ Stopped vim procdemo.txt 
[paul@RHEL4a ~]$ jobs 
[5]+ Stopped vim procdemo.txt 
```
### 3.1.3. & ampersand
Processes that are started in background using the & character at the end of the command line are also visible with the jobs command.
```
[paul@RHEL4a ~]$ find / > allfiles.txt 2> /dev/null & 
[6] 5230 
[paul@RHEL4a ~]$ jobs [5]+ Stopped vim procdemo.txt 
[6]- Running find / >allfiles.txt 2>/dev/null & 
[paul@RHEL4a ~]$ 
```
### 3.1.4. jobs -p 
An interesting option is jobs -p to see the process id of background processes. 
```
[paul@RHEL4b ~]$ sleep 500 & 
[1] 4902 
[paul@RHEL4b ~]$ sleep 400 & 
[2] 4903 
[paul@RHEL4b ~]$ jobs -p 
4902 
4903 
[paul@RHEL4b ~]$ ps `jobs -p`
PID TTY STAT TIME COMMAND 
4902 pts/0 S 0:00 sleep 500 
4903 pts/0 S 0:00 sleep 400 
[paul@RHEL4b ~]$
```
### 3.1.5. fg 
Running the fg command will bring a background job to the foreground. The number of the background job to bring forward is the parameter of fg. 
```
[paul@RHEL5 ~]$ jobs [1] Running sleep 1000 & [2]- Running sleep 1000 & [3]+ Running sleep 2000 & [paul@RHEL5 ~]$ fg 3 sleep 2000 
```
### 3.1.6. bg 
Jobs that are suspended in background can be started in background with bg. The bg will send a SIGCONT signal. Below an example of the sleep command (suspended with Ctrl-Z) being reactivated in background with bg. 
```
[paul@RHEL5 ~]$ jobs 
[paul@RHEL5 ~]$ sleep 5000 & 
[1] 6702 
[paul@RHEL5 ~]$ sleep 3000 
[2]+ Stopped sleep 3000 
[paul@RHEL5 ~]$ jobs 
[1]- Running sleep 5000 & 
[2]+ Stopped sleep 3000 
[paul@RHEL5 ~]$ bg 2 
[2]+ sleep 3000 & 
[paul@RHEL5 ~]$ jobs 
[1]- Running sleep 5000 & 
[2]+ Running sleep 3000 & 
[paul@RHEL5 ~]$
```