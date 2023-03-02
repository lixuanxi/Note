# 1. 常用文件管理命令

## 1.1 目录

根目录：			 /

常见文件夹 ：	bin(常用可执行文件命令) etc(配置) var(log日志) lib(安装包，头文件) home(用户家目录) proc(进程相关信息)

绝对路径：		从根目录开始描述     /home/acs/xx  开头是斜杠

相对路径：		从当前目录开始描述  acs/xx              开头不是斜杠

当前目录：		 .

上级目录：		 ..

家目录：			 ~     

```c++
pwd                //显示当前路径
```

**Linux 系统目录：**

- bin：存放二进制可执行文件
- boot：存放开机启动程序
- dev：存放设备文件： 字符设备、块设备
- home：存放普通用户
- etc：用户信息和系统配置文件 passwd、group
- lib：库文件：libc.so.6
- root：管理员宿主目录（家目录）
- usr：用户资源管理目录 unix software resource



## 1.2 快捷键

```c++
ctrl c  		   //取消命令，并且换行
ctrl u 			   //清空本行命令
ctrl l 			   //清屏
tab				   //可以补全命令和文件名，如果补全不了快速按两下tab键，可以显示备选选项
```

 复制文本

  windows/Linux下：Ctrl + insert，Mac下：command + c

粘贴文本

  windows/Linux下：Shift + insert，Mac下：command + v



如何将服务器中的文件整体复制出来？

    退出tmux
    cat filename：展示filename的文件内容
    鼠标选中文本开头的若干字符
    用滚轮滑到文件结尾
    按住Shift，同时鼠标点击文件结尾，此时会选中文件所有内容
    Windows/Linux下，按Ctrl + insert可以复制全文；Mac下，按Command + c可以复制全文。



## 1.3 常用命令

### man命令

`man` 命令 是 Linux 下的帮助指令，通过 `man` 指令可以查看 `Linux` 中的指令帮助、配置文件帮助和编程帮助等信息。

语法： `man`   (选项)  (参数)

```
-a：在所有的man帮助手册中搜索；
-f：等价于whatis指令，显示给定关键字的简短描述信息；
-P：指定内容时使用分页程序；
-M：指定man手册搜索的路径。
```

**参数**

- 数字：指定从哪本 man 手册中搜索帮助；
- 关键字：指定要搜索帮助的关键字。

**数字代表内容**

```
1：用户在shell环境可操作的命令或执行文件；
2：系统内核可调用的函数与工具等
3：一些常用的函数(function)与函数库(library)，大部分为C的函数库(libc)
4：设备文件说明，通常在/dev下的文件
5：配置文件或某些文件格式
6：游戏(games)
7：惯例与协议等，如Linux文件系统，网络协议，ASCII code等说明
8：系统管理员可用的管理命令
9：跟kernel有关的文件
```





### cd命令

`cd`（英文全拼：change directory）命令用于切换当前工作目录。

语法：`cd [dirName目录]`   

```c++
cd /usr/bin		   //跳转到/usr/bin	
cd /			   //跳转到根目录
cd ~			   //跳转到家目录  相当于cd /home/当前用户名
cd ..			   //返回上一级
cd -			   //返回上次的目录 类似windows返回  
```



### ls命令

`ls`（英文全拼：list）其功能是列出指定目录下的内容及其相关属性信息

蓝色的是文件夹，白色的是普通文件，绿色的是可执行文件

默认状态下，ls命令会列出当前目录的内容。而带上参数后，我们可以用ls做更多的事情。

语法：`ls [选项] [文件]` 

```c++
ls -a				//显示所有文件及目录 (包括以“.”开头的隐藏文件) 
ls -l				//使用长格式列出文件及目录信息
ls -lh				//显示所有信息大小单位换成兆
ls -r				//将文件以相反次序显示(默认依英文字母次序) 
ls -t				//根据最后的修改时间排序
ls -S				//根据文件大小排序
```



### cp命令

`cp`（英文全拼：copy）其功能为复制文件或目录。

cp命令可以将多个文件复制到一个具体的文件名或一个已经存在的目录下，也可以同时复制多个文件到一个指定的目录中。

语法：`cp [参数] [文件]`

| 参数 |                 说明                 |
| :--: | :----------------------------------: |
| `-f` | 若目标文件已存在，则会直接覆盖原文件 |
| `-i` |  若目标文件已存在，则会询问是否覆盖  |
| `-r` |          递归复制文件和目录          |

```c++
cp a/tmp.txt b/ 	    //把a目录下的tmp.txt复制到b目录 
cp a/tmp.txt b/tmp2.txt //把a目录下的tmp.txt复制到b目录并命名为tmp2.txt
cp a b -r               //把整个a目录复制到b目录下 后面需要-r
cp a c -r  				//把a目录复制到当前文件夹命名为c
```



### mkdir命令

`mkdir`（英文全拼：make directories），用来创建目录。

默认状态下，如果要创建的目录已经存在，则提示已存在，而不会继续创建目录。 所以在创建目录时，应保证新建的目录与它所在目录下的文件没有重名。 mkdir命令还可以同时创建多个目录。

语法：`mkdir [参数] [目录]`

```c++
-p	                     //递归创建多级目录 
mkdir a 				 //创建目录XXX
mkdir a/b/c -p 			 //创建目录 a/b/c
mkdir dir1 dir2 dir3     //创建目录 dir1 dir2 dir3
```



### mv命令

`mv`（英文全拼：move），用来移动文件或对其改名。

mv与cp的结果不同。mv命令好像文件“搬家”，文件名称发生改变，但个数并未增加。而cp命令是对文件进行复制操作，文件个数是有增加的。

语法：`mv [文件] [目的地]` 

```c++
mv file_1 file_2		 //将文件file_1重命名为file_2
mv file /dir      		 //将文件file移动到目录dir
mv /dir1/* .			 //将目录dir1下所有文件移动到当前目录下
```



### rm命令

`rm`命令的功能为删除一个目录中的一个或多个文件或目录，它也可以将某个目录及其下的所有文件及子目录均删除

rm也是一个危险的命令，使用的时候要特别当心 

在/（根目录）下执行rm * -rf）会毁掉整个系统！！！

语法：`rm [参数] [文件]` 

| 参数 |                说明                |
| :--: | :--------------------------------: |
|  -f  | 忽略不存在的文件，不会出现警告信息 |
|  -i  |      删除前会询问用户是否操作      |
| -r/R |              递归删除              |

```c++
rm XXX					  //删除普通文件  
rm XXX -r 				  //删除文件夹
支持正则表达
rm *.txt				  //删除所有txt文件
rm a/*                    //删除文件夹a内所有文件
```



### touch命令与cat命令

```C++
touch XXX				  //创建一个文件
cat XXX					  //展示文件XXX中的内容
```



### chmod命令

`chmod`（英文全拼：change the permissions mode of a file），简称为“change mode”，意为用来改变文件或目录权限的命令，但是只有文件的属主和超级用户root才能执行这个命令。有两种模式，一种是采用权限字母和操作符表达式；另一种是采用数字。 

Linux/Unix 的文件调用权限分为三级 : 文件所有者（Owner）、用户组（Group）、其它用户（Other Users）。

r=READ   w=Write  x=Execute

   -or d          r w x           r w x          r w x

File Type	 Owner	   Group    Other Users

只有文件所有者和超级用户可以修改文件或目录的权限。可以使用绝对模式（八进制数字模式），符号模式指定文件的权限。

r w x = 4 2 1   	eg:7 5 4 → u=rwx,g=rx,o-r

语法：`chmod [-cfvR] [--help] [--version] mode file...`	



参数说明：  mode：权限设定字串，格式：`[ugoa...][[+-=][rwxX]...][,...]`

其中：

- u 表示该文件的拥有者，g 表示与该文件的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示all三者皆是。
- \+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
- r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该文件是个子目录或者该文件已经被设定过为可执行。



|   参数    |                             说明                             |
| :-------: | :----------------------------------------------------------: |
|    -c     |          若该文件权限确实已经更改，才显示其更改动作          |
|    -f     |            若该文件权限无法被更改也不显示错误讯息            |
|    -v     |                    显示权限变更的详细资料                    |
|    -R     | 对目前目录下的所有文件与子目录进行相同的权限变更(即以递回的方式逐个变更) |
|  --help   |                         显示辅助说明                         |
| --version |                           显示版本                           |

```c++
chmod a+r file.txt			//将档案 file1.txt 设为所有人皆可读取
chmod -R a+r *				//将目前目录下的所有文件与子目录皆设为任何人可读取 
chmod u+x file.txt			//将file.txt设定为只有该文件拥有者可以执行
chmod +x test.sh  			//使脚本具有可执行权限 与chmod a+x等效
chmod 777 file				//chmod a=rwx file等效
```



### journalctl命令

journalctl（英文全拼：journal  control），其功能是用于查看指定的日志信息。在RHEL7/CentOS7及以后版本的Linux系统中，Systemd服务统一管理了所有服务的启动日志，带来的好处就是可以只用journalctl一个命令，查看到全部的日志信息了。

语法：`journalctl [文件]` 

|     参数     |                说明                |
| :----------: | :--------------------------------: |
|      -k      |            查看内核日志            |
|      -b      |       查看系统本次启动的日志       |
|      -u      |         查看指定服务的日志         |
|  -n [数目]   |            指定日志条数            |
|      -f      |   持续追踪最新日志，保持刷新内容   |
| --disk-usage | 查看当前日志占用磁盘的空间的总大小 |





## 1.4 **其他命令**

​	[Ctrl跳转](#8.3 常用命令)



------



# 2. tmux

## 2.1 介绍

功能：

​    (1) 分屏。

​    (2) 允许断开Terminal连接后，继续运行进程。

  结构：

​	 一个tmux可以包含多个session，一个session可以包含多个window，一个window可以包含多个pane。

​																	    tmux 

​									 session 0												     session 1

​			    window 0  			   window 1					   window 0  		        window 1

​			pane0 	pane1	  pane0 	pane1			pane0 	pane1		pane0 	 pane1



## 2.2 启动操作

​		新建一个session，其中包含一个window，window中包含一个pane，pane里打开了一个shell对话框。

```c++
tmux 					//新建session
tmux  a					//打开之前挂起的session
```

```c++
tmux ls					//获取target-session
tmux attach -t 2		//进入target-session为20的窗口
tmux kill-session -t targetSession  //关闭指定session
tmux kill-server		//关闭所有sessions
    
```



## 2.3 窗口操作

​	pane间：

```c++
Ctrl + a 松开后 %		  //将当前pane左右平分成两个pane
Ctrl + a 松开后 "		  //将当前pane上下平分成两个pane
Ctrl + d				//关闭当前pane；如果当前window的所有pane均已关闭，则自动关闭window；如果当前session的所有window均已关闭，则自动关闭session
Ctrl + a 松开后 方向键	//选择相邻的pane (或用鼠标点击)
Ctrl + a 同时按 方向键	//调整pane之间分割线的位置(或用鼠标拖动)
Ctrl + a 松开后 z		  //将当前pane全屏/取消全屏
```

​	session间：

```c++
Ctrl + a 松开后 d		  //挂起当前session
Ctrl + a 松开后 s		  //选择其他session          
						//方向键 —— 上：选择上一项 session/window/pane
		            	//方向键 —— 下：选择下一项 session/window/pane
		            	//方向键 —— 右：展开当前项 session/window
            			//方向键 —— 左：闭合当前项 session/window
Ctrl + a 松开后 c		  //在当前session中创建一个新的window
Ctrl + a 松开后 w		  //选择其他window,操作与选择其他session相同
```



## 2.4 文本操作

```c++
Ctrl + a 松开后 PageUp   //翻阅当前pane内的内容 (鼠标滚轮)
//在tmux中选中文本时，需要按住shift键。（仅支持Windows和Linux，不支持Mac，不过该操作并不是必须的，因此影响不大）
tmux中复制/粘贴文本的通用方式：
 (1) Ctrl + a 松开后 [
 (2) 用鼠标选中文本，被选中的文本会被自动复制到tmux的剪贴板
 (3) Ctrl + a 松开后 ]     会将剪贴板中的内容粘贴到光标处
```



------



# 3. Vim

## 3.1 介绍

​	功能：

 (1) 命令行模式下的文本编辑器。

 (2) 根据文件扩展名自动判别编程语言。支持代码缩进、代码高亮等功能。

 (3) 使用方式：vim filename

​			如果已有该文件，则打开它。

​			如果没有该文件，则打开个一个新的文件，并命名为filename

​	模式：

 (1) 一般命令模式

​			默认模式。命令输入方式：类似于打游戏放技能，按不同字符，即可进行不同操作。可以复制、粘贴、删除文本等。

 (2) 编辑模式

​			在一般命令模式里按下i，会进入编辑模式。

​			按下ESC会退出编辑模式，返回到一般命令模式。

 (3) 命令行模式

​    		在一般命令模式里按下:/?三个字母中的任意一个，会进入命令行模式。命令行在最下面。

​    		可以查找、替换、保存、退出、配置编辑器等。



## 3.2 模式切换

```c++
i  				  //进入编辑模式 光标左边
a				  //进入编辑模式 光标右边
ESC/Ctrl + [	  //进入一般命令模式
:/?				  //任意一个进入命令行模式
```



## 3.3 移动操作

```c++
h 或 ←		     //光标向左移动一个字符
j 或 ↓		     //光标向下移动一个字符
k 或 ↑		     //光标向上移动一个字符
l 或 →		     //光标向右移动一个字符
n + <Space>		  //输入数字b后再按空格，光标会向右移动这一行的n个字符
0 或 [Home]  	 //光标移动到本行开头
$ 或 [End]    	 //光标移动到本行末尾
(				  //光标移动到文件开头
)				  //光标移动到文件结尾
gg				  //光标移动到第一行，相当于1G
G				  //光标移动到最后一行
n 或 nG			 //光标移动到第n行 （v选择文本模式下只能用nG）
n + <Enter>		  //光标向下移动n行
```



## 3.4 查找替换

```c++
/word			  			//向光标之下寻找第一个值为word的字符串。
?word			  			//向光标之上寻找第一个值为word的字符串。
n				  			//重复前一个查找操作
N				  			//反向重复前一个查找操作
:n1,n2s/word1/word2/g		//在第n1行与n2行之间寻找word1这个字符串，并将该字符串替换为word2
:1,$s/word1/word2/g			//将全文的word1替换为word2
:1,$s/word1/word2/gc		//将全文的word1替换为word2，且在替换前要求用户确认。
```



## 3.4 文本操作

```C++
v				//选中文本
d				//删除选中的文本
dd				//删除当前行
y				//复制选中的文本
yy				//复制当前行
p				//将复制的数据在光标的下一行/下一个位置粘贴 
		 		//如果复制的整行则 p 表示在当前行下粘贴，P 在当前行上粘贴。
				//如果复制的几个单词则 p 表示在当前光标后粘贴，P 表示在当前光标前粘贴。
>				//将选中的文本整体向右缩进一次
<				//将选中的文本整体向左缩进一次
```



## 3.5 文件操作

```c++
u				//撤销
Ctrl + r		//取消撤销
:w				//保存
:w!				//强制保存
:q				//退出
:q!				//强制退出
:wq				//保存并退出
set paste 		//设置成粘贴模式，取消代码自动缩进
set nopaste 	//取消粘贴模式，开启代码自动缩进
set nu 			//显示行号
set nonu 		//隐藏行号
gg=G			//将全文代码格式化 
ggdG			//将全文删除
:noh 			//关闭查找关键词高亮
Ctrl + q		//当vim卡死时，可以取消当前正在执行的命令
```



## 3.6 异常处理

  每次用vim编辑文件时，会自动创建一个.filename.swp的临时文件。

  如果打开某个文件时，该文件的swp文件已存在，则会报错。此时解决办法有两种：

​    (1) 找到正在打开该文件的程序，并退出

​    (2) 直接删掉该swp文件即可



------



# 4. Shell

## 4.1 介绍

 

shell是我们通过命令行与操作系统沟通的语言。



shell脚本可以直接在命令行中执行，也可以将一套逻辑组织成一个文件，方便复用。

AC Terminal中的命令行可以看成是一个“shell脚本在逐行执行”。

 

Linux中常见的shell脚本有很多种，常见的有：

```shell
  Bourne Shell(/usr/bin/sh或/bin/sh)

  Bourne Again Shell(/bin/bash)

  C Shell(/usr/bin/csh)

  K Shell(/usr/bin/ksh)

  zsh

  …
```



Linux系统中一般默认使用bash

文件开头需要写#! /bin/bash，指明bash为脚本解释器。

学习技巧:

不要死记硬背，遇到含糊不清的地方，可以在AC Terminal里实际运行一遍。



脚本示例

```shell
#! /bin/bash
echo "Hello World!"
```

运行方式：

```shell
1.作为可执行文件
chmod +x test.sh  # 使脚本具有可执行权限
./test.sh  # 当前路径下执行
Hello World!  # 脚本输出
/home/acs/test.sh  # 绝对路径下执行
Hello World!  # 脚本输出
~/test.sh  # 家目录路径下执行
Hello World!  # 脚本输出

2.用解释器执行
bash test.sh
Hello World!  # 脚本输出
```



## 4.2 注释

**单行注释**：每行中 `#` 之后的内容均是注释

```shell
# 这是一行注释
echo 'Hello World'  #  这也是注释
```

**多行注释**

```shell
:<<EOF
第一行注释
第二行注释
第三行注释
EOF
#其中EOF可以换成其它任意字符串
:<<abc
第一行注释
第二行注释
第三行注释
abc
```



## 4.3 变量

**定义变量**

定义变量不需要加 `$` 符号，例如：

```shell
name1='yxc'  # 单引号定义字符串
name2="yxc"  # 双引号定义字符串
name3=yxc    # 也可以不加引号，同样表示字符串
```

**使用变量**

使用变量需要加上 `$` 符号，或者 `${}` 符号。花括号是可选的，主要为了帮助解释器识别变量边界。

```shell
name=yxc
echo $name  # 输出yxc
echo ${name}  # 输出yxc
echo ${name}acwing  # 输出yxcacwing
```

**只读变量**

使用 `readonly` 或者 `declare` 可以将变量变为只读。

```shell
name=yxc
readonly name
declare -r name  # 两种写法均可

name=abc  # 会报错，因为此时name只读
```

**删除变量**

`unset` 可以删除变量。

```shell
name=yxc
unset name
echo $name  # 输出空行
```

**变量类型**

1.自定义变量（局部变量）：子进程不能访问的变量
2.环境变量（全局变量）：子进程可以访问的变量

自定义变量改成环境变量：

```shell
name=yxc  # 定义变量
export name  # 第一种方法
declare -x name  # 第二种方法
```

环境变量改为自定义变量：

```shell
export name=yxc  # 定义环境变量
declare +x name  # 改为自定义变量
```



**字符串**

字符串可以用单引号，也可以用双引号，也可以不用引号。

单引号与双引号的区别：

​	单引号中的内容会原样输出，不会执行、不会取变量；
​	双引号中的内容可以执行、可以取变量；

```shell
name=yxc  # 不用引号
echo 'hello, $name \"hh\"'  # 单引号字符串，输出 hello, $name \"hh\"
echo "hello, $name \"hh\""  # 双引号字符串，输出 hello, yxc "hh"
```

获取字符串长度

```shell
name="yxc"
echo ${#name}  # 输出3
```

提取子串

```shell
name="hello, yxc"
echo ${name:0:5}  # 提取从0开始的5个字符
```



## 4.4 默认变量

**文件参数变量**

在执行shell脚本时，可以向脚本传递参数。$1是第一个参数，$2是第二个参数，以此类推。特殊的，$0是文件名（包含路径）。例如：

创建文件test.sh：

```shell
#! /bin/bash

echo "文件名："$0
echo "第一个参数："$1
echo "第二个参数："$2
echo "第三个参数："$3
echo "第四个参数："$4
```

然后执行该脚本：

```shell
chmod +x test.sh 
 ./test.sh 1 2 3 4
文件名：./test.sh
第一个参数：1
第二个参数：2
第三个参数：3
第四个参数：4
```

**其它参数相关变量**

|     参数     |                             说明                             |
| :----------: | :----------------------------------------------------------: |
|     `$#`     |            代表文件传入的参数个数，如上例中值为4             |
|     `$*`     | `由所有参数构成的用空格隔开的字符串，如上例中值为"$1 $2 $3 $4"` |
|     `$@`     | 每个参数分别用双引号括起来的字符串，如上例中值为"$1" "$2" "$3" "$4" |
|     `$$`     |                     脚本当前运行的进程ID                     |
|     `$?`     | 上一条命令的退出状态（注意不是stdout，而是exit code）。0表示正常退出，其他值表示错误 |
| `$(command)` |            返回command这条命令的stdout（可嵌套）             |
|   command    |           返回command这条命令的stdout（不可嵌套）            |
|              |                                                              |



## 4.5 数组

数组中可以存放多个不同类型的值，只支持一维数组，初始化时不需要指明数组大小。
数组下标从0开始。

**定义**：数组用小括号表示，元素之间用空格隔开。例如：

`array=(1 abc "def" yxc)`

也可以直接定义数组中某个元素的值：

```shell
array[0]=1
array[1]=abc
array[2]="def"
array[3]=yxc
```

**读取数组中某个元素的值**

格式：`${array[index]}`

```shell
array=(1 abc "def" yxc)
echo ${array[0]}
echo ${array[1]}
echo ${array[2]}
echo ${array[3]}
```

**读取整个数组**

格式：`${array[@]}  # 第一种写法 `

​			` ${array[*]}  # 第二种写法`

```shell
array=(1 abc "def" yxc)

echo ${array[@]}  # 第一种写法
echo ${array[*]}  # 第二种写法
```

**数组长度（类似于字符串）**

格式：`${#array[@]}  # 第一种写法`
	  	  `${#array[*]}  # 第二种写法`

```shell
array=(1 abc "def" yxc)

echo ${#array[@]}  # 第一种写法
echo ${#array[*]}  # 第二种写法
```



## 4.6 expr命令

`expr`命令用于求表达式的值，格式为：

```shell
`expr 表达式`
```

表达式说明：

 1. 用空格隔开每一项

 2. 用反斜杠放在shell特定的字符前面（发现表达式运行错误时，可以试试转义）

 3. 对包含空格和其他特殊字符的字符串要用引号括起来

 4. `expr`会在 `stdout` 中输出结果。如果为逻辑关系表达式，则结果为真，`stdout` 为1，否则为0。

 5. `expr` 的 `exit code`：如果为逻辑关系表达式，则结果为真，`exit code `为0，否则为1。

    

**字符串表达式**

 1. `length STRING `：返回STRING的长度 

 2. `index STRING CHARSET`

    `CHARSET` 中任意单个字符在 `STRING` 中最前面的字符位置，下标从1开始。如果在 `STRING` 中完全不存在 `CHARSET` 中的字符，则返回0.

 3. `substr STRING POSITION LENGTH`

    返回 `STRING` 字符串中从 `POSITION` 开始，长度最大为 `LENGTH` 的子串。如果 `POSITION` 或`LENGTH` 为负数，0或非数值，则返回空字符串。

示例：

```shell
str="Hello World!"

echo `expr length "$str"`  # ``不是单引号，表示执行该命令，输出12
echo `expr index "$str" aWd`  # 输出7，下标从1开始
echo `expr substr "$str" 2 3`  # 输出 ell
```



**整数表达式**

`expr` 支持普通的算术操作，算术表达式优先级低于字符串表达式，高于逻辑关系表达式。	

 	1.  `+ -` 		加减运算。两端参数会转换为整数，如果转换失败则报错。
 	2.  `* / %`    乘，除，取模运算。两端参数会转换为整数，如果转换失败则报错。 
 	3. `()`           可以该表优先级，但需要用反斜杠转义

示例：

```shell
a=3
b=4

echo `expr $a + $b`  # 输出7
echo `expr $a - $b`  # 输出-1
echo `expr $a \* $b`  # 输出12，*需要转义
echo `expr $a / $b`  # 输出0，整除
echo `expr $a % $b` # 输出3
echo `expr \( $a + 1 \) \* \( $b + 1 \)`  # 输出20，值为(a + 1) * (b + 1)  ()需要\转义
```



**逻辑关系表达式**

	1. `|`
如果第一个参数非空且非0，则返回第一个参数的值，否则返回第二个参数的值，但要求第二个参数的值也是非空或非0，否则返回0。如果第一个参数是非空或非0时，不会计算第二个参数。

	2. `&`
如果两个参数都非空且非0，则返回第一个参数，否则返回0。如果第一个参为0或为空，则不会计算第二个参数。

	3. `< <= = == != >= >`
	比较两端的参数，如果为true，则返回1，否则返回0。”==”是”=”的同义词。”expr”首先尝试将两端参数转换为整数，并做算术比较，如果转换失败，则按字符集排序规则做字符比较。
	
	3. `()`
	
	可以该表优先级，但需要用反斜杠转义

示例：

```shell
a=3
b=4

echo `expr $a \> $b`  # 输出0，>需要转义
echo `expr $a '<' $b`  # 输出1，也可以将特殊字符用引号引起来
echo `expr $a '>=' $b`  # 输出0
echo `expr $a \<\= $b`  # 输出1

c=0
d=5

echo `expr $c \& $d`  # 输出0
echo `expr $a \& $b`  # 输出3
echo `expr $c \| $d`  # 输出5
echo `expr $a \| $b`  # 输出3
```



## 4.7 read命令

`read` 命令用于从标准输入中读取单行数据。当读到文件结束符时，`exit code` 为1，否则为0。

参数说明

- p:  后面可以接提示信息（用引号圈着）
- -t：后面跟秒数，定义输入字符的等待时间，超过等待时间后会自动忽略此命令

实例：

```shell
acs@9e0ebfcd82d7:~$ read name  # 读入name的值
acwing yxc  # 标准输入
acs@9e0ebfcd82d7:~$ echo $name  # 输出name的值
acwing yxc  #标准输出
acs@9e0ebfcd82d7:~$ read -p "Please input your name: " -t 30 name  # 读入name的值，等待时间30秒
Please input your name: acwing yxc  # 标准输入
acs@9e0ebfcd82d7:~$ echo $name  # 输出name的值
acwing yxc  # 标准输出
```



## 4.8 echo命令

`echo` 用于输出字符串。命令格式：

```shell
echo STRING
```

`man echo` 查找echo的用法

**显示普通字符串**

```shell
echo "Hello AC Terminal"
echo Hello AC Terminal  # 引号可以省略
```

**显示转义字符**

```shell
echo "\"Hello AC Terminal\""  # 注意只能使用双引号，如果使用单引号，则不转义
echo \"Hello AC Terminal\"  # 也可以省略双引号
```

**显示变量**

```shell
name=yxc
echo "My name is $name"  # 输出 My name is yxc
```

**显示换行**

```shell
echo -e "Hi\n"  # -e 开启转义
echo "acwing"
```

输出结果：

```
Hi

acwing
```

**显示不换行**

```sh
echo -e "Hi \c" # -e 开启转义 \c 不换行
echo "acwing"
```

输出结果：

```s
Hi acwing
```

**显示结果定向至文件**

```shell
echo "Hello World" > output.txt  # 将内容以覆盖的方式输出到output.txt中
```

**原样输出字符串，不进行转义或取变量(用单引号)**

```shell
name=acwing
echo '$name\"'
```

输出结果

```
$name\"
```

**显示命令的执行结果**

```shell
echo `date`
```

输出结果：

```
Wed Sep 1 11:45:33 CST 2021
```



## 4.9 printf命令

`printf` 命令用于格式化输出，类似于 `C/C++` 中的 `printf` 函数

默认**不会在字符串末尾添加换行符**。

命令格式：

```
printf format-string [arguments...]
```

**用法示例**

脚本内容：

```shell
printf "%10d.\n" 123  # 占10位，右对齐
printf "%-10.2f.\n" 123.123321  # 占10位，保留2位小数，左对齐
printf "My name is %s\n" "yxc"  # 格式化输出字符串
printf "%d * %d = %d\n"  2 3 `expr 2 \* 3` # 表达式的值作为参数
```

输出结果：

```
       123.
123.12    .
My name is yxc
2 * 3 = 6
```



## 4.10 test命令与判断符号[]

**逻辑运算符&&和||**

1. `&&` 表示与，`||` 表示或、
2. 二者具有短路原则：
   `expr1 && expr2`：当 `expr1` 为假时，直接忽略 `expr2`
   `expr1 || expr2`：当 `expr1` 为真时，直接忽略 `expr2`
3. 表达式的 `exit code` 为0，表示真；为非零，表示假。（与 `C/C++` 中的定义相反）

**test命令**

在命令行中输入 `man test` ，可以查看 `test` 命令的用法。

`test` 命令用于判断文件类型，以及对变量做比较。

`test` 命令用 `exit code` 返回结果，而不是使用 `stdout` 。0表示真，非0表示假。

例如：

```shell
test 2 -lt 3  # 为真，返回值为0 
echo $?  # 输出上个命令的返回值，输出0
```

```shell
acs@9e0ebfcd82d7:~$ ls  # 列出当前目录下的所有文件
homework  output.txt  test.sh  tmp
acs@9e0ebfcd82d7:~$ test -e test.sh && echo "exist" || echo "Not exist"
#tesh.sh存在为真 返回0 0为假 &&执行右边 echo输出存在
exist  # test.sh 文件存在
acs@9e0ebfcd82d7:~$ test -e test2.sh && echo "exist" || echo "Not exist"
#tesh.sh不存在为假 返回非0 非0为真 && 不执行输出存在 所以输出存在为假 最后输出不存在
Not exist  # testh2.sh 文件不存在
```

**文件类型判断**

命令格式：

```shell
test -e filename  # 判断文件是否存在
```

| 测试参数 | 代表意义     |
| -------- | ------------ |
| -e       | 文件是否存在 |
| -f       | 是否为文件   |
| -d       | 是否为目录   |

**文件权限判断**

命令格式：

```shell
test -r filename  # 判断文件是否可读
```

| 测试参数 | 代表意义       |
| -------- | -------------- |
| -r       | 文件是否可读   |
| -w       | 文件是否可写   |
| -x       | 文件是否可执行 |
| -s       | 是否为非空文件 |

**整数间的比较**

命令格式：

```
test $a -eq $b  # a是否等于b
```

| 测试参数 | 代表意义       |
| -------- | -------------- |
| -eq      | a是否等于b     |
| -ne      | a是否不等于b   |
| -gt      | a是否大于b     |
| -lt      | a是否小于b     |
| -ge      | a是否大于等于b |
| -le      | a是否小于等于b |

**字符串比较**

| 测试参数          | 代表意义                                               |
| ----------------- | ------------------------------------------------------ |
| test -z STRING    | 判断STRING是否为空，如果为空，则返回true               |
| test -n STRING    | 判断STRING是否非空，如果非空，则返回true（-n可以省略） |
| test str1 == str2 | 判断str1是否等于str2                                   |
| test str1 != str2 | 判断str1是否不等于str2                                 |

**多重条件判定**

命令格式：

```shell
test -r filename -a -x filename
```

| 测试参数 | 代表意义                                            |
| -------- | --------------------------------------------------- |
| -a       | 两条件是否同时成立                                  |
| -o       | 两条件是否至少一个成立                              |
| !        | 取反。如 test ! -x file，当file不可执行时，返回true |

**判断符号[]**

`[]` 与 `test` 用法几乎一模一样，更常用于 `if` 语句中。另外 `[[]]` 是 `[]` 的加强版，支持的特性更多。

例如：

```shell
[ 2 -lt 3 ]  # 为真，返回值为0
echo $?  # 输出上个命令的返回值，输出0
```

```shell
acs@9e0ebfcd82d7:~$ ls  # 列出当前目录下的所有文件
homework  output.txt  test.sh  tmp
acs@9e0ebfcd82d7:~$ [ -e test.sh ] && echo "exist" || echo "Not exist"
exist  # test.sh 文件存在
acs@9e0ebfcd82d7:~$ [ -e test2.sh ] && echo "exist" || echo "Not exist"
Not exist  # testh2.sh 文件不存在
```

注意：

	1. `[]`内的每一项都要用空格隔开
	1. 中括号内的变量，最好用双引号括起来
	1. 中括号内的常数，最好用单或双引号括起来

例如：

```shell
name="acwing yxc"
[ $name == "acwing yxc" ]  # 错误，等价于 [ acwing yxc == "acwing yxc" ]，参数太多
[ "$name" == "acwing yxc" ]  # 正确
```



## 4.11 判断语句

**if…then形式**

类似于 `C/C++` 中的 `if-else` 语句。

**单层if**

命令格式：

```shell
if condition  
then
    语句1
    语句2
    ...
fi
```

示例：

```shell
a=3
b=4

if [ "$a" -lt "$b" ] && [ "$a" -gt 2 ]  #if后面要命令 不要返回值
then
    echo ${a}在范围内
fi
```

输出结果：

```
3在范围内
```

**单层if-else**

命令格式

```shell
if condition
then
    语句1
    语句2
    ...
else
    语句1
    语句2
    ...
fi
```

示例：

```shell
a=3
b=4

if ! [ "$a" -lt "$b" ]
then
    echo ${a}不小于${b}
else
    echo ${a}小于${b}
fi
```

输出结果：

```
3小于4
```

**多层if-elif-elif-else**

命令格式

```shell
if condition
then
    语句1
    语句2
    ...
elif condition
then
    语句1
    语句2
    ...
elif condition
then
    语句1
    语句2
else
    语句1
    语句2
    ...
fi
```

示例：

```shell
a=4

if [ $a -eq 1 ]
then
    echo ${a}等于1
elif [ $a -eq 2 ]
then
    echo ${a}等于2
elif [ $a -eq 3 ]
then
    echo ${a}等于3
else
    echo 其他
fi
```

输出结果：

```
其他
```

**case…esac形式**

类似于 `C/C++` 中的 `switch` 语句。

命令格式

```shell
case $变量名称 in
    值1)
        语句1
        语句2
        ...
        ;;  # 类似于C/C++中的break
    值2)
        语句1
        语句2
        ...
        ;;
    *)  # 类似于C/C++中的default
        语句1
        语句2
        ...
        ;;
esac
```

示例：

```shell
a=4

case $a in
    1)
        echo ${a}等于1
        ;;  
    2)
        echo ${a}等于2
        ;;  
    3)                                                
        echo ${a}等于3
        ;;  
    *)
        echo 其他
        ;;  
esac
```

输出结果：

```
其他
```



## 4.12 循环语句

**for…in…do…done**

命令格式：

```shell
for var in val1 val2 val3
do
    语句1
    语句2
    ...
done
```

示例1，输出a 2 cc，每个元素一行：

```shell
for i in a 2 cc
do
    echo $i
done
```

示例2，输出当前路径下的所有文件名，每个文件名一行：

```shell
for file in `ls`
do
    echo $file
done
```

示例3，输出1-10

```shell
for i in $(seq 1 10)
do
    echo $i
done
```

示例4，使用{1..10} 或者 {a..z}

```shell
for i in {a..z}
do
    echo $i
done
```

**for ((…;…;…)) do…done**

命令格式：

```shell
for ((expression; condition; expression))
do
    语句1
    语句2
done
```

示例，输出1-10，每个数占一行：

```shell
for ((i=1; i<=10; i++))
do
    echo $i
done
```

**while…do…done循环**

命令格式：

```shell
while condition
do
    语句1
    语句2
    ...
done
```

示例，文件结束符为 **Ctrl+d** ，输入文件结束符后 **read** 指令返回false。

```shell
while read name
do
    echo $name
done
```

示例，当用户输入yes或者YES时结束，否则一直等待读入。

```shell
until [ "${word}" == "yes" ] || [ "${word}" == "YES" ]
do
    read -p "Please input yes/YES to stop this program: " word
done
```

**break命令**

跳出当前一层循环，注意与 `C/C++` 不同的是：`break `不能跳出 `case` 语句。

示例

```shell
while read name
do
    for ((i=1;i<=10;i++))
    do
        case $i in
            8)
                break
                ;;
            *)
                echo $i
                ;;
        esac
    done
done
```

该示例每读入非EOF的字符串，会输出一遍1-7。
该程序可以输入 `Ctrl+d` 文件结束符来结束，也可以直接用 `Ctrl+c` 杀掉该进程。

**continue命令**

跳出当前循环。

示例：

```shell
for ((i=1;i<=10;i++))
do
    if [ `expr $i % 2` -eq 0 ]
    then
        continue
    fi
    echo $i
done
```

该程序输出1-10中的所有奇数。

**死循环的处理方式**

如果AC Terminal可以打开该程序，则输入 **Ctrl+c** 即可。

否则可以直接关闭进程：

	1. 使用top命令找到进程的PID
	2. shirt+m 
	3. 输入kill -9 PID即可关掉此进程



## 4.13 函数

`bash` 中的函数类似于 `C/C++` 中的函数，但 `return` 的返回值与 `C/C++` 不同，返回的是 `exit code`，取值为0-255，0表示正常结束。

如果想获取函数的输出结果，可以通过 `echo` 输出到 `stdout` 中，然后通过 `$(function_name)` 来获取 `stdout` 中的结果。

函数的 `return` 值可以通过 `$?` 来获取。

命令格式：

```shell
[function] func_name() {  # function关键字可以省略
    语句1
    语句2
    ...
}
```

**不获取 `return` 值和 `stdout` 值**

示例

```shell
func() {
    name=yxc
    echo "Hello $name"
}

func
```

输出结果：

```
Hello yxc
```

**获取 `return` 值和 `stdout` 值**

不写 `return` 时，默认 `return 0`。

示例

```shell
func() {
    name=yxc
    echo "Hello $name"

    return 123
}

output=$(func)
ret=$?

echo "output = $output"
echo "return = $ret"
```

输出结果：

```shell
output = Hello yxc
return = 123
```

**函数的输入参数**

在函数内，`$1` 表示第一个输入参数，`$2` 表示第二个输入参数，依此类推。

注意：函数内的 `$0` 仍然是文件名，而不是函数名。

示例：

```shell
func() {  # 递归计算 $1 + ($1 - 1) + ($1 - 2) + ... + 0
    word=""
    while [ "${word}" != 'y' ] && [ "${word}" != 'n' ]
    do
        read -p "要进入func($1)函数吗？请输入y/n：" word
    done

    if [ "$word" == 'n' ]
    then
        echo 0
        return 0
    fi  

    if [ $1 -le 0 ] 
    then
        echo 0
        return 0
    fi  

    sum=$(func $(expr $1 - 1))
    echo $(expr $sum + $1)
}

echo $(func 10)
```

输出结果：

```
55
```

**函数内的局部变量**

可以在函数内定义局部变量，作用范围仅在当前函数内。

可以在递归函数中定义局部变量。

命令格式：

```shell
local 变量名=变量值
```

例如：

```shell
#! /bin/bash

func() {
    local name=yxc
    echo $name
}
func

echo $name
```

输出结果：

```
yxc
```

第一行为函数内的name变量，第二行为函数外调用name变量，会发现此时该变量不存在。



## **4.14 exit命令**

`exit` 命令用来退出当前 `shell` 进程，并返回一个退出状态；使用 `$?` 可以接收这个退出状态。

`exit` 命令可以接受一个整数值作为参数，代表退出状态。如果不指定，默认状态值是 0。

`exit` 退出状态只能是一个介于 0~255 之间的整数，其中只有 0 表示成功，其它值都表示失败。

示例：

创建脚本 `test.sh`，内容如下：

```shell
#! /bin/bash

if [ $# -ne 1 ]  # 如果传入参数个数等于1，则正常退出；否则非正常退出。
then
    echo "arguments not valid"
    exit 1
else
    echo "arguments valid"
    exit 0
fi
```

执行该脚本：

```shell
acs@9e0ebfcd82d7:~$ chmod +x test.sh 
acs@9e0ebfcd82d7:~$ ./test.sh acwing
arguments valid
acs@9e0ebfcd82d7:~$ echo $?  # 传入一个参数，则正常退出，exit code为0
0
acs@9e0ebfcd82d7:~$ ./test.sh 
arguments not valid
acs@9e0ebfcd82d7:~$ echo $?  # 传入参数个数不是1，则非正常退出，exit code为1
1
```



## 4.15 文件重定向

每个进程默认打开3个文件描述符：

 	1. `stdin`  标准输入，从命令行读取数据，文件描述符为0
 	2. `stdout` 标准输出，向命令行输出数据，文件描述符为1
 	3. `stderr` 标准错误输出，向命令行输出数据，文件描述符为2

可以用文件重定向将这三个文件重定向到其他文件中。

**重定向命令列表**

| 命令               | 说明                                        |
| ------------------ | ------------------------------------------- |
| `command > file`   | 将 `stdout` 重定向到 `file` 中              |
| `command < file`   | 将 `stdin` 重定向到 `file` 中               |
| `command >> file`  | 将 `stdout` 以追加方式重定向到 `file` 中    |
| `command n> file`  | 将文件描述符 n 重定向到 `file` 中           |
| `command n>> file` | 将文件描述符 n 以追加方式重定向到 `file` 中 |

**输入和输出重定向**

```shell
echo -e "Hello \c" > output.txt  # 将stdout重定向到output.txt中
echo "World" >> output.txt  # 将字符串追加到output.txt中

read str < output.txt  # 从output.txt中读取字符串
 
echo $str  # 输出结果：Hello World
```

**同时重定向stdin和stdout**

创建bash脚本：

```shell
#! /bin/bash

read a
read b

echo $(expr "$a" + "$b")
```

创建input.txt，里面的内容为：

```
3
4
```

执行命令：

```
acs@9e0ebfcd82d7:~$ chmod +x test.sh  # 添加可执行权限
acs@9e0ebfcd82d7:~$ ./test.sh < input.txt > output.txt  # 从input.txt中读取内容，将输出写入output.txt中
acs@9e0ebfcd82d7:~$ cat output.txt  # 查看output.txt中的内容
7
```



## 4.16 **引入外部脚本**

类似于 **C/C++** 中的 **include** 操作，**bash** 也可以引入其他文件中的代码。

语法格式：

```shell
. filename  # 注意点和文件名之间有一个空格

或

source filename
```

示例

创建 `test1.sh`，内容为：

```shell
#! /bin/bash

name=yxc  # 定义变量name
```

然后创建 `test2.sh`，内容为：

```
#! /bin/bash

source test1.sh # 或 . test1.sh

echo My name is: $name  # 可以使用test1.sh中的变量
```

执行命令：

```
acs@9e0ebfcd82d7:~$ chmod +x test2.sh 
acs@9e0ebfcd82d7:~$ ./test2.sh 
My name is: yxc
```



------



# 5. ssh

1. 教程

## 5.1 ssh登录

**基本用法**

远程登录服务器：`ssh user@hostname`

 -    `user` : 用户名
 -    `hostname` : IP地址或域名

第一次登录时会提示：

```
The authenticity of host '123.57.47.211 (123.57.47.211)' can't be established.
ECDSA key fingerprint is SHA256:iy237yysfCe013/l+kpDGfEG9xxHxm0dnxnAbJTPpG8.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

输入 `yes` ，然后回车即可。
这样会将该服务器的信息记录在 `~/.ssh/known_hosts` 文件中。

然后输入密码即可登录到远程服务器中。

默认登录端口号为22。如果想登录某一特定端口：

```shell
ssh user@hostname -p 22
```



**配置文件**

创建文件：`~/.ssh/config`

然后在文件中输入：

```shell
Host myserver1
    HostName IP地址或域名
    User 用户名

Host myserver2
    HostName IP地址或域名
    User 用户名
```

之后再使用服务器时，可以直接使用别名 `myserver1`、`myserver2`



**密钥登录**

创建密钥：`ssh-keygen`

然后一直回车即可

执行结束后，`~/.ssh/` 目录下会多两个文件：

-  `id_rsa` ：私钥
-  `id_rsa.pub` ：公钥

之后想免密码登录哪个服务器，就将公钥传给哪个服务器即可。

例如，想免密登录 `myserver` 服务器。则将公钥中的内容，复制到 `myserver` 中的~/.ssh/authorized_keys文件里即可。

也可以使用如下命令一键添加公钥：

```shell
ssh-copy-id myserver(配置的服务器别名)
```



**ssh远程执行命令**

命令格式：`ssh user@hostname command`

例如：`ssh user@hostname ls -a`

或者

```shell
# 单引号中的$i可以求值
ssh myserver 'for ((i = 0; i < 10; i ++ )) do echo $i; done'
```

或者

```shell
# 双引号中的$i不可以求值
ssh myserver "for ((i = 0; i < 10; i ++ )) do echo $i; done"
```



**注意：**

**ssh远程执行命令**

```shell
ssh server "cd homework ; ls"
```

基本能完成常用的对于远程节点的管理了，几个注意的点：

​	1. 如果不加**双引号**，第二个ls命令在本地执行

​	2. **分号**，两个命令之间用分号隔开

**整条ssh命令用引号包围**

```shell
a=1
ssh myserver echo $a # 正确 
ssh myserver "echo $a" # 正确
ssh myserver 'echo $a' # 错误
```

**双引号**在**本地**进行解析，所以传过去命令不是 `echo $a` ，而是 `echo 1`
**单引号**在**服务器**进行解析，传过去的是 `echo $a`，服务器不知道$a的值，解析为空

```shell
ssh myserver "for ((i = 0; i < 10; i ++ )) do echo $i; done" # 错误
ssh myserver 'for ((i = 0; i < 10; i ++ )) do echo $i; done' # 正确
```

**双引号**在**本地**进行解析，本地不知道 **$i** 的值，解析为空

**单引号**在**服务器**进行解析，**$i** 的值在服务器随循环变化



**shell命令变量中的空格问题(用ssh执行)**

```shell
ssh ser mkdir homework/lesson_4/homework_4/\"$1\" # 正确
ssh ser mkdir homework/lesson_4/homework_4/"'$1'" # 正确
ssh ser mkdir homework/lesson_4/homework_4/'"$1"' # 错误
```

	1. 如果shell命令(用ssh执行)中有空格，**变量**用双引号引起来
	1. 最外层是双引号，内嵌单引号，$等特殊符号依旧可以识别
	3. 最外层是单引号，内嵌双引号，$等特殊符号无法识别
	1. mkdir "my dir"    →    mkdir my dir    →    创建my和dir文件夹
	1. mkdir " 'my dir' "    →    mkdir 'my dir'    →    创建my dir文件夹



## 5.2 scp传文件

**基本用法**

命令格式：`scp source destination`

将 `source` 路径下的文件复制到 `destination` 中

一次复制多个文件：`scp source1 source2 destination`



复制文件夹到服务器：

将本地家目录中的 `tmp` 文件夹复制到 `myserver` 服务器中的 `/home/acs/` 目录下:

```shell
scp -r ~/tmp myserver:/home/acs/
```

将本地家目录中的 `tmp` 文件夹复制到 `myserver` 服务器中的 `~/homework/` 目录下:

```shell
scp -r ~/tmp myserver:homework/
```



复制文件夹到本地：

将 `myserver` 服务器中的 `~/homework/` 文件夹复制到本地的当前路径下：

```shell
scp -r myserver:homework .
```

将 `myserver` 服务器中的 `~/homework/` 文件夹复制到本地的~/file路径下：

```shell
scp -r myserver:homework ~/file
```

指定服务器的端口号：`scp -P 22 source1 source2 destination`   P是大P

**注意** ： `scp` 的 `-r -P` 等参数尽量加在 `source` 和 `destination` 之前。	

-

**使用 `scp` 配置其他服务器的 `vim` 和 `tmux`**

```shell
scp ~/.vimrc ~/.tmux.conf myserver:
```



------



# 6. git

## 6.1 git教程

代码托管平台：git.acwing.com



## 6.2 git基本概念

- 工作区：仓库的目录。工作区是独立于各个分支的。
- 暂存区：数据暂时存放的区域，类似于工作区写入版本库前的缓存区。暂存区是独立于各个分支的。
- 版本库：存放所有已经提交到本地仓库的代码版本
- 版本结构：树结构，树中每个节点代表一个代码版本。



## 6.3 git常用命令

### **全局设置**

- `git config --global user.name xxx`：设置全局用户名，信息记录在 `~/.gitconfig` 文件中

- `git config --global user.email xxx@xxx.com`：设置全局邮箱地址，信息记录在 `~/.gitconfig` 文件中

- `git init`：将当前目录配置成git仓库，信息记录在隐藏的.git文件夹中  

  **注意：一般是配置为新仓库才需要设置**

  

### **常用命令**

- `git add XX` ：将XX文件添加到暂存区  //无论删减都是此操作

  ​	`	git add .`：将所有待加入暂存区的文件加入暂存区

- `git commit -m "给自己看的备注信息"`：将暂存区的内容提交到当前分支  

- `git commit --amend `：修改备注的信息

- `git status`：查看仓库状态

- `git log`：查看当前分支的所有版本

- `git push -u (第一次需要-u以后不需要) orgin master` ：将当前分支推送到远程仓库

  **注意：** **新仓库默认主分支为master**

- `git clone git@git.acwing.com:xxx/XX.git` ：将远程仓库XX下载到当前目录下 

  **注意：当前目录不需提前配置成git仓库 clone后自动成为仓库**

- `git branch`：查看所有分支和当前所处分支

  

**查看命令**

- `git diff XX`：查看XX文件相对于暂存区修改了哪些内容
- `git status`：查看仓库状态
- `git log`：查看当前分支的所有版本
- `git log --pretty=oneline`：用一行来显示
- `git reflog`：查看HEAD指针的移动历史（包括被回滚的版本）
- `git branch`：查看所有分支和当前所处分支
- `git pull` ：将本地仓库的当前分支与远程仓库的当前分支合并，把远程仓库拉下来

### **删除命令**

- `git rm --cached XX`：将文件从仓库索引目录中删掉，不希望管理这个文件

- `git restore --staged xx`：将xx从暂存区里移除

- `git checkout — XX或git restore XX`：=XX文件尚未加入暂存区的修改全部撤销

  **注意：变回暂存区版本 若暂存区没内容 则变会HEAD指针版本**	
  
- `git branch -d branch_name`：删除本地仓库的branch_name分支 ，需在其他分支下执行

- `git push -d origin branch_name`：删除远程仓库的branch_name分支

### **代码回滚**

- `git reset --hard HEAD^ 或git reset --hard HEAD~` ：将代码库回滚到上一个版本 ，不会删掉版本
- `git reset --hard HEAD^^`：往上回滚两次，以此类推
- `git reset --hard HEAD~100`：往上回滚100个版本
- `git reset --hard 版本号`：回滚到某一特定版本
- `git restore xxx` ：可以将误删的暂存区文件恢复

### **远程仓库**

- `git remote add origin git@git.acwing.com:xxx/XXX.git`：将本地仓库关联到远程仓库 

  **注意：origin的意思是“远程仓库”，链接从仓库下获取**

- `git remote rm origin` :取消本地仓库与远程仓库的关联

- `git push -u (第一次需要-u以后不需要)` ：将当前分支推送到远程仓库

  ```c++
  /*
  	-u选项会指定一个默认主机 
  	将本地的master分支推送到origin主机，同时指定origin为默认主机
  	后面就可以不加任何参数使用git push了
  */
  ```

- `git push origin branch_name`：将本地的某个分支推送到远程仓库  

- `git clone git@git.acwing.com:xxx/xx.git`：将远程仓库xx下载到当前目录下

- `git push --set-upstream origin branch_name`：设置本地的branch_name分支对应远程仓库的branch_name分支

  **注意：一般在添加新分支后，git push失败会提示需要将此分支对应远程仓库**

- `git push -d origin branch_name`：删除远程仓库的branch_name分支

- `git checkout -t origin/branch_name`：将远程的branch_name分支拉取到本地

  **注意：此命令直接将分支拉取本地，一般用于本地无此分支，需要从远程仓库下载此分支**

- `git pull` ：将本地仓库的当前分支与远程仓库的当前分支合并，把远程仓库拉下来

- `git pull origin branch_name`：将本地仓库的当前分支与远程仓库的branch_name分支合并

  **注意：git pull相当于是从远程获取最新版本并merge到本地 ，一般用于将旧的分支更新到远程仓库新的版本** 

- `git branch --set-upstream-to=origin/branch_name1 branch_name2`：将远程的branch_name1分支与本地的branch_name2分支对应

  **注意：此命令是将本地的分支和远程存在的分支对应**

### **分支命令**

- `git branch branch_name`：创建新分支 

  **注意：只会创建分支 节点需要 commit出来**

- `git branch`：查看所有分支和当前所处分支

- `git checkout -b branch_name`：创建并切换到branch_name这个分支

- `git checkout branch_name`：切换到branch_name这个分支

  ```
           A---B---C branch
           /
  D---E---F master
  ```

- `git merge branch_name`：将分支branch_name合并到当前分支上  (快速合并模式)

  ```
            A---B---C branch
           /         	master
  D---E---F 
  ```

- `git merge --no-ff branch_name`：禁止快速合并

         	A---B---C branch
            /         \
         D---E---F-----------G master

- `git branch -d branch_name`：删除本地仓库的branch_name分支 

- `git push --set-upstream origin branch_name`：设置本地的branch_name分支对应远程仓库的branch_name分支

- `git push -d origin branch_name`：删除远程仓库的branch_name分支

- `git checkout -t origin/branch_name` ：将远程的branch_name分支拉取到本地

- `git branch --set-upstream-to=origin/branch_name1 branch_name2`：将远程的branch_name1分支与本地的branch_name2分支对应

### **stash暂存**

应用场景：

1. 当正在dev分支上开发某个项目，这时项目中出现一个bug，需要紧急修复，但是正在开发的内容只是完成一半，还不想提交，这时可以用git stash命令将修改的内容保存至堆栈区，然后顺利切换到hotfix分支进行bug修复，修复完成后，再次切回到dev分支，从堆栈中恢复刚刚保存的内容。
2. 由于疏忽，本应该在dev分支开发的内容，却在master上进行了开发，需要重新切回到dev分支上进行开发，可以用git stash将内容保存至堆栈中，切回到dev分支后，再次恢复内容即可。
3. 总的来说，git stash命令的作用就是将目前还不想提交的但是已经修改的内容进行保存至堆栈中，后续可以在某个分支上恢复出堆栈中的内容。这也就是说，stash中的内容不仅仅可以恢复到原先开发的分支，也可以恢复到其他任意指定的分支上。git stash作用的范围包括工作区和暂存区中的内容，也就是说没有提交的内容都会保存至堆栈中。

- `git stash`：将工作区和暂存区中尚未提交的修改存入栈中
- `git stash apply`：将栈顶存储的修改恢复到当前分支，但不删除栈顶元素
- `git stash drop`：删除栈顶存储的修改
- `git stash pop`：将栈顶存储的修改恢复到当前分支，同时删除栈顶元素
- `git stash list`：查看栈中所有元素



### 常见错误

```
[new branch]      master     -> origin/master
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

	git pull <remote> <branch>

If you wish to set tracking information for this branch you can do so with:

 	git branch --set-upstream-to=origin/<branch> master
```

这种情况一般是本地新建仓库关联远程仓库后想拉取内容，使用 `git pull` 命令时候出现

问题为本地仓库没有对应远程仓库的分支

解决方案：

1. 直接添加指定的远程分支

    `git pull origin master` ：操作master

2. 先指定本地分支对应远程的分支，然后再去pull

    `git branch --set-upstream-to=origin/master master` ：制定本地master到远程master

    `git pull`

    使用之前需要保证有分支，`git branch -a` ：查看， `git checkout` ：切换



------



# 7. thrift

> 我们写一个应用时，这个应用程序并不止一个服务，而且不同的服务分配到不同服务器(或者进程)上，也就是我们常说的[微服务](https://baike.baidu.com/item/%E5%BE%AE%E6%9C%8D%E5%8A%A1/18758759?fr=aladdin) 。

## 7.1 简介

官网：[Apache Thrift - Home](https://thrift.apache.org/)

**Apache Thrift**软件框架用于可伸缩的跨语言服务开发，它将**软件栈**和**代码生成引擎**结合在一起，以构建在C++、Java、Python、PHP、Ruby、Erlang、Perl、Haskell、C#、Cocoa、JavaScript、Node.js、Smalltalk、OCaml和Delphi等语言之间高效、无缝地工作的服务。

**Thrift使用C++进行编写**，在安装使用的时候需要安装依赖，windows安装方式见官网即可。安装方式：[thrift官网介绍安装方式](http://thrift.apache.org/docs/install/) 

Ubuntu 安装 Thrift 以及常见问题：https://www.acwing.com/file_system/file/content/whole/index/content/3034732/

```shell
thrift -version //查看thrift版本
```



## **7.2 Thrift IDL**

Thrift 采用IDL（Interface Definition Language）来定义通用的服务接口，然后通过Thrift提供的编译器，可以将服务接口编译成不同语言编写的代码，通过这个方式来实现跨语言的功能。

- 通过命令调用Thrift提供的编译器将服务接口编译成不同语言编写的代码。
- 这些代码又分为服务端和客户端，将所在不同进程(或服务器)的功能连接起来。

```
thrift -r --gen <language> <Thrift filename>
```



## 7.3 如何创建一个Thrift服务?

1. 定义服务接口(存放接口的文件夹就是thrift文件)
2. 作为服务端的服务，需要生成server。
3. 作为请求端的服务，需要生成client。



### **例子：一个游戏的匹配服务分析**

**一般情况如图所示**

![https://cdn.acwing.com/media/article/image/2021/10/05/97206_32d5aa8525-%E6%97%A0%E6%A0%87%E9%A2%98.png](https://cdn.acwing.com/media/article/image/2021/10/05/97206_32d5aa8525-%E6%97%A0%E6%A0%87%E9%A2%98.png)

**分析图示内容**
这个游戏的功能可能运行在一个或多个服务器(或进程)上，而thrift就是将不同服务器不同语言的功能连接起来。
图中的三个节点(功能)是完全独立的，既可以在同一个服务器上，也可以在不同服务器上。
每一个节点就是一个进程，每个进程可以使用不同的语言来实现。

- 在GAME节点上实现客户端通过调用匹配系统的服务端中实现的两个服务接口函数获取功能，实现跨语言跨服务的工作。
- 每个节点(功能)之间通过thrift定义的服务接口作为有向边进行连接。
  弧尾所在的节点创建客户端，弧头所在的节点创建服务端。
- 匹配系统节点实现服务端，其中有一个匹配池：不断的接收玩家和删除玩家，同时根据一定的规则给每个玩家安排一局游戏。
- 匹配系统节点实现客户端，通过调用数据存储节点的服务端中实现的一个服务接口函数获取功能，实现跨语言跨服务的工作。
- 每个功能(节点)之间通过thrift定义的服务接口作为有向边进行连接。
  弧尾所在的节点创建客户端，弧头所在的节点创建服务端。
- 数据存储节点实现服务端。别人已经将服务接口和服务端实现好了。
- 服务接口功能介绍:
  add_user：向匹配池中添加玩家。
  remove_user：从匹配池中删除玩家。
  save_data：将匹配信息存储起来。

**补充**

- 有向边也称弧,边的始点称为弧尾,终点称为弧头。
- 当做项目时，可能有人已经将服务接口实现好了，即将服务端实现了，我们只需要创建客户端即可。

**分析总结:**

在实现服务之前，最好先画个图分析，这样目标明确、思路清晰。

**图中的要素**

1. 不同服务作为节点
2. 每个服务是在哪个服务器上实现的
3. 每个服务通过什么语言实现
4. 服务之间通过怎样的服务接口进行连接。
5. 通过业务逻辑确认每个服务需要创建哪些服务端和客户端。



------



1. 创建thrift文件夹 其下创建thrift文件 参考: [tutorial/tutorial.thrift](https://gitbox.apache.org/repos/asf?p=thrift.git;a=blob;hb=HEAD;f=tutorial/tutorial.thrift)

​		文件内定义语言类型 接口

2. 官网内查找相应的接口代码实现 

   服务端

   进入相应文件夹 一般新建一个src表示源文件

   输入命令

   ```
   thrift -r --gen cpp 创建的.thrift文件
   ```

3. 修改`gen-cpp`文件夹名字 将`Mach_server.skeleton.cpp`复制到当前文件夹

4. 进入文件实现要求 每个函数可以先return 0

5. 编译代码 编译C++ 

   1. 编译 `g++ -c main.cpp 其他用到的cpp文件 /*.cpp    `
   2. 链接 `g++ *.o -o main -lthrift(thrift用到的动态链接库)  `

6. 客户端 

   进入相应文件夹 一般新建一个src表示源文件

   输入命令

   ```
   thrift -r --gen py 创建的.thrift文件
   ```

7. 可以删除可执行文件

   复制官网客户端例子 前四行是将当局路径加入python环境变量里 可以删掉

   修改from路径第一行

   `from match_client.match import Match`  

   修改类型 第二行 根据ttypes的结构修改

   `from match_client.match.ttypes import User`  

   删掉多余代码 保留 Connect 和 Close

   修改client后的函数为自己的函数名 Match 

   增加main函数内容 调用thrift中的add_user函数

8. 编译文件 `python3 clien.py` 需要先打开服务端

9. 加了锁 链接 `g++ *.o -o main -lthrift -pthreada`





## 7.4 补充知识点

### C++代码编译和链接

编译：

```
g++ -c 用到的.cpp文件 
```

链接：

```
g++ *.o -o 生成文件名称 -lthrift -pthread -...用到的库
```

编译C++时，如果你用到了线程，需要加上线程的动态链接库的参数`-pthread`。
`-lthrift`参数将所有thrift动态连接文件连接起来。

C++编译很慢，链接很快。所以每次修改项目，重新编译时，只需要编译修改过的.cpp文件即可，防止编译时间过长。
即修改哪个文件就编译哪个文件。
基于这一点考虑就有了make和cmake工具。但没啥用。

```
//前面加上time查看编译和链接的时间。
time g++ -c .cpp文件
time g++ *.o -o 生成文件名称 -lthrift -pthread
```



### 多线程多进程

每执行一个程序就是开了一个单独的进程，每一个进程当中可以开启多个线程。

开多线程的开销是很小的，开多进程的开销是很大的。

**端口：**

如果把IP地址比作一间房子 ，端口就是出入这间房子的门。真正的房子只有几个门，但是一个IP地址的端口可以有65536（即：2^16）个之多！端口是通过端口号来标记的，端口号只有整数，范围是从0 到65535（2^16-1）。
同一个端口只能由一个进程来监听。所以我们一旦启动了一个服务，那么这个服务就不能在被另一个进程启动了。
服务器的端口号要与客户端的端口号相同。

### `#include <thread>`

C++中有一个thread的库，可以用来开线程。
通过定义一个变量将函数名作为参数，就能开一个线程了。
首先定义线程的操作。
并行中经典的生产者和消费者模型。
生产者、消费者是两个线程。
生产者:add_user()、remove_user()
消费者:匹配用户的功能。
生产者和消费者之间需要一个媒介。
这个媒介可以有很多种方法。比如:消费队列。
很多语言都有自己实现的消费队列，也可以自己实现消费队列。
实现消费队列，就需要用到一些锁(mutex)。
并行编程的基本概念:锁。

### 互斥锁

在编程中，引入了对象互斥锁的概念，来保证共享数据操作的完整性。每个对象都对应于一个可称为" 互斥锁" 的标记，这个标记用来保证在任一时刻，只能有一个线程访问该对象。

锁🔒有两个操作。一个P操作(上锁)，一个V操作(解锁)。
定义互斥锁：`mutex m`;
锁一般使用信号量来实现的，`mutex` 其实就是一个信号量(它特殊也叫互斥量)。互斥量就是同一时间能够分给一个人，即S=1。
信号量S:S=10表示可以将信号量分给10个人来用。

P操作的主要动作是: 
①S减1； 
②若S减1后仍大于或等于0，则进程继续执行；  
③若S减1后小于0，则该进程被阻塞后放入等待该信号量的等待队列中，然后转进程调度。  
V操作的主要动作是： 
①S加1； 
②若相加后结果大于0，则进程继续执行； 
③若相加后结果小于或等于0，则从该信号的等待队列中释放一个等待进程，然后再返回原进程继续执行或转进程调度。

对于P和V都是原子操作，就是在执行P和V操作时，不会被插队。从而实现对共享变量操作的原子性。
特殊:S=1表示互斥量，表示同一时间，信号量只能分配给一个线程。

多线程为啥要用锁? 因为多线程可能共享一个内存空间，执行顺序是随机的，导致出现重复读取并修改的现象。



------



# 8. 管道、环境变量与常用命令

## 8.1 管道

**概念**

 管道类似于文件重定向，可以将前一个命令的 `stdout` 重定向到下一个命令的 `stdin`

Linux 管道使用竖线`|`连接多个命令，这被称为管道符。Linux 管道的具体语法格式如下：

```shell
command1 | command2
command1 | command2 [ | commandN... ]
```

当在两个命令之间设置管道时，管道符`|`左边命令的输出就变成了右边命令的输入。只要第一个命令向标准输出写入，而第二个命令是从标准输入读取，那么这两个命令就可以形成一个管道。大部分的 Linux 命令都可以用来形成管道。

**要点**

1. 管道命令仅处理 `stdout` ，会忽略 `stderr` 。
2. 管道右边的命令必须能接受stdin。
3. 多个管道命令可以串联。

**与文件重定向的区别**

- 文件重定向左边为命令，右边为文件。
- 管道左右两边均为命令，左边有 `stdout` ，右边有 `stdin` 。

**举例**

统计当前目录下所有python文件的总行数，其中 `find` 、 `xargs ` 、`wc` 等命令可以参考常用命令这一节内容。

```shell
find . -name '*.py' | xargs cat | wc -l
```



## 8.2 **环境变量**

**概念**：

Linux系统中会用很多环境变量来记录配置信息。
环境变量类似于全局变量，可以被各个进程访问到。我们可以通过修改环境变量来方便地修改系统配置。

**查看：**

列出当前环境下的所有环境变量：

```shell
env  # 显示当前用户的变量
set  # 显示当前shell的变量，包括当前用户的变量;
export  # 显示当前导出成用户变量的shell变量
```

输出某个环境变量的值：

```
echo $PATH
```

**修改**：

环境变量的定义、修改、删除操作可以参考[4. Shell](#4. Shell)

为了将对环境变量的修改应用到未来所有环境下，可以将修改命令放到 `~/.bashrc` 文件中。
修改完 `~/.bashrc` 文件后，记得执行 `source ~/.bashrc` ，来将修改应用到当前的bash环境下。

为何将修改命令放到 `~/.bashrc` ，就可以确保修改会影响未来所有的环境呢？

每次启动 `bash` ，都会先执行 `~/.bashrc` 。
每次 `ssh` 登陆远程服务器，都会启动一个 `bash` 命令行给我们。
每次 `tmux` 新开一个 `pane` ，都会启动一个 `bash` 命令行给我们。
所以未来所有新开的环境都会加载我们修改的内容。

`/etc/profile:` 
 该文件登录操作系统时，为**每个用户**设置环境信息，当用户第一次登录时,该文件被执行。也就是说这个文件对每个shell都有效，用于获取系统的环境信息。可以通过**命令source /etc/profile立即生效**

**注意：添加环境变量时，在文件最后添加**

```
export PATH=./...路径：$PATH   //$PATH代表之前的环境变量，他们之前用:隔开 
```



**常见环境变量：**

1. `HOME` ：用户的家目录。

2. `PATH` ：可执行文件（命令）的存储路径。路径与路径之间用:分隔。当某个可执行文件同时出现在多个路径中时，会选择从左到右数第一个路径中的执行。下列所有存储路径的环境变量，均采用从左到右的优先顺序。

3. `LD_LIBRARY_PATH` ：用于指定动态链接库(.so文件)的路径，其内容是以冒号分隔的路径列表。

4. `C_INCLUDE_PATH` ：C语言的头文件路径，内容是以冒号分隔的路径列表。

5. `CPLUS_INCLUDE_PATH` ：CPP的头文件路径，内容是以冒号分隔的路径列表。

6. `PYTHONPATH` ：Python导入包的路径，内容是以冒号分隔的路径列表。

7. `JAVA_HOME` ：jdk的安装目录。

8. `CLASSPATH` ：存放Java导入类的路径，内容是以冒号分隔的路径列表。

   

## **8.3 常用命令**

Linux命令非常多，本节讲解几个常用命令。其他命令依赖于大家根据实际操作环境，边用边查。

### **系统状况**

1. `top`：查看所有进程的信息（Linux的任务管理器）

   - 打开后，输入M：按使用内存排序

   - 打开后，输入P：按使用CPU排序
   - 打开后，输入q：退出
2. `df -h`：查看硬盘使用情况
3. `free -h`：查看内存使用情况
4. `du -sh`：查看当前目录占用的硬盘空间
5. `ps aux`：查看所有进程
6. `kill -9 pid`：杀死编号为 `pid` 的进程
   传递某个具体的信号：`kill -s SIGTERM pid`
7. `netstat -nt`：查看所有网络连接
8. w：列出当前登陆的用户
9. ping www.baidu.com：检查是否连网
9. `cat /proc/cpuinfo` :查看配置

### **文件权限**

1. `chmod`：修改文件权限
   - `chmod +x xxx`：给xx添加可执行权限
   - `chmod -x xxx`：去掉xx的可执行权限
   - `chmod 777 xxx`：将xx的权限改成777
   - `chmod 777 xxx -R`：递归修改整个文件夹的权限

### 文件检索

1. `find /path/to/directory/ -name '*.py'`：搜索某个文件路径下的所有 `*.py` 文件  按文件名搜索

    `find 路径 -type d/p/s/c/b/l/ f:文件`：按文件类型搜索

    `find 路径 -maxdepth x -name '*.cpp' `：指定x为搜索深度，maxdepth要放在其他参数前

    `find 路径 -size +20M -size -50M `：单位：k、M、G，按文件大小搜索必须有上下限

    -atime、mtime、ctime  x天  amin、mmin、cmin  x分钟。 按照时间搜索 

    a 表示最近访问时间 

    m 表示最近更改时间，指更改文件属性一类的 

    c 表示最近改动时间，指更改文件内容

1. `grep xxx`：从 `stdin` 中读入若干行数据，如果某行中包含xx，则输出该行；否则忽略该行。

2. `wc`：统计行数、单词数、字节数

   - 既可以从 `stdin` 中直接读入内容；也可以在命令行参数中传入文件名列表；
   - `wc -l`：统计行数
   - `wc -w`：统计单词数
   - `wc -c`：统计字节数

3. `tree`：展示当前目录的文件结构

   - `tree /path/to/directory/`：展示某个目录的文件结构
   - `tree -a`：展示隐藏文件

4. `ag xxx`：搜索当前目录下的所有文件，检索xx字符串

    // `ag` 和 `grep` 的区别 ：`ag` 不会区分大小写

5. `cut`：分割一行内容

   - 从 `stdin` 中读入多行数据
   - `echo $PATH | cut -d ':' -f 3,5`：输出 `PATH` 用 `:` 分割后第3、5列数据
   - `echo $PATH | cut -d ':' -f 3-5`：输出 `PATH` 用 `:` 分割后第3-5列数据
   - `echo $PATH | cut -c 3,5`：输出 `PATH` 的第3、5个字符
   - `echo $PATH | cut -c 3-5`：输出 `PATH` 的第3-5个字符

6. `sort`：将每行内容按字典序排序
   可以从 `stdin` 中读取多行数据
   可以从命令行参数中读取文件名列表

7. `xargs`：将 `stdin` 中的数据用空格或回车分割成命令行参数
   `find . -name '*.py' | xargs cat | wc -l`：统计当前目录下所有python文件的总行数

### **查看文件内容**

1. `more`：浏览文件内容
   - 回车：下一行
   - 空格：下一页
   - b：上一页
   - q：退出
2. `less`：与 `more` 类似，功能更全
   - 回车：下一行
   - y：上一行
   - Page Down：下一页
   - Page Up：上一页
   - q：退出
3. `head -3 xxx`：展示xx的前3行内容
   - 同时支持从 `stdin` 读入内容
4. `tail -3 xxx`：展示xx末尾3行内容
   - 同时支持从 `stdin` 读入内容 

### 用户相关

`history`：展示当前用户的历史操作。内容存放在 `~/.bash_history` 中

### **工具**

1. `md5sum`：计算 `md5` 哈希值
   可以从 `stdin` 读入内容
   也可以在命令行参数中传入文件名列表；
2. `time command`：统计 `command` 命令的执行时间
3. `ipython3`：交互式 `python3` 环境。可以当做计算器，或者批量管理文件。
   - `! echo "Hello World"`：**!表示执行shell脚本**
4. `watch -n 0.1 command`：每0.1秒执行一次 `command` 命令
5. `tar`：压缩文件
   - `tar -zcvf xxx.tar.gz /path/to/file/*`：压缩
   - `tar -zxvf xxx.tar.gz`：解压缩
   - `tar -xvf file.tar` ：解压 tar包
6. `diff xxx yyy`：查找文件xx与yy的不同点

### 安装软件

`sudo command`：以 `root` 身份执行 `command` 命令
`apt-get install xxx`：安装软件
`pip install xxx --user --upgrade`：安装 `python` 包



------



# 9. 租云服务器

## 9.1 概述

云平台的作用:

1. 存放我们的docker容器，让计算跑在云端。
2. 获得公网IP地址，让每个人可以访问到我们的服务



任选一个云平台即可，推荐配置：

1. 1核 2GB（后期可以动态扩容，前期配置低一些没关系）
2. 网络带宽采用按量付费，最大带宽拉满即可（费用取决于用量，与最大带宽无关）
3. 系统版本：ubuntu 20.04 LTS（推荐用统一版本，避免后期出现配置不兼容的问题）



## 9.2 租云服务器

腾讯云地址：https://cloud.tencent.com/

阿里云地址：https://www.aliyun.com/

华为云地址：https://www.huaweicloud.com/



**创建工作用户 `acs` 并赋予 `sudo` 权限**
登录到新服务器。打开AC Terminal，然后：

```shell
ssh root@xxx.xxx.xxx.xxx  # xxx.xxx.xxx.xxx替换成新服务器的公网IP
ssh root@xxx.xxx.xxx.xxx  # 注意腾讯云登录的用户不是root，而是ubuntu
```

创建 `acs` 用户：

```shell
adduser acs  # 创建用户acs
usermod -aG sudo acs  # 给用户acs分配sudo权限

#在指定home文件下创建目录
useradd -d 指定家目录 用户名

#删除用户(不删除home目录)
userdel 用户名

#删除用户(删除目录,最好别用)
userdel -r tom
```



若出现进入新用户终端出现：

```
To run a command as administrator (user "root"), use "sudo <command>". See "man sudo_root" for detai
```

使用命令：

```
touch ~/.sudo_as_admin_successful
```

**配置免密登录方式**
退回AC Terminal，然后配置 `acs` 用户的别名和免密登录，可以参考[5. ssh](#5. ssh)



**配置新服务器的工作环境**

将AC Terminal的配置传到新服务器上：

```shell
scp .bashrc .vimrc .tmux.conf server_name:  # server_name需要换成自己配置的别名
```



**安装 `tmux` 和 `docker`**
登录自己的服务器，然后安装tmux：

```shell
sudo apt-get update
sudo apt-get install tmux
```

打开 `tmux`。（养成好习惯，所有工作都在 `tmux` 里进行，防止意外关闭终端后，工作进度丢失）

然后在 `tmux` 中根据[docker安装教程](https://docs.docker.com/engine/install/ubuntu/)安装 `docker` 即可。



# 10 docker教程

在 `tmux` 中根据[docker安装教程](https://docs.docker.com/engine/install/ubuntu/)安装 `docker` 即可



## **10.1 将当前用户添加到docker用户组**

为了避免每次使用 `docker` 命令都需要加上 `sudo` 权限，可以将当前用户加入安装中自动创建的 `docker` 用户组(可以参考[官方文档](https://docs.docker.com/engine/install/linux-postinstall/))：

```shell
sudo groupadd docker						// 1. 创建docker组
sudo usermod -aG docker $USER(你的用户名)	 // 2. 将您的用户添加到docker组中 $USER代表当前用户名
```

安装sudo：

`apt-get install sudo`



## **10.2 镜像(images)**

1. `docker pull ubuntu:20.04`：拉取一个镜像

   `xxx:xxx` ：	名称  ：版本号tag

2. `docker images`：列出本地所有镜像

3. `docker image rm ubuntu:20.04` 或 `docker rmi ubuntu:20.04`：删除镜像`ubuntu:20.04`

4. `docker [container] commit CONTAINER IMAGE_NAME:TAG`：创建某个container的镜像

5. `docker save -o ubuntu_20_04.tar ubuntu:20.04`：将镜像 `ubuntu:20.04` 导出到本地文件 `ubuntu_20_04.tar` 中

6. `docker load -i ubuntu_20_04.tar`：将镜像 `ubuntu:20.04` 从本地文件 `ubuntu_20_04.tar` 中加载出来



## **10.3 容器(container)**

1. `docker [container] create -it ubuntu:20.04`：利用镜像 `ubuntu:20.04` 创建一个容器。

2. `docker ps -a`：查看本地的所有容器

   `docker ps` ：查看运行中的容器

   `docker exec -itu root {containerName} passwd`：给容器root添加密码

3. `docker [container] start CONTAINER`：启动容器

4. `docker [container] stop CONTAINER`：停止容器

5. `docker [container] restart CONTAINER`：重启容器

6. `docker [contaienr] run -itd ubuntu:20.04`：创建并启动一个容器

   `-p aa:bb` ：将 docker 的 bb 端口映射到机器的 aa 端口

7. `docker [container] attach CONTAINER`：进入容器
   
   - 先按 `Ctrl-p` ，再按 `Ctrl-q` 可以挂起容器
   
8. `docker [container] exec CONTAINER COMMAND`：在容器中执行命令

9. `docker [container] rm CONTAINER`：删除容器

10. `docker container prune`：删除所有已停止的容器

11. `docker export -o xxx.tar CONTAINER`：将容器 `CONTAINER` 导出到本地文件 `xxx.tar` 中

12. `docker import xxx.tar image_name:tag`：将本地文件 `xxx.tar` 导入成镜像，并将镜像命名为 `image_name:tag`

13. `docker export/import` 与 `docker save/load` 的区别：
    - `export/import` 会丢弃历史记录和元数据信息，仅保存容器当时的快照状态
    - `save/load` 会保存完整记录，体积更大
    
14. `docker top CONTAINER`：查看某个容器内的所有进程

15. `docker stats`：查看所有容器的统计信息，包括CPU、内存、存储、网络等信息

16. `docker cp xxx CONTAINER:xxx` 或 `docker cp CONTAINER:xxx xxx`：在本地和容器间复制文件

17. `docker rename CONTAINER1 CONTAINER2`：重命名容器

18. `docker update CONTAINER --memory 500MB`：修改容器限制



### 实战

进入AC Terminal，然后：

```shell
scp /var/lib/acwing/docker/images/docker_lesson_1_0.tar server_name:  # 将镜像上传到自己租的云端服务器
ssh server_name  # 登录自己的云端服务器

docker load -i docker_lesson_1_0.tar  # 将镜像加载到本地
docker run -p 20000:22 --name my_docker_server -itd docker_lesson:1.0  # 创建并运行docker_lesson:1.0镜像

docker attach my_docker_server  # 进入创建的docker容器
passwd  # 设置root密码
```

去云平台控制台中修改安全组配置，放行端口 `20000` 。

返回AC Terminal，即可通过 `ssh` 登录自己的 `docker` 容器：

```shell
ssh root@xxx.xxx.xxx.xxx -p 20000  # 将xxx.xxx.xxx.xxx替换成自己租的服务器的IP地址
```

然后，可以仿照上节课内容，创建工作账户 `acs` 。

最后，可以参考[ssh登录](#5.1 ssh登录)登录配置 `docker` 容器的别名和免密登录
