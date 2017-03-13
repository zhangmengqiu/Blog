title: 用命令行连接WIFI
author: Feng Tao
tags:
  - Computer Network
  - Useful Tool
categories: []
date: 2016-12-14 09:49:00
---
### wpa_supplicant
这里使用的命令行工具是wpa_supplicant，可以用来在命令行界面连接WIFI。

使用iwlist扫描无线网络
```shell
iwlist scan
```

### configure

Using template wpa_supplicant.conf, replace the pass

```bash
wpa_passphrase SSID PASS > ~/wpa_pass.conf
```
check the net interface card
```bash
iwconfig
wpa_supplicant -i wlan0 -D wext -c /etc/wpa_supplicant/wpa_supplicant.conf
dhclient wlan0
```
<!-- more -->

Link a shortcut

```bash
vim ~/.bashrc
```

Add follow command in the end

```bash
alias wifi='sudo wpa_supplicant -B -i wlp2s0 -D wext -c /etc/wpa_supplicant/wpa_supplicant.conf && sudo dhclient wlp2s0'
# reload .bashrc
source ~/.bashrc
```
	
### Sample of Config 

```config
update_config=1
network={
  ssid="dddd"
  scan_ssid=1
  #psk="23456789"
  psk=9a2b010602a92b62fe766450f5c66ebb05171954f5282c288e898dbc0c6c63b7
  proto=RSN
  key_mgmt=WPA-PSK
  pairwise=CCMP
  auth_alg=OPEN
}
```