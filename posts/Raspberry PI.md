---
title: Raspberry PI
date: 2016-10-01 22:58:01 
categories: Linux
tags: Linux

---

## 无线网卡配置 ##

修改`/etc/network/interfaces`文件

### 静态IP ###

`# sudo vi /etc/network/interfaces`

<pre>
auto lo

iface lo inet loopback
iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet static
wpa-ssid wifi-name #你要连接的wifi ssid 
wpa-psk wifi-pwd #你的wpa连接密码 
address 192.169.1.XXX #设定的静态IP地址 
netmask 255.255.255.0 #网络掩码 
gateway 192.168.1.1 #网关 
network 192.168.1.1 #网络地址
#wpa-roam /etc/wpa-supplicant/wpa_supplicant.conf
iface default inet dhcp
</pre>

保存退出,然后
`# sudo reboot`


### 动态IP ###

`# sudo vi /etc/network/interfaces`

<pre>
allow-hotplug wlan0
iface wlan0 inet manual
wpa-essid "wifi-name"
wpa-psk "wifi-pwd"
</pre>

保存退出,然后
`# sudo reboot`

----------

## 数据库(mysql5) ##

- 安装之前可以先更新下本机软件  
`# sudo apt-get update`

- 查看是否安装mysql服务  
`dpkg -l|grep MySQL`
注意：在2016-02-26-raspbian-jessie这个版本中：系统安装了mysql和java
在2015-05-05-raspbian-wheezy这个版本中：系统没有安装mysql、java

- 卸载mysql  
`apt-get remove 软件名称（例如：apt-get remove  mysql-server）`
一开始安装的操作系统是raspbian-jessie，故需要将mysql卸载掉，如果安装的是2015-05-05-raspbian-wheezy系统，跳过此步骤


- 安装mysql 
`# sudo apt-get install mysql-server`  
然后就是等待，在安装的过程中会提示你让你输入mysql的root密码，会提示两次。输入完成之后就会安装成功了,然后输入如下命令进入mysql：  
`mysql -u root -p`

- 开启mysql远程访问  
 - 修改/etc/mysql/my.cnf文件,找到下面这行，并用#注释掉:  
bind-address        = 127.0.0.1  
或者修改为bind-address        = 0.0.0.0  
 - 登录mysql，输入下面命令  
`mysql>grant all privileges on *.* to username@"%" identified by "password";（username一般是root，password是新的密码）`  
`mysql> FLUSH PRIVILEGES;`

- 开启3306端口远程访问
这里的iptable命令和centos中命令不一样，所以参考了一下文档，使用 ufw软件来开启3306端口
 - 安装ufw  
`apt-get install ufw`
 - 启用ufw  
`ufw enable`  
`ufw default deny`
 - 开启3306、22（ssh端口）端口  
`ufw allow 3306`  
`ufw allow 22`  
注意：如果不开启22端口，下次启动树莓派时，系统的22端口会禁用，不能使用ssh登录树莓派

- 重新启动系统  
为了使得配置参数生效，这里选择重新启动操作系统，使用`# sudo reboot`这个命令