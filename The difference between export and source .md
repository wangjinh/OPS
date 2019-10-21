
1. shell与export命令
```
    用户登录到Linux系统后，系统将启动一个用户shell。在这个shell中，可以使用shell命令或声明变量，也可以创建并运行shell脚本程序。运行shell脚本程序时，系统将创建一个子shell。此时，系统中将有两个shell，一个是登录时系统启动的shell，另一个是系统为运行脚本程序创建的shell。当一个脚本程序运行完毕，它的脚本shell将终止，可以返回到执行该脚本之前的shell。从这种意义上来说，用户可以有许多 shell，每个shell都是由某个shell（称为父shell）派生的。
 
    在子shell中定义的变量只在子shell内有效。如果在一个shell脚本程序中定义了一个变量，当该脚本程序运行时，这个定义的变量只是该脚本程序内的一个局部变量，其他的shell不能引用它，要使某个变量的值可以在其他shell中被改变，可以使用export命令对已定义的变量进行输出。

export命令将使系统在创建每一个新的shell时定义这个变量的一个拷贝。这个过程称之为变量输出。
 
［例］在本例中，变量myfile是在dispfile脚本程序中定义的。然后用export命令将变量myfile输出至任何子shell，例如当执行printfile脚本程序时产生的子shell。
 
dispfile脚本程序清单：
/**************begin dispfile**************/
myfile=”List”
export myfile
echo “Displaying $myfile”
pr –t –n $myfile
printfile
/**************end dispfile***************/
　
printfile脚本程序清单：
/**************begin printfile**************/
echo “Printing $myfile”
lpr $myfile&
/**************end printfile**************/

$ dispfile
Displaying List
1 screen
2 modem
3 paper
Printing List
$
    而source执行脚本就可以再父sh生效是由于这个命令其实只是简单地读取脚本里面的语句依次在当前shell里面执行，没有建立新的子shell。那么脚本里面所有新建、改变变量的语句都会保存在当前shell里面。
```
2. source命令
```
source命令也称为“点命令”，也就是一个点符号（.）,是bash的内部命令。

功能：使Shell读入指定的Shell程序文件并依次执行文件中的所有语句。source命令通常用于重新执行刚修改的初始化文件，使之立即生效，而不必注销并重新登录。

用法：

source filename 或 . filename
source命令(从 C Shell 而来)是bash shell的内置命令;点命令(.)，就是个点符号(从Bourne Shell而来)是source的另一名称。

source filename 与 sh filename 及./filename执行脚本的区别在那里呢？

1.当shell脚本具有可执行权限时，用sh filename与./filename执行脚本是没有区别得。./filename是因为当前目录没有在PATH中，所有"."是用来表示当前目录的。
2.sh filename 重新建立一个子shell，在子shell中执行脚本里面的语句，该子shell继承父shell的环境变量，但子shell新建的、改变的变量不会被带回父shell，除非使用export。
3.source filename：这个命令其实只是简单地读取脚本里面的语句依次在当前shell里面执行，没有建立新的子shell。那么脚本里面所有新建、改变变量的语句都会保存在当前shell里面。

举例说明：

1.新建一个test.sh脚本，内容为:A=1
2.然后使其可执行chmod +x test.sh 
3.运行sh test.sh后，echo $A，显示为空，因为A=1并未传回给当前shell运行./test.sh后，也是一样的效果、
4.运行source test.sh 或者 . test.sh，然后echo $A，则会显示1，说明A=1的变量在当前shell中
```



