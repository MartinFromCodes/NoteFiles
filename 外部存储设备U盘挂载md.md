# 树莓派挂载U盘

## 操作步骤
1. 首先把U盘插入树莓派，然后查看一下是否有被识别到。
sudu fdisk -l
这里好像一定要是sudo才行，如果不获取权限好像什么都不显示。其中/dev/mmc表示的是TF卡，容量是8G，而/dev/sda/表示的是我们的第一个硬件（U盘），容量也是8G，只有一个分区。

2. 查看了U盘已经正确被识别，现在准备进行挂载。
新建一个目录
sudo mkdir /mnt/usb_flash
然后挂载设备
sudo mount /dev/sda1 /mnt/usb_flash/
虽然挂载成功，但是迅雷远程下载的时候提示“没有检测到存储设备” 好像挂载后普通用户没权限写入的问题，挂载的时候加入归属。
sudo mount -o uid=pi,gid=pi /dev/sda1 /mnt/usb-flash/

3. 需要拔出U盘的时候，可以这样取消挂载
sudo umount /mnt/usb_flash
4. 如果提示设备在忙，那么可以使用下面的方法尝试。
问题现象：
```
#umount /dev/sda1
umount: /mnt/usb: device is busy
```
查找占用目录进程：
```
#lsof |grep /mnt/usb
bash 1971 root cwd DIR 8,1 16384 1 /mnt/usb/
bash 2342 root 3r DIR 8,1 16384 1 /mnt/usb/
kill -9 1971 杀掉进程
```
卸载：
```
#umount /mnt/usb
```
## 挂载移动硬盘或者U盘
外接U盘或移动硬盘时，默认不会自动挂载，所以需要用 mount 命令挂载。通常我们在 /mnt 或 /media 目录下新建一个目录作为挂载点。比如：
```
sudo mkdir /mnt/1GB_USB_flash
sudo mount -o uid=pi,gid=pi /dev/sda1 /mnt/1GB_USB_flash
//用完之后卸载
sudo umount /mnt/1GB_USB_flash
```
sda1 是取决于你的实际情况，a表示第一个硬盘，1表示第一个分区。

挂载exFAT格式的硬盘
FAT 格式U盘 mount 本身就能支持，但如果你的U盘或移动硬盘使用的是 exFAT 格式，mount 会说不支持。没关系，安装 exfat-fuse 软件之后 mount 就支持了。

```
sudo apt-get install exfat-fuse
```
如果想开机自动挂载，而不是每次手工执行，可以编辑 /etc/fstab 文件。比如在末尾添加一行：

```
/dev/sda1 /mnt/usbdisk vfat rw,defaults 0 0
```
**挂载NTFS格式的硬盘**(读写方式挂载)
默认挂载NTFS格式的硬盘只有只读权限，需要借助其它工具实现。

```
#安装所需软件包
sudo apt-get install fuse-utils ntfs-3g
#加载内核模块
modprobe fuse
#编辑fstab让移动硬盘开机自动挂载
sudo nano /etc/fstab
#在最后一行添加如下内容
/dev/sda1 /mnt/myusbdrive ntfs-3g defaults,noexec,umask=0000 0 0
#保存重启，即可生效

```
**挂载FAT32格式的硬盘**
如果想开机自动挂载，而不是每次手工执行，可以编辑 /etc/fstab 文件。在末尾添加一行
```
#创建挂载点
sudo mkdir /mnt/myusbdrive
#编辑fstab让移动硬盘开机自动挂载
sudo nano /etc/fstab
#在最后一行添加如下内容
/dev/sda1 /mnt/myusbdrive auto defaults,noexec,umask=0000 0 0
#保存重启，即可生效
```
每次开机就会自动挂载
说明：
sda1是取决于你的实际情况，a表示第一个硬盘，1表示第一个分区。
umask=0000 0 0
前面四个0就是对所有人,可读可写可执行,
后面两个0,第一个代表dump,0是不备份
第二个代表fsck检查的顺序,0表示不检查
卸载：sudo umount /mnt/myusbdrive

**热插挂载**
需要希望想电脑一样，插上自动识别挂载在某一目录下，拔下自动umount，请按一下操作

```
sudo vi /etc/udev/rules.d/10-usbstorage.rules
P.S:此文件默认没有，需要新建
```
```
[ruby] view plaincopy
 
KERNEL!="sd*", GOTO="media_by_label_auto_mount_end"  
 
SUBSYSTEM!="block",GOTO="media_by_label_auto_mount_end"  
 
IMPORT{program}="/sbin/blkid -o udev -p %N"  
 
ENV{ID_FS_TYPE}=="", GOTO="media_by_label_auto_mount_end"  
 
ENV{ID_FS_LABEL}!="", ENV{dir_name}="%E{ID_FS_LABEL}"  
 
ENV{ID_FS_LABEL}=="", ENV{dir_name}="Untitled-%k"  
 
ACTION=="add", ENV{mount_options}="relatime,sync"  
 
ACTION=="add", ENV{ID_FS_TYPE}=="vfat", ENV{mount_options}="iocharset=utf8,umaskk  
 
=000"  
 
ACTION=="add", ENV{ID_FS_TYPE}=="ntfs", ENV{mount_options}="iocharset=utf8,umaskk  
 
=000"  
 
ACTION=="add", RUN+="/bin/mkdir -p /mnt/udisk/", RUN+="/bin/mount -o $env{mount__  
 
options} /dev/%k /mnt/udisk/"  
 
 
 
ACTION=="remove", ENV{dir_name}!="", RUN+="/bin/umount -l /mnt/udisk/}", RUN+="//  
 
bin/rmdir /mnt/udisk/"  
 
LABEL="media_by_label_auto_mount_end"
```


 
参考链接地址：http://shumeipai.nxez.com/2013/09/08/raspberry-pi-to-mount-the-removable-hard-disk.html