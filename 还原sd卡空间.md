1输入 diskpart 启动分区程序，不区分大小写，有的系统需要启动管理员权限，在运行终端时，请使用管理员权限来启动。 

2输入 list disk 显示所有磁盘信息，以数字编号展示，一般最后一个序号表示的是自己的移动存储设备。 

3输入 select disk 1 就表示选择7G的内存卡，可以根据磁盘容量大小来判断自己的内存卡还是磁盘。 

4 输入 clean 回车，OK，将会删除该磁盘的所有分区，当然也会清空所有数据，请谨慎操作，避免删除自己重要的数据。

5.create partition primary，重建SD卡分区信息 

6.format fs=fat32 quick，对SD卡执行快速格式化 

7.exit，退出diskpart工具

按win键+r，输入cmd，打开命令窗口，然后输入diskpart，之后输入list disk
然后select disk 1，最后clean。在磁盘管理里面重新给SD卡分个驱动号就行了
