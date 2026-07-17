---
coding: UTF-8
title: Linux系统编程
date: 2026-03-22T23:39:23
updated: 2026-07-17T02:23:26
tags: []
share: true
---

# Linux系统编程

## 1. 函数

### 1.1 open/close

```cpp
int open(const char *pathname, int flags);
int open(const char *pathname, int flags, mode_t mode);

flags: O_RDONLY | O_WRONLY | O_RDWR |
O_APPEND | O_CREAT | O_EXCL | O_TRUNC | O_NONBLOCK | 

return: 成功返回文件打开得到的文件描述符，失败则返回-1，设置errno查看说明

int close(int fd);
```

### 1.2 read/write

```cpp
ssize_t read(int fd, void buf[.count], size_t count);

return: 成功返回读到的字节数(0表示读到文件尾)，失败返回-1，设置errno查看说明

ssize_t write(int fd, const void buf[.count], size_t count);

return: 成功返回写入的字节数(0表示已经全部写入)，失败返回-1，设置errno查看说明
```

![](../assets/Linux%E7%B3%BB%E7%BB%9F%E7%BC%96%E7%A8%8B.png)

- read和write函数时Unbuffered I/O，指的是无用户级的缓冲区。内核有自己维持的缓冲区。而fputc之类的函数有自己的缓冲区。

> [!NOTE] 预读入缓输出

### 1.3 fcntl

- 改变一个已打开文件的访问控制属性。

```cpp
int fcntl(int fd, int cmd, ... /* arg */ );
cmd可以为`F_GETFL`和`F_SETFL`，用于获取与设置属性信息（file flag）
返回值flags是位图表示文件属性。
```

### 1.4 lseek

- 读和写用的同一个偏移地址
- 使用lseek获取、拓展文件大小（要想真实改文件大小，必须得有IO操作）
-
```cpp
off_t lseek(int fd, off_t offset, int whence);
返回值是从文件起始开始的偏移值
whence: SEEK_SET|SEEK_CUR|SEEK_END
```

### 1.5 truncate

```cpp
int truncate(const char *path, off_t length);
```

## 2. 进程

### 2.1 PCB

- PCB即进程控制块，本质是一个任务结构体。其中有一个成员是指针，指向一个数组，这个数组叫做“文件描述符表”，记录文件的状态。文件描述表中每一行是一个文件描述符（key:value形式），其中每个值指向一个文件描述结构体。
- 一个进程能打开的最大文件数最大是1024（0-1023），其中0代表`STDIN_FILENO`，1代表`STDOUT_FILENO`，2代表`STDERR_FILENO`，操作者打开的文件一般从3开始排序到1023，其优先使用表中可用的最小的编号。

### 2.2 阻塞与非阻塞

- 阻塞与非阻塞是文件的属性。
- 读常规文件是不会阻塞的，读设备文件和网络文件有可能会阻塞。
- 设置终端非阻塞`open ("/dev/tty", O_RDWR|O_NOBLOCK)`，默认是阻塞的。
-
