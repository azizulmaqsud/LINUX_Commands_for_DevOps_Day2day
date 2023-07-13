# LINUX Day2Day Job Description for a DevOps Engineer [Very Imoprtant]

## Manually set up a repository

create a .repo file within /etc/yum.repos.d using a text editor. Here I am creating the repository file for MySQL 5.7

Step1 :
- cd /etc/yum.repos.d/

Step2: 
- vim mysql57-community.repo
- [mysql57-community]
- name=MySQL 5.7 Community Server
- baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
- enabled=1
- gpgcheck=1
- gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

Step4:
- yum install mysql

## YUM CONFIGURTION

- YUM stands for Yellow Dog Updater Manager.
- Yum is the default package management utility in RHEL/Centos.
- Yum uses repository to get the necessary rpm files.
- A repository is collection of rpm files.
- Repository may contain multiple versions of the same RPM package.
- Repository may contain different builds for different architectures for example one for i686 and other for x86_64.
- A repository can be configured locally or remotely.

Repository configuration files are stored in /etc/yum.repos.d/ directory with an extension .repo

vi /etc/yum.repos.d/example.repo

- [examplerepo]			             // label of the repository
- name=examplerepo		           // name of the repository.
- baseurl=file:///rhcelab/repo	 // define the location of rpm files.
- enabled=1	                     // value = 1 means enabled. or, 0 means the repository is disabled.
- gpgcheck=0	                   // value = 1 means integrity will be checked. or, 0 means integrity will not be checked.
- :wq!	                         // save and quit from the file.


# CONFIGURE IP NETWORKING WITH NMCLI

- yum install NetworkManager
- systemctl start NetworkManager
- systemctl enable NetworkManager  (make sure it is up 24/7)
- nmcli connection show --active
- nmcli device status
- nmcli connection add type ethernet ifname eth0
- nmcli connection show 
- nmcli connection up ethernet-eth0
- nmcli connection show --active

To Change the Linux system IP ADDRESS:
- nmcli connection modify ethernet-eth0 ipv4.address 172.31.16.0/24
- nmcli connection modify ethernet-eth0 ipv4.method manual

To modify IPV4 Gateway:
- nmcli connection modify ethernet-eth0 ipv4.gateway “172.31.16.2”

To modify the DNS:
- nmcli connection modify ethernet-eth0 ipv4.dns “172.31.16.101”

# Reset Root Password in Linux CentOS:

- Boot to Recovery Mode. When system restarts, press “ESC” key to interrupt the boot process. 
- Select the kernel from the GRUB/Boot menu; press the arrow keys.
- Pressing ‘e’ from your keyboard will open the editing menu.
- In the editing menu, locate the “ro” kernel parameter and replace it with “rw”. 
- Add an additional parameter “init=/sysroot/bin/sh”
- Press Ctrl + X to enter into single-user mode 
- Run the “chroot /sysroot” command to convert the root file to read and write mode
- Set a new password for root, command: passwd root (Changing the password for user root)
- SELinux relabelling (to set the permissions for files or folders)
- Exit from terminal
- Hit reboot

# Create a .tar backup
- tar -cvf backup.tar finaldraft.sh
- tar -czf backup.tar.gz finaldraft.sh 
- tar -xvfz backup.tar.gz

Exclude files when creating a tar backup
- tar --exclude file.txt --exclude file.sh -cvfz backup.tar.gz

Extract content from a tar (.gz) backup
- tar -xvfz backup.tar.gz

Here:
- c -> Create the archive
- v -> Show the process verbosely
- f -> Name the archive
- x -> Extract the content
- z -> Compressed gzip archive file

# Tunning Linux system
Profile tuned are divided into power-saving profiles, and performance-boosting profiles. 
The performance-boosting profiles include profiles focus on the following aspects:
- low latency for storage and network
- high throughput for storage and network
- virtual machine performance
- virtualization host performance

TuneD recommends the most suitable profile for your system, commands:
- yum install tuned
- systemctl enable --now tuned
- yum install tuned-profiles-realtime 
- tuned-adm active  (Verify that a TuneD profile is active and applied)
- tuned-adm verify
- tuned-adm recommend
- tuned-adm profile powersave

# Create Group
- useradd   //adds accounts
- usermod   //modifies accounts
- userdel   //deletes accounts
- groupadd  //adds groups
- groupmod  //modifies groups
- groupdel  //deletes groups
- groupadd [-g gid [-o]] [-r] [-f] groupname

# Create Account

useradd -d /home/example/ -g testers -s /bin/bash -m example

# SELinux [Security Enhanced Linux]
It is an access control system built into the Linux kernel. It enforces resource policies according to the level of user access, programs, and services.

SELINUX=enforcing|permissive|disabled   //It Defines the top-level state of SELinux on a system.

- enforcing — The SELinux security policy is enforced.
- permissive — The SELinux system prints warnings but does not enforce the policy.
- disabled — SELinux is fully disabled. SELinux hooks are disengaged from the kernel and the pseudo-file system is unregistered.

SELINUXTYPE=targeted|strict    //It Specifies which policy SELinux should enforce.
- targeted — Only targeted network daemons are protected.
- strict — Full SELinux protection, for all daemons. Security contexts are defined for all subjects and objects, and every action is processed by the policy enforcement server.

To check SELinux:
- sestatus  
- cat /etc/selinux/config

To Change SELinux Mode:
- vi /etc/sysconfig/selinux
- SELINUX=enforcing
- SELINUXTYPE=targeted
- :wq!

# Create Cron Job
Example: send an email every Monday at 5 pm, how to exec in a cron job in Linux?

Enter the command to edit the crontab file:

crontab -e

In the crontab file, add the following line to schedule the cron job:

0 17 * * MON /sh/opt/script.sh

Save the crontab file and exit the text editor.


# SWAP PARTITION 
For Prioritization and Hibernation, and if your RAM fills up completely, then AWAP Partition comes in the picture!

1st way:

Use fdisk command to create a disk partition of type 82 (Linux swap) 

Initialize the partition (for example, /dev/sda2) as a swap partition:

mkswap /dev/sda2

Enable swapping to the swap partition:

swapon /swapfile

Add an entry to /etc/fstab for the swap partition so that the system uses it following the next reboot:

/dev/sda2       swap       swap       defaults       0 0

2nd way:

Create a swap partition fdisk /dev/vda

Press n              [for new ]

+512 M

t                    [type for SWAP partition number is 82]

:wq! 		               [quit]

partprobe /dev/vda3  [To hit the kernel about new partition]

mkswap /dev/vda3     [To use the swap partition] 

mount –a             [To check errors]

vi /etc/fstab        [For permanent mount]

/dev/vda3 swap swap 

swapon -s

df -h                [Recheck file]

# Logical Volume Management (LV)
It combines multiple individual hard drives and/or disk partitions into a single volume group (VG). 
New VG can be subdivided into logical volumes (LV) or used as a single large volume.

fdisk -l  [to see mapper]

Steps:

Install a new hard disk drive

Designate Physical Volumes

pvcreate /dev/vda1

Manage Volume Groups

vgcreate vgname /dev/vda1

Manage Logical Volumes, VG can be subdivided into one or more LVs

lvcreate -L size -n lvname vgname  [size maybe anything like 500MB..]

Logical volumes- 8e

fdisk /dev/vda

n

+500M

t ->8e

:wq!

Reboot the VM

pvcreate /dev/vdb

vgcreate –s 16 test /dev/vdb

lvcreate –l 30 –n new test

Mkdir /mnt/storage

MAKE VFAT FILE SYSTEM:

A virtual file allocation table (VFAT) is an extension to the file allocation table (FAT) from Windows 95 and onward for creating, storing and managing files with long names.

Mkfs.vfat /dev/test/new

Vim /etc/fstab

/dev/test/new /mnt/storage vfat

/dev/test/new


# Thank YOU!
# Stay CONNECTED !!
