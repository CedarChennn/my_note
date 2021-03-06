# gcc

## 静态库（static library）

### 制作静态库(linux)
下面以fun1.c , fun2.c和head.h三个文件为例讲述静态库的制作和使用, 其中head.h文件中有函数的声明,  fun1.c和fun2.c中有函数的实现.
```shell
gcc -c fun1.c fun2.c
ar rcs libtest1.a fun1.o fun2.o
gcc  main.c -L./ -l test1 -I./  -o main1
```
### 静态库的创建(windows)

1. 创建一个新项目，在已安装的模板中选择“常规”，在右边的类型下选择“空项目”，在名称和解决方案名称中输入staticlib。点击确定。

2. 在解决方案资源管理器的头文件中添加,mylib.h文件，在源文件添加mylib.c文件（即实现文件）。

3. 在mylib.h文件中添加如下代码：
```C++
#ifndef TEST_H
#define TEST_H int myadd**(**int a**,**int b**);**
#endif
```
4. 在mylib.c文件中添加如下代码：
```C
#include"test.h"
int myadd(int a,int b)
{return a + b;} 
```
5. 配置项目属性。因为这是一个静态链接库，所以应在项目属性的“配置属性”下选择“常规”，在其下的配置类型中选择“静态库（.lib）。

6. 编译生成新的解决方案，在Debug文件夹下会得到mylib.lib (对象文件库），将该.lib文件和相应头文件给用户，用户就可以使用该库里的函数了。


### 静态库的使用(windows)

- 方法一：配置项目属性

1. 添加工程的头文件目录：工程---属性---配置属性---c/c++---常规---附加包含目录：加上头文件存放目录。
2. 添加文件引用的lib静态库路径：工程---属性---配置属性---链接器---常规---附加库目录：加上lib文件存放目录。
3.  然后添加工程引用的lib文件名：工程---属性---配置属性---链接器---输入---附加依赖项：加上lib文件名。

- 方法二：使用编译语句
```C
#pragma comment(lib,"./mylib.lib")
```
- 方法三：添加工程中

就像你添加.h和.c文件一样,把lib文件添加到工程文件列表中去.切换到"解决方案视图",--->选中要添加lib的工程-->点击右键-->"添加"-->"现有项"-->选择lib文件-->确定.


### 优点
1. 函数库最终被打包到应用程序中，实现是函数本地化，寻址方便、速度快。
（库函数调用效率==自定义函数使用效率）
2. 程序在运行时与函数库再无瓜葛，移植方便。

### 缺点

1. 消耗系统资源较大, 每个进程使用静态库都要复制一份, 无端浪费内存。
2. 静态库会给程序的更新、部署和发布带来麻烦。如果静态库libxxx.a更新了，所有使用它的应用程序都需要重新编译、发布给用户（对于玩家来说，可能是一个很小的改动，却导致整个程序重新下载）。

- 静态库对函数库的链接是放在编译时期完成的，静态库在程序的链接阶段被复制到了程序中，和程序运行的时候没有关系；
- 程序在运行时与函数库再无瓜葛，移植方便。
- 浪费空间和资源，所有相关的目标文件与牵涉到的函数库被链接合成一个可执行文件。



- 程序开发和发布:空间浪费是静态链接的一个问题，另一个问题是静态链接对程序的更新、部署和发布也会带来很多麻烦。比如程序中所使用的mylib.lib是由一个第三方厂商提供的，当该厂商更新容量mylib.lib的时候，那么我们的程序就要拿到最新版的mylib.lib，然后将其重新编译链接后，将新的程序整个发布给用户。这样的做缺点很明显，即一旦程序中有任何模块更新，整个程序就要重新编译链接、发布给用户，用户要重新安装整个程序。



## 动态库
要解决空间浪费和更新困难这两个问题，最简单的办法就是把程序的模块相互分割开来，形成独立的文件，而不是将他们静态的链接在一起。简单地讲，就是不对哪些组成程序的目标程序进行链接，等程序运行的时候才进行链接。也就是说，把整个链接过程推迟到了运行时再进行，这就是动态链接的基本思想。

### 制作

```shell
gcc -fpic -c fun1.c fun2.c
gcc -shared fun1.o fun2.o -o libtest2.so
//让系统找到共享库
gcc main.c  -L./  -l test2  -I./ -o main2
```

### 如何让系统找到共享库

- 拷贝自己制作的共享库到/lib或者/usr/lib
- 临时设置LD_LIBRARY_PATH:
```bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:库路径
```
- 永久设置, 把export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:库路径, 设置到∼/.bashrc文件中, 然后在执行下列三种办法之一:
	- 执行. ~/.bashrc使配置文件生效(第一个.后面有一个空格)
	- 执行source ~/.bashrc配置文件生效
	- 退出当前终端, 然后再次登陆也可以使配置文件生效
- 永久设置,把export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:库路径，设置到/etc/profile文件中
- 将其添加到 /etc/ld.so.cache文件中
	- 编辑/etc/ld.so.conf文件, 加入库文件所在目录的路径
	- 运行sudo ldconfig -v, 该命令会重建/etc/ld.so.cache文件

###  动态库的创建(windows)

1. 创建一个新项目，在已安装的模板中选择“常规”，在右边的类型下选择“空项目”，在名称和解决方案名称中输入mydll。点击确定。

2. 在解决方案资源管理器的头文件中添加,mydll.h文件，在源文件添加mydll.c文件（即实现文件）。

3. 在test.h文件中添加如下代码：

#ifndef TEST_H#define TEST_H __declspec**(**dllexport**)** int myminus**(**int a**,** int b**);** #endif

5.在test.c文件中添加如下代码：

#include"test.h"__declspec**(**dllexport**)** int myminus**(**int a**,** int b**){**	**return** a **-** b**;****}**

5. 配置项目属性。因为这是一个动态链接库，所以应在项目属性的“配置属性”下选择“常规”，在其下的配置类型中选择“动态库（.dll）。

6. 编译生成新的解决方案，在Debug文件夹下会得到mydll.dll (对象文件库），将该.dll文件、.lib文件和相应头文件给用户，用户就可以使用该库里的函数了。

	- 疑问一：__declspec(dllexport)是什么意思？动态链接库中定义有两种函数：导出函数(export  function)和内部函数(internal  function)。 导出函数可以被其它模块调用，内部函数在定义它们的DLL程序内部使用。 
	- 疑问二：动态库的lib文件和静态库的lib文件的区别？在使用动态库的时候，往往提供两个文件：一个引入库（.lib）文件（也称“导入库文件”）和一个DLL（.dll）文件。虽然引入库的后缀名也是“lib”，但是，动态库的引入库文件和静态库文件有着本质的区别，对一个DLL文件来说，其引入库文件（.lib）包含该DLL导出的函数和变量的符号名，而.dll文件包含该DLL实际的函数和数据。在使用动态库的情况下，在编译链接可执行文件时，只需要链接该DLL的引入库文件，该DLL中的函数代码和数据并不复制到可执行文件，直到可执行程序运行时，才去加载所需的DLL，将该DLL映射到进程的地址空间中，然后访问DLL中导出的函数。

### 动态库使用(windows)
#### 方法一：隐式调用

创建主程序TestDll，将mydll.h、mydll.dll和mydll.lib复制到源代码目录下。(P.S：头文件Func.h并不是必需的，只是C++中使用外部函数时，需要先进行声明)在程序中指定链接引用链接库 : #pragma comment(lib,"./mydll.lib")

#### 方法二：显式调用*

HANDLE hDll**;** //声明一个dll实例文件句柄 hDll = LoadLibrary**("mydll.dll");** //导入动态链接库 MYFUNC minus_test**;** //创建函数指针 //获取导入函数的函数指针 minus_test = (MYFUNC)GetProcAddress(hDll, "myminus");
### 特点
- 动态库把对一些库函数的链接载入推迟到程序运行的时期。
- 可以实现进程之间的资源共享。（因此动态库也称为共享库）
- 将一些程序升级变得简单。
- 甚至可以真正做到链接载入完全由程序员在程序代码中控制（显示调用）

## 比较
 静态库的优点:
 	1 执行速度快, 是因为静态库已经编译到可执行文件内部了
 	2 移植方便, 不依赖域其他的库文件
 缺点: 
 	1 耗费内存, 是由于每一个静态库的可执行程序都会加载一次
 	2 部署更新麻烦, 因为静态库修改以后所有的调用到这个静态库的可执行文
件都需要重新编译

 动态库的优点:
 	1 节省内存
 	2 部署升级更新方便, 只需替换动态库即可, 然后再重启服务.
 缺点: 
 	1 加载速度比静态库慢
 	2 移植性差, 需要把所有用到的动态库都移植.

由于由静态库生成的可执行文件是把静态库加载到了其内部, 所以静态库生成的可执行文件一般会比动态库大.
- QT gcc path
 `D:\Programs\QT\Tools\mingw810_64\bin`

| 选项         | 含义                                                         |
| ------------ | ------------------------------------------------------------ |
| \-o file     | 指定生成的输出文件名为file                                   |
| \-E          | 只进行预处理 ,生成`.i`                                       |
| \-S\(大写\)  | 只进行预处理和编译,生成`.s`                                  |
| \-c\(小写\)  | 只进行预处理、编译和汇编,生成`.o`                            |
| -l   library | Search the library named library when linking                |
| -L dir       | Add directory dir to the list of directories to be searched for -l |
| -I           | specify directories to search for header files               |


| ***\*文件后缀\**** | ***\*含义\****        |
| ------------------ | --------------------- |
| .c                 | C 语言文件            |
| .i                 | 预处理后的 C 语言文件 |
| .s                 | 编译后的汇编文件      |
| .o                 | 编译后的目标文件      |

# makefile

## 基本规则

```makefile
target: object
	command
```



当前目录下有main.c fun1.c fun2.c sum.c, 根据这个基本规则编写一个简单的makefile文件, 生成可执行文件main.

- 第一版

  ```makefile
  main:main.c fun1.c fun2.c sum.c
  	gcc main.c fun1.c fun2.c sum.c -o main.out -I ./
  ```

  

- 第二版

  ```makefile
  main.out:main.o fun1.o fun2.o sum.o
  	gcc main.o fun1.o fun2.o sum.o -o main.out
  main.o:main.c
  	gcc  main.c -c  -o main.o  -I ./
  fun1.o:fun1.c
  	gcc fun1.c -c -o fun1.o
  fun2.o:fun2.c
  	gcc fun2.c -c -o fun2.o
  sum.o:sum.c
  	gcc sum.c -c -o sum.o
  ```

  



- 第三版

  ```makefile
  target=main.out
  object = main.o fun1.o fun2.o sum.o
  CC= gcc
  CPPFLAGS = -I ./
  $(target):$(object)
  	$(CC) $^ -o $@
  %.o:%.c
  	$(CC)  $< -c  -o $@  $(CPPFLAGS)
  ```

- 第四版

  ```makefile
  src = $(wildcard ./*.c)
  object = $(patsubst %.c,%.o,$(src))
  target = main.out
  CC= gcc
  CPPFLAGS = -I ./
  $(target):$(object)
  	$(CC) $^ -o $@
  %.o:%.c
  	$(CC)  $< -c  -o $@  $(CPPFLAGS)
  ```

  

- 第五版

  ```makefile
  src = $(wildcard ./*.c)
  object = $(patsubst %.c,%.o,$(src))
  target = main.out
  CC= gcc
  CPPFLAGS = -I ./
  $(target):$(object)
  	$(CC) $^ -o $@
  %.o:%.c
  	$(CC)  $< -c  -o $@  $(CPPFLAGS)
  
  .PHONY:clean
  clean:
  	-rm -f $(target) $(object)
  ```

  











| ***\*整型常量\**** | ***\*所需类型\****         |
| ------------------ | -------------------------- |
| 10                 | 代表int类型                |
| 10l, 10L           | 代表long类型               |
| 10ll, 10LL         | 代表long long类型          |
| 10u, 10U           | 代表unsigned int类型       |
| 10ul, 10UL         | 代表unsigned long类型      |
| 10ull, 10ULL       | 代表unsigned long long类型 |

| ***\*打印格式\**** | ***\*含义\****             |
| ------------------ | -------------------------- |
| %hd                | 输出short类型              |
| %d                 | 输出int类型                |
| %ld                | 输出long类型               |
| %lld               | 输出long long类型          |
| %hu                | 输出unsigned short类型     |
| %u                 | 输出unsigned int类型       |
| %lu                | 输出unsigned long类型      |
| %llu               | 输出unsigned long long类型 |


- 输入/输出函数家族

| 家族名  | 目的       | 可用于所有流 | 只用于stdin和stdout |
| ------- | ---------- | ------------ | ------------------- |
| getchar | 字符输入   | fgetc、getc  | getchar             |
| putchar | 字符输出   | fputc、putc  | putchar             |
| gets    | 文本行输入 | fgets        | gets                |
| puts    | 文本行输出 | fputs        | puts                |
| scanf   | 格式化输入 | fscanf       | scanf               |
| printf  | 格式化输出 | fprintf      | printf              |



# Operation system(Linux)
## process
```C
fork();
exec();
getpid();
getppid();

```

## interprocess communcation

- pipe
- fifo
- mmap
- signal

```c
signal();
kill();
abort();
alarm();
setitimer();
sigchld();
```







## thread

```c
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,void *(*start_routine) (void *), void *arg);
void pthread_exit(void *retval);
int pthread_join(pthread_t thread, void **retval);
int pthread_detach(pthread_t thread);
int pthread_cancel(pthread_t thread);
int pthread_equal(pthread_t t1, pthread_t t2);
```










