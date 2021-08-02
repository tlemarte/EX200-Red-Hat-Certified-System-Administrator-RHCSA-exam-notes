# Understand and use essential tools

## Access a shell prompt and issue commands with correct syntax

## Use input-output redirection (>, >> , | , >2, etc.)
* The '>' symbol is used for output (STDOUT) redirection. Overwrites the output to a specified destination
```
[tlemarte@bluebox ~]$ hostnamectl > file.txt
[tlemarte@bluebox ~]$ cat file.txt 
 Static hostname: bluebox
 Pretty hostname: BlueBox
       Icon name: computer-laptop
         Chassis: laptop
      Machine ID: 123..890
         Boot ID: 123..890
Operating System: Fedora 34 (Workstation Edition)
     CPE OS Name: cpe:/o:fedoraproject:fedora:34
          Kernel: Linux 5.13.4-200.fc34.x86_64
    Architecture: x86-64
 Hardware Vendor: ..
  Hardware Model: ThinkPad L480
```
* The ‘>>’ symbol is also for output redirection. Appends output to a destination 
```
[tlemarte@bluebox ~]$ grep '^VERSION' /etc/os-release >> file.txt
[tlemarte@bluebox ~]$ cat file.txt 
 Static hostname: bluebox
 Pretty hostname: BlueBox
       Icon name: computer-laptop
         Chassis: laptop
      Machine ID: 123..890
         Boot ID: 123..890
Operating System: Fedora 34 (Workstation Edition)
     CPE OS Name: cpe:/o:fedoraproject:fedora:34
          Kernel: Linux 5.13.4-200.fc34.x86_64
    Architecture: x86-64
 Hardware Vendor: ..
  Hardware Model: ThinkPad L480
VERSION="34 (Workstation Edition)"
VERSION_ID=34
VERSION_CODENAME=""
```
* noclobber can be enabled to prevent accidental overwrites of files with output redirection. This can then be bypassed using the pipe character for an extra layer of safety when modifying files using output redirection.
```
[tlemarte@bluebox ~]$ set -o noclobber 
[tlemarte@bluebox ~]$ set -o | grep noclobber
noclobber      	on
[tlemarte@bluebox ~]$ date > file.txt 
bash: file.txt: cannot overwrite existing file
[tlemarte@bluebox ~]$ date >| file.txt 
[tlemarte@bluebox ~]$ cat file.txt 
Thu Jul 29 12:58:49 PM PDT 2021
```
* | (pipe) : Connect output of one command to input of another
named pipes
* \>2 : Outputs STDERROR
* &> : outputs STDOUT and STDERROR
* < : STIN redirects an file or output as input to another command.
```
[tlemarte@bluebox ~]$ df -hlT >| file.txt
[tlemarte@bluebox ~]$ cat < file.txt 
Filesystem     Type      Size  Used Avail Use% Mounted on
devtmpfs       devtmpfs  7.8G     0  7.8G   0% /dev
tmpfs          tmpfs     7.8G   25M  7.8G   1% /dev/shm
tmpfs          tmpfs     3.2G  2.0M  3.2G   1% /run
/dev/sda3      btrfs     237G  6.1G  231G   3% /
/dev/sda3      btrfs     237G  6.1G  231G   3% /home
/dev/sda2      ext4      976M  238M  672M  27% /boot
/dev/sda1      vfat      599M   17M  583M   3% /boot/efi
tmpfs          tmpfs     7.8G   17M  7.8G   1% /tmp
tmpfs          tmpfs     1.6G  160K  1.6G   1% /run/user/1000
```
HERE documents
tee
* $(...) : Uses the output of another command. Commands between the parenthesis are evaluated first, then passed into other commands.
* !$ : Uses your last used argument

## Use grep and regular expressions to analyze text

* Grep: grabs a line that contains specified text

```
[tlemarte@Centos lib]$ ls | grep fi
firewalld
firmware
fontconfig
tmpfiles.d
```

RegEx is a system for doing more complicated parsing.

* ^... (hat or caret) searches for lines beginning in specified text

```
[tlemarte@Centos ~]$ cat lorem-ipsum | grep ^Lorem
Lorem ipsum dolor sit amet, consectetur adipiscing elit.
```

* \\b...\\b can search for word boundaries and enables whole word searching

```
[tlemarte@Centos ~]$ cat lorem-ipsum | grep '\bMorbi\b'
Morbi blandit diam et enim ultricies, in pretium enim pharetra.
Morbi semper nisl commodo cursus pretium.
Finibus Morbi mauris ut viverra volutpat,
```

## Access remote systems using SSH

* Ssh : Securely connect to remote device using password or key

```
[tlemarte@Centos ~]$ ssh -i Centos_key.pem tlemarte@52.156.78.48
The authenticity of host '52.156.78.48 (52.156.78.48)' can't be established.
ECDSA key fingerprint is SHA256:jHeDafFDyNE/Vh0YDCKmLgd/4gDtLdM0cEKHLCqGDOc.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

## Log in and switch users in multiuser targets

* To verify system is running in multiuser, use "systemctl get-default"

```
[tlemarte@Centos ~]$ systemctl get-default
multi-user.target
```

* su : change user. "su" by itself logs the user into root. "su username" prompts you to log in to the user requested.

```
[tlemarte@Centos ~]$ su
[root@Centos tlemarte]# whoami
root
```

## Archive, compress, unpack, and uncompress files using tar, star, gzip, and bzip2

* Compress commands: gzip and bzip, and tar can compress files. tar can also archive files.

```
[tlemarte@Centos ~]$ gzip file
[tlemarte@Centos ~]$ ls -lh
-rw-rw-r--. 1 tlemarte tlemarte  14K Jul 23 19:58 file
-rw-rw-r--. 1 tlemarte tlemarte 4.1K Jul 23 20:42 file.gz
```
```
[tlemarte@Centos ~]$ bzip2 file
[tlemarte@Centos ~]$ ls -lh
-rw-rw-r--. 1 tlemarte tlemarte  14K Jul 23 19:58 file
-rw-rw-r--. 1 tlemarte tlemarte 3.2K Jul 23 20:42 file.bz2
````
```
[tlemarte@Centos ~]$ tar -czf file.tgz file
#Remember "Create Zipped File/Folder". Tar archives then compresses using gzip
[tlemarte@Centos ~]$ ls -lh
-rw-rw-r--. 1 tlemarte tlemarte  14K Jul 23 19:58 file
-rw-rw-r--. 1 tlemarte tlemarte 4.3K Jul 23 20:45 file.tgz
```
* Archive command: You can user tar and star to archive files. Star supports extended attributes like SELinux contexts on older systems.

```
[tlemarte@Centos ~]$ star -xattr -H=exustar -c -f=file.star file
star: 2 blocks + 0 bytes (total of 20480 bytes = 20.00k).
[tlemarte@Centos ~]$ ls -lh
-rw-rw-r--. 1 tlemarte tlemarte  14K Jul 23 19:58 file
-rw-rw-r--. 1 tlemarte tlemarte  20K Jul 23 21:00 file.star
```

* Uncompress commands: gunzip, bunzip2, and tar can uncompress files

```
[tlemarte@Centos ~]$ gunzip file.gz
[tlemarte@Centos ~]$ bunzip2 file.bz2
[tlemarte@Centos ~]$ tar -xzf file.tgz
#Remember "eXtract Zipped File/Folder". Uncompresses using gzip
[tlemarte@Centos ~]$ ls -lh
total 16K
-rw-rw-r--. 1 tlemarte tlemarte 14K Jul 23 19:58 lorem-ipsum
```

## Create and edit text files

* Nano : Basic text editor
* Vi : Advanced text editor ( !wq to exit )
* Touch : Creates a file of a given name

```
[tlemarte@Centos ~]$ touch test
[tlemarte@Centos ~]$ ls -l
total 0
-rw-rw-r--. 1 tlemarte tlemarte 0 Jul 23 8:00 test
```

## Create, delete, copy, and move files and directories

* Mkdir : creates a directory. -m creates with permissions. (mkdir -m 777 dir1). -p creates with parents (mkdir -p ParentDir/ChildDir )
* Mv : moves a file or directory, also used for renaming
* Cp : Copy a file or directory
* Rm : Remove a file or directory. Needs -r (recursive) to clear directories that contain files
* Rmdir : Removes a directory. Needs -r (recursive) to clear directories that contain files

## Create hard and soft links

* All directories contain 2 hard links. The first is  ' . ' which is a link to the current directory, the second is ' .. ' which is a link to the parent directory.
* ln : Creates a hard link. Can not create links across file systems or partitions.
* ls -s : Creates a soft link. Can point to a file or directory across file systems and partitions.

## List, set, and change standard ugo/rwx permissions

* ls -l : "List Long" or get all files in a directory with detailed information. "ls -l filename" will get you detailed information about a specific file

```
[tlemarte@Centos /]$ ls -l
total 24
lrwxrwxrwx.   1 root root    7 Aug 12  2018 bin - usr/bin
dr-xr-xr-x.   5 root root 4096 Jul 23 18:11 boot
drwxr-xr-x.  19 root root 3200 Jul 23 17:57 dev
drwxr-xr-x.  93 root root 8192 Jul 23 18:12 etc
drwxr-xr-x.   3 root root   22 Jul 23 17:57 home
lrwxrwxrwx.   1 root root    7 Aug 12  2018 lib - usr/lib
lrwxrwxrwx.   1 root root    9 Aug 12  2018 lib64 - usr/lib64
drwxr-xr-x.   2 root root    6 Aug 12  2018 media
drwxr-xr-x.   3 root root 4096 Jul 23 17:57 mnt
drwxr-xr-x.   2 root root    6 Aug 12  2018 opt
dr-xr-xr-x. 163 root root    0 Jul 23 17:56 proc
dr-xr-x---.   3 root root  103 Jul 23 17:57 root
drwxr-xr-x.  31 root root  940 Jul 23 18:11 run
lrwxrwxrwx.   1 root root    8 Aug 12  2018 sbin - usr/sbin
drwxr-xr-x.   2 root root    6 Aug 12  2018 srv
dr-xr-xr-x.  13 root root    0 Jul 23 17:56 sys
drwxrwxrwt.   3 root root   85 Jul 23 18:12 tmp
drwxr-xr-x.  12 root root  144 Mar  4 12:15 usr
drwxr-xr-x.  21 root root 4096 Mar  4 12:19 var
```

## Locate, read, and use system documentation including man, info, and files in /usr/share/doc

* man : using "man command_name" you can get more information for the command_name command

```
[tlemarte@Centos ~]$ man man
MAN(1)        Manual pager utils        MAN(1)

NAME
        man - an interface to the on-line reference manuals

SYNOPSIS ...
```

* info : using "info command_name" you can get more information for the command_name command

```
[tlemarte@Centos ~]$ info info
Next: Stand-alone Info,  Up: (dir)

Stand-alone GNU Info
********************

This documentation describes the stand-alone Info reader which you can
use to read Info documentation.
```

* man/info -k : Both commands can use the -k flag to have the argument used a search term for man/info pages

```
[tlemarte@Centos ~]$ man -k directory
access (3p)          - determine accessibility of a file relative to directory file descriptor
adcli (8)            - Tool for performing actions on an Active Directory domain
alphasort (3)        - scan a directory for matching entries
alphasort (3p)       - scan a directory
```

* If information is unavailable in man or info, /usr/share/doc also contains information regarding commands

```
[tlemarte@Centos ~]$ ls /usr/share/doc/
abattis-cantarell-fonts        iptstate           libXext                      python3-magic
adcli                          iputils            libxml2                      python3-markupsafe
at                             irqbalance         libXrender                   python3-netifaces
attr                           jansson            libyaml                      python3-newt
```

# Create simple shell scripts

## Conditionally execute code (use of: if, test, [ ], etc.)

## Use Looping constructs (for, etc.) to process file, command line input

## Process script inputs ($1, $2, etc.)

## Processing output of shell commands within a script

## Processing shell command exit codes

# Operate running systems

## Boot, reboot, and shut down a system normally

* Booting is initiated depending on VM/Baremetal/ETC
* Shutdown commands

```
[tlemarte@Centos ~]$ halt
[tlemarte@Centos ~]$ systemctl halt
[tlemarte@Centos ~]$ shutdown -h now
[tlemarte@Centos ~]$ init 0
[tlemarte@Centos ~]$ telinit 0
```

* Reboot/Restart commands

```
[tlemarte@Centos ~]$ reboot
[tlemarte@Centos ~]$ systemctl reboot
[tlemarte@Centos ~]$ shutdown -r now
[tlemarte@Centos ~]$ init 6
[tlemarte@Centos ~]$ telinit 6
```

## Boot systems into different targets manually
 * List target with 'ls -l /lib/systemd/system/runlevel*.target' or 'systemctl list-units --type target --all'
```
[tlemarte@Centos ~]$ ls -l /lib/systemd/system/runlevel*.target
lrwxrwxrwx. 1 root root 15 Apr 14 06:51 /lib/systemd/system/runlevel0.target -> poweroff.target
lrwxrwxrwx. 1 root root 13 Apr 14 06:51 /lib/systemd/system/runlevel1.target -> rescue.target
lrwxrwxrwx. 1 root root 17 Apr 14 06:51 /lib/systemd/system/runlevel2.target -> multi-user.target
lrwxrwxrwx. 1 root root 17 Apr 14 06:51 /lib/systemd/system/runlevel3.target -> multi-user.target
lrwxrwxrwx. 1 root root 17 Apr 14 06:51 /lib/systemd/system/runlevel4.target -> multi-user.target
lrwxrwxrwx. 1 root root 16 Apr 14 06:51 /lib/systemd/system/runlevel5.target -> graphical.target
lrwxrwxrwx. 1 root root 13 Apr 14 06:51 /lib/systemd/system/runlevel6.target -> reboot.target
```
-- or --
```
[tlemarte@Centos ~]$ systemctl list-units --type target -all
  UNIT                      LOAD      ACTIVE   SUB    DESCRIPTION
  basic.target              loaded    active   active Basic System
  cloud-config.target       loaded    active   active Cloud-config availability
  cloud-init.target         loaded    active   active Cloud-init target
  cryptsetup.target         loaded    active   active Local Encrypted Volumes
  emergency.target          loaded    inactive dead   Emergency Mode
● firewalld.target          not-found inactive dead   firewalld.target
  getty-pre.target          loaded    inactive dead   Login Prompts (Pre)
  getty.target              loaded    active   active Login Prompts
  graphical.target          loaded    inactive dead   Graphical Interface
  initrd-fs.target          loaded    inactive dead   Initrd File Systems
  initrd-root-device.target loaded    inactive dead   Initrd Root Device
  initrd-root-fs.target     loaded    inactive dead   Initrd Root File System
  initrd-switch-root.target loaded    inactive dead   Switch Root
  initrd.target             loaded    inactive dead   Initrd Default Target
  local-fs-pre.target       loaded    active   active Local File Systems (Pre)
  local-fs.target           loaded    active   active Local File Systems
  multi-user.target         loaded    active   active Multi-User System
  network-online.target     loaded    active   active Network is Online
  network-pre.target        loaded    active   active Network (Pre)
  network.target            loaded    active   active Network
  nss-lookup.target         loaded    active   active Host and Network Name Lookups
  nss-user-lookup.target    loaded    active   active User and Group Name Lookups
  paths.target              loaded    active   active Paths
  remote-fs-pre.target      loaded    inactive dead   Remote File Systems (Pre)
  remote-fs.target          loaded    active   active Remote File Systems
  rescue.target             loaded    inactive dead   Rescue Mode
  shutdown.target           loaded    inactive dead   Shutdown
  slices.target             loaded    active   active Slices
  sockets.target            loaded    active   active Sockets
  sshd-keygen.target        loaded    active   active sshd-keygen.target
  swap.target               loaded    active   active Swap
  sysinit.target            loaded    active   active System Initialization
● syslog.target             not-found inactive dead   syslog.target
  time-sync.target          loaded    inactive dead   System Time Synchronized
  timers.target             loaded    active   active Timers
  umount.target             loaded    inactive dead   Unmount All Filesystems

LOAD   = Reflects whether the unit definition was properly loaded.
ACTIVE = The high-level unit activation state, i.e. generalization of SUB.
SUB    = The low-level unit activation state, values depend on unit type.

36 loaded units listed.
To show all installed unit files use 'systemctl list-unit-files'.
```

* To set default boot target user 'systemctl set-default'. A reboot is required for changes to take effect.
```
[tlemarte@Centos ~]$ sudo systemctl set-default graphical.target
Removed /etc/systemd/system/default.target.
Created symlink /etc/systemd/system/default.target → /usr/lib/systemd/system/graphical.target.
```
* To immediatley change to a target, use 'systemctl isolate'.
```
[tlemarte@Centos ~]$ sudo systemctl isolate graphical.target
[tlemarte@Centos ~]$ sudo systemctl isolate multi-user.target
```
## Interrupt the boot process in order to gain access to a system
### Guide from [CertDepot](https://www.certdepot.net/rhel7-interrupt-boot-gain-access-system/)
 * At the beginning of the boot process, at the GRUB 2 menu, type the e key to edit.
 * Then, go to the kernel line (the line starting with linux16) and add the following statements at the end:
```
rd.break enforcing=0
```
 * Note: rd.break asks for a break at an early stage of the boot process. enforcing=0 puts the system into SELinux Permissive mode. Don’t confuse with selinux=0 that completely disables SELinux.
 * Press Ctrl x to resume the boot process.
 * Then, mount the /sysroot partition as read/write:
```
switch_root:/# mount –o remount,rw /sysroot
```
 * Execute the chroot command on the /sysroot partition:
```
switch_root:/# chroot /sysroot
```
 * Change the root password:
```
sh-4.2# passwd root
Changing password for user root.
New passwd: mypassword
Retype new password: mypassword
passwd: all authentication token updated successfully.
sh-4.2# exit
exit
switch_root:/# exit
logout
```
 * Connect to your server at the console (don’t reboot now!) with the root user and the new password:
```
...
[  OK  ] Started Network Manager Script Dispatcher Service.
[  OK  ] Started Crash recovery kernel arming.
[  OK  ] Reached target Multi-User System.

CentOS Linux 7 (Core)
Kernel 3.10.0-229.14.1.el7.x86_64 on an x86_64

vm login: root
Password: mypassword
```
* Then type:
```
# restorecon /etc/shadow
# reboot
```
* If you strictly follow this procedure, you don’t need to force a SELinux relabel (# touch /.autorelabel or # fixfiles onboot) or load the SELinux policy (# /usr/sbin/load_policy -i).
* For the RHCSA exam, you need to intensely practice this procedure.

## Identify CPU/memory intensive processes and kill processes
* To see usage of system resources from the command line, use "top" or "ps -edf"
```
[tlemarte@Centos ~]$ top
top - 21:11:43 up  4:52,  1 user,  load average: 0.00, 0.10, 0.07
Tasks: 147 total,   1 running, 146 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  6.2 sy,  0.0 ni, 93.8 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   3274.5 total,   2338.1 free,    258.4 used,    677.9 buff/cache
MiB Swap:      0.0 total,      0.0 free,      0.0 used.   2774.8 avail Mem

    PID USER      PR  NI    VIRT    RES    SHR S  %CPU  %MEM     TIME+ COMMAND
  11529 tlemarte  20   0   64512   4380   3748 R   6.2   0.1   0:00.01 top
      1 root      20   0  179868  14396   9368 S   0.0   0.4   0:03.19 systemd
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kthreadd
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp
      4 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_par_gp
      6 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 kworker/0:0H-kblockd
      9 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 mm_percpu_wq
     10 root      20   0       0      0      0 S   0.0   0.0   0:00.16 ksoftirqd/0
     11 root      20   0       0      0      0 I   0.0   0.0   0:00.30 rcu_sched
     12 root      rt   0       0      0      0 S   0.0   0.0   0:00.00 migration/0
     13 root      rt   0       0      0      0 S   0.0   0.0   0:00.00 watchdog/0
     14 root      20   0       0      0      0 S   0.0   0.0   0:00.00 cpuhp/0
     16 root      20   0       0      0      0 S   0.0   0.0   0:00.00 kdevtmpfs
     17 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 netns
     18 root      20   0       0      0      0 S   0.0   0.0   0:00.06 kauditd
     19 root      20   0       0      0      0 S   0.0   0.0   0:00.00 khungtaskd
     20 root      20   0       0      0      0 S   0.0   0.0   0:00.00 oom_reaper
```
--or--
```
[tlemarte@Centos ~]$ ps -edf
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 16:19 ?        00:00:03 /usr/lib/systemd/systemd --switched-root --system --deserialize 17
root           2       0  0 16:19 ?        00:00:00 [kthreadd]
root           3       2  0 16:19 ?        00:00:00 [rcu_gp]
root           4       2  0 16:19 ?        00:00:00 [rcu_par_gp]
root           6       2  0 16:19 ?        00:00:00 [kworker/0:0H-kblockd]
root           9       2  0 16:19 ?        00:00:00 [mm_percpu_wq]
root          10       2  0 16:19 ?        00:00:00 [ksoftirqd/0]
root          11       2  0 16:19 ?        00:00:00 [rcu_sched]
root          12       2  0 16:19 ?        00:00:00 [migration/0]
root          13       2  0 16:19 ?        00:00:00 [watchdog/0]
root          14       2  0 16:19 ?        00:00:00 [cpuhp/0]
```
* you can use 'nice' to run a program with modified scheduling priority. 
```
[tlemarte@Centos ~]$ nice -n 10 ./script.sh
```
* To change the priority (here +5) of an already running process, get its PID (Process ID) through top or ps (here 1234) and type:
```
[tlemarte@Centos ~]$ renice +5 1234
```
--or--
```shell
[tlemarte@Centos ~]$ renice +5 $(pgrep script.sh)
```
* To kill a process, use 'kill'. Alternativley, you can use 'pkill' or 'pgrep' to find and terminate a process from other criteria 
```
[tlemarte@Centos ~]$ kill 1234
```
--or--
```
[tlemarte@Centos ~]$ pkill script.sh
```
## Adjust process scheduling

## Manage tuning profiles

## Locate and interpret system log files and journals

## Preserve system journals

## Start, stop, and check the status of network services

## Securely transfer files between systems
*  You can use 'scp' to securely copy files and directories between systems. it uses similar syntax as SSH to sent the files. 
```
[tlemarte@Centos ~]$ scp file.txt remote_username@10.10.0.2:/remote/directory/newfilename.txt
[tlemarte@Centos ~]$ scp remote_username@10.10.0.2:/remote/file.txt /local/directory
[tlemarte@Centos ~]$ scp user1@host1.com:/files/file.txt user2@host2.com:/files
```
# Configure local storage

## List, create, delete partitions on MBR and GPT disks

## Create and remove physical volumes

## Assign physical volumes to volume groups

## Create and delete logical volumes

## Configure systems to mount file systems at boot by universally unique ID (UUID) or label

## Add new partitions and logical volumes, and swap to a system non-destructively

# Create and configure file systems

## Create, mount, unmount, and use vfat, ext4, and xfs file systems

## Mount and unmount network file systems using NFS

## Extend existing logical volumes

## Create and configure set-GID directories for collaboration

## Configure disk compression

## Manage layered storage

## Diagnose and correct file permission problems

# Deploy, configure, and maintain systems

## Schedule tasks using at and cron

## Start and stop services and configure services to start automatically at boot

## Configure systems to boot into a specific target automatically

## Configure time service clients

## Install and update software packages from Red Hat Network, a remote repository, or from the local file system

## Work with package module streams

## Modify the system bootloader

# Manage basic networking

## Configure IPv4 and IPv6 addresses

## Configure hostname resolution

## Configure network services to start automatically at boot

## Restrict network access using firewall-cmd/firewall

# Manage users and groups

## Create, delete, and modify local user accounts

## Change passwords and adjust password aging for local user accounts

## Create, delete, and modify local groups and group memberships

## Configure superuser access

# Manage security

## Configure firewall settings using firewall-cmd/firewalld

## Create and use file access control lists

## Configure key-based authentication for SSH

## Set enforcing and permissive modes for SELinux

## List and identify SELinux file and process context

## Restore default file contexts

## Use boolean settings to modify system SELinux settings

## Diagnose and address routine SELinux policy violations

# Manage containers

## Find and retrieve container images from a remote registry

## Inspect container images

## Perform container management using commands such as podman and skopeo

## Perform basic container management such as running, starting, stopping, and listing running containers

## Run a service inside a container

## Configure a container to start automatically as a systemd service

## Attach persistent storage to a container

###### As with all Red Hat performance-based exams, configurations must persist after reboot without intervention.