---
coding: UTF-8
title: C++与Linux
date: 2026-03-22T23:39:23
updated: 2026-07-17T02:23:39
tags: []
share: true
---

# C++与Linux

## 1. GCC

### 1.1 编译

- GCC 编译器通常以 Linux 命令的形式在终端（Shell）中使用。如果需要其他特定的GCC组件，例如C++编译器（g++）或Fortran编译器（gfortran），需要额外再安装。
- GCC编译执行程序的4个步骤：预处理，编译，汇编，链接
- 预处理
	- hello.c->hello.i（gcc -E）
	- 展开宏与头文件，替换条件编译，删除注释与空行
- 编译
	- hello.i->hello.s（gcc -S）
	- 检查语法规范，编译为汇编指令
	- 消耗时间、系统资源最多
- 汇编
	- hello.s->hello.o（gcc -c）
	- 将汇编指令翻译为机器指令
- 链接
	- hello.o->a.out（gcc，参数-o是用来指定输出文件的名字）
	- 数据段合并
	- 数据地址回填：暂时记录动态库中函数的相对地址，在链接阶段找plt的地址作为基准地址进而找到动态库中函数的绝对地址。

### 1.2 指令

指令格式：gcc [options] [filenames]

| 选项    | 说明  |
| :---- | :------------------------------------ |
| -E    | 只进行预处理，生成预处理后的源代码文件（xx.o）             |
| -S    | 只进行预处理+编译，生成编译后的文件（xx.s）              |
| -c    | 只进行预处理+编译+汇编，生成目标文件（xx.o）             |
| -O    | 优化生成的代码，可以使用-O1、-O2或-O3进行不同级别的优化      |
| -g    | 编译时生成调试信息，以便进行源代码级调试 |
| -Wall | 显示编译时的警告信息           |
| -std  | 指定所使用的C语言标准，如-std=c11|
| -D    | 向程序中注册宏定义，相当于程序中的`#define NAME VALUE` |
| -I    | 指定包含头文件的目录           |
| -l    | 链接指定的库文件             |
| -L    | 指定链接库文件的目录           |

### 1.3 链接器

- 链接器：工作于链接阶段，工作时需要`-l`和`-L`。
- 动态链接器：工作于程序运行阶段，工作时需要提供动态库所在的目录位置。

| 维度   | 链接器（Linker）        | 动态链接器（Dynamic Linker）            |
| ---- | --------------------------------------------------------------------- | ------------------------------------------------------------------ |
| 工作阶段 | 编译后 → 生成可执行文件前（静态阶段）                | 程序启动时 / 运行中（动态阶段）                |
| 核心作用 | 1. 合并多个目标文件（`.o`）<br><br>2. 解析函数 / 变量引用<br><br>3. 链接静态库（`.a`）或标记动态库依赖 | 1. 找到程序依赖的动态库（`.so`）<br><br>2. 加载库到内存<br><br>3. 解析程序对动态库的函数 / 变量引用 |
| 操作对象 | 目标文件（`.o`）、静态库（`.a`）、动态库（`.so`）的 “引用标记”              | 已生成的可执行文件、动态库（`.so`）             |
| 输出结果 | 生成完整的可执行文件（或共享库）   | 无 “输出文件”，仅让程序在内存中完成依赖加载，正常运行     |
| 典型工具 | Linux 下的`ld`（gcc 编译时自动调用）           | Linux 下的`ld-linux.so`（比如`/lib64/ld-linux-x86-64.so.2`）             |

- 例如制作了一个播放音乐的程序

- **链接器（Linker）的工作**：
    - 写的代码编译成`music.o`，播放功能需要调用 “音频库”；
    - 如果是**静态库**（`.a`）：链接器直接把音频库的代码 “复制” 到你的程序里，生成独立的`music.out`可执行文件；
    - 如果是**动态库**（`.so`）：链接器不复制代码，只在`music.out`里 “记一笔”：“我需要`libaudio.so`这个库才能运行”，然后生成可执行文件。

-  **动态链接器（Dynamic Linker）的工作**：
    - 双击`music.out`启动程序时，动态链接器先跑起来；
    - 它读取`music.out`里的 “依赖记录”，找到系统里的`libaudio.so`；
    - 把`libaudio.so`加载到内存，告诉程序 “音频函数的地址在这里，你可以调用了”；
    - 程序真正开始播放音乐。

## 2. GDB

### 2.1 使用方法

- 要想使用gdb，需要在编译时开启调试功能

```cpp
gcc：在编译时添加 -g 选项生成调试信息
gcc -g -o program program.c(-g：生成调试符号，便于 GDB 进行源代码级调试。)

Makefile：大项目时添加
CC = gcc
CFLAGS = -Wall -g

CMakeLists.txt：构建工具
对于C++:
在CMakeLists.txt文件中添加如下语句：
SET(CMAKE_BUILD_TYPE "Debug")
SET(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g2 -ggdb")
SET(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")

对于C:
在CMakeLists.txt文件中添加如下语句：
SET(CMAKE_BUILD_TYPE "Debug")
SET(CMAKE_C_FLAGS_DEBUG "$ENV{CFLAGS} -O0 -Wall -g -ggdb")
SET(CMAKE_C_FLAGS_RELEASE "$ENV{CFLAGS} -O3 -Wall")
```

### 2.2 gdb指令

| 类型                                | 说明                                            | 指令                                                                                 |
| :-------------------------------- | :-------------------------------------------- | :--------------------------------------------------------------------------------- |
| 安装                                | 查看是否安装了gdb                                    | rpm -qa\|grep gdb                                                                  |
|                                   | 查看安装版本                                        | gdb -v                                                                             |
| 启动                                | 启动gdb                                         | gdb program                                                                        |
| 退出                                | 退出gdb                                         | quit；q                                                                             |
| 显示当前文件源码                          | 显示第linenum行的上下文内容                             | list linenum                                                                       |
|                                   | 显示函数名为function的函数的源程序                         | list function                                                                      |
|                                   | 显示当前行后面的源程序                                   | list                                                                               |
|                                   | 显示当前文件开始处的源程序                                 | list -                                                                             |
| 显示其他文件源码                          | 显示file文件下第linenum行                            | list file\:linenum                                                                 |
|                                   | 显示file文件的函数名为function的函数的源程序                  | list file\:function                                                                |
| 显示源码设置                            | 设置一次显示源代码的函数（一般打印当前行的上5行和下5行，默认是10行）          | set listsize count                                                                 |
|                                   | 查看当前listsize的设置                               | show listsize                                                                      |
| 断点                                | 设置断点                                          | break；b                                                                            |
| 当前文件打断点                           | 在第10行设置断点                                     | b 10                                                                               |
|                                   | 在func函数入口处设置断点                                | b func                                                                             |
|                                   | 设置临时断点（temporary breakpoint），即断点只会在首次触发后被自动删除 | tbreak \<location\>；例如b xxx.c:n if intValue ==5表示 如果intValue的值等于5，在xxx.c文件第n行中设置断点 |
| 其他文件打断点                           | 在源文件为filename的linenum行设置断点                    | b filename:linenum                                                                 |
|                                   | 在源文件filename的function函数的入口处设置断点               | b filename:function                                                                |
| 断点查询                              | 查询所有断点                                        | info b；info break； i b                                                             |
| 删除断点                              | 删除所有的断点                                       | delete                                                                             |
|                                   | 删除断点为num的断点                                   | delete num                                                                         |
|                                   | 删除不连续的断点 num1 num3                            | delete num1 num3                                                                   |
|                                   | 删除连续的断点，删除 n-m的断点                             | delete n-m                                                                         |
| 禁用断点                              | 指定断点无效,不会删除断点                                 | disable[range…]；dis [range…]                                                       |
| 启用断点                              | 指定断点有效，对应解开disable设置的无效断点                     | enable[range…]；ena[range…]                                                         |
| 运行控制                              | 执行代码                                          | run；r；run 参数1 参数2 ...                                                              |
|                                   | 不添加断点从第一行开始逐行执行代码                             | start                                                                              |
|                                   | 退出进入的函数，执行到当前函数返回为止                           | finish；fin                                                                         |
|                                   | 循环体内运行程序，直到退出循环体                              | until；u                                                                            |
|                                   | 继续运行程序，如果有断点，则调到下一个断点处                        | continue；c                                                                         |
| 单步调试                              | 函数调用当做一条简单语句执行                                | next；n                                                                             |
|                                   | 函数调用进入被调用函数体内                                 | step；s                                                                             |
| 查看运行时变量的值                         | 打印var的值                                       | print var；p var                                                                    |
|                                   | 打印var的地址                                      | print &var；p &var                                                                  |
|                                   | 查看var的类型                                      | ptype var                                                                          |
| 自动显示变量的值，当程序停住时，或在单步追踪时，这些变量会自动显示 | 设置自动显示                                        | display 变量名                                                                        |
|                                   | 查看display设置的自动显示的信息                           | info display                                                                       |
|                                   | 删除自动显示                                        | undisplay num(info display时显示的编号）                                                  |
|                                   | 删除自动显示                                        | delete display num                                                                 |
|                                   | 禁用num显示                                       | disable display num                                                                |
|                                   | 启用num显示                                       | enable display num                                                                 |
| 监视变量                              | 监视变量var的值，当变量的值发生改变时，停止程序的执行                  | watch var；w var                                                                    |
|                                   | 查看watch设置的监视点的信息                              | info watchpoints                                                                   |
|                                   | 禁用num监视                                       | disable num                                                                        |
|                                   | 启用num监视                                       | enable num                                                                         |
| 堆栈跟踪                              | 显示当前函数调用的堆栈跟踪信息，列出栈帧(与函数一一对应)                 | backtrace；bt                                                                       |
|                                   | 切换栈帧                                          | frame num                                                                          |
| 查看当前调试状态的信息                       | 查看断点                                          | info break；i b                                                                     |
|                                   | 查看display                                     | info display                                                                       |
|                                   | 查看watch                                       | info w                                                                             |
|                                   | 查看函数的参数列表                                     | info args                                                                          |
|                                   | 查看当前函数的局部变量                                   | info locals                                                                        |
|                                   | 查看寄存器的值                                       | info registers；i r                                                                 |
|                                   | 显示当前线程的列表                                     | info threads                                                                       |
|                                   | 显示当前进程接收到的信号                                  | info signals                                                                       |

-  在 Linux 操作系统中，当程序执行发生异常崩溃时，系统可以将发生崩溃时的内存数据、调用堆栈情况等信息自动记录下载，并存储到一个文件中，该文件通常称为 core 文件，Linux 系统所具备的这种功能又称为核心转储（core dump）。GDB 对 core 文件的分析和调试提供有非常强大的功能支持，也可以通过 GDB 调试产生的 core 文件来快速解决问题。
	1. 查看是否开启 core dump 这一功能 ：ulimit -a
	2. core file size（core 文件大小）对应的值为 0，表示当前系统未开启 core dump 功能
	3. 开启 core dump：ulimit -c unlimited （unlimited 表示不限制 core 文件的大小）

## 3. 静态库和共享库

### 3.1 库的定义

- 程序库是包含了数据和执行码的文件，其不能单独执行，可以作为其他执行程序的一部分来完成某些功能。
- 程序库分为静态库（static library）和共享库（shared object）。
- 静态库是可执行程序运行欠就已经加入到执行码中，成为执行程序的一部分，静态库在应用程序生成时不必再编译，节省编译时间；共享库是在执行程序启动时加载到执行程序中的，可以被多个执行程序共享使用，动态库中函数调用绑定的函数地址要比静态库的晚。
- 使用场景
	- 静态库：对空间要求较低，而时间要求较高的核心程序。库函数本地化，调用速度快。
	- 动态库：对空间要求较高，而时间要求较低的程序。节省内存，便于更新，延迟绑定。

### 3.2 制作库

>[!NOTE] 制作库
> 制作库一般用的是编译后的机器码文件来提高效率，也不共享源码，只提供lib文件和相应的头文件。

- 静态库创建：
	1. 制作编译文件：`gcc -c file1.c -o file1.o`
	2. 制作库：`ar rcs libname.a file1.o file2.o ...`
	3. 使用时带上静态库：`gcc test.c ./lib/libxxx.a -o test`。
- 动态库创建：
	1. 制作编译文件：`gcc -c xxx.c -o xxx.o -fPIC`（源码需要生成与位置无关的代码）
	2. 制作库：`gcc -shared -o libname.so file1.o file2.o ...`
	3. 使用时要指定所使用的动态库：`gcc test.c -o test -l name -L ./lib`（-l后加库名，-L后加库的路径）
- 动态链接库使用时的问题及四种解决方法：[2.3 链接器](./%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BC%96%E8%AF%91.md##2.3%2520%E9%93%BE%E6%8E%A5%E5%99%A8)
	1. 在当前终端中执行`export LD_LIBRARY_PATH=动态库路径`，环境变量是跟着进程走的，当开启新的终端时就启动新的进程，需要再次执行该指令。不然动态连接器无法找到指定的动态库。
	2. 配置bash可以永久生效，在`./bashrc`文件中写入`export LD_LIBRARY_PATH=动态库路径`（建议绝对路径）并保存。然后`source ./bashrc`。
	3. 把动态库放入`/lib`或者`/lib64`下。可以通过`ldd 可执行文件`查看所需的动态库。
	4. 修改配置文件，`vi /etc/ld.so.conf`，追加写入动态库的绝对路径保存，`ldconfig -v`使配置文件生效。

### 3.3 说明

- 动态库是否加载到内存，取决于程序是否运行
- 动态库每次加载的位置不固定
- 动态库和静态库共存时，编译器默认优先使用动态库

## 4. Makefile

- makefile本质是个脚本，把一系列指令当作一个集合放在一个脚本中便于执行。
- 用途：
	- 项目代码编译管理
	- 节省编译项目时间
	- 一次编写终身受益
- 基本原则：
	- 若想生成目标，先检查规则中的依赖条件是否存在，如不存在，则寻找是否有规则用来生成该依赖文件。
	- 检查规则中的目标是否需要更新，必须先检查它的所有依赖，依赖中有任意一个文件被更新则目标必须更新。
	- 目标的时间必须晚于依赖条件的时间，否则更新目录。
- 重点：1个规则，2个函数，3个变量，模式规则，伪目标。
```cpp
命名：makefile 或者Makefile

//1个规则：
目标：依赖条件
    命令（命令前面是一个tab缩进）
例子：
hello:hello.o
	gcc hello.o -o hello
hello.o:hello.c
	gcc -c hello.c -o hello.o

//2个函数：
src = $(wildcard *.c) //匹配当前工作目录下的所有.c文件，并将其文件名组成列表赋值给变量src

obj = $(patsubst %.c, %.o, $(src)) //把参数3中包含参数1的部分替换为参数2，即把src变量里所有后缀为.c的文件体检成.o。


//3个变量
$@：在规则的命令中，表示规则中的目标。
$^：在规则的命令中，表示所有依赖条件。
$<：在规则的命令中，表示第一个依赖条件。如果将该变量应用在模式规则中，它可以将依赖条件列表中的依赖依次取出套用模式规则。
例子：
a.out: $(obj)
	gcc $^ -o $@
add.o: add.c
	gcc -c $< -o $@

//模式规则:规则代替，简化编写，便于扩展
例子：
%.o:%.c
	gcc -c $< -o $@

//静态模式规则：指定哪个变量用哪个规则
例子：
$(obj):%.o:%.c
	gcc -c $< -o $@

//伪目标:并不需要生成文件
例子：
.PHONY: clean ALL

//clean
clean没有依赖：“-”的作用是删除不存在文件时不报错。
例子：
clean:
	-rm -rf $(obj) a.out

//参数：
-n:模拟执行make，make clean命令
-f:指定文件执行make命令（适用于xxx.mk）
```

## 5. CMakeLists

```cpp
# CMakeList.txt
cmake_minimum_required(VERSION 3.15)

# 项目信息
project(MyProject VERSION 1.0 LANGUAGES CXX)

# 设置默认构建类型为 Debug
set(CMAKE_BUILD_TYPE Debug)

# 指定 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 针对 Debug 模式的编译选项
set(CMAKE_CXX_FLAGS_DEBUG "-g -O0")
set(CMAKE_C_FLAGS_DEBUG "-g -O0")

# 添加可执行目标
add_executable(my_executable main.cpp)
```

## 6. 内核管理

- 栈帧：随着函数调用而在stack上开辟的一段内存空间。用于存放函数调用时产生的局部变量和临时值。

![](../assets/C++%E4%B8%8ELinux.png)

![](../assets/C++%E4%B8%8ELinux-1.png)
![](../assets/C++%E4%B8%8ELinux-2.png)

## 7.

## 10. Linux Socket

### 10.1 服务端TCP

#### 10.1.1 服务端代码

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
 
#define PORT 8080
#define BUFFER_SIZE 1024
 
int main() {
    int server_fd, new_socket;
    struct sockaddr_in address;
    int addrlen = sizeof(address);
    char buffer[BUFFER_SIZE] = {0};
    const char *response = "Hello from server";
 
    // 1. 创建 TCP Socket
    if ((server_fd = socket(AF_INET, SOCK_STREAM, 0)) < 0) {
        perror("socket failed");
        exit(EXIT_FAILURE);
    }
 
    // 2. 设置地址结构
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY; 
    address.sin_port = htons(PORT);       
 
    // 3. 绑定Socket
    if (bind(server_fd, (struct sockaddr *)&address, sizeof(address)) < 0) {
        perror("bind failed");
        exit(EXIT_FAILURE);
    }
 
    // 4. 监听连接，最大等待队列长度3
    if (listen(server_fd, 3) < 0) {
        perror("listen failed");
        exit(EXIT_FAILURE);
    }
    printf("Server listening on port %d...\n", PORT);
 
    // 5. 接受客户端连接
    if ((new_socket = accept(server_fd, (struct sockaddr *)&address, (socklen_t*)&addrlen)) < 0) {
        perror("accept failed");
        exit(EXIT_FAILURE);
    }
    printf("Client connected\n");
 
    // 6. 读取客户端数据
    read(new_socket, buffer, BUFFER_SIZE);
    printf("Received from client: %s\n", buffer);
 
    // 7. 向客户端发送响应
    send(new_socket, response, strlen(response), 0);
    printf("Response sent\n");
 
    // 8. 关闭连接
    close(new_socket);
    close(server_fd);
    return 0;
}
```

#### 10.1.2 函数说明

- unistd.h：Unix系统调用（如 read, write, close）。注意：这是POSIX标准，在Windows上不原生支持。
- sys/socket.h 和 netinet/in.h：网络编程相关的套接字API（如 socket, bind, listen 等）。同样，这些是Unix/Linux的头文件。
- server_fd = socket(AF_INET, SOCK_STREAM, 0)中AF_INET：使用IPv4地址族。SOCK_STREAM：使用TCP协议（流式套接字）。0：协议类型，0表示自动选择（对于TCP就是IPPROTO_TCP）。
- bind() 系统调用的作用是为套接字分配一个本地地址（IP + Port）。当你指定一个具体的IP地址（如 192.168.1.100）时，你是在说：“我只接受发往这个特定IP的连接”。当你使用 INADDR_ANY (0.0.0.0) 时，你是在说：“我接受发往这台机器任何一个IP地址的连接，只要端口是8080”。

- close() 是 POSIX 标准定义的一个系统调用 (system call)。来自于#include <unistd.h>。它不仅可以关闭文件，还可以关闭套接字 (socket)、管道 (pipe)、设备等任何通过 open()、socket()、pipe() 等系统调用创建的资源（Linux一切皆文件）。close() 是一个通用的资源释放函数，而 socket 只是它能操作的一种资源类型。

- sin_addr 是 struct sockaddr_in 中的一个成员，它的类型是 struct in_addr。所以，sin_addr 本身是一个结构体，而 sin_addr.s_addr 才是真正的32位IP地址整数。

#### 10.1.3 监听Socket

- 服务器监听套接字 (server_fd)
- 创建方式：server_fd = socket(AF_INET, SOCK_STREAM, 0);这个套接字是由 socket() 系统调用直接创建的。
- 作用：它是一个监听套接字 (listening socket)。它的唯一职责是监听指定的IP地址和端口（在您的代码中是 0.0.0.0:8080）上的连接请求。它本身不用于发送或接收应用数据。
生命周期：在服务器启动时创建。调用 bind() 绑定到地址和端口。调用 listen() 进入监听状态。它会持续存在，等待并接受多个客户端的连接请求

#### 10.1.4 通信Socket

- 客户端通信套接字 (new_socket)
创建方式：new_socket = accept(server_fd, (struct sockaddr \*)&address, (socklen_t\*)&addrlen);这个套接字是由 accept() 系统调用返回的。accept() 的参数 server_fd 正是那个监听套接字。
- 作用：它是一个已连接套接字 (connected socket) 或通信套接字。它代表了一个具体的、已经建立的 TCP 连接。它用于与特定的客户端进行双向数据通信。read() 和 send() 都是操作这个 new_socket，用来收发实际的应用数据。
- 生命周期：当一个新的客户端发起连接请求（connect()）时，accept() 被唤醒，并为这个新的连接创建一个新的 new_socket。服务器可以为每个连接的客户端创建一个独立的 new_socket。当与该客户端的通信结束后，服务器调用 close(new_socket) 来关闭这个特定的连接，而不影响监听套接字 server_fd 继续监听其他连接。

#### 10.1.5 建立连接

- 从服务器的连接请求等待队列中，取出一个已经完成三次握手的客户端连接请求，并为这个新连接创建一个新的套接字（socket），以便服务器可以与该客户端进行独立的数据通信。简单来说，它完成了“从监听到建立连接”的最后一步。
- int accept(int sockfd, struct sockaddr \*addr, socklen_t \*addrlen); 是一个阻塞调用（除非套接字被设置为非阻塞模式）。如果当前没有客户端连接请求，accept() 会一直等待，直到有客户端成功连接（即完成TCP三次握手）。一旦有连接到来，accept() 就会返回。
- 创建新套接字：它不会使用原来的监听套接字（server_fd）来收发数据。而是创建并返回一个全新的套接字（new_socket），专门用于与这个特定的客户端通信。原来的监听套接字（server_fd）则继续回到监听状态，等待下一个连接。
- 完成连接建立：当客户端调用 connect() 发起连接时，TCP 三次握手在内核层面完成。握手成功后，这个连接会被放入服务器的“已完成连接队列”（completed connection queue）。accept() 就是从这个队列中取出一个连接。
- accept() 会覆盖 address 结构体中的 sin_port 和 sin_addr，将其设置为客户端的端口和IP。原始的服务器地址信息确实会丢失，因为 address 被重用作输出缓冲区。但是这不是问题，因为服务器地址是程序自己配置的，应该由程序自己记住。accept() 的设计目的就是获取客户端信息，而不是维护服务器信息。

#### 10.1.6 内核socket

- 阶段 1：服务器调用 listen()
    - listen(server_fd, 5);这是一个系统调用，会从用户空间陷入内核空间。
    - 内核根据 server_fd 找到对应的内核套接字结构。
    - 内核将该套接字状态设置为 LISTEN。
    - 内核在内核的 TCP 协议栈中注册：“端口 8080 现在有进程在监听”。
    - 内核创建半连接队列和全连接队列。
    - 此时，内核已经准备好处理发往 8080 端口的任何 TCP 包。
- 阶段 2：客户端发送 SYN 包
    - 客户端内核发送一个 SYN 包到 服务器IP:8080。
    - 服务器网卡收到这个数据包。
    - 网络驱动程序将数据包交给内核的 IP 层，再交给 TCP 层。
    - TCP 层查找：有没有进程在监听 8080 端口？
    - 找到：是的！有一个套接字状态为 LISTEN。
    - 内核 TCP 协议栈自动响应：将这个连接放入半连接队列。构造一个 SYN-ACK 包。通过网卡发送回客户端。
- 阶段 3：客户端回复 ACK 包
    - 客户端发送 ACK 包。
    - 服务器内核收到 ACK。
    - TCP 层再次查找：8080 端口的监听套接字。
    - 找到半连接队列中的条目。
    - 内核 TCP 协议栈自动处理：将连接从半连接队列移到全连接队列。连接状态变为 ESTABLISHED。
    - 此时，三次握手完成。整个过程服务器程序（用户空间）毫不知情。
- 阶段 4：accept() 取走连接
    - 程序调用 accept()。
    - 系统调用进入内核。
    - 内核检查全连接队列。
    - 发现有连接！
    - 内核：从队列中取出连接。为这个连接创建一个新的内核套接字结构（用于数据传输）。返回一个新的文件描述符（new_socket）给程序。

- 数据包收发需要“socket”，但不是程序代码中的socket，是内核的socket。

#### 10.1.7客户端连接不变

- 客户端完全不会察觉服务器内部创建了一个新的 socket 文件描述符。对客户端来说，连接始终是同一个，因为它只关心 TCP 连接的“四元组”（源IP、源端口、目的IP、目的端口），而这个四元组在整个通信过程中从未改变。
- 四元组未变：
    1. 客户端看到的始终是 (自己的IP:端口 -> 服务器IP:8080)。
    2. 服务器 IP 和端口没变，客户端 IP 和端口也没变。
    3. 所以对客户端 TCP 栈来说，这是同一个连接。
- 数据包内容不变：
    1. 当通过 new_socket 调用 send() 发送数据时：数据进入内核。内核根据 new_socket 找到对应的 struct sock。内核使用该 struct sock 中的连接信息（序列号、确认号等）封装 TCP 包。发送出去的包仍然是 (服务器IP:8080 -> 客户端IP:54321)。
    2. 客户端收到的包与握手完成后收到的第一个包没有任何区别。
- TCP 状态机连续：
    1. 从客户端视角，连接状态从 SYN_SENT → ESTABLISHED，然后一直保持 ESTABLISHED。
    2. 服务器内部如何管理这个连接（用哪个文件描述符），对客户端状态机完全没有影响。

- 过程分解：
    1. 三次握手完成（内核层面）：客户端发送 SYN。服务器内核回复 SYN-ACK。客户端回复 ACK。
    2. 此时，TCP 连接已在内核层面建立，状态为 ESTABLISHED。
    3. 内核已经为这个连接维护了一个完整的 struct sock（内核套接字结构），包含了序列号、窗口大小、缓冲区等所有状态。
    4. accept() 被调用：程序调用 accept()。系统调用进入内核。
    5. 内核从全连接队列中取出那个已经存在的连接。即：找到该连接对应的内核 struct sock。创建一个新的文件描述符（如 new_socket = 4）。将这个文件描述符与已有的内核 struct sock 关联起来。
    6. 返回 new_socket 给您的程序。

- accept() 并没有“创建一个新的网络连接”，它只是为已经存在的、已完成握手的连接，在用户空间创建了一个用于通信的句柄。
- accept() 返回的 new_socket 只是一个用户空间的整数句柄，它指向的是内核中早已存在的、代表该连接的 struct sock。内核中的连接实体并没有“新建”，只是被“移交”给了一个可用于读写的文件描述符。

#### 10.1.8 并发

上面的循环是串行处理：必须处理完一个客户端，才能接受下一个。如果想同时处理多个客户端（并发），可以用多进程/多线程/IO复用

#### 10.1.9 多进程改进

```cpp
while(1) {
    new_socket = accept(server_fd, ...); // 取出一个连接
    
    pid_t pid = fork();
    
    if (pid == 0) {
        // 子进程代码空间
        close(server_fd); // 子进程不需要监听套接字
        
        // 处理客户端请求（可能耗时）
        char buffer[1024];
        read(new_socket, buffer, 1024);
        send(new_socket, "Hello", 5, 0);
        
        close(new_socket);
        exit(0); // 子进程结束
    }
    else if (pid > 0) {
        // 父进程代码空间
        close(new_socket); // 父进程关闭已交给子进程的连接描述符
        // 继续 while 循环，accept 下一个
    }
    else {
        perror("fork failed");
    }
    
}
```
- fork() 一次调用后，父进程和子进程都会从该点继续执行。系统会给父进程返回子进程的进程号（正整数），给子进程返回 0。通过 if (pid == 0) 可以让子进程执行处理客户端请求的代码，而父进程则回到 accept() 继续监听新的连接，从而实现并发处理。

#### 10.1.10 多线程改进

```cpp
#include <pthread.h>

void *handle_client(void *arg) {
    int new_socket = *(int*)arg;
    free(arg); // 释放动态分配的内存
    
    // 处理请求
    char buffer[1024];
    read(new_socket, buffer, 1024);
    send(new_socket, "Hello", 5, 0);
    
    close(new_socket);
    pthread_exit(NULL);
}

int main() {
    // ... socket, bind, listen ...
    
    while(1) {
        int *new_sock_ptr = malloc(sizeof(int)); // 必须动态分配
        *new_sock_ptr = accept(server_fd, ...);
        
        pthread_t thread_id;
        if (pthread_create(&thread_id, NULL, handle_client, (void*)new_sock_ptr) != 0) {
perror("pthread_create failed");
            free(new_sock_ptr);
        }
        
        // 分离线程，避免僵尸线程
        pthread_detach(thread_id);
    }
}
```
- 传递给线程的  new_socket  必须动态分配（ malloc ），否则主线程修改时，线程可能读到错误值。
```cpp
// 错误示例：不要这样做！

while (1) {
    int new_socket = accept(server_fd, ...);
    pthread_create(&tid, NULL, handle_client, &new_socket); // 传递局部变量地址
}
```
- 主线程的 new_socket 是一个局部变量，存储在主线程的栈上。当主线程循环到下一次 accept 时，会复用同一块栈内存，导致 new_socket 的值被覆盖。而此时，子线程可能还在使用这个地址读取 new_socket 的值，就会读到已经被主线程修改的新值，而不是创建线程时的旧值，从而引发错误。

#### 10.1.11 多路复用改进

```cpp
#include <sys/epoll.h>

#define MAX_EVENTS 64

int epfd = epoll_create1(0);
struct epoll_event ev, events[MAX_EVENTS];

// 将监听套接字加入 epoll
ev.events = EPOLLIN;
ev.data.fd = server_fd;
epoll_ctl(epfd, EPOLL_CTL_ADD, server_fd, &ev);

while(1) {
    // 阻塞等待事件
    int nfds = epoll_wait(epfd, events, MAX_EVENTS, -1);
    
    for (int i = 0; i < nfds; i++) {
        if (events[i].data.fd == server_fd) {
            // 新连接到来
            int new_socket；
            while ((new_socket = accept(server_fd, NULL, NULL)) > 0) {
                // 将新连接也加入 epoll 监听（边缘触发 ET 模式）
                ev.events = EPOLLIN | EPOLLET;
                ev.data.fd = new_socket;
                epoll_ctl(epfd, EPOLL_CTL_ADD, new_socket, &ev);
            }
            if (errno == EAGAIN || errno == EWOULDBLOCK) {
                // 正常情况：连接队列已空
            } else {
                perror("accept");
            }
        }
        else {
            // 已有连接有数据可读
            int sockfd = events[i].data.fd;
            char buffer[1024];
            int n = read(sockfd, buffer, sizeof(buffer));
            
            if (n <= 0) {
                // 连接关闭或错误
                close(sockfd);
                epoll_ctl(epfd, EPOLL_CTL_DEL, sockfd, NULL);
            } else {
                // 处理数据（如 echo）
                send(sockfd, buffer, n, 0);
            }
        }
    }
}
```
- 多路复用的本质是“把多条数据流合并到一条物理信道上传输，接收端再把它们拆开”，这样就能用更少的硬件资源同时服务更多用户。核心思路可以拆成三步。为了防止各路信号互相干扰，系统会给每路信号分配互不重叠的资源——要么是不同的频率（FDM）、不同的时间片（TDM）、不同的波长（WDM），或者不同的空间路径（SDM）。
    1. 发端把多路信号“打包”：把每个用户的信号按某种规则（时间、频率、波长等）切成小片，然后把这些小片拼成一条高速流。
    2. 在一条线路上传：这条线路可以是铜线、光纤或无线频段，只要带宽足够，就能把打包后的信号一起发出去。收端把信号“拆包”
    3. 按发端约定的规则，把收到的高速流重新还原成原来的多路信号，送给对应的用户。

- 此处本质是让内核帮你同时监视多个文件描述符，一旦有就绪的就立刻通知你，这样你就可以用一个线程处理成百上千个连接，而不用为每个连接都创建线程或进程。

- 核心思想
    1. 问题：传统方式中，如果要同时处理多个客户端连接，通常需要为每个连接创建一个线程或进程。当连接数很多时，系统资源消耗巨大，效率低下。
    2. 解决方案：多路复用技术允许一个线程同时监视多个文件描述符（如套接字），一旦某个文件描述符就绪（可以读或写），就立即通知应用程序进行处理。
- 实现机制
    1. select：将文件描述符集合传递给内核，内核轮询检查每个文件描述符是否就绪。select 使用固定大小的位图来表示文件描述符集合，因此有最大文件描述符数量的限制（通常是 1024）。每次调用 select 都需要将整个集合从用户空间复制到内核空间，效率较低。
    2. poll：与 select 类似，但使用动态数组来表示文件描述符集合，没有最大文件描述符数量的限制。同样需要将整个集合从用户空间复制到内核空间。
    3. epoll（Linux 特有）：使用事件驱动的方式，内核维护一个就绪事件的链表，只将就绪的事件返回给应用程序，避免了轮询和复制整个集合的开销。epoll 支持水平触发和边缘触发两种模式，效率更高。
- 工作流程
    1. 注册：应用程序将需要监视的文件描述符及其感兴趣的事件（如读就绪、写就绪）注册到内核的多路复用器中。
    2. 等待：应用程序调用多路复用系统调用（如 select、poll、epoll_wait），进入阻塞状态，等待内核通知。
    3. 通知：内核监视所有注册的文件描述符，一旦有文件描述符就绪，就将就绪的文件描述符及其事件返回给应用程序。
    4. 处理：应用程序根据返回的就绪事件，对相应的文件描述符进行读写操作，然后继续等待下一次通知。

- 当一个监听 socket（server_fd）变得“可读”时，表示：有新的客户端连接请求到达了！也就是说，“可读”在这里不是指收到了数据，而是“可以 accept 了”。
- 因为 Linux 把 网络连接、文件、socket 都抽象成“文件描述符”（file descriptor），并统一用“可读/可写”来表示事件。
    1. 普通文件：可读 = 有数据可以读取。
    2. 监听 socket：可读 = 有新连接可以 accept。
    3. 已连接 socket：可读 = 客户端发来了数据。

- 每次调用 epoll_wait 时，内核会把“哪些 socket 就绪了”写进这个数组。旧内容会被完全覆盖。

### 10.2 客户端TCP

#### 10.2.1 客户端代码

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
 
#define PORT 8080
#define BUFFER_SIZE 1024
 
int main() {
    int sock = 0;
    struct sockaddr_in serv_addr;
    char buffer[BUFFER_SIZE] = {0};
    const char *message = "Hello from client";
 
    // 1. 创建 TCP Socket
    if ((sock = socket(AF_INET, SOCK_STREAM, 0)) < 0) {
        perror("socket creation failed");
        exit(EXIT_FAILURE);
    }
 
    // 2. 设置服务器地址
    serv_addr.sin_family = AF_INET;
    serv_addr.sin_port = htons(PORT);
    if (inet_pton(AF_INET, "127.0.0.1", &serv_addr.sin_addr) <= 0) {
        perror("invalid address");
        exit(EXIT_FAILURE);
    }
 
    // 3. 连接服务器
    if (connect(sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) < 0) {
        perror("connection failed");
        exit(EXIT_FAILURE);
    }
 
    // 4. 向服务器发送数据
    send(sock, message, strlen(message), 0);
    printf("Message sent\n");
 
    // 5. 读取服务器响应
    read(sock, buffer, BUFFER_SIZE);
    printf("Received from server: %s\n", buffer);
 
    // 6. 关闭连接
    close(sock);
    return 0;
}
```

#### 10.2.2 函数说明

- inet_pton(int af, const char \*src, void \*dst): 把“人类可读的文本格式”IP 地址（如 "127.0.0.1"）转换成“机器能用的二进制格式”。不能这样写serv_addr.sin_addr.s_addr = 127.0.0.1;  // 编译都通不过！127.0.0.1 是一个“点分十进制表示法”，它是给人看的，不是 C 语言的合法整数常量。htonl(INADDR_ANY) 是规范的服务端写法。
