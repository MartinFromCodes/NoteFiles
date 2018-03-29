# 配置wifi和ip

周一的时候树莓派总算是到手了，很早之前就了解过了，心里长草很多年，但就是一直没出手。最近在搞Arduino的小玩意，我们做的这个东西需要网络通信（一个可以远程控制的机器人小车），必须要借助上位机（一台android手机）的网络来接受命令，真想再给它再上一块WiFi扩展板，把手机扔了。然后就突然想起了这货，树莓派是基于linux的只有信用卡的大小计算机。你可以把这货当成一个正常linux服务器就是，基本上你平时在linux上能做到的事情，它都能做到，比如当成rails服务器，在上面运行rails程序什么的(不过我没试，应该是可以的，sinatra可以)。然后这货有usb口，再然后插上你在某宝买的usb无线网卡，它就可以用WiFi了。下面是我第一次，第二次以及第n次连上WiFi的过程。

第一次连上WiFi
第一次连wifi之前，你需要做一件事情，那就是打开树莓派的terminal（俗称命令行），你有两种选择：

通过HDMI连个显示器，再接个USB接个键盘，然后你就可以像用一台普通的pc一样用树莓派了
插个网线，通过树莓派的ip地址ssh进去。
```
  ssh pi@your_raspi_ip
  #password: raspberry
```
我是通过插网线的方式进去的，这个方法比较麻烦的地方是，你得去找到树莓派从DHCP的获取到的ip(当然能设静态ip又是另一回事了)。我是在自己家连的时候，可以从路由器的客户端列表里面找到树莓派的ip。

在进到terminal后，你就可以开始安装软件，修改配置了

可能需要安装的软件（因为我拿到手的时候，发现系统里已经有了，不知道是某宝的亲帮我装的，还是raspbian已经预装了）

``` sudo apt-get install wireless-tools ```
然后可以开始配置网络了，修改/etc/network/interfaces文件，把它修改成这个样子

```
 auto lo
 iface lo inet loopback
 auto eth0
 iface eth0 inet dhcp
 allow-hotplug wlan0
 auto wlan0
 iface wlan0 inet dhcp
     wpa-ssid YOUR-SSID-HERE
     wpa-psk YOUR-PASSWORD-HERE
```
主要是添加wpa-ssid和wpa-psk，直接把你要连接的wifi的ssid和对应密码写上就行了。

重启网络
```
 /etc/init.d/networking restart
 # or: service networking restart
``` 
然后你应该就已经连上wifi了，如果没有连上：
检查时候你要连接的wifi是不是隐藏的WiFi(不广播自己的ssid的WiFi网络)，这种配置方法没办法连接隐藏的WiFi。通过下面的命令检查你要连的WiFi是不是在列表里： iwlist wlan0 scan
检查你的ssid和密码是否正确！！！
检查你要连的wifi网络是否正常，检查你的USB无线网卡是不是正常
如果还连不上就google吧，我也无能为力了
这样的配置在你重新启动树莓派后也能自动连接这个WiFi，这里连接WiFi使用的是wpa_supplicant

自动连接多个WiFi网络
当你经常切换到不同WiFi网络中时，你可以配置多个WiFi网络，让树莓派能自动连接到可用WiFi网络中。这里就要用到高大上的wpa_supplicant.conf了

修改/etc/wpa_supplicant/wpa_supplicant.conf，下面是我使用的配置文件：
```
 ctrl_interface=/var/run/wpa_supplicant
 #ap_scan=1
 network={
        ssid="wo_shi_yige_wifi_ssid"
        scan_ssid=1
        psk="wo_shi_mi_ma"
        priority=5
 }
 network={
        ssid="pi"
        psk="onlyforpi"
        priority=1
 }
```

ap_scan:1是默认值，因此我注掉了
1：这个模式下总是先连接可见的WiFi，如果扫描完所有可见的网络之后都没有连接上，则开始连接隐藏WiFi。

2：会按照network定义的顺序连接WiFi网络，遇到隐藏的将立刻开始连接，因此在这个模式下连接顺序不受priority影响
ctrl_interface:这个文件夹里面存的是一个当前使用的interface的socket文件，可以供其他程序使用读取WiFi状态信息
network：是一个连接一个WiFi网络的配置，可以有多个，wpa_supplicant会按照priority指定的优先级（数字越大越先连接）来连接，当然，在这个列表里面隐藏WiFi不受priority的影响，隐藏WiFi总是在可见WiFi不能连接时才开始连接。
ssid:网络的ssid
psk:密码
priority:连接优先级，越大越优先
scan_ssid:连接隐藏WiFi时需要指定该值为1
修改/etc/network/interfaces使用wpa_supplicant.conf来配置无线网络
```
 auto lo
 iface lo inet loopback
 auto eth0
 iface eth0 inet dhcp
 allow-hotplug wlan0
 auto wlan0
 iface wlan0 inet dhcp
     pre-up wpa_supplicant -Dwext -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf -B 
```     
以后每次启动时，树莓派都会主动去连接配置文件中预定义的这些wifi网络。

在这个配置里面有一个ssid=‘pi’网络，这是一个最低优先级网络，是用来在陌生网络中配置树莓派的。当处在一个树莓派配置里面的没有的WiFi网络中时，我会自己创建一个叫pi的WiFi，当其他网络都不可用时，树莓派连到这个网络里，然后我便可以ssh进树莓派，添加网络配置，然后重启，就可以让树莓派加入到新的网络中。
