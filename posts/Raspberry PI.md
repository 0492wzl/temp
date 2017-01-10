
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

### 查看是否安装mysql服务 ###
`dpkg -l|grep MySQL`
注意：在2016-02-26-raspbian-jessie这个版本中：系统安装了mysql和java
在2015-05-05-raspbian-wheezy这个版本中：系统没有安装mysql、java

### 卸载mysql ###
`apt-get remove 软件名称（例如：apt-get remove  mysql-server）`
一开始安装的操作系统是raspbian-jessie，故需要将mysql卸载掉，如果安装的是2015-05-05-raspbian-wheezy系统，跳过此步骤

### 安装mysql ###
`# sudo apt-get install mysql-server`  
然后就是等待，在安装的过程中会提示你让你输入mysql的root密码，会提示两次。输入完成之后就会安装成功了,然后输入如下命令进入mysql：  
`mysql -u root -p`

### 开启mysql远程访问 ###
- 修改/etc/mysql/my.cnf文件,找到下面这行，并用#注释掉:  
bind-address        = 127.0.0.1  
或者修改为bind-address        = 0.0.0.0  
- 登录mysql，输入下面命令  
`mysql>grant all privileges on *.* to username@"%" identified by "password";（username一般是root，password是新的密码）`  
`mysql> FLUSH PRIVILEGES;`

- 重新启动系统  
为了使得配置参数生效，这里选择重新启动操作系统，使用`# sudo reboot`这个命令

----------

## 防火墙设置ufw ##

### 安装方法 ###

`sudo apt-get install ufw`

### 使用方法 ###

- 启用
`sudo ufw enable`
`sudo ufw default deny`
作用：开启了防火墙并岁系统启动，同时关闭所有外部对本机的访问（本机访问外部正常）

- 关闭
`sudo ufw disable`

- 查看防火墙状态
`sudo ufw status`

- 开启/禁用相应的端口或服务
`sudo ufw allow 80` 允许外部访问80端口
`sudo ufw delete allow 80` 禁止外部访问80端口
`sudo ufw allow from 192.168.1.1` 允许此IP访问本机的所有端口
`sudo ufw deny smtp` 禁止外部访问smtp服务
`sudo ufw deny proto top from 10.0.0.0/8 to 192.168.0.1 port` 要拒绝所有的TCP流量 从10.0.0.0/8 到192.168.0.1地址的22端口
可以允许所有的RFC1918网络（局域网/无线局域网的）访问这个主机（/8，/16，/12是一种网络分级）
`sudo ufw allow from 10.0.0.0/8`
`sudo ufw allow from 172.16.0.0/12`
`sudo ufw allow from 192.168.0.0/16`

----------

## 花生壳 ##

### 下载与解压 ###

- 下载
[文件下载](http://oc1kexygx.bkt.clouddn.com/phddns_raspberry.tgz "文件下载")
- 解压
`tar zxvf phddns_raspberry.tgz`
- 进入phddns2文件夹，执行
`./oraynewph start` 如提示Oraynewph start success说明花生壳成功安装运行。

### 查看运行状态及SN码 ###
在任意路径命令行执行 oraynewph status 即可看到花生壳运行状态以及SN码。

### 登录管理后台并设置内网映射 ###
在浏览器输入地址：b.oray.com，在花生壳管理页面，输入SN码与密码（首次登录默认密码admin）

### 重置，停止服务和卸载 ###

- 重置
`oraynewph reset` 重新登录花生壳管理后台，使用初始密码admin登录重置
- 停止服务
`oraynewph stop` 即可关闭运行中的花生壳服务
- 卸载花生壳
`oraynewph uninstall` 即可卸载花生壳服务
` oraynewph status` 确认花生壳服务已经成功卸载

参考网站：[参考网站](http://service.oray.com/question/2680.html "参考网站")

----------

## 定时执行脚本crontab ##
Linux中，周期执行的任务一般由cron这个守护进程来处理。cron读取一个或多个配置文件，这些配置文件中包含了命令行及其调用时间。cron的配置文件称为“crontab”，是“cron table”的简写。

### cron服务【Ubuntu环境】 ###
- 查看cron状态
`sudo service cron status`
- 开启cron
`sudo /etc/init.d/cron start`
- 关闭cron
`sudo /etc/init.d/cron stop`
- 重启cron
`sudo /etc/init.d/cron restart`

### crontab用法 ###
`crontab –e` : 修改 crontab 文件，如果文件不存在会自动创建。 
`crontab –l` : 显示 crontab 文件。 
`crontab -r` : 删除 crontab 文件。
`crontab -ir` : 删除 crontab 文件前提醒用户。

### 例子 ###
- 每天早上6点
`0 6 * * * echo "Good morning." >> /tmp/test.txt` //注意单纯echo，从屏幕上看不到任何输出，因为cron把任何输出都email到root的信箱了。
 
- 每两个小时(第一个为15，指明没两个小时的第15min中执行一次)
`15 */2 * * * echo "Have a break now." >> /tmp/test.txt` 
 
- 晚上11点到早上8点之间每两个小时和早上八点
`0 23-7/2，8 * * * echo "Have a good dream" >> /tmp/test.txt`
 
- 每个月的4号和每个礼拜的礼拜一到礼拜三的早上11点
`0 11 4 * 1-3 command line`
 
- 1月1日早上4点
`0 4 1 1 * command line`
 
- 每小时（第一分钟）执行/etc/cron.hourly内的脚本
`01 * * * * root run-parts /etc/cron.hourly`
 
- 每天（凌晨4：02）执行/etc/cron.daily内的脚本
`02 4 * * * root run-parts /etc/cron.daily`
 
- 每星期（周日凌晨4：22）执行/etc/cron.weekly内的脚本
`22 4 * * 0 root run-parts /etc/cron.weekly`
 
- 每月（1号凌晨4：42）去执行/etc/cron.monthly内的脚本
`42 4 1 * * root run-parts /etc/cron.monthly`
注意:  "run-parts"这个参数了，如果去掉这个参数的话，后面就可以写要运行的某个脚本名，而不是文件夹名。 　
 
- 每天的下午4点、5点、6点的5 min、15 min、25 min、35 min、45 min、55 min时执行命令。
`5，15，25，35，45，55 16，17，18 * * * command`
 
- 每周一，三，五的下午3：00系统进入维护状态，重新启动系统。
`00 15 * *1，3，5 shutdown -r +5`
 
- 每小时的10分，40分执行用户目录下的innd/bbslin这个指令：
`10，40 * * * * innd/bbslink`
 
- 每小时的1分执行用户目录下的bin/account这个指令：
`1 * * * * bin/account`
 
- 每天早晨三点二十分执行用户目录下如下所示的两个指令（每个指令以;分隔）：
`203 * * * （/bin/rm -f expire.ls logins.bad;bin/expire$#@62;expire.1st）　　`
 
- 每年的一月和四月，4号到9号的3点12分和3点55分执行/bin/rm -f expire.1st这个指令，并把结果添加在mm.txt这个文件之后（mm.txt文件位于用户自己的目录位置）。
`12,553 4-91,4 * /bin/rm -f expire.1st$#@62;$#@62;mm.txt`

----------

## git ##
### 查看ssh目录 ###
`cd ~/.ssh`
### 生成密钥 ###
`ssh-keygen -t rsa -C "your_email@youremail.com"`
### 添加到github账户 ###


----------

## 通用操作 ##
### 更新本机软件 ###
`sudo apt-get update`
### 卸载软件 ###
`sudo apt-get remote 软件名`
### 查看所有安装的软件 ###
`sudo dpkg -l`
