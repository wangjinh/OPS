基础正则
```
^word     ##搜索以word开头的 vi/vim中 ^ 一行的开头
word$     ##搜索以word结尾的 vi/vim中 $ 一行的结尾
^$        ##表示空行
.         ##代表且只能代表任意一个字符
\         ##例：\. 只代表点本身，转义符号，让有特殊身份意义的字符，脱掉马甲，还原
\n        ##换行符
\r        ##匹配回车
\w         ##匹配任意一个字符和数字
*         ##重复0次或多次前面的一个字符
.*        ##匹配所有字符。例：^.* 以任意多个字符开头，.*$以任意多个字符结尾
[abc]     ##匹配字符集内的任意一个字符
[^abc]    ##匹配不包含 ^ 后的任意字符的内容。中括号里的 ^ 为取反
[1-9]     ##表示匹配括号内的范围内的任意字符
a\{n,m\}  ##重复n到m次前一个重复的字符。若用egrep、sed -r可以去掉斜线
\{n,\}    ##重复至少n 次前一个重复的字符。若用egrep、sed -r可以去掉斜线
\{n\}     ##重复n 次前一个重复的字符。若用egrep、sed -r可以去掉斜线
\{,m}\    ##重复少于m次
注：egrep，grep –E或sed –r 过滤一般特殊字符可以不转义
```
扩展正则（egrep或grep -E）
```
+          ##重复一次或一次以上前面的一个字符
?          ##重复0次或一次前面的一个字符
|          ##或者的意思，用或的方式查找多个符合的字符串
()         ##找出括号内的字符串

^linux     ##以linux开始
linux$     ##以linux结束
linuxfan.   ##匹配linuxfans等
coo[kl]    ##匹配cool或cook
9[^5689]    ##匹配91,92等，但不匹配95,96,98,99
[0-9]     ##匹配任意一个所有的数字
[a-z]|[A-Z]  ##匹配任意一个所有大小写字母,|属于扩展正则grep -E支持
colou?r    ##匹配color或colour，但是不能匹配colouur
rollno-9+   ##匹配rollno-9、rollno-99，rollno-999，但不匹配rollno-
co*l      ##匹配cl，col，cool，coool等
ma(tri)x    ##匹配matrix
[0-9]{3}    ##匹配任意一个三位数，等于[0-9][0-9][0-9]
[0-9]{2,}   ##匹配任意一个两位数或更多位的数字
[0-9]{2,5}   ##匹配从两位数到五位数之间的任意一个数字
Oct (1st|2nd) ##匹配Oct 1st或Oct 2nd
a\.b      ##匹配a.b，但不能匹配ajb
[a-z0-9_]+\@[a-z0-9_]+\.[a-z]{2,4}       ##匹配一个邮箱地址
[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3} ##匹配IP地址。
```
BRE与ERE元字符对应表
```
基本正则表达式	扩展正则表达式
\?	   			  	 ?
\+	   			  	 +
\|	   			 	 |
\{ \}				{ }
\( \)				( )
```
grep

```
语法：grep [选项] [条件表达式] 目标文件

cat /proc/meminfo |grep -e Mem -e Cache -e Swap     ##查看系统内存、缓存、交换分区-e的作用是匹配多个表达式

grep -R -o -n -E  '[a-z0-9_]+\@[a-z0-9_]+\.[a-z]{2,4}' /etc/     ##查找/etc目录下的所有文件中的邮件地址；-R递归，-n表示匹配的行号，-o只输出匹配内容，-E支持扩展正则表达式，

grep -R -c 'HOSTNAME' /etc/ |grep -v "0$"     ##查找/etc/目录下文件中包含“HOSTNAME”的次数，-c统计匹配次数，-v取反

grep -R -l 'HOSTNAME' /etc/           ##查找包含“HOSTNAME”的文件名，-l显示匹配的文件名，-L显示不匹配的文件名

dmesg | grep -n --color=auto 'eth'       ##查找内核日志中eth的行，显示颜色及行号
dmesg | grep -n -A3 -B2 --color=auto 'eth'     ##用 dmesg 列出核心信息，再以 grep 找出内含 eth 那行,在关键字所在行的前两行与后三行也一起找出出来显示

cat /etc/passwd |grep -c bash$         ##统计系统中能登录的用户的个数

touch /tmp/{123,123123,456,1234567}.txt   ##创建测试文件，以下三条命令是一样的效果，匹配文件名123，可以包含1个到多个
ls |grep -E '(123)+'
ls |grep '\(123\)\+'
ls |egrep '(123)+'

ps -ef |grep -c httpd             ##统计httpd进程数量
grep -C 4 'games' --color /etc/passwd       ##显示games匹配的“-C”前后4行
grep ^adm /etc/group             ##查看adm组的信息
ip a |grep -E '^[0-9]' |awk -F : '{print $2}'     ##获取网卡名称
ifconfig eth0 |grep -E -o 'inet addr:[^ ]*' |grep -o '[0-9.]*'   ##截取ip地址，[^ ]*表示以非空字符作为结束符，[0-9.]*表示数字和点的组合
ip a |grep inet |grep eth0 |grep -o "inet[^/]*" |grep -o "[0-9.]*"  ##截取ip地址
ifconfig eth0 |grep -i hwaddr |awk '{print $5}'   ##截取MAC地址
ip a |grep -A 3 "eth0" |grep link/ether |grep -o "ether[^r]*" |grep -o -E "[0-9a-f:]+"|grep -E "[0-9a-f:]{2}$"      ##截取MAC地址
 
grep "^m" oldboy.log       ##过滤输出以m开头的行
grep "m$" oldboy.log 
grep -vn "^$" oldboy.log       ##过滤空行
grep -o "0*" oldboy.log 
grep -o "oldb.y" oldboy.log 
grep "\.$" oldboy.log       ##以.结尾的行
grep "0\{3\}" oldboy.log       ##重复三次
```
sed

```
语法：sed [options] 'command' file(s) 
选项：
-n 抑制自动打印pattern space，sed默认输出全部，-n用于取消默认输出
-i 编辑文件
-r 支持扩展正则表达式

1.改：

语法：sed '/正则匹配条件/s/old/new/g' 文件
sed 's/dhcp/static/g' /etc/sysconfig/network-scripts/ifcfg-eth1 ##只是显示，不修改
sed -i 's/dhcp/static/g' /etc/sysconfig/network-scripts/ifcfg-eth1 ##只修改，不显示
sed -i 's/dhcp/static/g' ip ##将所有的dhcp替换为static
sed -i '/^IP1/s/static/dhcp/g' ip ##将IP1开头的行替换
sed -i '2s/static/dhcp/g' ip ##指定特定行号2行替换
cat -n /etc/selinux/config ##查看并显示行号
sed -i '7s/disabled/enforcing/g' /etc/selinux/config ##修改第7行，disabled为enforcing，开启selinux

2.删：

语法：sed '/表达式/d' 文件
vim ip ##添加空行
sed '/^$/d' ip ##删除空行并显示在屏幕上
sed -i '/IP1/d' ip ##删除包含IP1的行
sed -i '/^IP2/d' ip ##删除以IP2开头的行
sed -i '2d' ip ##删除第二行

3.增：

语法：sed ' /表达式/a "需要添加的文字"' 文件
sed 'a IP3=static' ip ##每一行后都加上IP3=static
sed '3a IP3=static' ip ##只在第3行后加上IP3=static，并显示不修改
sed '3i IP3=static' ip ##只在第3行前加上IP3=static，显示不修改
sed -i '3a IP3=static' ip ##修改，不显示
sed -i '/^IP3/a "test add"' ip ##在以IP3开头的行后添加

4.查：

语法：sed -n '/表达式/p' 文件
sed -n '2p' /etc/hosts ##查看第二行
sed -n '/www/p' /var/named/chroot/var/named/linuxfan.cn.zone ##查看包含www的解析记录
sed -n '/.100$/p' /var/named/chroot/var/named/linuxfan.cn.zone ##查看以.100结尾的行
sed -n '2~2p' ip ##从第二行，每隔两行显示

ifconfig eth0|sed -n '2p'|sed 's#.*dr:##g'|sed 's# Bc.*##g'    ##注：当sed命令处理的内容为多行内容，则以/作为表达式的分隔，若sed命令处理的内容为单行内容，作为截取的作用，以#号作为分隔符；
10.0.0.9 
ifconfig eth0|sed -n '2p'|sed -r 's#(.*dr:)(.*)(Bc.*$)#\2#g'    ##-r支持扩展正则，\2将2转义，打印出第二个范围(.*)
10.0.0.9 
ifconfig eth0|sed -n '2p'|sed -r 's#.*dr:(.*) Bc.*$#\1#g'
10.0.0.9 
ifconfig eth0|sed -nr '2s#^.*dr:(.*) Bc.*$#\1#gp'
10.0.0.9
ifconfig eth0|sed -nr '1s#^.*dr (.*)#\1#gp'
00:0C:29:33:C8:75 
ifconfig eth0|sed -n '1p'|sed -r 's#(^.*dr )(.*)#\2#g'
00:0C:29:33:C8:75
ifconfig eth0|sed -n '1p'|sed 's#^.*dr ##g'     
00:0C:29:33:C8:75
ifconfig eth0|sed -nr '1s#^.*t (.*) 00.*$#\1#gp'
HWaddr
stat /etc/hosts|sed -n '4p'               
Access: (0644/-rw-r--r--) Uid: (  0/  root)  Gid: (  0/  root)
stat /etc/hosts|sed -n '4p'|sed 's#^.*ss: (##g'|sed 's#/-.*$##g'
0644
stat /etc/hosts|sed -n '4p'|sed -r 's#^.*s: \((.*)/-.*$#\1#g'
0644
stat /etc/hosts|sed -nr '4s#^.*s: \((.*)/-.*$#\1#gp'
0644
stat /etc/hosts|sed -nr '4s#(^.*s: \()(.*)(/-.*$)#\2#gp'
0644

echo eth0 | sed -nr 's#(.*)\..*#\1#p'
注释：
-n  ##读入下一输入行，并从下一条命令开始处理
-r  ##从文件中读取输入行，支持扩展正则表达式。
s   ##尝试根据模式空间匹配扩展正则表达式；
#   ##作为分隔符；
.*  ##匹配所有字符。例：^.*以任意多个字符开头, .*$以任意多个字符结尾
\.  ##例：\.只代表点本身，转义符号，让有特殊身份意义的字符，还原
(.*) ##为第一个域,匹配所有字符
\1  ##截取符合第一个域正则的数据
p   ##打印行
注：当sed命令处理的内容为多行内容，则以/作为表达式的分隔，若sed命令处理的内容为单行内容，作为截取的作用，以#号作为分隔符；

sed -nr s'/^ *([0-9]{1,}).*/\1/' 

s #这个不用说了吧
^ * #按照文本的格式,匹配前面有任意数量空格的字符.
\([0-9]\{1,\}\) #为第一个域,匹配至少有1个数字
.* #\1正则匹配后面的字符
\1 #截取符合第一个域正则的数据
```
awk

```
语法：
awk [选项] '模式{动作（action）}' 文件1 文件2 ...
选项： -F   指定输入分隔符，可以是字符串或正则表达式
常用动作： print、printf

chkconfig --list |grep 3:启用 |awk '{print $1}'
tail -1 /etc/passwd |awk -F ':' 'BEGIN{OFS="---"}{print $1,$6,$7}'  ##OFS指定输出分隔符
ifconfig eth1 |awk -F '[ :]+' 'NR==2 {print $4}'
ifconfig eth1 |awk -F '[ :]+' 'NR==2 {print "eth1_ip="$4}' ##可以加入显示内容
awk 'BEGIN {print "line one \nline two\nline three"}'  ## \n 表示换行符
匹配范围（ranges）：指定的匹配范围，格式为part1,part2

awk -F : '$3==3,$3==10{print $1,$3,$7}' /etc/passwd
awk -F : '$1=="root",$1=="adm"{print $1,$3,$7}' /etc/passwd
awk -F : '/^r/,/^a/{print $1,$3,$7}' /etc/passwd

awk区块原理：

区域构成： 
BEGIN { 动作 } ##开始处理第一行文本之前的操作
{ 动作 } ##针对每一行文本的处理操作
END { 动作 } ##处理完最后一行文本之后的操作

执行流程： 
 首先执行 BEGIN { } 区块中的初始化操作；
 然后从指定的数据文件中循环读取一个数据行（自动更新 NF、 NR、 $0、 $1…… 等内建变量的值），并执行'模式或条件{ 动作 }'；
 最后执行 END { } 区块中的后续处理操作。

案例：
awk -F : 'BEGIN{printf "%-10s%-10s%-20s\n","UserName","ID","Shell"}{printf "%-10s%-10s%-20s\n",$1,$3,$7}' /etc/passwd ##在awk处理之前打印头部BEGIN{}

ifconfig eth0 |awk -F':' 'NR==2{print $2,$4}'|awk 'BEGIN{OFS=" / "}{print "IP="$1,"MASK="$3}'
ifconfig ens192 |awk -F 'inet | netmask' 'NR==2{print $2,$4}'|awk 'BEGIN{OFS=" / "}{print "IP="$1,"MASK="$3}'

awk的变量：

	awk变量：

	FS：列分隔符，默认位空白
	RS：行分隔符，默认位换行符
	OFS：输出列分隔符
	ORS：输出行分隔符

	awk内置变量：

	NR：处理中所指定第几行数
	FNR：单个文件的行数
	NF：列的个数
案例：
ifconfig eth1 |awk '{print NR}'
ifconfig eth1 |awk '{print NF}'

自定义变量案例：
awk 'BEGIN{test="www.linuxfan.cn";print test}'
awk -v test="linuxfan.cn" 'BEGIN{print test}'

printf的使用：

格式：printf "格式",列表1,列表2 ...

	特征：
	a.必须指定format(格式)，用于指定后面item（列表）的输出格式
	b.printf语句不会自动打印换行符：\n
	c.format格式以%加一个字符，如下：
	%c:显示字符的ASCII码
	%d，%i:十进制整数
	%f:显示浮点数（小数）
	%s:显示字符串
	%u:无符号整数
	%%:显示%

d.修饰符：N:显示宽度，-：左对齐，+：显示数值符号，如%-c(左对齐)

案例：
chkconfig --list |grep 3:启用 |awk '{printf "%-10s",$1}' ##在统一行显示
awk -F : '{printf "%-15s %-10d %-10s\n",$1,$3,$7}' /etc/passwd

awk的操作符：

算数操作符： x^y、x/y、x+y、x-y、x%y
比较操作符：>、<、>=、<=、==、!=
逻辑操作符：&&、||、！

awk常见的模式类型

正则表达式（regexp）：awk -F : '/^u/{print $1}' /etc/passwd

表达式（expression）：值位非0或位非空是满足条件，如$1 ~ /foo/或 $1 == "root"
案例：
awk -F : '$3>=500{print $1,$3,$7}' /etc/passwd ##打印普通用户
awk -F : '$3+1<=100&&$3+1>=10{print $1,$3,$7}' /etc/passwd ##UID在10-100之间的用户
awk -F : '$2=="!!"{print $1,$2}' /etc/shadow ##检查未初始化密码的用户
passwd -d u01 
awk -F : '$2==""{print $1}' /etc/shadow ##打印密码为空的用户
awk -F : '$7~"bash$"{print $1,$3,$7}' /etc/passwd ##匹配$7为bash结束行
awk -F : '$7!~"bash$"{print $1,$3,$7}' /etc/passwd
```