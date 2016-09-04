---
title: Linux养成
date: 2016-08-27 23:35:32 
categories: Linux
tags: Linux

---


## Linux安装 ##

### 虚拟机配置 ###
- 内存：centos6.3 最小需要618M内存
- 处理器：虚拟化Intel VT-x/...把真实CPU性能映射到虚拟机中
- CD/DVD：已连接 需要选中
- 网络适配器：
 - 桥接：虚拟机和真实机通信用的是真实网卡，配置简单，但是需要占用真实机网段的IP
 - NAT:使用VMnet8网卡与真实机通信，虚拟机只能与真实机通信，而不能与网段内其他机器通信，可以上网
 - Host-only：使用VMnet1网卡与真实机通信，虚拟机只能与真实机通信，而不能与网段内其他机器通信，**不可以上网**

<!--more-->

### 系统分区 ###
- 分区类型
 - 主分区：最多只能是4个
 - 扩展分区：
    - 最多只能有1个
    - 主分区加扩展分区最多有4个
    - 不能写入数据，只能包含逻辑分区
 - 逻辑分区
- 格式化
 - 又称逻辑格式化，它是指根据用户选定的文件系统（如FAT16,FAT32,NTFS,EXT2,EXT3,EXT4等），在磁盘特定区域写入特定数据，在分区中划出一片用于存放文件的分配表、目录表等用于文件管理的磁盘空间。
    - FAT16:最大只能支持2GB的分区
    - FAT32:最大支持16T的分区，单个文件大小不能超过4GB
    - NTFS:windows中支持更大分区、更大单个文件的文件系统
    - EXT2\EXT3\EXT4:Linux专有文件系统
- 分区设备文件名
 - 设备文件名
    - /dev/hda1(IDE硬盘接口)，hd代表硬盘接口，a代表第一块硬盘，1代表第一个分区
    - /dev/sda1(SCSI硬盘接口，SATA硬盘接口)
- 挂载
 - 必须分区
    - /(根分区)
    - swap分区(交换分区，内存2倍，不超过2GB)
 - 推荐分区
    - /boot(启动分区，200MB)

### Linux系统安装 ###
| 设备 | 大小(MB) | 挂载点/RAID/卷 | 类型 |
|:--:|:--:|:--:|:--:|
| sda1 | 200 | /boot | ext4 |
| sda2 | 2000 | /home | ext4 |
| sda3 | 1000 | - | swap |
| sda4(扩展分区，会变成sda5) | 剩余空间 | / | ext4 |

- 软件包选择
 - Desktop(桌面)
 - Minimal Desktop(最小化桌面)
 - Minimal(最小化)，正式做服务器
 - Basic Server(基本服务器)，初学者适用*
 - Database Server(数据库服务器)
 - Web Server(网页服务器)
 - Virtual Host(虚拟主机)
 - software development workstation(软件开发工作站)

- 远程链接虚拟机

<pre>
xshell快速链接VMware方法：

1.VMware中设置ip地址：ifconfig eth0 192.168.1.117 netmask 255.255.255.0;

使用桥接方式，

2.控制面板\网络和 Internet\网络连接：

WLAN,VMware Network Adapter VMnet1/VMnet8 属性设置勾选 VMware Bridge protocal.

并且中VMware Network Adapter VMnet1属性中的Internet协议版本4中选择 自动获取IP地址；

3.Xshell中设置ip与VMware中一致即可；且用户身份验证为 root；
</pre>

----------

## 命令基本格式及文件处理命令 ##

### 命令基本格式 ###
- `[root@localhost ~]#`  
	`root`:当前登陆用户  
	`localhost`:主机名  
	`~`:当前所在目录(家目录)  
	`#`:超级用户提示符，普通用户提示符为`$`  

### 常用目录作用 ###
- /	根目录
- /bin		命令保存目录(普通用户就可以读取的命令)
- /sbin		命令保存目录(超级用户才能使用的命令)
- /boot		启动目录，启动相关文件
- /dev		设备文件保存目录
- /etc		配置文件保存目录
- /home		普通用户的家目录
- /lib		系统库保存目录
- /mnt		系统挂载目录
- /medir		挂载目录
- /root		超级用户的家目录
- /tmp		临时目录
- /proc		直接写入内存的，不能直接操作，目录保存的是内存的过载点
- /sys		同/proc
- /var		系统相关文档内容
- /usr		系统软件资源目录
  - /usr/bin		系统命令(普通用户)
  - /usr/sbin	系统命令(超级用户)

### 目录操作 ###
- 查看目录 `ls [选项] [文件或目录]`  
	选项:  
	1. `-a` 显示所有文件，包括隐藏文件  
	2. `-l` 显示详细信息 **别名为`# ll`**  
		<pre>
		# ls -l
	
		总用量 44
		权限 	引用计数	文件所有者	所属组	大小(kb)	最后修改时间	文件名
		-rw-------. 1 		root 		root  	1272 		8月  27 23:03 	anaconda-ks.cfg
		-rw-r--r--. 1 		root 		root 	27338 		8月  27 23:03 	install.log
		-rw-r--r--. 1 		root 		root  	7572 		8月  27 23:01 	install.log.syslog
		</pre>
	
		`-rw-r--r--.`
		权限，一共10位，第1位`-`为文件类型，一共有7种文件类型，其中3个常用的为`-:文件``d:目录``l软链接文件`，其它4个是块设备文件、字符设备文件、套接字文件和管道文件。后9位每3个一组，`rw-:u所有者``r--:g所属组``r--:o其他人`
		`r`读 `w`写 `x`执行 
		`.`:代表ACL权限
	3. `-d` 查看目录属性  
		<pre>
		# ls -ld /etc/
		drwxr-xr-x. 104 root root 12288 8月  28 00:42 /etc
		</pre>
	4. `-h` 人性化显示文件大小
		<pre>
		# ls -lh(人性化)
		总用量 44K
		-rw-------. 1 root root 1.3K 8月  27 23:03 anaconda-ks.cfg
		-rw-r--r--. 1 root root  27K 8月  27 23:03 install.log
		-rw-r--r--. 1 root root 7.4K 8月  27 23:01 install.log.syslog
		</pre>
	5. `-i` 显示inode  	
　　
- 新建目录 `mkdir -p [目录名]` (make directories)  	
	`-p` 递归创建 `mkdir -p 目录1/目录2/目录3`

- 切换所在目录 `cd [目录]` (change directory)
	- 简化操作
	`cd ~`或者`cd`	进入当前目录的家目录
	`cd -`		进入上次目录
	`cd ..`		进入上一级目录
	`cd .`		进入当前目录
	
	- 相对路径：参照当前所在目录，进行查找
	如：`# cd ../usr/local/src/`
	- 绝对路径：从根目录开始置顶，一级一级递归查找。在任何位置下，都能进入指定目录。
	如：`# cd /etc/`

- 查询所在目录位置 `pwd` (print working directory)

- 删除空目录 `rkdir [目录名]` (remove empty directories)

- 删除文件或目录 `rm` (remove)  
	`rm -rf [文件或目录]`
	`-r` 删除目录
	`-f` 强制

- 复制命令 `cp [选项] [原文件或目录] [目标目录]` (copy)
	选项
	- `-r` 复制目录
	- `-p` 连带文件属性复制
	- `-d` 若源文件是链接文件，则复制链接属性
	- `-a` 相当于 `-pdr`

- 剪切或改名命令 `mv [原文件或目录] [目标目录]` (move)
	剪切目录不需要加`-r`

### 链接命令 ###
- `ln -s [原文件] [目标文件]` (link)
- 功能描述：生成链接文件  
  选项：`-s` 创建软链接
　　
- 硬链接特性
 - 拥有相同的i节点和存储block块，可以看做是同一个文件
 - 可通过i节点识别
 - 不能跨分区
 - 不能针对目录使用  
- 软链接特性
 - 类似windows快捷方式
 - 软链接拥有自己的I节点和Block块，但是数据块中只保存原文件的文件名和I节点号，并没有实际的文件数据。
 - lrwxrwxrwx l为软连接标识，软链接文件权限都为rwxrwxrwx,对文件的真实权限须根据原文件。
 - 修改任意文件，另一个都改变
 - 删除原文件，软链接不能使用
 - 软链接要写绝对路径

----------

## 文件搜索命令 ##

- 文件搜索命令locate
- 命令搜索命令whereis与which
- 文件搜索命令find
- 字符串搜索命令grep
- find命令与grap命令区别

### locate命令 ###
- `locate 文件名`
  在后台数据库中按文件名搜索，搜索速度更快
- /var/lib/mlocate  
  #locate 命令所搜索的后台数据库，一天更新一次
- updatedb
  更新数据库
- 只能根据文件名搜索
- /etc/updatedb.conf配置文件
  - PRUNE_BIND_MOUNTS = 'yes' 开启搜索限制
  - PRUNEFS 搜索时，不搜索系统文件
  - PRUNENAMES 搜索时，不搜索的文件类型
  - PRUNEPATHS 搜索时，不搜索的路径

### whereis & which 命令 ###
搜索系统命令的命令
- `whereis [选项] 命令名`  
	#搜索命令所在路径及帮助文档所在位置  
	选项：  
		-b:只查看可执行文件  
		-m:只查看帮助文件
- `which 命令名`
  - 搜索命令所在路径
  - 文件别名(有则显示)

### find 命令 ###
- `find [搜索范围] [搜索条件]`
- `find / -name install.log`
 - 应避免大范围搜索，会非常耗费系统资源
 - find是在系统当中搜索完全符合条件的文件名。如果需要匹配，使用通配符匹配，通配符是完全匹配。
    - `*` 匹配任意内容
    - `?` 匹配任意一个字符
    - `[]` 匹配任意一个中括号内的字符
 - `find /root -iname install.log`  
  不区分大小写
- `find /root -user root`  
  按照所有者搜索
- `find /root -nouser`  
  没有所有者的文件，即垃圾文件，但除了以下两种情况：
    - 系统自己产生的文件
    - 外来文件(U盘拷贝等)
- `find /var/log -mtime +10`  
  查找10天前修改的文件
 - -10 10内修改的文件
 - 10  10天当天修改的文件
 - +10 10天前修改的文件
 - atime 文件访问时间
 - ctime 改变文件属性
 - mtime 修改文件内容
- `find . -size 25k`  
  查找文件大小是25k的文件
 - +25k 大于25k
 - 25k 等于25k
 - -25k 小于25k
 - 单位必须要加，`k`表示千字节,`M`表示兆字节
- `find /root inum 123456`  
  查找i节点是123456的文件
- `find /etc -size +20k -a -size -50k`  
  查找etc目录下，大于20k并且小于50k的文件
 - `-a` and，逻辑与，两个条件都满足
 - `-o` or,逻辑或，两个条件满足一个即可
- `find /etc -size +20k -a -size -50k -exec ls -lh {} \`  
  查找etc目录下，大于20k并且小于50k的文件,并显示详细信息
 - `-exec 命令 {} \` 固定格式，对搜索结果执行操作

### grep命令 ###
- `grep [选项] 字符串 文件名`  
  在文件中匹配附和条件的字符串  
  选项：
 - `-i` 忽略大小写
 - `-v` 排除指定字符串

### find与grep区别 ###
- find命令：在系统当中搜索完全符合条件的**文件名**。如果需要匹配，使用通配符匹配，通配符是完全匹配。
- grep命令：在文件当中搜索符合条件的**字符串**，如果需要匹配，则使用正则表达式进行匹配，正则表达式是包含匹配。

----------

## 帮助命令 ##

### man命令 ###

- `man 命令`  
在结果中，再输入`/-d` 搜索`-d`相关内容，n下一个，N上一个

- man的级别
 1. 查看命令的帮助
 2. 查看可被内核调用的函数的帮助
 3. 查看函数和函数库的帮助
 4. 查看特殊文件的帮助（主要是/dev目录下的文件）
 5. 查看配置文件的帮助
 6. 查看游戏的帮助
 7. 查看其他杂项的帮助
 8. 查看系统管理员可用命令的帮助
 9. 查看和内核相关文件的帮助

- 查看命令拥有哪个级别的帮助
 - `man -f 命令`相当于`whatis 命令`  
    例如
    - man -5 passwd
    - man -4 null
    - man -8 config
 - `man -k 命令`相当于`apropos 命令`  
  所有包含命令关键字的文件或命令

### 其他帮助命令 ###

- `命令 --help`  
  获取命令选项的帮助
- `help shell内部命令`  
  获取shell内部命令的帮助
 - `whereis cd`  
    确定是否是shell内部命令
 - `help cd`  
    获取内部命令的帮助
- 详细帮助命令info  
`info 命令`
 - `回车` 进入子标记页面(带有*号标记)
 - `u` 进入上层页面
 - `n` 进入下一个帮助小节
 - `p` 进入上一个帮助小节
 - `q` 退出

----------

## 压缩与解压缩命令 ##
常用压缩格式
- `.zip` `.gz` `.bz2`
- `.tar.gz` `.tar.bz2`

### .zip格式压缩 ###
- `zip 压缩文件名 源文件`  
  压缩文件
- `zip -r 压缩文件名 源文件`  
  压缩目录
- `unzip 压缩文件`  
  解压缩.zip文件

### .gz格式压缩 ###
- `.gzip 源文件`  
  压缩为.gz格式的压缩文件，源文件会消失
- `.gzip -c 源文件 > 压缩文件`  
  压缩为.gz格式，源文件保留  
  例如：`gzip -c aabc > aabc.gz`,`>`为输出作用，将`gzip -c aabc`结果输出在`aabc.gz`中
- `gzip -r 目录`  
  压缩目录下所有的子文件，但是不能压缩目录
- `gzip -d 压缩文件`  
  解压缩文件
- `gunzip 压缩文件`  
  解压缩文件
- `gunzip 目录`  
  解压缩目录里面的gz文件，并不解压缩目录本身

### .bz2格式压缩 ###
**不能压缩目录**
- `bzip2 源文件`  
  压缩为.bzip2格式，不保留源文件
- `bzip2 -k 源文件`  
  压缩为.bzip2格式，保留源文件
- `bzip2 -d 压缩文件`  
  解压缩，-k保留压缩文件 `bzip2 -dk 压缩文件`
- `bunzip2 压缩文件`  
   解压缩，-k保留压缩文件

### 打包命令tar ###
- `tar -cvf 打包文件名 源文件`  
  选项：  
  `-c`:打包  
  `-v`:显示过程  
  `-f`:指定打包后的文件名  
  例如：`tar -cvf aaa.tar aaa`
- `tar -xvf 打包文件名`  
  选项：  
  `-x`:解打包  
  例如：`tar -xvf aaa.tar`
- `tar -zcvf 压缩包名.tar.gz 源文件1 源文件2 ...`  
  选项：  
  `-zc`:压缩为.tar.gz格式
- `tar -zxvf 压缩包名.tar.gz`  
  选项：  
  `-zx`:解压缩.tar.gz格式
- `tar -jcvf 压缩包名.tar.bz2 源文件`    
  选项：  
  `-jc`:压缩为.tar.bz2格式
- `tar -jxvf 压缩包名.tar.bz2`  
  选项：  
  `-jx`:解压缩.tar.bz2格式
- `tar -jxvf 压缩包名.tar.bz2 -C /tmp`  
  指定解压缩位置
- `tar -jtvf 压缩包名.tar.bz2`  
  只查看压缩包内容

----------

## 关机和重启命令 ##

### shutdown命令 ###
会在关机或重启前，正确得保存数据和服务。
- `# shutdown [选项] 时间`  
  选项：
 - `-c`:取消前一个关机命令
 - `-h`:关机
 - `-r`:重启  
 时间：
 - `now`
- `# shutdown [选项] 时间 &`  
  表示放入后台执行

### 其他关机命令 ###
- `# half`
- `# poweroff`
- `#init 0`

### 其他重启命令 ###
- `# reboot`
- `# init 6`

### 系统运行级别 ###
- `# init x`调用
 - 0 关机
 - 1 单用户
 - 2 不完全多用户，不含NFS服务
 - 3 完全多用户（当前界面）
 - 4 未分配
 - 5 图形界面
 - 6 重启

- `# cat /ect/inittab`:修改系统默认运行级别
- `# runlevel`:查询系统运行级别

### 退出登陆命令 ###
- `# logout`

----------

## 其他常用命令 ##

### 挂载命令 ###
- 查询与自动挂载  
`# mount`  
查询系统中已经挂载的命令  
`# mount -a`  
依据配置文件etc/fstab的内容，自动挂载

- 挂载命令格式
`# mount [-t 文件系统] [-o 特殊选项] 设备文件名 挂载点`
  选项:
  - -t 文件系统：加入文件系统类型来指定挂载的类型，可以在ext3,ext4,iso9660等文件系统
  - -o 特殊选项：可以指定挂载的额外选项

| 参数 | 说明 |
|:----:|:----:|
| atime/noatime | 更新访问时间*/不更新访问时间。访问分区文件时候，是否更新文件的访问时间 |
| async/sync | 异步*/同步 |
| auto/noauto | 自动*/手动，mount -a命令置顶时，是否会自动安装/etc/fstab文件内容挂载 |
| defaults | 定义默认值，相当于rw,suid,dev,exec,auto,nouser,async这七个选项 |
| exec/noexec | 执行*/不执行，设定是否允许在文件系统中执行可执行文件 |
| remount | 重新挂载已经挂载的文件系统，一般用于指定修改特殊权限 |
| rw/ro | 读写*/只读，文件系统挂载时，是否具有读写权限 |
| suid/nosuid | 具有*/不具有SUID权限，设定文件系统是否具有SUID和SGID权限 |
| user/nouser | 允许/不允许*普通用户挂载，设定文件系统是否允许普通用户挂载 |
| usrquota | 写入代表文件系统支持用户磁盘配额，默认不支持 |
| grpquota | 写入代表文件系统支持组磁盘配额，默认不支持 |



- 挂载光盘

`# mkdir /mnt/cdrom`:建立挂载点
`# mount -t iso9660 /dev/cdrom /mnt/cdrom`或`# mount -t iso9660 /dev/sr0 /mnt/cdrom`:挂载光盘  

- 卸载命令
`# umount 设备文件名或挂载点`  
  `# umount /mnt/cdrom`

- 挂载U盘
`# fdisk -l`
  查看系统中已经识别的硬盘，当U盘插入后，可查看U盘设备文件名
`# mount -t vfat /dev/sdb1/mnt/usb/`

- Linux默认不支持NTFS文件系统

### 查看用户登陆信息 ###
- `# w`
<pre>
 01:58:21   up 1:16,        3 users,      load average: 0.00, 0.00, 0.00
  系统时间  系统运行时间1h16  登陆了3用户  系统在1min，5m,15m执行的平均负载
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
root     tty1     -                00:42   27:02   0.12s  0.12s -bash
root     pts/0    192.168.31.169   00:44    1:13m  0.05s  0.05s -bash
root     pts/1    192.168.31.169   00:45    1.00s  0.20s  0.10s w
	
tty:本机  pts/0:第一个远程终端
</pre>

命令输出：
　 1. USER:登陆的用户名
　 2. TTY:登陆终端，tty0本机，pts/0第一个远程终端
　 3. FROM:从哪个IP登陆
　 4. LOGIN@:登陆时间
　 5. IDLE:用户闲置时间
　 6. JCPU:指的是和该终端链接的所有进程占用的时间。这个时间不包括过去的后台作业时间，但却包括当前正在运行的后台作业所占用的时间
　 7. PCPU：指当前进程所占用的时间
　 8. WHAT:当前正在运行的命令

- `# who`  
命令输出：
 - 用户名
 - 登陆终端
 - 登陆时间（登陆来源的IP）
　　
- 查询当前登陆和过去登陆的用户信息
`# last`
  last命令默认是读取/var/log/wtmp文件数据  
  命令输出：
  - 用户名
  - 登陆终端
  - 登陆IP
  - 登陆时间
  - 退出时间（在线时间）
　　
- 查看所有用户的最后一次登陆时间  
`# lastlog`  
  lastlog命令默认是读取/var/log/lastlog文件内容  
  命令输出：
  - 用户名
  - 登陆终端
  - 登陆IP
  - 最后一次登陆时间







----------

## 其他 ##
- `touch filename` 新建文件
- `echo $PATH` 显示环境变量
- `aaa > bbb` 将aaa写入bbb
- `date` 当前时间
- 大写字母代表图形界面