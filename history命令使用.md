##1. history作用


linux的history命令的作用是，记录执行过的命令。
用法：
history [n]  n为数字，列出最近的n条命令
-c  将目前shell中的所有history命令消除
history [-raw] histfiles
-a 将目前新增的命令写入histfiles, 默认写入~/.bash_history
-r  将histfiles内容读入到目前shell的history记忆中
-w 将目前history记忆的内容写入到histfiles

```
shell > history  
   34  14-10-28 16:19:24 ll  
   35  14-10-28 16:19:26 vim test  
   36  14-10-28 16:19:33 ll  
   37  14-10-28 16:19:34 ll  
展示3行
```
shell > history 3  
 1032  14-11-02 16:10:41 history  
 1033  14-11-02 16:10:46 history |more  
 1034  14-11-02 16:11:15 history 3  
```
使用! 执行历史命令。
! number 执行第几条命令
! command 从最近的命令查到以command开头的命令执行
!! 执行上一条
[plain] view plain copy
shell > !1046  
history 3  
 1045  14-11-02 16:22:38 head ~/.bash_history   
 1046  14-11-02 16:35:37 history 3  
 1047  14-11-02 16:35:48 history 3  
shell > !!  
history 3  
 1046  14-11-02 16:35:37 history 3  
 1047  14-11-02 16:35:48 history 3  
 1048  14-11-02 16:35:52 history 3  
shell > !head  
head ~/.bash_history   
#1414484377  
cd update/  
#1414484377  
2. history配置修改


history记录的行数
[plain] view plain copy
shell > echo $HISTSIZE  
1000  
shell >   
默认记录1000行
配置文件在/etc/profile中修改
[plain] view plain copy
HISTSIZE=1000  
export  HISTSIZE  
历史命令文件记录在 ~/.bash_history中

想要让linux的history命令显示时间，history是默认不带时间，
在/etc/profile 中增加
[plain] view plain copy
export HISTTIMEFORMAT="%y-%m-%d %H:%M:%S "  
查看.bash_history
[plain] view plain copy
shell > head ~/.bash_history   
#1414484377  
cd update/  
#1414484377  
ll  
#1414484388  
vim address   
#1414484439  
ll  
#1414484440  
ll  
shell >   

3. 同一账号同时多次登录写入history


普通情况下， 当以bash登录系统时，系统会从~/.bash_history读取以前运行的命令
当注销时，把最新的1000（HISTSIZE）条命令更新到~/.bash_history文件中。
也可以使用history -w强制立刻写入，仅保留最新的。

当同一账号，同时登录多个bash时，只有最后一个退出的会写入bash_history,其他的都被覆盖了。

4. Ctrl+r 反向查询历史命令


使用Ctrl+r反向查询历史命令，将匹配的最新一条显示出来
如果还想继续向上查询，继续按Ctrl+r
[plain] view plain copy
shell > history 3                                         
 1048  14-11-02 16:35:52 history 3  
 1049  14-11-02 16:36:11 head ~/.bash_history   
 1050  14-11-02 16:41:05 history 3  
(reverse-i-search)`his': head ~/.bash_history   

地址：http://blog.csdn.net/yonggang7/article/details/40710623

