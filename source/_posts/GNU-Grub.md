title: Linux Cheat Sheet
author: Feng Tao
tags:
  - Note
categories: []
date: 2017-03-03 15:52:00
---
### Configuring GRUB 2


#### Editing the File

The grub file is a system file, therefore any editing must be done by a user with `Administrator/root` privileges. The file is a simple text file and can be edited by any text editor. The default text editor in Ubuntu is Gedit, and the file can be edited with the following command. `gksu` is the graphical equivalent of `sudo` and the `&` allows the terminal to be used to update GRUB 2 once the user saves the file.
```shell
gksu gedit /etc/default/grub &
```
After making changes and saving the file, the GRUB 2 menu must be updated to include the changes by running:
```shell
sudo update-grub
```

<!--more-->

#### Specific Entries
`/etc/default/grub`  [GRUB menu settings]

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="rd.lvm.lv=centos/swap vconsole.font=latarcyrheb-sun16 rd.lvm.lv=centos/root crashkernel=auto vconsole.keymap=us rhgb quiet"
GRUB_DISABLE_RECOVERY="true"

#sed -e '/^#/d' -e '/^$/d' /etc/default/grub
GRUB_DEFAULT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=2
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT=""
GRUB_CMDLINE_LINUX=""
```

### Setting a GRUB password
The command to create a grub password is
`grub-md5-crypt`
```shell
[root@workstation1 ~]# grub-md5-crypt 
Password:
Retype password:
$1$2c4ol/$gMHLZBZZqJT8oRPpiShkf/
```
Once you have the md5 hash of the password you need to copy it and insert it into the grub.conf file The format of the line is below (put this before the “default=“ line)

`password --md5 results_of_grub_md_5_command`
```
[root@localhost yum.repos.d]# cat /boot/grub/grub.conf
...
password --md5 $1$2c4ol/$gMHLZBZZqJT8oRPpiShkf/
...
```
### Getting into a system without the root or GRUB password
Insert your Linux Install disk in the CDROM drive
2. Enter the computer’s (VM) BIOS, then configure the computer to boot to the CDROM drive first
3. When you get the boot menu choose `Rescue installed system`
4. When asked What language would you like to use during the installation process?, choose
`English`
5. When asked "What type of keyboard do you have" choose `us`
6. When asked “what type of media contains the rescue image”, choose `Local CD/DVD`
7. When asked if you want to start networking interfaces on this system, choose `No`
8. When asked "The rescue environment will now attempt to fine your Linux installation and
mount it under the directory `/mnt/sysimage...` choose `Continue`
9. When prompted that "Your system has been mounted under /mnt/sysimage" choose `OK`
10. When presented for a list of 3 options, choose `shell Start shell`, and choose `OK`
11. You should now be at a shell prompt#, type `chroot /mnt/sysimage`
12. Reset the password with the command
`passwd`
the system will prompt you to choose a new password and type it twice
13. now use vi or nano to edit the file `/boot/grub/grub.conf`
a. remove the line starting `password --md5 ....`
b. save the file
14. reboot your computer, notice there is no longer a GRUB password and you have reset the root password and can log in again.
15. Type `exit` twice, this will return you to the menu of “shell, fakd or reboot”,
16. Choose `reboot` and click `OK`

### Repairing a broken MBR
Insert your Linux Install disk in the CDROM drive
2. Enter the computer’s (VM) BIOS, then configure the computer to boot to the CDROM drive first
3. When you get the boot menu choose `Rescue installed system`
4. When asked "What language would you like to use during the installation process?, choose
`English`
5. When asked "What type of keyboard do you have" choose `us`
6. When asked “what type of media contains the rescue image”, choose `Local CD/DVD`
7. When asked if you want to start networking interfaces on this system, choose `No`
8. When asked "The rescue environment will now attempt to fine your Linux installation and
mount it under the directory `/mnt/sysimage...` choose `Continue`
9. When prompted that "Your system has been mounted under /mnt/sysimage" choose OK
10. When presented for a list of 3 options, choose `shell Start shell`, and choose `OK`
11. You should now be at a shell prompt#, type chroot `/mnt/sysimage`
12. Type
`grub`
This will put you in interactive grub mode. Follow the rest of the session below (the commands you type are highlighted AND bold)
```shell
GNU GRUB version 0.97 (640K lower / 3072K upper memory)
[ Minimal BASH-like line editing is supported. For the first word, TAB
lists possible command completions. Anywhere else TAB lists the possible completions of a device/filename.]
grub> root (hd0,0)
File system type is ext2fs, partition type 0x83
grub> setup (hd0)
Checking if "/boot/grub/stage1" exists... no
Checking if "/grub/stage1" exists... yes
Checking if "/grub/stage2" exists... yes
Checking if "/grub/e2fs_stage1_5" exists... yes
Running "embed /grub/e2fs_stage1_5 (hd0)"... 15 sectors are embedded.
succeeded
Running "install /grub/stage1 (hd0) (hd0)1+15 p (hd0,0)/grub/stage2 /grub/grub
.conf"... succeeded Done.
grub>
quit
```
type `exit` to exit your chroot shell
14. type exit again to reboot
15. Choose `reboot` from the menu and click `OK`

IMPORTANT this assumes your `/boot` partition is the first partition on the first hard drive (/dev/sda1) which is almost always is. If for some reasons it’s NOT you have to modify your `root(hd0,0)` line

### GRUB 2: HEAL YOUR BOOTLOADER
The next few commands work with both `grub>` and `grub rescue>`. The `set pager=1` command invokes the pager, which prevents text from scrolling off the screen. You can also use the `ls` command which lists all partitions that Grub sees, like this:
```grub
grub> ls
(hd0) (hd0,msdos5) (hd0,msdos6) (hd1,msdos1)
grub> ls (hd0,5)/
lost+found/ var/ etc/ media/ bin/ initrd.gz
boot/ dev/ home/ selinux/ srv/ tmp/ vmlinuz
```
 If you have multiple partitions, read the contents of the `/etc/issue` file with the cat command to identify the distro,  such as `cat (hd0,5)/etc/issue`.
Assuming you  nd the root  lesystem you’re looking for inside `(hd0,5)`, make sure that it contains the `/boot/grub` directory and the Linux kernel image you wish to boot into, such as `vmlinuz-3.13.0-24-generic`. Now type the following:
```grub
grub> set root=(hd0,5)
grub> linux /boot/vmlinuz-3.13.0-24-generic root=/dev/sda5
grub> initrd /boot/initrd.img-3.13.0-24-generic
```
Once you’ve keyed these in, type `boot` at the next `grub>` prompt and Grub will boot into the specied operating system.
Things are a little different if you’re at the `grub rescue>` prompt. Since the bootloader hasn’t been able to  nd and load any of the required modules, you’ll have to insert them manually:
```grub
grub rescue> set root=(hd0,5)
grub rescue> insmod (hd0,5)/boot/grub/normal.mod
grub rescue> normal
grub> insmod linux
```
Once this module has been loaded you can proceed to point the boot loader to the kernel image and initrd files just as before and round off the procedure with the boot command to bring up the distro.
Once you’ve successfully booted into the distro, don’t forget to regenerate a new configuration  file for Grub with the
```shell
grub-mkconfig -o /boot/grub/grub.cfg
```
command. You’ll also have to install a copy of the bootloader into the MBR with the
```shell
sudo grub2-install /dev/sda
```
command.

if you lose the Grub 2 bootloader you can restore Grub within a few steps with the help of a live distro. Assuming
 you’ve installed a distro on /dev/sda5, you can reinstall Grub by  rst creating a mount directory for the distro with
```shell
sudo mkdir -p /mnt/distro
```
and then mounting the partition with
```shell
mount /dev/sda5 /mnt/distro
```
You can then reinstall Grub with
```shell
grub2-install --root-directory=/mnt/distro /dev/sda
```
This command will rewrite the MBR information on the `/dev/sda` device, point to the current Linux installation and rewrite some Grub 2  files such as `grubenv` and `device.map`.
[More Reference](https://www.linuxvoice.com/issues/010/grub.pdf)

### Creating a partition and formatting it with an ext4 file system
To create a partition and format it with the ext4 file system
1. fdisk /dev/disk_specifier ex. `fdisk /dev/sda`
2. type `p` to print out the partition table, note the LAST partition identifier
3. type `n` to create a NEW partition
4. when prompted for a start cylinder, just hit the return key
5. when prompted for the end cylinder, type
`+100M` (to create a 100M partition, a 1G partition would be `+1G`)
6. type `p` to print the new partition table, note the /dev/xxx identifier of the new partition... you
need this later. write it here ________________________
7. type `w` to write your change and exit fdisk
8. type partprobe to inform the kernel of the new partitions
9. create a new file system with the command
`mkfs -t ext4 /dev/xxx` (where /dev/xxx is the partition you created and listed in step 6)
10. create a "mount point" where you want the directory to be grafted onto the file system tree
`mkdir /mnt/mynewpartition`
11. mount the file system
`mount /dev/xxx /mnt/mynewpartition`
12. verify it's mounted with
`df -h`

Remember to have it automatically mount on reboots, you need to edit `/etc/fstab` and add a new line to reference the new partitions. If the for example if our new partition is `/dev/sda7` and we want to mount it as `/mnt/mynewpartition` we could type the following command to add it to the system

```shell
[root@workstation1 ~]# echo
"/dev/sda7 /mnt/mynewpartition
ext4 defaults 1 2" >> /etc/fstab
```

### Adding a swap file

Creating a swap file
1. use dd to create a empty space
`dd if=/dev/zero of=new_swap_file ibs=1M count=size_in_megabytes`
2. use mkswap to initialize the empty space as swap
`mkswap new_swap_file`
3. Protect the swap space with chmod (this is VERY important from a security standpoint)
`chmod 700 new_swap_file`
4. Add Entry to /etc/fstab for new swap file
`/bin/echo “new_swap_file swap swap defaults 0 0” >> /etc/fstab`
5. Add new swap file immediately to system
`swapon –a`
6. Verify swap file is active with
`swapon –s`

### Creating a LUKS encrypted file system
Create a partition as normal, for the purpose of this cheat sheet, we will call it `/dev/sdb1`
2. Determine a “/dev/mapper name”. For the purpose of this class we will call it encrypted_data
3. Create a mount point, for the purpose of this cheat sheet we will call it `/usr/mysecretdata`
`mkdir /usr/mysecretdata`
4. Create random data on the partition you just created (this is optional and it can take a LONG LONG LONG time. However it is very good from a security standpoint)
`dd if=/dev/urandom of=/dev/sdb1`
5. Initialize the partition for encryption cryptsetup `luksFormat /dev/sdb1`
6. Tell the encryption software to start using encryption on the partition, and create a special encrypted block device on the underlying physical partition.
`cryptsetup luksOpen /dev/sdb1 encrypted_data`
7. Create a Linux usable file system on the special encrypted block device 
`mkfs –t ext4 /dev/mapper/encrypted_data`
8. Add the following line to /etc/fstab so your disk will automatically mount at system startup.
`/dev/mapper/encrypted_data /usr/mysecretdata ext4 defaults 1 2`
9. Inform the system to create the special encrypted block device on system startup. Edit (create if necessary) `/etc/crypttab`, add the following line
`encrypted_data /dev/sdb1 none`
10. Mount the disk, so you can immediately use it without rebooting (or reboot if you prefer)
`mount /dev/mapper/encrypted_data /usr/mysecretdata`
or
`reboot`

### Connecting to NFS resources
1. Make sure netfs is chkconfig’ed on (this is needed to automatically mount NFS shares on reboot 
`chkconfig --list netfs`
```shell
[root@workstation1 ~]# chkconfig --list netfs
netfs 0:off 1:off 2:off 3:on 4:on 5:on 6:off
```
2. Use showmount to determine what resources are on a server `showmount –e ip_or_hostname_of_remote_server`
```shell
[root@workstation1 ~]# showmount -e 192.168.2.186 Export list for 192.168.2.186:
/shared *
/nfshome *
```
3. Make a directory to use as the “graft point”
`mkdir /mnt/a`
Mount one of the remote file systems
`mount remote_server:remote_path mount_point`
```shell
[root@workstation1 ~]# mount 192.168.2.186:/shared /mnt/a
```
4. Verify the remote file system is present on your system now.
`df –h`
5. Add a line to `/etc/fstab` so it always mounts on system boot.
```shell
 [root@workstation1 ~]# echo "192.168.2.186:/shared /mnt/a nfs defaults 0
```

### Joining an NIS (YP) domain
NIS is a system that allows you to share “user accounts” and other information across a network. NIS was highly used in Unix/Linux installation for the centralized user management features.
To join an NIS domain you must first have the following information
 NIS domain name
 Server IP (optional)
The Steps are
1. make sure ypbind is set to run in run level 3 and 5 `chkconfig --level 35 ypbind on`
2. run system-config-authentication
3. Choose “NIS” from “User Account Database selector”
4. enter your NIS Domain Name
5. enter your Servers IP address
6. click `Apply`
7. close out of system-config-authentication
You should now be able to access network accounts via NIS. One useful command to verify that NIS is working is
ypcat passwd which reads the password file from NIS
You also need to make sure your users home directories are available on the system... this is usually done with NFS.

### Creating a YUM repository (using HTTP)
Creating a YUM repo (http) 
1. Setup a web server
`yum install httpd`
`service httpd start` 
`chkconfig --level 35 httpd on`
```shell
[root@workstation1 ~]# yum install httpd
...
[root@localhost ~]# service httpd start
Starting httpd:
[root@localhost ~]# chkconfig --level 35 httpd on
```
2. Install the createrepo package 
```shell
yum –q –y install createrepo
```
3. Copy the rpm files to `/var/www/html` (from the CentOS 5.6 CDROM in this case, make sure the CDROM is inserted into the computer or the VMware instance)
```shell
[root@workstation1 ~]# mkdir /var/www/html/myrepo
[root@workstation1 ~]# cp -rp /media/CentOS_6.2_Final/Packages/* /var/www/html/myrepo
```
Note the CentOS 6.2 distribution has 2 DVDs so you’ll need to copy the Packages directory from EACH DVD into `/var/www/html/myrepo`
```shell
[root@workstation1 myrepo]# umount /media/CentOS_6.2_Final/
(eject cdrom from vmware, load CentOS disk 2)
[root@workstation1 myrepo]# cp -rp /media/CentOS_6.2_Final_/Packages/* /var/www/html/myrepo/ (all one line)
cp: overwrite `/var/www/html/myrepo/TRANS.TBL'? y
```
4. Create the rpm listing
```shell
[root@workstation1]# createrepo /var/www/html/myrepo
```
5. Optional step: create group lists (list of related packages to install at one time (ie to use with yum groupinstall or yum grouplist). To do this you need to create an xml file to descript which file is in which packages. In this case we’ll copy the group list that’s provided on the CentOS 6.2 install. (you will need to re-insert and re-mount CentOS DVD #1)
```shell
[root@workstation1 ~]# cd /var/www/html/myrepo/
[root@workstation1 myrepo]# cp /media/CentOS_6.2_Final_/repodata/*comps.xml . [root@workstation1 myrepo]# createrepo -g *comps.xml .
(lines of data will scroll on the screen)
```
6. Now you can use your repo. On your clients you need to create a repo file in `/etc/yum.repos.d` Using your favorite editor create a file called `/etc/yum.repos.d/myrepo.repo`
```
[myrepo]
name=CentOS-$releasever - Base baseurl=http://your_servers_IP/myrepo gpgcheck=1
enabled=1
```
7. Clear your yum cache
```shell
[root@workstation1 ~]# yum clean all
```
8. View your new yum repo
```shell
[root@workstation1 ~]# yum repolist
```
9. View the items in your yum repo
```shell
[root@workstation1 ~]# yum list |grep myrepo
```