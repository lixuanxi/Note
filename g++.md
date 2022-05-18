# g++

g++ 是 GNU 开发的 C++ 编译器，是 GNU 编译器套件 GCC（GNU Compiler Collection）的组成部分。另外，gcc 是 GNU 的 C 编译器。

看官方手册你会发现 g++ 的命令选项真的多如繁星，令人头皮发麻。但是常用的命令选项也就那几个，足以完成日常编译，g++ 使用起来还是比较简单的！



## 1. g++ 过程

g++ 编译器是 GCC 的一部分，一个完整的C++编译过程（例如g++ main.cpp生成可执行文件），GCC 编译工作一般分为四个步骤。：

- 编译预处理，也称预编译，可以使用命令`g++ -E`执行
- 编译，可以使用`g++ -S`执行
- 汇编，可以使用`as` 或者`g++ -c`执行
- 链接，可以使用`g++ xxx.o xxx.so xxx.a`执行



考察如下简单的 C++ 程序。

```c++
#include <iostream>
using namespace std;

int main(){
        cout<<"hello world"<<endl;
        return 0;
}
```

下面分别进行预处理、编译、汇编和链接，将 C++ 源码文件 test.cpp 转换为可执行文件 test.out。

### 1. 预处理（Preprocessing）

**编译预处理阶段：主要对包含的头文件（＃include ）和宏定义（＃define,#ifdef … ）还有注释等进行处理。**

由预处理器 cpp 完成，将 .cpp 源文件预处理为 .i 文件。

```shell
#生成预处理后的.i文件
g++ -E test.cpp -o test.i

#或
cpp test.cpp -o test.i
```

预编译完成后，`#include`引入的内容 被全部复制进预编译文件中，除此之外，如果文件中有使用宏定义也会被替换处理。

```
预编译过程最主要的工作，就是宏命令的替换
#include命令的工作就是单纯的导入，这里其实并不限制导入的类型，甚至可以导入.cpp、.txt等等。
感兴趣的同学可以预编译一个包含Qt中信号的文件，会看到预编译之后:
    emit直接成了空。发射信号实质就是一次函数调用;
    头文件中的signals:也被替换成了protected:（Qt5被替换为public:）
    以及Qt中其他的宏定义都在预编译时被处理如：Q_OBJECT Q_INVOKEABLE等
```



### 2. 编译（Compilation）

**编译过程：把预处理完的文件进行一系列语法分析、词法分析、语义分析（C++ 语法错误的检查）及优化后产生相应的汇编代码。**

编译是整个程序构建过程中最为核心的部分，也是最复杂的部分之一。由编译器 cc1plus 完成，将 .i 文件编译为 .s 的汇编文件。

使用 -S 选项，只进行编译而不进行汇编，生成汇编代码。

```shell
#生成汇编.s文件
g++ -S test.i -o test.s			

#或
ln -s /usr/libexec/gcc/x86_64-redhat-linux/4.8.2/cc1plus /usr/local/bin
cc1plus test.i -o test.s
```

注意，GCC 编译套件中，C++ 的编译器是 cc1plus，C 是 cc1，Objective-C 是 cc1obj，Fortran 是  f771，Java 是 jc1。所以 g++ 这个命令是对预处理器、编译器、汇编器和链接器的包装，会根据不同的参数去调用这些构建程序。



### 3. 汇编（Assembly）

**汇编阶段：生成目标代码 *.o**

由汇编器 as 完成，将 .s 文件汇编成 .o 的二进制目标文件。有两种方式：

- 使用 g++ 直接从源代码生成目标代码 `g++ -c *.s -o *.o`
- 使用汇编器从汇编代码生成目标代码 `as *.s -o *.o`

```shell
#生成二进制.o文件
#使用 g++ 直接从源代码生成目标代码
g++ -c test.s -o test.o

#或使用汇编器从汇编代码生成目标代码
as test.s -o test.o
```



### 4. 链接（Linking）

**链接阶段：生成可执行文件；Windows下生成 .exe**

由链接器 ld，将 .o 文件链接成可执行程序。

```shell
#生成二进制.out可执行文件
g++ test.o -o test
```

链接的过程，其核心工作是解决模块间各种符号(变量，函数)相互引用的问题，更多的时候我们除了使用.o以外，还将静态库和动态库链接一同链接生成可执行文件。



------



## 2. 命令格式

```shell
g++ [-c|-S|-E] [-std=standard]
    [-g] [-pg] [-Olevel]
    [-Wwarn...] [-pedantic]
    [-Idir...] [-Ldir...]
    [-Dmacro[=defn]...] [-Umacro]
    [-foption...] [-mmachine-option...]
    [-o outfile] [@file] infile...
```

- `gcc -I 头文件所在位置  *.c -0 main` ：-I 指定头文件所在目录位置
- `-c` ：只做预处理，编译，汇编。得到二进制文件
- `-g` ：编译时添加调试文件，用于 gdb 调试
- `-Wall`： 显示所有警告信息
- `-D` ：向程序中“动态”注册宏定义
- `-l` ：指定动态库库名
- `-L` ：指定动态库路径





------



## 3.命令选项

下面列出常用的命令选项。

#### 1. 总体选项

```
-E
	只激活预处理,这个不生成文件,你需要把它重定向到一个输出文件里面。例子用法:   
	gcc -E hello.c > pianoapan.txt   
	gcc -E hello.c | more   
	慢慢看吧,一句`hello word`也要预处理成800行的代码。     
-S   
	只激活预处理和编译，就是指把文件编译成为汇编代码。例子用法： 
	gcc -S hello.c   
	将生成.s的汇编代码，可以用文本编辑器查看。    
-c    
	只激活预处理,编译,和汇编,也就是他只把程序做成obj文件。例子用法:   
	gcc -c hello.c   
	将生成.o的目标文件（object file）。 
-o
	指定目标名称，缺省的时候，gcc/g++编译出来的文件是a.out。例子如下：   
	g++ -o hello.out hello.cpp
	g++ -o hello.asm -S hello.cpp   
```



#### 2. 目录选项

```
-I[dir]
	在你是用#include "file"的时候，gcc/g++会先在当前目录查找你所指定的头文件，如果没有找到，会到系统默认的头文件目录找。如果使用-I指定了目录，编译器会先在指定的目录查找，然后再去系统默认头文件目录查找。对于#include <file>，gcc/g++会到-I指定的目录查找，查找不到，然后再到系统默认的头文件目录查找。
-include [file]
	相当于“#include”，用于包含某个代码,简单来说,就是编译某个文件,需要另一个文件的时候,就可以   
	用它设定,功能就相当于在代码中使用#include。例子用法:   
	gcc hello.c -include /root/pianopan.h   
-I-
	就是取消前一个参数的功能,所以一般在-Idir之后使用   
-idirafter [dir]   
	在-I的目录里面查找失败，将到目录dir里面查找。
-iprefix [prefix]，-iwithprefix [dir]
	一般一起使用，当-I的目录查找失败，会到prefix+dir下查找。
-L[dir]   
	编译的时候，指定搜索库的路径。比如你自己的库，可以用它指定目录，不然编译器将只在标准库的
	目录找。这个dir就是目录的名称。
-l[library]    
	指定编译的时使用的库，例子用法   
	gcc -lcurses hello.c   
	使用curses库编译连接，生成程序。  
```



#### 3. 预处理选项

```
-Dmacro
	相当于C语言中的#define macro
-Dmacro=defn
	定义宏，相当于C语言中的#define macro defn
-Umacro
	取消宏定义，相当于C语言中的#undef macro
-undef
	取消任何非标准宏的定义，C++标准预定义的宏仍然有效
```



#### 4. 链接方式选项

```
-static
	此选项将禁止使用动态库。优点：程序运行不依赖于其他库。缺点：可执行文件比较大。
-shared
	此选项将尽量使用动态库，为默认选项。优点：生成文件比较小。缺点：运行时需要系统提供动态库。
-symbolic
	建立共享目标文件的时候，把引用绑定到全局符号上。对所有无法解析的引用作出警告（除非用连接选项，'-Xlinker -z -Xlinker defs'取代)。注：只有部分系统支持该选项。
-Wl,option   
	此选项传递option给链接程序；如果option中间有逗号，就将option分成多个选项传递给链接器
-Wl,-Bstatic
	告诉链接器ld只链接静态库，如果只存在动态链接库，则链接器报错。
-Wl,-Bdynamic
	告诉链接器ld优先使用动态链接库，如果只存在静态链接库，则使用静态链接库
-Wl,-rpath,<dir>
	指定链接器共享库查找目录为<dir>
-Wl,-s 或 -Wl,--strip-all
	指定链接器消除所有符号信息
-Wl,-S 或 -Wl,--strip-debug
	指定链接器消除调试符号信息，而非所有符号信息
```



#### 5. 错误与告警选项

```
-pedantic
	允许发出ANSI/ISO C标准所列出的所有警告
-pedantic-errors
	允许发出ANSI/ISO C标准所列出的错误
-Wall
	一般使用该选项，允许发出GCC能够提供的所有有用的警告。也可以用-W{warning}来标记指定的警告
-Wno-deprecated
	使用C++标准废弃特性不告警
-Werror
	要求GCC将所有的警告当成错误进行处理，在警告发生时中止编译过程。
-Werror={warning}
	将指定警告设置为错误。例如-Werror=return-type，如果函数需要返回值却没有return语句，则编译报错
-Wunknown-pragmas
	遇到GCC无法识别的编译指导指令，发出警告。在使用了-Wall选项时，就不需要使用该命令选项了
-Wno-pragmas
	遇到GCC无法识别的编译指导指令，不发出警告
-w
	关闭所有警告,建议不要使用此项。
```



#### 6. 调试选项

```
 -g   
	指示编译器，在编译时，产生调试信息。
-gstabs   
	此选项以stabs格式生成调试信息,但不包括gdb调试信息。 
-gstabs+   
	此选项以stabs格式声称调试信息,并且包含仅供gdb使用的额外调试信息.   
-ggdb    
	此选项将尽可能的生成gdb可以使用的调试信息。
-glevel
	请求生成调试信息，同时用level指出需要多少信息，默认的level是2
-pg
	编译的过程中加入额外的代码， 供性能分析工具gprof剖析程序的耗时情况
```



#### 7. 优化选项

```
-O0   
-O1   
-O2   
-O3   
	编译器优化选项分为4个级别，-O0表示没有优化，-O1为缺省值，建议使用-O2，-O3优化级别最高。
```



#### 8. 其他选项

```
-fpic
	编译器生成位置无关目标码（PIC，position-independent code），用于动态链接库，即Linux下的.so文件。通过全局偏移表（GOT，Global Offset Table）访问所有常量地址。程序启动时通过动态加载程序解析GOT条目。如果链接的so文件的GOT大小超过计算机特定的最大大小，则会从链接器收到错误消息，指示-fpic不起作用。这种情况下，请使用-fPIC重新编译
-fPIC
	同-fpic功能一致，生成位置无关目标码，用于生成动态链接库，建议使用该选项，而非-fpic
-v, --verbose
	显示详细的编译、汇编、链接命令
-pipe
	使用管道代替编译过程中的临时文件,在使用非gnu汇编工具的时候,可能有些问题   
	g++ -pipe -o hello.out hello.cpp
-ansi
	关闭gnu c中与ansi c不兼容的特性，激活ansi c的专有特性(包括禁止一些asm inline typeof关键字,以及
	UNIX,vax等预处理宏。
-fno-asm   
	此选项实现ansi选项功能的一部分，它禁止将asm,inline和typeof用作关键字。   
-fno-strict-prototype
	只对g++起作用,使用这个选项,g++将对不带参数的函数,都认为是没有显式的对参数的个数和类型说明,而不是没有
	参数.而gcc无论是否使用这个参数,都将对没有带参数的函数,认为没有显式说明的类型。
-fthis-is-varialble   
	就是向传统c++看齐,可以使用this当一般变量使用。
-fcond-mismatch   
	允许条件表达式的第二和第三参数类型不匹配,表达式的值将为void类型。
-funsigned-char   
-fno-signed-char   
-fsigned-char   
-fno-unsigned-char   
	这四个参数是对char类型进行设置,决定将char类型设置成unsigned char(前两个参数)或者signed char(后
	两个参数)。
-fpermissive
	把代码的语法错误作为警告，并继续编译。请谨慎使用该选项。
-imacros file   
	将file文件的宏,扩展到gcc/g++的输入文件,宏定义本身并不出现在输入文件中     
-nostdinc   
	使编译器不在系统缺省的头文件目录里面找头文件,一般和-I联合使用,明确限定头文件的位置。 
-nostdin C++
	规定不在g++指定的标准路经中搜索,但仍在其他路径中搜索,此选项在创建libg++库使用。
-C
	在预处理的时候,不删除注释信息,一般和-E使用,有时候分析程序，用这个很方便的。 
-m
	生成与具体CPU相关的程序。
-mtune=cpu-type 
	为指定类型的CPU生成代码。cpu-type 可以是：i386，i486，i586，pentium，i686，pentium4 等等。
-m32
-m64
	生成32bits程序或64bits程序
-mmmx
-msse
-msse2
-mno-mmx
-mno-sse
-mno-sse2
	使用或者不使用MMX，SSE，SSE2指令。
-M
	生成文件依赖的信息，包含目标文件所依赖的所有源文件。你可以用gcc -M hello.c来测试一下，很简单。   
-MM   
	和上面的那个一样，但是它将忽略由#include造成的依赖关系。   
-MD
	和-M相同，但是输出将导入到.d的文件里面。
-MMD   
	和-MM相同，但是输出将导入到.d的文件里面。
-Wa,option   
	此选项传递option给汇编器；如果option中间有逗号，就将option分成多个选项传递给汇编器 
-x language filename   
	设定文件所使用的语言,使后缀名无效,对以后的多个有效.也就是根据约定C语言的后缀名称是.c的，而C++的后缀
	名是.C或者.cpp。如果你很个性，决定你的C代码文件的后缀名是.pig，那你就要用这个参数,这个参数对他后面
	的文件名都起作用，除非到了下一个参数的使用。可以使用的参数有下面的这些：
	c,objective-c,c-header,c++,cpp-output,assembler,assembler-with-cpp。   
	看到英文，应该可以理解的。例子用法:   
	gcc -x c hello.pig
-x none filename
	关掉上一个选项，也就是让gcc根据文件名后缀，自动识别文件类型，例子用法:   
	gcc -x c hello.pig -x none hello2.c
```



------



## 4. 链接注意事项

`-L` 与 `-l` 链接器参数，就是指定链接时去（哪里）找（什么）库。

- `-l`，代表链接哪个库，会自动检索`lib`开头的对应库名。 例如`-lpthread`,`-lQt5Core`。会自动检索`libpthread.so,libpthread.a,libQt5Core.so,libQt5Core.a`
- `-l`，指定去哪里找库文件。例如指定：`-L/home/threedog/test`，则在编译时会优先检索`/home/threedog/test/libpthread.so`等文件。
- 链接库最直接的办法是不用任何参数，直接写库的路径加载编译参数里。
- 查找顺序
  - 如果直接写的库的全路径，则会直接去找到库，不走下面的顺序检索。
  - -L，优先级最高
  - 然后是系统的环境变量LIBRARY_PATH
  - 最后再找内定目录 /lib /usr/lib /usr/local/lib 这是当初编译 gcc时写在程序内的
  - 如果都找不到，会报错找不到文件或找不到-lxxxx



g++链接库时，默认优先链接动态链接库。静态库与动态库混合链接时，有如下两种方法：
 （1）静态链接库使用绝对路径，动态链接库使用-l。以boost库为例，如果我们要使用静态库则可书写如下：

```shell
 g++ main.cpp -pthread /usr/lib64/libboost_thread.a /usr/lib64/libboost_system.a
```

（2）使用 `-Wl,-Bstatic` 告诉链接器 `ld` 链接静态库，不存在静态库，则`ld`报错，只存在动态链接库也报错。使用 `-Wl,-Bdynamic` 告诉链接器**优先**使用动态链接库，如果只存在静态库，则链接静态库，不报错。示例如下：

```
g++  main.cpp -Wl,-Bstatic -lboost_system -lboost_thread -Wl,-Bdynamic
```

**注意：**

（1）命令末尾`-Wl,-Bdynamic`，作用是告诉链接器，后续系统库的链接默认使用动态链接，否则会出现找不到系统库的错误，诸如：

```
/usr/bin/ld: cannot find -lgcc_s
collect2: ld returned 1 exit status
```

（2）链接时，库要放在目标文件的后面，否则会报"undefined reference to: xxx"错误。具体参见gcc手册的如下描述：

```
the linker searches and processes libraries and object files in the order they are 
specified. Thus, `foo.o -lz bar.o' searches library `z' after file foo.o but before 
bar.o. If bar.o refers to functions in `z', those functions may not be loaded.
```



## 5. 静态库制作

静态库名字以 lib 开头，以.a 结尾

例如：libmylib.a

静态库生成指令

ar rcs libmylib.a file1.o

**步骤：**

1. 写好源代码
2. 编译源代码生成.o 文件：`gcc - c xxx.c -0 xxx.o`
3. 制作静态库：`ar rcs libname.a file1.o file2.o `
4. 静态库的使用：`g++ test.cpp lib 库名.a -o a.out`

用编译器隐式声明来解决的编译发出的警告问题

编译器只能隐式声明返回值为 int 的函数形式：int add(int ,int )； 如果函数不是返回的 int，则隐式声明失效，会报错

**可以使用头文件的方法加载静态库：**

1. 创建 .h 头文件

   ```c++
   #ifndef   xxx	//define 为头文件守卫
   #define	  xxx	//防止在代码中多次 include 头文件，多次展开静态库，带来的额外开销
   
   //声明			
   
   #endif
   ```

2. `g++ main.cpp(程序名) [静态库的路径] -o [生成可执行文件名] -I [头文件路径]  `



------



## 6. 动态库制作

1. 生成位置无关的.o 文件

   gcc -c add.c -o add.o -fPIC

   使用这个参数过后，生成的函数就和位置无关，挂上@plt 标识，等待动态绑定

2. 使用 gcc -shared 制作动态库

   gcc -shared -o lib 库名.so add.o sub.o div.o

3. 编译可执行程序时指定所使用的动态库。-l:指定库名 -L:指定库路径

   gcc test.c -o a.out -l mymath -L ./lib

4.  运行可执行程序./a.out

**动态库加载错误原因及解决方式** 

出错原因分析： 

连接器： 工作于链接阶段，工作时需要 -l 和 -L 

动态链接器： 工作于程序运行阶段，工作时需要提供动态库所在目录位置 

指定动态库路径并使其生效，然后再执行文件 （临时生效，终端重启环境变量失效）

**四种解决方法：**

【1】通过环境变量指定动态库所在位置：**export LD_LIBRARY_PATH=动态库路径**

【2】为了让永久生效，需要修改到 .bashrc 

1.  vi ~/.bashrc
2.  写入 export LD_LIBRARY_PATH=动态库路径 保存
3.  ..bashrc 或者 source .bashrc 或者重开终端让其自己加载
3.  ./a.out 成功！！！--- 使用 ldd a.out 查看

【3】拷贝自定义动态库 到 /lib (标准 C 库所在目录位置)

​		`cp lib动态库名称.so /lib`

【4】配置文件法

1. sudo vi /etc/ld.so.conf
2. 写入 动态库绝对路径 保存
3. sudo ldconfig -v 使配置文件生效
4. ./a.out 成功！！！--- 使用 ldd a.out 查看



------



# gdb

## 1. 调试基础指令

使用 gdb 之前，要求对文件进行编译时增加-g 参数，加了这个参数过后生成的编译文件会大一些， 这是因为增加了 gdb 调试内容 

gdb 调试工具： 大前提：程序是你自己写的。 ---逻辑错误

**基础指令：**

`-g`：使用该参数编译可以执行文件，得到调试表

`gdb ./[程序名]`

`list`： list 1 列出源码。根据源码指定 行号设置断点

`b`： b 20 在 20 行位置设置断点

`run/r`: 运行程序

`n/next`: 下一条指令（会越过函数）

`s/step`: 下一条指令（会进入函数）

`p/print`：p i 查看变量的值

`continue`：继续执行断点后续指令

`finish`：结束当前函数调用

`quit`：退出 gdb 当前调试

**其他指令：**

`run`：使用 run 查找段错误出现位置

`set args`： 设置 main 函数命令行参数 （在 start、run 之前） 

`run 字串 1 字串 2 ...`: 设置 main 函数命令行参数 

`info b`: 查看断点信息表 

`b 20 if i = 5`： 设置条件断点 

`ptype`：查看变量类型 

`bt`：列出当前程序正存活着的栈帧

`frame`： 根据栈帧编号，切换栈帧

`display`：设置跟踪变量

`undisplay`：取消设置跟踪变量。 使用跟踪变量的编号



**gdb 常见错误说明**

没有符号表被读取—编译时没加-g 参数 

file 后面加使用-g 编译的文件，可以不用退出，gdb 直接读取后进行调试。
