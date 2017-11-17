# 在.net 项目中使用 Redis

 Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。从2010年3月15日起，Redis的开发工作由VMware主持。从2013年5月开始，Redis的开发由Pivotal赞助。这是百度百科的介绍!

## 定义
redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)、zset(sorted set --有序集合)和hash（哈希类型）。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。

## 在.net 项目中使用Redis
### 1 安装服务端
在 rdis官网[下载安装文件](https://redis.io/) ,安装成功之后启动服务端。这里推荐看看这个[教程](http://www.runoob.com/redis/redis-tutorial.html)。安装完成之后启动服务端。这里我演示一下本地的Windos服务端启动

1 进入安装目录，复制路径。然后在CMD中进入到相关路径，执行命令开启服务端。redis服务需要一直开启这个窗口，无法后台运行，可以设置配置文件修改。这里仅作演示。

2 然后再打开一个CMD窗口测试服务是否正常，使用客户端工具连接到redis服务然后添加一个key
```
redis-cli.exe -h 127.0.0.1 -p 6379

```