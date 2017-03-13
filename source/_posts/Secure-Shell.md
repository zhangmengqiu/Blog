title: Secure Shell
author: Feng Tao
tags:
  - Computer Network
  - Useful Tool
  - Information Security
categories: []
date: 2016-12-14 10:04:00
---
### install

packages on <a href="http://mirror.centos.org/centos/7/os/x86_64/Packages/">centos 7 mirror</a>
packages on <a href="http://mirror.centos.org/centos/6/os/x86_64/Packages/">centos 6 mirror</a>

#### server
on ubuntu 
```bash
sudo apt -y update
sudo apt install openssh-server
```
optional
```bash
service sshd start
```
on centos
```bash
sudo yum install openssh-server
sudo chkconfig --list sshd
```
start service
```bash
sudo systemctl sshd start 
```
or 
```bash
/etc/init.d/sshd start
```

<!-- more -->

#### client
on ubuntu
```bash
sudo apt -y update
sudo apt install openssh-client
```
on centos
```bash
wget http://mirror.centos.org/centos/7/os/x86_64/Packages/openssh-clients-6.6.1p1-22.el7.x86_64.rpm
rpm -ivh openssh-clients-6.6.1p1-22.el7.x86_64.rpm
```
### ssh-keygen
```bash
	ssh-keygen -t rsa -b 2048 -P '' -f ~/.ssh/id_rsa
```
### ssh

##### login
```bash
ssh USER@HOST -p PORT
ssh HOST -l USER -p PORT
```
##### command without login
```bash
ssh -N USER@HOST COMMAND
ssh USER@HOST $(< cmd.txt)
ssh USER@HOST "cat cmd.txt"
```
##### copy id
```bash
cat .ssh/id_rsa.pub | ssh USER@HOST "cat >>.ssh/authorized_keys"
```
##### file transfer

using tar compression

local -> remote
```bash
tar -zcvf - source/ | ssh USER@HOST "tar -zxvf - -C /destination"
```
local <- remote
```bash
tar -zxvf - -C destination/ < <(ssh USER@HOST "tar -zcvf - /source")
```
for large file

local -> remote
```bash
rsync -partial -progress -rsh=ssh FILE_SOURCE USER@HOST:DESTINATION_FILE
```
remote -> local
```bash
rsync -partial -progress -rsh=ssh USER@HOST:REMOTE_FILE DESTINATION_FILE
```

##### port forwording

###### local

localhost:3000 -> google.com:80
```bash
ssh -nNT -L 3000:google.com:80 USER@localhost
```
localhost:3000 -> SERVER:80
```bash
ssh -nNT -L 3000:localhost:80 USER@SERVER	
```
localhost:3000 -> HOST:22
```bash
ssh -CfNg -L 3000:localhost:22 USR@HOST
```
localhost:3000 -> HOSTA -> HOSTB:22
```bash
ssh -CfNg -L 3000:HOSTB:22 USER@HOSTA
```

###### remote

localhost:22 <- HOST:3000
```bash
ssh -nNT -R 3000:localhost:22 USER@HOST
```
HOSTB:22 <- HOSTA:3000
```bash
ssh -CfNg -R 3000:HOSTB:22 USER@HOSTA
```
###### dynamic

proxy on localhost:3000
localhost:PORT -> HOST:PORT
```bash
ssh -CfNg -D 3000 USER@HOST
```
##### jump A to B
```bash
ssh -t USER1@REACHABLE_HOST ssh USER2@UNREACHABLE_HOST
```
##### screen
```bash
ssh -t USER@HOST screen -r
ssh -t USER@HOST screen -xRR
```
##### mysql db tansfer
```bash
mysqldump --add-drop-table -extended-insert -force -log-error=error.log -uUSER -pPASS OLD_DB_NAME | ssh -C USER@HOST "mysql -uUSER -pPASS NEW_DB_NAME"
```
##### bandwidth moniter
```bash
yes|pv|ssh USER@HOST "cat>/dev/null"
```
##### network moniter
```bash
ssh ROOT@HOST 'tshark -f "port !22" -w -' |wireshark -k -i -
```
tshart can be replaced by tcpdump
```bash
ssh ROOT@HOST tcpdump -w - 'port !22' |wireshark -k -i -
```
##### quiker, more stable and stronger client
```bash
ssh -4 -C -c blowfish-cbc
```
### scp 
```bash
scp CompressionLevel=9 USER@HOST:DIR/SOURCE/FILE .
scp -C -o -r USER@HOST:/SOURCE/DIR .
scp /FROM/FILE USER@HOST:/TO/FILE
scp -r USER1@HOST1:/SOURCE/DIR1 USER2@HOST2:/TO/DIR2
```

### sshfs
```bash
sshfs USER@HOST:/path/to/folder /path/to/mount/point
```