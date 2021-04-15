---
title: linux优化
date: 2021-04-15 16:37:58
tags: linux
---

##### 最大文件描述符

查看用户级文件描述符限制
ulimit -n

##### 常用性能检测工具

1. mpstat 实时监测系统每个CPU的使用情况
   mpstat -P ALL 5 2 每隔5秒输出一次结果，共输出两次

-P 指定监听CPU数量 ALL表示所有的

1. ifstat 网络流量检测工具
   ifstat -a 2 5 每隔2秒输出一次结果，共输出5次

   <!--more-->

-a 检测系统上的所有网卡
-i 置顶要检测的网卡接口
-t 在每行输出信息前加上时间戳
-b 以Kbit/s 为单位显示数据，而不是默认的KB/s

1. vmstat 显示Linux系统虚拟内存状态，也可以报告关于进程、内存、I/O等系统整体运行状态

-a：显示活跃和非活跃内存
-f：显示从系统启动至今的fork数量 。
-m：显示slabinfo
-n：只在开始时显示一次各字段名称。
-s：显示内存相关统计信息及多种系统活动数量。
delay：刷新时间间隔。如果不指定，只显示一条结果。
count：刷新次数。如果不指定刷新次数，但指定了刷新时间间隔，这时刷新次数为无穷。
-d：显示磁盘相关统计信息。
-p：显示指定磁盘分区统计信息
-S：使用指定单位显示。参数有 k 、K 、m 、M ，分别代表1000、1024、1000000、1048576字节（byte）。默认单位为K（1024 bytes）
-V：显示vmstat版本信息
结果说明

```
root@dev-kz ~]# vmstat -a
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free  inact active   si   so    bi    bo   in   cs us sy id wa st
 8  0      0 6551064 1505368 7826268    0    0    28   204    2    1  4  1 95  0  0
 procs
 r:	运行队列中进程数量，这个值也可以判断是否需要增加CPU。（长期大于1）
 b	等待IO的进程数量。
 memory 单位为kb
 swpd	使用虚拟内存大小，如果swpd的值不为0，但是SI，SO的值长期为0，这种情况不会影响系统性能。
 free	空闲物理内存大小。
 buff	用作缓冲的内存大小。
 cache	用作缓存的内存大小，如果cache的值大的时候，说明cache处的文件数多，如果频繁访问到的文件都能被cache处，那么磁盘的读IO bi会非常小。
 显示活跃和非活跃内存，显示增加了inact和active列
 Swap
si	每秒从交换区写到内存的大小，由磁盘调入内存。
so	每秒写入交换区的内存大小，由内存调入磁盘。
#注意：内存够用的时候，这2个值都是0，如果这2个值长期大于0时，系统性能会受到影响，磁盘IO和CPU资源都会被消耗。有些朋友看到空闲内存（free）很少的或接近于0时，就认为内存不够用了，不能光看这一点，还要结合si和so，如果free很少，但是si和so也很少（大多时候是0），那么不用担心，系统性能这时不会受到影响的。因为linux总是先把内存用光#
IO
bi	每秒读取的块数
bo	每秒写入的块数
#注意：随机磁盘读写的时候，这2个值越大（如超出1024k)，能看到CPU在IO等待的值也会越大。#
system（系统）
in	每秒中断数，包括时钟中断。
cs	每秒上下文切换数。
#注意：上面2个值越大，会看到由内核消耗的CPU时间会越大。#
CPU（以百分比表示）
us	用户进程执行时间百分比(user time) us的值比较高时，说明用户进程消耗的CPU时间多，但是如果长期超50%的使用，那么我们就该考虑优化程序算法或者进行加速。
sy:	内核系统进程执行时间百分比(system time) sy的值高时，说明系统内核消耗的CPU资源多，这并不是良性表现，我们应该检查原因。
wa	IO等待时间百分比 wa的值高时，说明IO等待比较严重，这可能由于磁盘大量作随机访问造成，也有可能磁盘出现瓶颈（块操作）。
id	空闲时间百分比

vmstat 命令说明
vmstat 
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 3  0      0 6409500 107068 3114000    0    0    28   204    2    1  4  1 95  0  0

vmstat –a 显示活跃和非活跃内存，显示增加了inact和active列，
vmstat -a
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free  inact active   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 6404564 1564288 7906164    0    0    28   204    2    1  4  1 95  0  0

 vmstat -s 查看内存使用的详细信息
# vmstat -s
     16266396 K total memory
      6630016 K used memory
      7911380 K active memory
      1569920 K inactive memory
      6393512 K free memory
       107912 K buffer memory
      3134956 K swap cache
            0 K total swap
            0 K used swap
            0 K free swap
     63303097 non-nice user cpu ticks
          874 nice user cpu ticks
     15854910 system cpu ticks
   1602624489 idle cpu ticks
      2913879 IO-wait cpu ticks
            0 IRQ cpu ticks
       259139 softirq cpu ticks
            0 stolen cpu ticks
    475729903 pages paged in
   3443010588 pages paged out
            0 pages swapped in
            0 pages swapped out
   1541906602 interrupts
   3663049957 CPU context switches
   1557999201 boot time
     18590338 forks

 vmstat -d 查看磁盘的读/写     
vmstat -d
disk- ------------reads------------ ------------writes----------- -----IO------
       total merged sectors      ms  total merged sectors      ms    cur    sec
vda   4765681 108195 951460566 89014815 18863838 19708386 6886046288 2548559282      0  36110

查看/dev/sda1磁盘的读/写
# vmstat -p /dev/vda1
vda1          reads   read sectors  writes    requested writes
             4765849  951464366   18481572 6886084424
```

1. iostat iostat是I/O statistics（输入/输出统计）的缩写，iostat工具将对系统的磁盘操作活动进行监视。它的特点是汇报磁盘活动统计情况，同时也会汇报出CPU使用情况。iostat也有一个弱点，就是它不能对某个进程进行深入分析，仅对系统的整体情况进行分析
2. mpstat 主要用于多CPU环境下，它显示各个可用CPU的状态系你想。这些信息存放在/proc/stat文件中。在多CPUs系统里，其不但能查看所有CPU的平均状况信息，而且能够查看特定CPU的信息。

```
 mpstat -P ALL
Linux 3.10.0-693.11.6.el7.x86_64 (offline) 	2019年07月04日 	_x86_64_	(8 CPU)

14时08分17秒  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
14时08分17秒  all    5.65    0.00    0.79    0.27    0.00    0.09    0.16    0.00    0.00   93.03
14时08分17秒    0    5.13    0.00    0.78    0.07    0.00    0.08    0.13    0.00    0.00   93.80
14时08分17秒    1    6.26    0.00    0.91    0.12    0.00    0.10    0.26    0.00    0.00   92.35
14时08分17秒    2    6.41    0.00    0.91    0.11    0.00    0.09    0.14    0.00    0.00   92.34
14时08分17秒    3    6.08    0.00    0.88    0.09    0.00    0.09    0.14    0.00    0.00   92.73
14时08分17秒    4    5.72    0.00    0.75    0.08    0.00    0.05    0.13    0.00    0.00   93.26
14时08分17秒    5    5.33    0.00    0.68    0.07    0.00    0.21    0.19    0.00    0.00   93.53
14时08分17秒    6    5.04    0.00    0.67    0.06    0.00    0.09    0.16    0.00    0.00   93.98
14时08分17秒    7    5.21    0.00    0.74    1.60    0.00    0.05    0.13    0.00    0.00   92.28

```

1. netstat 网络信息统计工具
2. strace 测试服务器性能
3. nc 快速构建网络连接
4. lsof 查看当前系统打开的文件描述符
5. tcpdump 网络抓包工具
6. wireshark 网络抓包工具