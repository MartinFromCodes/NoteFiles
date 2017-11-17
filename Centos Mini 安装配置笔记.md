# CentosMinimal 安装配置笔记

## 系统安装
使用vm虚拟机安装CentosMinimal系统，然后配置使用，

## 安装后基本配置

###  网络设置
nmcli d 查看网卡信息，如果是禁用的可以使用

nmtui 可视化网络配置界面系统命令

配置好了之后，重启网络服务
service network restart

### ipconfig密码安装
ifconfig命令是设置或显示网络接口的程序，可以显示出我们机器的网卡信息，可是有些时候最小化安装CentOS等Linux发行版的时候会默认不安装ifconfig等命令，这时候你进入终端，运行ifconfig命令就会出错!
使用以下命令，来安装ifconfig命令,net-tools包提供了ifconfig命令

``` 
yum provides ifconfig
yum install net-tools
```
### 安装pip
yum -y install epel-release

yum install python-pip 安装

pip install --upgrade pip 升级

### 安装git
yum -y install git
