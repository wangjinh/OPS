linux系统分析相关的命令行工具

pmap命令
```
1、pmap命令用于报告进程的内存映射关系

pmap(选项)(参数)

选项
	-x：显示扩展格式；
	-d：显示设备格式；
	-q：不显示头尾行；
	-V：显示指定版本。

实例：

# pidof nginx
13312 5371

然后列出某个进程(PID:5633)的信息时如下：
# pmap -x 5371
5371:   nginx: worker process                
Address           Kbytes     RSS   Dirty Mode   Mapping
0000000000400000     564     344       0 r-x--  nginx
000000000068c000      68      68      60 rw---  nginx
000000000069d000      56      12      12 rw---    [ anon ]
000000000a0c8000    1812    1684    1684 rw---    [ anon ]
0000003ac0a00000     112      40       0 r-x--  ld-2.5.so
0000003ac0c1c000       4       4       4 r----  ld-2.5.so
0000003ac0c1d000       4       4       4 rw---  ld-2.5.so
0000003ac0e00000    1340     284       0 r-x--  libc-2.5.so
0000003ac0f4f000    2044       0       0 -----  libc-2.5.so
0000003ac114e000      16      16       8 r----  libc-2.5.so

以上列表项的含义如下:
Address: 内存分配地址
Kbytes:   实际分配的内存大小
RSS:       程序实际占用的内存大小
Mapping: 分配该内存的模块的名称
```
2、top命令
```
top命令是Linux下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况。下面详细介绍它的使用方法。

top - 01:06:48 up 1:22, 1 user, load average: 0.06, 0.60, 0.48
Tasks: 29 total, 1 running, 28 sleeping, 0 stopped, 0 zombie
Cpu(s): 0.3% us, 1.0% sy, 0.0% ni, 98.7% id, 0.0% wa, 0.0% hi, 0.0% si
Mem: 191272k total, 173656k used, 17616k free, 22052k buffers
Swap: 192772k total, 0k used, 192772k free, 123988k cached

PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND
1379 root 16 0 7976 2456 1980 S 0.7 1.3 0:11.03 sshd
14704 root 16 0 2128 980 796 R 0.7 0.5 0:02.72 top
1 root 16 0 1992 632 544 S 0.0 0.3 0:00.90 init
2 root 34 19 0 0 0 S 0.0 0.0 0:00.00 ksoftirqd/0
3 root RT 0 0 0 0 S 0.0 0.0 0:00.00 watchdog/0

统计信息区
前五行是系统整体的统计信息。第一行是任务队列信息，同 uptime 命令的执行结果。其内容如下：

01:06:48 当前时间
up 1:22 系统运行时间，格式为时:分
1 user 当前登录用户数
load average: 0.06, 0.60, 0.48 系统负载，即任务队列的平均长度。
三个数值分别为 1分钟、5分钟、15分钟前到现在的平均值。

第二、三行为进程和CPU的信息。当有多个CPU时，这些内容可能会超过两行。内容如下：

Tasks: 29 total 进程总数
1 running 正在运行的进程数
28 sleeping 睡眠的进程数
0 stopped 停止的进程数
0 zombie 僵尸进程数

Cpu(s): 0.3% us 用户空间占用CPU百分比
1.0% sy 内核空间占用CPU百分比
0.0% ni 用户进程空间内改变过优先级的进程占用CPU百分比
98.7% id 空闲CPU百分比
0.0% wa 等待输入输出的CPU时间百分比
0.0% hi
0.0% si

最后两行为内存信息。内容如下：

Mem: 191272k total 物理内存总量
173656k used 使用的物理内存总量
17616k free 空闲内存总量
22052k buffers 用作内核缓存的内存量

Swap: 192772k total 交换区总量
0k used 使用的交换区总量
192772k free 空闲交换区总量
123988k cached 缓冲的交换区总量。
内存中的内容被换出到交换区，而后又被换入到内存，但使用过的交换区尚未被覆盖，
该数值即为这些内容已存在于内存中的交换区的大小。
相应的内存再次被换出时可不必再对交换区写入。

进程信息区

统计信息区域的下方显示了各个进程的详细信息。首先来认识一下各列的含义。

序号 列名 含义
a    PID 进程id
b    PPID 父进程id
c    RUSER Real user name
d    UID 进程所有者的用户id
e    USER 进程所有者的用户名
f    GROUP 进程所有者的组名
g    TTY 启动进程的终端名。不是从终端启动的进程则显示为 ?
h    PR 优先级
i    NI nice值。负值表示高优先级，正值表示低优先级
j    P 最后使用的CPU，仅在多CPU环境下有意义
k    %CPU 上次更新到现在的CPU时间占用百分比
l    TIME 进程使用的CPU时间总计，单位秒
m    TIME+ 进程使用的CPU时间总计，单位1/100秒
n    %MEM 进程使用的物理内存百分比
o    VIRT 进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES
p    SWAP 进程使用的虚拟内存中，被换出的大小，单位kb。
q    RES 进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA
r    CODE 可执行代码占用的物理内存大小，单位kb
s    DATA 可执行代码以外的部分(数据段+栈)占用的物理内存大小，单位kb
t    SHR 共享内存大小，单位kb
u    nFLT 页面错误次数
v    nDRT 最后一次写入到现在，被修改过的页面数。
w    S 进程状态。
D   =不可中断的睡眠状态
R   =运行
S   =睡眠
T   =跟踪/停止
Z   =僵尸进程
x   COMMAND 命令名/命令行
y   WCHAN 若该进程在睡眠，则显示睡眠中的系统函数名
z   Flags 任务标志，参考 sched.h

默认情况下仅显示比较重要的 PID、USER、PR、NI、VIRT、RES、SHR、S、%CPU、%MEM、TIME+、COMMAND 列。
```
3、vmstat命令详解
```
vmstat命令是最常见的Linux/Unix监控工具，可以展现给定时间间隔的服务器的状态值,包括服务器的CPU使用率，内存使用，虚拟内存交换情况,IO读写情况。这个命令是我查看Linux/Unix最喜爱的命令，一个是Linux/Unix都支持，二是相比top，我可以看到整个机器的CPU,内存,IO的使用情况，而不是单单看到各个进程的CPU使用率和内存使用率

一般vmstat工具的使用是通过两个数字参数来完成的，第一个参数是采样的时间间隔数，单位是秒，第二个参数是采样的次数，如:

root@ubuntu:~# vmstat 2 1
procs -----------memory---------- ---swap-- -----io---- -system-- ----cpu----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
 1  0      0 3498472 315836 3819540    0    0     0     1    2    0  0  0 100  0
2表示每个两秒采集一次服务器状态，1表示只采集一次。 可以根据自己实际情况去调整参数查看服务器状态。

参数含义：

r 表示运行队列(就是说多少个进程真的分配到CPU)，我测试的服务器目前CPU比较空闲，没什么程序在跑，当这个值超过了CPU数目，就会出现CPU瓶颈了。这个也和top的负载有关系，一般负载超过了3就比较高，超过了5就高，超过了10就不正常了，服务器的状态很危险。top的负载类似每秒的运行队列。如果运行队列过大，表示你的CPU很繁忙，一般会造成CPU使用率很高。
b 表示阻塞的进程,进程阻塞。
swpd 虚拟内存已使用的大小，如果大于0，表示你的机器物理内存不足了，如果不是程序内存泄露的原因，那么你该升级内存了或者把耗内存的任务迁移到其他机器。
free   空闲的物理内存的大小，我的机器内存总共8G，剩余3415M。
buff   Linux/Unix系统是用来存储，目录里面有什么内容，权限等的缓存，我本机大概占用300多M
cache cache直接用来记忆我们打开的文件,给文件做缓冲，我本机大概占用300多M(这里是Linux/Unix的聪明之处，把空闲的物理内存的一部分拿来做文件和目录的缓存，是为了提高 程序执行的性能，当程序使用内存时，buffer/cached会很快地被使用。)
si  每秒从磁盘读入虚拟内存的大小，如果这个值大于0，表示物理内存不够用或者内存泄露了，要查找耗内存进程解决掉。我的机器内存充裕，一切正常。
so  每秒虚拟内存写入磁盘的大小，如果这个值大于0，同上。
bi  块设备每秒接收的块数量，这里的块设备是指系统上所有的磁盘和其他块设备，默认块大小是1024byte，我本机上没什么IO操作，所以一直是0，但是我曾在处理拷贝大量数据(2-3T)的机器上看过可以达到140000/s，磁盘写入速度差不多140M每秒
bo 块设备每秒发送的块数量，例如我们读取文件，bo就要大于0。bi和bo一般都要接近0，不然就是IO过于频繁，需要调整。
in 每秒CPU的中断次数，包括时间中断
cs 每秒上下文切换次数，例如我们调用系统函数，就要进行上下文切换，线程的切换，也要进程上下文切换，这个值要越小越好，太大了，要考虑调低线程或者进程的数目,例如在apache和nginx这种web服务器中，我们一般做性能测试时会进行几千并发甚至几万并发的测试，选择web服务器的进程可以由进程或者线程的峰值一直下调，压测，直到cs到一个比较小的值，这个进程和线程数就是比较合适的值了。系统调用也是，每次调用系统函数，我们的代码就会进入内核空间，导致上下文切换，这个是很耗资源，也要尽量避免频繁调用系统函数。上下文切换次数过多表示你的CPU大部分浪费在上下文切换，导致CPU干正经事的时间少了，CPU没有充分利用，是不可取的。
us 用户CPU时间，我曾经在一个做加密解密很频繁的服务器上，可以看到us接近100,r运行队列达到80(机器在做压力测试，性能表现不佳)。
sy 系统CPU时间，如果太高，表示系统调用时间长，例如是IO操作频繁。
id 空闲 CPU时间，一般来说，id + us + sy = 100,一般我认为id是空闲CPU使用率，us是用户CPU使用率，sy是系统CPU使用率。
wt 等待IO CPU时间。
```
4、pidstat 概述
```
pidstat是sysstat工具的一个命令，用于监控全部或指定进程的cpu、内存、线程、设备IO等系统资源的占用情况。pidstat首次运行时显示自系统启动开始的各项统计信息，之后运行pidstat将显示自上次运行该命令以后的统计信息。用户可以通过指定统计的次数和时间来获得所需的统计信	

pidstat [ 选项 ] [ <时间间隔> ] [ <次数> ]

选项：
	-u：默认的参数，显示各个进程的cpu使用统计
	-r：显示各个进程的内存使用统计
	-d：显示各个进程的IO使用情况
	-p：指定进程号
	-w：显示每个进程的上下文切换情况
	-t：显示选择任务的线程的统计信息外的额外信息


[root@node5 ~]# pidstat -u -p ALL
Linux 3.10.0-862.el7.x86_64 (node5)     10/17/2019      _x86_64_        (2 CPU)

03:42:15 PM   UID       PID    %usr %system  %guest    %CPU   CPU  Command
03:42:15 PM     0         1    0.00    0.00    0.00    0.00     1  systemd
03:42:15 PM     0         2    0.00    0.00    0.00    0.00     0  kthreadd
03:42:15 PM     0         3    0.00    0.00    0.00    0.00     0  ksoftirqd/0
03:42:15 PM     0         5    0.00    0.00    0.00    0.00     0  kworker/0:0H
03:42:15 PM     0         6    0.00    0.02    0.00    0.02     1  kworker/u4:0
03:42:15 PM     0         7    0.00    0.00    0.00    0.00     0  migration/0
03:42:15 PM     0         8    0.00    0.00    0.00    0.00     0  rcu_bh
03:42:15 PM     0         9    0.00    0.01    0.00    0.01     0  rcu_sched
03:42:15 PM     0        10    0.00    0.00    0.00    0.00     0  lru-add-drain
03:42:15 PM     0        11    0.00    0.00    0.00    0.00     0  watchdog/0
03:42:15 PM     0        12    0.00    0.00    0.00    0.00     1  watchdog/1
03:42:15 PM     0        13    0.00    0.00    0.00    0.00     1  migration/1
03:42:15 PM     0        14    0.00    0.00    0.00    0.00     1  ksoftirqd/1
03:42:15 PM     0        16    0.00    0.00    0.00    0.00     1  kworker/1:0H
03:42:15 PM     0        18    0.00    0.00    0.00    0.00     1  kdevtmpfs
03:42:15 PM     0        19    0.00    0.00    0.00    0.00     1  netns
03:42:15 PM     0        20    0.00    0.00    0.00    0.00     0  khungtaskd
03:42:15 PM     0        21    0.00    0.00    0.00    0.00     1  writeback
03:42:15 PM     0        22    0.00    0.00    0.00    0.00     1  kintegrityd
03:42:15 PM     0        23    0.00    0.00    0.00    0.00     1  bioset

说明:
PID：进程ID
%usr：进程在用户空间占用cpu的百分比
%system：进程在内核空间占用cpu的百分比
%guest：进程在虚拟机占用cpu的百分比
%CPU：进程占用cpu的百分比
CPU：处理进程的cpu编号
Command：当前进程对应的命令
```
5、lsof命令性能监测与优化
```
lsof命令用于查看你进程开打的文件，打开文件的进程，进程打开的端口(TCP、UDP)。找回/恢复删除的文件。是十分方便的系统监视工具，因为lsof命令需要访问核心内存和各种文件，所以需要root用户执行。

lsof (选项)

选项：
	-a：列出打开文件存在的进程；
	-i<条件>：列出符合条件的进程。（4、6、协议、:端口、 @ip ）
	-p<进程号>：列出指定进程号所打开的文件；

lsof
command     PID USER   FD      type             DEVICE     SIZE       NODE NAME
init          1 root  cwd       DIR                8,2     4096          2 /
init          1 root  rtd       DIR                8,2     4096          2 /
init          1 root  txt       REG                8,2    43496    6121706 /sbin/init
init          1 root  mem       REG                8,2   143600    7823908 /lib64/ld-2.5.so
init          1 root  mem       REG                8,2  1722304    7823915 /lib64/libc-2.5.so
init          1 root  mem       REG                8,2    23360    7823919 /lib64/libdl-2.5.so
init          1 root  mem       REG                8,2    95464    7824116 /lib64/libselinux.so.1
init          1 root  mem       REG                8,2   247496    7823947 /lib64/libsepol.so.1
init          1 root   10u     FIFO               0,17                1233 /dev/initctl
migration     2 root  cwd       DIR                8,2     4096          2 /

lsof输出各列信息的意义如下：

COMMAND：进程的名称
PID：进程标识符
PPID：父进程标识符（需要指定-R参数）
USER：进程所有者
PGID：进程所属组
FD：文件描述符，应用程序通过文件描述符识别该文件。

文件描述符列表：

cwd：表示current work dirctory，即：应用程序的当前工作目录，这是该应用程序启动的目录，除非它本身对这个目录进行更改
txt：该类型的文件是程序代码，如应用程序二进制文件本身或共享库，如上列表中显示的 /sbin/init 程序
lnn：library references (AIX);
er：FD information error (see NAME column);
jld：jail directory (FreeBSD);
ltx：shared library text (code and data);
mxx ：hex memory-mapped type number xx.
m86：DOS Merge mapped file;
mem：memory-mapped file;
mmap：memory-mapped device;
pd：parent directory;
rtd：root directory;
tr：kernel trace file (OpenBSD);
v86  VP/ix mapped file;
0：表示标准输出
1：表示标准输入
2：表示标准错误

一般在标准输出、标准错误、标准输入后还跟着文件状态模式：

u：表示该文件被打开并处于读取/写入模式。
r：表示该文件被打开并处于只读模式。
w：表示该文件被打开并处于。
空格：表示该文件的状态模式为unknow，且没有锁定。
-：表示该文件的状态模式为unknow，且被锁定。

同时在文件状态模式后面，还跟着相关的锁：

N：for a Solaris NFS lock of unknown type;
r：for read lock on part of the file;
R：for a read lock on the entire file;
w：for a write lock on part of the file;（文件的部分写锁）
W：for a write lock on the entire file;（整个文件的写锁）
u：for a read and write lock of any length;
U：for a lock of unknown type;
x：for an SCO OpenServer Xenix lock on part      of the file;
X：for an SCO OpenServer Xenix lock on the      entire file;
space：if there is no lock.

文件类型：

DIR：表示目录。
CHR：表示字符类型。
BLK：块设备类型。
UNIX： UNIX 域套接字。
FIFO：先进先出 (FIFO) 队列。
IPv4：网际协议 (IP) 套接字。
DEVICE：指定磁盘的名称
SIZE：文件的大小
NODE：索引节点（文件在磁盘上的标识）
NAME：打开文件的确切名称
```
