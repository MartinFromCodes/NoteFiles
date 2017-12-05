Usage: fping [options] [targets...]
用法：fping [选项] [ping的目标]
   -a         show targets that are alive
              显示可ping通的目标

   -A         show targets by address
               将目标以ip地址的形式显示

   -b n       amount of ping data to send, in bytes (default 56)
               ping 数据包的大小。（默认为56）

   -B f       set exponential backoff factor to f

               设置指数反馈因子到f 【这个不懂，求指教~】

   -c n       count of pings to send to each target (default 1)

                ping每个目标的次数 (默认为1)

   -C n       same as -c, report results in verbose format

                同-c, 返回的结果为冗长格式

   -e         show elapsed time on return packets

                显示返回数据包所费时间

   -f file    read list of targets from a file ( - means stdin) (only if no -g specified)

               从文件获取目标列表( - 表示从标准输入)(不能与 -g 同时使用)

   -g         generate target list (only if no -f specified)

               生成目标列表(不能与 -f 同时使用)
                (specify the start and end IP in the target list, or supply a IP netmask)
                (ex. fping -g 192.168.1.0 192.168.1.255 or fping -g 192.168.1.0/24)
                (可指定目标的开始和结束IP， 或者提供ip的子网掩码)
                (例：fping -g 192.168.1.0 192.168.1.255 或 fping -g 192.168.1.0/24)

   -H n       Set the IP TTL value (Time To Live hops)
                设置ip的TTL值 (生存时间)

   -i n       interval between sending ping packets (in millisec) (default 25)
               ping包之间的间隔(单位：毫秒)(默认25)

   -l         loop sending pings forever
              循环发送ping

   -m         ping multiple interfaces on target host
                ping目标主机的多个网口

   -n         show targets by name (-d is equivalent)
                将目标以主机名或域名显示(等价于 -d )

   -p n       interval between ping packets to one target (in millisec)
                对同一个目标的ping包间隔(毫秒)
                (in looping and counting modes, default 1000)
                (在循环和统计模式中，默认为1000)

   -q         quiet (don't show per-target/per-ping results)
               安静模式(不显示每个目标或每个ping的结果)

   -Q n       same as -q, but show summary every n seconds
               同-q, 但是每n秒显示信息概要

   -r n       number of retries (default 3)
               当ping失败时，最大重试次数(默认为3次)

   -s         print final stats
               打印最后的统计数据

   -I if      bind to a particular interface
              绑定到特定的网卡

   -S addr    set source address
                  设置源ip地址

   -t n       individual target initial timeout (in millisec) (default 500)
               单个目标的超时时间(毫秒)(默认500)

   -T n       ignored (for compatibility with fping 2.4)
                请忽略(为兼容fping 2.4)

   -u         show targets that are unreachable
                显示不可到达的目标

   -O n       set the type of service (tos) flag on the ICMP packets
                在icmp包中设置tos（服务类型）

   -v         show version
                显示版本号
   targets    list of targets to check (if no -f specified)
                需要ping的目标列表(不能和 -f 同时使用)

-h              show this page
                 显示本帮助页


3. 使用实例

```
fping -A -u -c 4 192.168.1.1 192.168.1.74 192.168.1.20
192.168.1.1  : xmt/rcv/%loss = 4/4/0%, min/avg/max = 1.54/2.30/4.32
192.168.1.74 : xmt/rcv/%loss = 4/0/100%
192.168.1.20 : xmt/rcv/%loss = 4/4/0%, min/avg/max = 0.07/0.07/0.08
```