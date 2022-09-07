# Acm Tips

## C++输入输出基础

C++语言未定义任何输入输出语句，而是使用标准库（standard library）来提供IO机制

****C++输入的每个字符串 由 空白（空格符 /换行符/制表符间）隔开，文件结束标识符为crtl+Z(windows)，control+D(linux)****

### **两种常用的输入模式**

**cin >> x**

- 自动跳过 ==`tab/space/enter`== 取数据；
- 从缓冲区读数据到变量 `x` , 而不是从键盘输入读取。(读取前清空缓冲区：`cin.sync();`)

**用法解析**

1. 输入一个数字或字符

2. 接收一个字符串(到char数组或string)，遇  ==`tab/space/enter`== 结束

    但是并不会吃掉遇到的 **空格/换行符**，该空格/换行符还会参与下一次的读取。

**getline(cin, x)**

- 需要 ==`#include <string>`== 头文件

- 可以读入 ==`space/tab`== ，遇到 ==`enter`== 停止读取，**且读取完成后会丢弃末尾的换行符**。

- `getline()`函数的全部参数为 ==`getline(istream is, string str, 结束符)`==

    结束符默认是换行符 ==`\n`==

- 当同时使用 `cin>>, getline()` 时，需要注意的是，在 `cin>>` 读入数据完成之后，如果接下来的字符是换行符，那么在使用 `getline()` 之前需要用 ==`getchar()/cin.get()/cin.ignore() `== 把换行符吞掉，然后在使用 `getlint()` 读入下一行数据。

    否则，`getlint()`会首先读入换行符，然后停止读取，最终导致最后读入的数据为空。



cin>> 语句读取用户输入的数据时，它会在遇到换行符时停止。换行字符未被读取，而是仍保留在键盘缓冲区中。从键盘读取数据的输入语句只在键盘缓冲区为空时等待用户输入值，但现在不为空。

cin 遇空格或换行，会停止识别，如果你打算输入的字符串中带1个或多个空格，则采用getline把停止识别的符号设置为‘\n’（即换行符），就能正确输入输出了。

cin.get 函数执行时，它开始从先前输入操作停止的键盘缓冲区读取，并发现了换行符，所以它无须等待用户输入另一个值。

读取string对象时，string对象会自动忽略开头处的空白（即空格符、换行符、制表符等），并从第一个真正的字符开始读起，直到遇见下一处空白为止，认为为一个字符串。



### 四种基本输入形式

**1. 一组输入数据**

C语法：

```c
#include <stdio.h>
int main() {
    int a,b;
    scanf("%d %d",&a, &b);
    printf("%d\n",a+b);
}
```

注意：输入前不要打印提示信息。输出完毕后立即终止程序，不要等待用户按键。

C++语法：

```c++
#include<iostream>
using namespace std;
int main() {
    int a ,b;
    cin>>a>>b;
    cout<<a+b<<endl;
    return 0;
}
```

**2. 多组输入数据，不说明多少组，直到读至输入文件末尾为止**

```c
#include<iostream>
using namespace std;
int main() {
    int a ,b;
    cin>>a>>b;
    cout<<a+b<<endl;
    return 0;
}
```

说明：scanf函数返回值就是读出的变量个数，如：scanf( “%d %d”, &a, &b );如果只有一个整数输入，返回值是1，如果有两个整数输入，返回值是2，如果一个都没有，则返回值是EOF。EOF是一个预定义的常量，等于-1。

C++语法：

```c++
#include<iostream>
using namespace std;
int main() {
    int a ,b;
    while (cin>>a>>b)
        cout<<a+b<<endl;
    return 0;
}
```

cin是一个对象，表达式cin >> m >> n在读入发生错误返回0，否则返回cin的地址。

**3. 多组输入数据，不说明多少组，以某特殊输入为结束标志**

C语法：

```c
#include <stdio.h>
int main() {
    int a,b;
    while(scanf("%d %d",&a, &b) && (a||b))
         printf("%d\n",a+b);
}
```

C++语法：

```c++
#include<iostream>
using namespace std;
int main() {
    int a ,b;
    while(cin>>a>>b&&(a||b))
        cout<<a+b<<endl;
    return 0;
}
```

**4. 多组输入数据，开始输入一个N，接下来是N组数据**

C语法：

```c
#include<stdio.h>
int main() {
    int a ,b,n;
    scanf("%d",&n);
    while(n--) {
        scanf("%d %d",&a, &b);
        printf("%d\n",a+b);
    }
    return 0;
}
```

C++语法：

```c++
#include<iostream>
using namespace std;
int main() {
    int a ,b,n;
    cin>>n
    while(n--) {
     cin>>a>>b;
     cout<<a+b<<endl;
    }
    return 0;
}
```



### 三种字符串输入

**1. 每个字符串中不含空格、制表符及回车**

给定**一行字符串**，每个字符串用**空格**间隔，**一个样例为一行，用 `scanf("%s",str)` 

```c
char str1[1000], str2[1000];
scanf("%s%s", str1, str2);
```

```c++
string str;
cin >> str;
```

如果字符串内有空格，则用 `get`

```c++
string str;
gets(str);
```

**2、字符串中含有空格、制表符，但不含回车**

给定**一行字符串**，每个字符串用**逗号**间隔，**一个样例为一行**

方法：使用 getline 读取一整行字符串到字符串input中，然后使用字符串流stringstream，读取单个数字或者字符。每个字符中间用','间隔。

istream& getline ( istream &is , string &str , char delim ); 

1. istream &is 表示一个输入流，譬如cin；
2. string&str表示把从输入流读入的字符串存放在这个字符串中
3. char delim表示遇到这个字符停止读入，在不设置的情况下系统默认该字符为'\n'(回车)

gets函数用回车作为字符串的分界符，gets返回NULL表示出错或end of file。

```c++
while (getline(cin, input)) {  //读取一行
  	vector<string> strs;
   	string str;
    stringstream ss(input);
    while(getline(ss, str,','))	
      	strs.push_back(str);
```

这样，str的内容就是"Hello world!"了。另外，gets返回NULL表示出错或end of file。

**3、给定一行字符串，每个字符串用空格间隔，一个样例为一行**

```c++
string input;
while (getline(cin, input)) {  //读取一行
	stringstream data(input);  //使用字符串流
	int num = 0, sum = 0;
	while (data >> num) 
		sum += num;
```



## 常用优化(实用/调试/技巧)

### 1. 万能编译头文件

`#include <bits/stdc++.h>`

```c++
#include <iostream> 
#include <cstdio> 
#include <fstream> 
#include <algorithm> 
#include <cmath> 
#include <deque> 
#include <vector> 
#include <queue> 
#include <string> 
#include <cstring> 
#include <map> 
#include <stack> 
#include <set> 
```

主要用途：减少代码量



### 2. 预编译

**预处理洪宏**

```c++
#define long long ll
```

C++/C语言中条件编译相关的预编译指令，包括 `#define、#undef、#ifdef、#ifndef、#if、#elif、#else、#endif、defined`

> #define            定义一个预处理宏
> #undef            取消宏的定义 																																	#if                   编译预处理中的条件命令，相当于C语法中的if语句
> #ifdef              判断某个宏是否被定义，若已定义，执行随后的语句
> #ifndef            与#ifdef相反，判断某个宏是否未被定义
> #elif                若#if, #ifdef, #ifndef或前面的#elif条件不满足，则执行#elif之后的语句，相当于C语法中的else-if
> #else              与#if, #ifdef, #ifndef对应, 若这些条件不满足，则执行#else之后的语句，相当于C语法中的else
> #endif             #if, #ifdef, #ifndef这些条件命令的结束标志.
> defined         　与#if, #elif配合使用，判断某个宏是否被定义																				

主要用途：替换、选择性编译



### 3. 文件输入/文件输出

```c++
#define DEBUG
int main() {
    #ifdef DEBUG
	    freopen("input.in", "r", stdin);
	    //freopen("output.out", "w", stdout);
    #endif
    int n;
    scanf("%d",&n);
    //cout << "Hello world!" << endl;
    return 0;
}
// 取消的话只需要 注释掉 #define DEBUG
// input.in文件一定要放在程序所在目录下
```

主要用途：减少DEBUG中繁琐的复制/粘贴/输入操作



### 4. 程序运行时间

```c++
#include<iostream>
#include<ctime>
using namespace std;
#define DEBUG
int main() {
    int n;
    scanf("%d",&n);
    //cout << "Hello world!" << endl;
    #ifdef DEBUG
	    printf("Time cost : %lf s\n",(double)clock()/CLOCKS_PER_SEC);
    #endif
    return 0; 
}
```

注意：此代码记录输入时间，故应该配合**文件输入/文件输出**代码

主要用途：与**文件输入/文件输出**同时使用，初步计算程序运行时间



### 5. typedef

```c++
typedef long long ll;
//typedef __int128 lll;
// 类似于宏编译#define
#define long long ll;
#define __int128 lll;
```

主要用途：减少代码量

typedef 是用来定义一种类型的新别名的，它不同于宏（#define），不是简单的字符串替换。它的新名字具有一定的封装性，所以新命名的标识符具有更易定义变量的功能，它是语言编译过程的一部分，但它并不实际分配内存空间。

 #define 只是简单的字符串替换（原地扩展），它本身并不在编译过程中进行，而是在这之前（预处理过程）就已经完成了。因此，它不会做正确性检查，不管含义是否正确它照样会带入，只有在编译已被展开的源程序时才会发现可能的错误并报错。



### 6. cin和cout取消同步

```c++
ios::sync_with_stdio(false);
cin.tie(0);
cout.tie(0);
```

主要用途：减少输入输出的时间



### 7. endl、"\n"和'\n'

**"\n"**

"\n"表示搜索一个字符串，只有一个数据是回车符

**'\n'**

'\n' 表示一个字符，两者在输出上是一样的！

**endl**

1. 在c++中，终端输出换行时，用cout<<......<<endl 与 “\n”都可以，这是初级的认识。但二者有小小的区别，用endl时会刷新缓冲区，使得栈中的东西刷新一次，但用“\n”不会刷新，它只会换行，盏内数据没有变化。但一般情况，二者的这点区别是很小的，在大的程序中可能会用到。

2. endl除了写’\n’进外，还调用flush函数，刷新缓冲区，把缓冲区里的数据写入文件或屏幕.考虑效率就用’\n’。
3. cout << endl;除了往输出流中插入一个’\n’还有刷新输出流的作用。
4. cout << endl; 等价于: cout << ‘\n’ << flush; 
5. **在没有必要刷新输出流的时候应尽量使用cout << ‘\n’, 过多的endl是影响程序执行效率低下的因素之一**



### 8. 常量

```c++
const int N = 1000+10;
const int M = 100000+10;
const int MOD = 1e9+7;
const double PI = acos(-1.0);
const double EXP = 1E-8;
const int INF = 0x3f3f3f3f;
```

主要用途：提高代码复用性



### 9. O1,O2,O3编译优化

```c++
#pragma GCC optimize("O3")
#pragma G++ optimize("O3")
```



### 10. 输出空格分隔、末尾无空格的数组

```c++
for (int i=1;i<=n;i++) {
    printf("%d%c",a[i]," \n"[i==n]);
}
```

