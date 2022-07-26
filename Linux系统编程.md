# open函数



open函数如下，有两个版本的

![open1](https://raw.githubusercontent.com/lixuanxi/image/main/linux/open1.png)

返回一个文件描述符，理解为整数，出错返回-1

pathname   文件路径

flags     权限控制，只读，只写，读写。 O_RDONLY, O_WRONLY, O_RDWR

 

第二个open

多了一个mode参数，用来指定文件的权限，数字设定法

文件权限 = mode & ~umask

 

open常见错误：

1. 打开文件不存在

2. 以写方式打开只读文件（权限问题）

3. 以只写方式打开目录

4. 当open出错时，程序会自动设置errno，可以通过strerror(errno)来查看报错数字的含义

   以打开不存在文件为例：

![open2](https://raw.githubusercontent.com/lixuanxi/image/main/linux/open2.png)

​	执行该代码：

![open3](https://raw.githubusercontent.com/lixuanxi/image/main/linux/open3.png)



open函数：

​	int open(char *pathname, int flags)  #include <unistd.h>

参数：

​		pathname: 欲打开的文件路径名

​		flags：文件打开方式：  #include <fcntl.h> 	O_RDONLY|O_WRONLY|O_RDWR 	

  				O_CREAT|O_APPEND|O_TRUNC|O_EXCL|O_NONBLOCK ....

​		mode: 参数3使用的前提， 参2指定了 O_CREAT。 

​	  			取值8进制数，用来描述文件的访问权限。rwx  0664 

返回值：

​		成功： 打开文件所得到对应的 文件描述符（整数）

​		失败： -1， 设置errno  

 

 close函数：

 		int close(int fd);





# read、write函数

**read函数：**

  ssize_t read(int fd, void *buf, size_t count);

  参数：

​    	fd：文件描述符

​    	buf：存数据的缓冲区

​    	count：缓冲区大小

  返回值：

​    	0：读到文件末尾。

​    	成功； > 0 读到的字节数。

​    	失败： -1， 设置 errno

​    	-1： 并且 errno = EAGIN 或 EWOULDBLOCK, 说明不是read失败，而是read在以非阻塞方式读一个设备文件（网络文件），并且文件无数据。



**write函数：**

  ssize_t write(int fd, const void *buf, size_t count);

  参数：

​    	fd：文件描述符

​    	buf：待写出数据的缓冲区

​    	count：数据大小

  返回值：

​    	成功； 写入的字节数。

   	 失败： -1， 设置 errno



可以在复制函数里加入错误检测：

```c++
if (fd1 == -1) {
	perror(“open argv[1] error”);
	exit(1);		//头文件 stdlib.h
}
```

错误处理函数：		与 errno 相关

```c++
printf("xxx error: %d\n", errno);

char *strerror(int errnum);

printf("xxx error: %s\n", strerror(errno));

void perror(const char *s);

	perror("open error");	// 头文件 stdio.h
```

![read1](https://raw.githubusercontent.com/lixuanxi/image/main/linux/read1.png)





![read2](https://raw.githubusercontent.com/lixuanxi/image/main/linux/read2.png)





![read3](https://raw.githubusercontent.com/lixuanxi/image/main/linux/read3.png)





# 系统调用和库函数比较—预读入缓输出

![预读入缓输出1](https://raw.githubusercontent.com/lixuanxi/image/main/linux/%E9%A2%84%E8%AF%BB%E5%85%A5%E7%BC%93%E8%BE%93%E5%87%BA1.png)

![预读入缓输出2](https://raw.githubusercontent.com/lixuanxi/image/main/linux/%E9%A2%84%E8%AF%BB%E5%85%A5%E7%BC%93%E8%BE%93%E5%87%BA2.png)

原因分析： 

read/write 这块，每次写一个字节，会疯狂进行内核态和用户态的切换，所以非常耗时。 

fgetc/fputc，有个缓冲区，4096，所以它并不是一个字节一个字节地写，内核和用户切换就比较少 



预读入，缓输出机制。 所以系统函数并不是一定比库函数牛逼，能使用库函数的地方就使用库函数。 

标准 IO 函数自带用户缓冲区，系统调用无用户级缓冲。系统缓冲区是都有的。





# 文件描述符

![文件描述符1](C:\Users\lxx\Desktop\文件描述符1.png)



文件描述符是指向一个文件结构体的指针

PCB进程控制块：本质 结构体。

  	成员：文件描述符表。
  	
  	文件描述符：0/1/2/3/4。。。。/1023   遵循原理：表中可用的且最小的。
  	
  		0 - STDIN_FILENO
  	
  		1 - STDOUT_FILENO
  	
  		2 - STDERR_FILENO



# 阻塞和非阻塞

阻塞、非阻塞： 是设备文件、网络文件的属性。

​		产生阻塞的场景。 读设备文件。读网络文件。（读常规文件无阻塞概念。）

​		/dev/tty -- 终端文件。

​		open("/dev/tty", O_RDWR|O_NONBLOCK)  --- 设置 /dev/tty 非阻塞状态。(默认为阻塞状态)

 

# fcntl改文件属性

fcntl用来改变一个【已经打开】的文件的 访问控制属性

重点掌握两个参数的使用， F_GETFL，F_SETFL

 

fcntl：

  int (int fd, int cmd, ...)

fd   文件描述符

cmd  命令，决定了后续参数个数

 

  int flgs = fcntl(fd, F_GETFL);

 

  flgs |= O_NONBLOCK

 

  fcntl(fd, F_SETFL, flgs);

 

  获取文件状态： F_GETFL

 

  设置文件状态： F_SETFL
