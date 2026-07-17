---
coding: UTF-8
title: Linux基础
date: 2026-03-22T23:39:23
updated: 2026-07-18T03:56:51
tags: []
share: true
---

# Linux基础

## 1. Linux系统概要

### 1.1 Linux特点

- Linux：开放性（开源）、多用户、多任务良好的用户界面、优异的性能与稳定性

- Windows：是单用户多任务
- 单用户/多用户
	- 单用户：在登录计算机操作系统时只能允许同时登录==一个==用户
	- 多用户：在登录计算机操作系统时只能允许同时登录==多个==用户
- 单任务/多任务
	- 单任务：允许用户同时进行==一个==操作任务数量
	- 多任务：允许用户同时进行==多个==操作任务数量

### 1.2 Linux发展

- RedHat
	- CentOS：完全免费
	- Fedora
	- Rocky
	- AlmaLinux
- Debain
	- Ubuntu：视窗界面良好的Linux系统
	- Adamantix：基于Debian，特别关注安全。
- Slackware
	- Kate OS

![](../assets/Linux%E5%9F%BA%E7%A1%80-8.png)

### 1.3 文件管理

- Linux 一切皆是文件。
- 文件系统：操作系统如何管理文件，内部定义了一些规则和定义。
- Windows是根据盘符进行访问文件的，在Linux中是维护着一个树状结构的文件模型，通过相对路径/绝对路径进行查找文件资源，可以随意挂在磁盘

### 1.4 二级目录

|目录|作用|
|:---:|:---|
|boot|引导分区|
|dev|设备信息|
|bin|可执行文件，例如命令脚本|
|sbin|管理员可执行文件的命令与脚本|
|media|多媒体，默认挂载路径|
|etc|配置文件目录|
|lib|类库|
|mnt|默认挂在磁盘位置|
|proc|进程信息（非线程）|
|home|普通用户的家目录|
|root|root用户的家目录|
|opt|软件安装位置|
|usr|多个用户共享的软件区域|
|run|运行时的系统常量与变量|
|srv|服务启动后需要提取的数据|
|sys|系统运行时的内核信息|
|tmp|临时文件目录，可能重启后清除|
|var|临时文件目录，重启后不会清除|

![](../assets/Linux%E5%9F%BA%E7%A1%80-1.png)

### 1.5 手册

- `man`指令可以查看使用手册，时系统的手册分页程序。

| 章节  | 手册页类型                  |
| :-: | :--------------------- |
|  1  | 可执行程序或shell命令          |
|  2  | 系统调用（内核提供的函数）          |
|  3  | 库调用（程序库中的函数）           |
|  4  | 特殊文件（通常位于`/dev/`）      |
|  5  | 文件格式和规范，如`/etc/passwd` |
|  6  | 游戏                     |
|  7  | 杂项（包括宏包和规范）            |
|  8  | 系统管理命令（通常只针对root用户）    |
|  9  | 内核例程                   |

### 1.6 配置相关

```bash
//==配置静态ip
vi /etc/NetworkManager/system-connections/ens160.nmconnection

修改[ipv4]里面的地址 method=auto ​ 自动获取改为 method=manual
address=192.168.152.139/24,192.168.152.1
dns=114.114.114.114;8.8.8.8
:wq! #保存退出

nmcli connection reload
nmcli connection down ens160 # 断开网卡连接
nmcli connection up ens160 # 激活网卡连接
or
systemctl restart NetworkManager
or
重启系统 reboot

//关闭防火墙
systemctl status firewalld
systemctl disable firewalld
systemctl stop firewalld

//软件安装限制
vi /etc/selinux/config
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.

//修改主机名称
vi /etc/hostname
```

## 2. 指令

### 2.1 基础指令

|作用|==基础指令==|
|:---:|:---|
|查看文件列表|ls；ll；ll -h；ll -a；ll -ah；ll -R；（参数是单词就是两个-，是个简写就一个-）|
|路径切换|cd；pwd；|
|清屏|clear；Ctrl+L；|
|手册|man ...；help ...；|
|重启|reboot；reboot -w；|
|关机|shutdown；shutdown -h now '关机提示'；init 0；halt；poweroff；|

### 2.2 文件指令

|    作用     | ==文件指令==                                                                              |
| :-------: | :------------------------------------------------------------------------------------ |
|  创建文件/目录  | mkdir 文件夹名；makdir -p 文件夹名；mkdir -p dianying{A,B,C}；                                   |
|           | touch 文件名；makefile 文件名;                                                               |
|   创建软链接   | ln -s 原始文件路径地址 快捷方式的路径                                                                |
|   创建硬链接   | ln 原始文件路径地址 快捷方式的路径（用于防止文件非法删除）                                                       |
|  文件/目录操作  | cp -r；cp -a；                                                                          |
|           | mv；                                                                                   |
|           | rm；rm -r；rm -rf；                                                                      |
|           | rmdir (只能移动空的目录)；                                                                     |
| 查看文件/目录信息 | stat ...；（最近访问、最近更改、最近改动）                                                             |
|  查看文件类型   | file ...；file /usr/bin/ping；                                                          |
|   文件查看    | head ...；head -n ...；                                                                 |
|           | tail ...；tail -n ...；tail -f ...（f监控的时Inode）；tail -F ...(F监控的是文件名)；                   |
|           | cat ...；tac ...；                                                                      |
|           | more；more ...；逐页显示（b,n,空格,回车,q）                                                       |
|           | less；less ...；逐页显示（b,n,空格,回车,q）                                                       |
|           | wc -lwc ...；                                                                          |
|   文件编辑    | vi ...；                                                                               |
|   文件搜索    | find <路径范围> [选项] <选项的值> ；                                                             |
|           | find ./ -name '\*.txt'；find ./ -maxdepth 1 -name '\*.jpg'；find ./ -size +20M '\*.jpg' |
|           | find ./ -type f；                                                                      |
|           | find ./ -type d \|wc -l；                                                              |
| 找命令文件的位置  | whereis ping：                                                                         |
|   解/压缩    | tar -zx(解压)v(过程)f(文件) ...；tar -zc(压缩)v(过程)f(文件) 压缩后的名字 源文件；                           |
|           | tar -jx(解压)v(过程)f(文件) ...；tar -jc(压缩)v(过程)f(文件) 压缩后的名字 源文件；                           |
|           | unzip ...；zip -r 压缩后的名字 源文件；                                                          |
| 重新加载配置文件  | source /etc/profile；                                                                  |

### 2.3 状态指令

|    作用     | ==状态指令==                                               |
| :-------: | :----------------------------------------------------- |
| 查看磁盘分区信息  | df；df -h；                                              |
|  查看目录大小   | du；du -h --max-depth=1 /etc；                           |
| 查看块状设备的信息 | lsblk（Name:名称，Size:设备大小，Type:类型，MountPoint:挂载点）        |
|   内存查看    | free；free -m；                                          |
|   使用时间    | uptime；                                                |
|   用户信息    | id；id 用户名；whoami；                                      |
|   服务管理    | service <服务名> start/stop/restart；systemctl stop httpd； |
|  系统内核信息   | uname；uname -a；                                        |
|   进程信息    | ps；ps -ef；ps -ef \| grep panel；pstree -p；              |
|           | top（VIRT虚拟内存 RES 常驻内存 SHR 共享内存）；                       |
|   进程管理    | kill ...；killall ...；                                  |
| 查看进程执行的调用 | strace 可执行文件                                           |

> [!NOTE] swap是一个特殊的分区，以磁盘代替内存，当内存使用满的时候，可以将一部分数据写出到swap分区。

### 2.4 网络指令

|作用|==网络指令==|
|:---:|:---|
|主机名|hostname；hostname -f(显示全限定域名即FQDN)；|
|修改主机名|hostname ...；vi /etc/hostname；|
|修改DNS域名|vi /etc/hosts；|
|网卡信息|ifconfig；|
|网络状态|netstat -tnlp；netstat -anp；netstat -r；(一个机器默认有65536个端口，即[0,65535])|
|查看与目标ip是否能够连通|ping ...；|
|查看与目标ip的指定端口能否连通|telnet [ip地址] [端口号]；|
|标识指定的资源|curl -X GET ...(restful所有的资源在网络上都有唯一的定位)|

### 2.5 输出重定向

- 改变数据输出的位置/方向
```bash
ll /etc > test.txt
ll /opt >> test.txt
echo ...//输出内容到控制台
```

|名称|代码|操作符|
|:---:|:---:|:---|
|标准输入(stdin)|0|< 或 <<|
|标准输出(stdout)|1|>, >>, 1> 或 1>>|
|标准错误输出(stderr)|2|2> 或 2>>|

> [!NOTE] 2>1 代表将stderr重定向到当前路径下文件名为1的regular file中，而2>&1代表将stderr重定向到文件描述符为1的文件(即/dev/stdout)中，这个文件就是stdout在file system中的映射
```bash
ll /etc > test.txt 2>&1
ll /opt >> test.txt 2>&1
```

### 2.6 管道

- 将前面命令的结果作为参数传递给后面的命令
```bash
head -8 /etc/profile |tail -1；查看文件第8行内容
ls \|wc -l；//统计文件数
```

### 2.7 cut sort wc

- cut：用指定的规则来切分文本
```bash
//以冒号作为分隔符显示第1，2，3列，再过滤包含school的行
cut -d ':' -f 1,2,3 passwd |grep school
```
- sort：对文本进行排序
```bash
//对文本中的行按照首字母的字典序递增排序
sort passwd

//对每一行的文本以冒号进行切割，按照第2列的内容(字符串)进行排序
sort -t ':' -k 2 passwd

//对每一行的文本以冒号进行切割，按照第2列的内容(数字)进行排序
sort -t ':' -n -k 2 passwd

//对每一行的文本以冒号进行切割，按照第2列的内容(数字)进行排序（逆序）
sort -t ':' -r -n -k 2 passwd
```
- wc :word count，统计单词数量
	- -l：行
	- -w：word
	- -c：char
```bash
wc passwd

```

### 2.8 grep

- grep (global regular expression) 命令用于查找文件里符合条件的字符串或正则表达式，并将匹配的行打印出来。
```bash
grep [参数] pattern [files]
//pattern表示要查找的字符串或正则表达式。
//files表示要查找的文件名，可以同时查找多个文件，如果省略 files 参数，则默认从标准输入中读取数据。(find是根据文件名找文件，grep是根据指定输入内容找所在行)
//参数-i：忽略大小写进行匹配。
//参数-v：反向查找，只打印不匹配的行。
//参数-n：显示行号
//参数-r：递归寻找
//参数-E：使用扩展正则表达式进行匹配ERE（默认是基础正则表达式BRE）
//参数-F：纯字符串匹配，不解析正则

//从当前目录下的所有文件中找包含copy字样的内容
grep -r 'copy' ./

//从ps的输出传递到grep作为输入，查找这些输入中包含“jdk”的行
ps -ef |grep jdk

//从文件passwd中查找包含“root”的行
grep root passwd

//基础正则查找包含op的行
grep -n "op.*" passwd
```

### 2.9 sed

- `sed`（Stream Editor，流编辑器）的本质是：
	1. **逐行读取**：从输入（文件 / 管道）中逐行读取文本，放入「模式空间」（临时缓冲区）；
	2. **按规则处理**：对模式空间中的内容执行指定的编辑指令（替换、删除、新增等）；
	3. **输出结果**：处理完一行就输出一行（默认不修改原文件）；
	4. **循环至结束**：直到所有行处理完毕。

```bash
//流式处理文本指令
sed [选项] '编辑指令' 输入文件

//在文件的第二行追加（a）字符串“asdfghjkl”
sed '2a asdfghjkl' passwd
```
|选项|作用|
|:---:|---|
|-i|直接修改原文件（不加仅输出结果，不改动文件）|
|-i.bak|修改原文件的同时，生成 `.bak` 后缀的备份文件（安全操作）|
|-e|执行多个编辑指令（多规则处理）|
|-n|静默模式，只输出匹配到的行（默认输出所有行）|
|-f|从脚本文件中读取编辑指令（适合复杂操作）|

```bash
\# 推荐：用 | 替代 /，无需转义 URL 中的 /
sed -i 's|http://dl.rockylinux.org|https://mirrors.aliyun.com/rockylinux|g' file

\# 不推荐：用 / 需转义，易出错
sed -i 's/http:\/\/dl.rockylinux.org/https:\/\/mirrors.aliyun.com\/rockylinux/g' file
```
> [!NOTE] 默认分隔符是 `/`，但当内容包含 `/` 时（如 URL），可用 `|`、`\#`、`@` 替代，避免转义：

````bash
ver=9
\# 正确：双引号解析 $ver 为 9
sed -i "s/rockylinux\/8/rockylinux\/$ver/g" /etc/yum.repos.d/Rocky.repo

\# 错误：单引号不解析变量，替换为 rockylinux/$ver
sed -i 's/rockylinux\/8/rockylinux\/$ver/g' /etc/yum.repos.d/Rocky.repo
````
> [!NOTE] 当替换内容包含变量时，必须用双引号包裹编辑指令（Shell 解析变量）

### 2.10 awk

- `awk` 核心逻辑是：
	1. **逐行读取**：从输入（文件 / 管道）中逐行读取文本；
	2. **按分隔符切分**：默认以「空格 / 制表符」为分隔符，将每行切分成多个字段（列），用 `$1`、`$2`、`$3`... 表示第 1、2、3 列，`$0` 表示整行；
	3. **执行处理逻辑**：对每行 / 每列执行指定的条件判断、计算、输出等操作；
	4. **循环至结束**：直到所有行处理完毕。
```bash
awk [选项] '条件 { 操作 }' 输入文件
//条件：可选，满足条件才执行后续操作（省略则处理所有行）；
//操作：必选（需用 `{}` 包裹），对匹配行执行的动作（如打印、计算、判断）；
//特殊模式：`BEGIN { 操作 }`（处理文本前执行）、`END { 操作 }`（处理完所有行后执行）。

//提取所有用户名（第1列）
awk -F: '{print $1}' passwd

//提取用户名 + UID（第1列和第3列），用 | 分隔输出
awk -F: 'BEGIN{OFS="|"} {print $1, $3}' passwd

//只输出 root 用户的家目录（第6列）
awk -F: '$1=="root" {print $6}' passwd

//筛选 UID ≥ 1000 的普通用户（第3列是 UID）
awk -F: '$3 >= 1000 {print $1, $3}' passwd

//筛选 YUM 源中包含 aliyun 的 baseurl 行（正则匹配）
awk -F= '/^baseurl/ && $2 ~ /aliyun/ {print $0}' /etc/yum.repos.d/Rocky.repo

//排除注释行和空行，只输出有效配置行
awk '!/^#/ && !/^$/ {print $0}' /etc/yum.repos.d/Rocky.repo

//筛选行号 10-20 之间的内容（NR 是行号）
awk 'NR >=10 && NR <=20 {print NR, $0}' passwd
```

|选项|作用|
|:---:|---|
|`-F 分隔符`|指定列分隔符（默认是空格 / 制表符）|
|`-v 变量=值`|定义 awk 变量（传递 Shell 变量到 awk 中）|
|`-f 脚本文件`|从脚本文件中读取 awk 指令（适合复杂逻辑）|

### 2.11 通配符

- Shell 通配符的核心作用是**匹配文件名 / 目录名**，由 Shell 先解析后传给命令。部分命令的参数可以接收正则表达式。

|特性|Shell 通配符|正则表达式|
|:---:|:---|:---|
|作用对象|匹配「文件 / 目录名」（给 shell 解析）|匹配「文本内容」（给 grep/awk/sed 等工具解析）|
|所属解析层|由 shell（bash/zsh 等）先解析，再传给命令|由具体命令（grep/awk）解析，shell 不处理|
|核心语法|简单：`*`/`?`/`[]`/`{}` |复杂：`.*`/`+`/`^`/`$`/`()` 等完整正则规则|

- shell通配符

|通配符|含义|典型示例|匹配结果（举例）|
|---|---|---|---|
|`*`|匹配**任意长度**的任意字符（包括 0 个字符）|`ls *.txt`|a.txt、test123.txt、.txt（空命名 txt 文件）|
|`?`|匹配**单个**任意字符|`ls file?.log`|file1.log、fileA.log、file_.log|
|`[]`|匹配括号内**单个**指定字符（支持范围、排除）|`ls file[1-3].txt`|file1.txt、file2.txt、file3.txt|
|`[!]`/`[^]`|匹配括号内**除外**的单个字符|`ls file[!1-3].txt`|file4.txt、fileA.txt（排除 1/2/3）|
|`{}`|花括号扩展（不是 “匹配”，是 “生成” 多个字符串）|`ls {a,b,c}.txt`|等价于 ls a.txt b.txt c.txt|

### 2.12 引号问题

- **不加引号**：shell 会先解析变量 / 命令替换 / 转义字符 / 通配符 / 特殊字符（空格），再把结果传给命令；
- **加双引号 `" "`**：shell 仅解析变量 / 命令替换 / 部分转义字符，不解析通配符。
- **加单引号 `' '`**：shell 完全不解析，直接把内容原样传给命令；

|解析行为|不加引号|加双引号 `" "`|加单引号 `' '`|
|---|---|---|---|
|变量解析（`$var`）|✅ 解析|✅ 解析|❌ 不解析（原样）|
|命令替换（`` `cmd` ``和`$(cmd)`）|✅ 解析|✅ 解析（但保留原始格式）|❌ 不解析（原样）|
|转义字符（`\`）|✅ 仅解析 `\$`和`\"`和`\'`和``  \` ``和`\|`和`\;`和`\>`和`\<`和和`\#`和`\&`和`\\`和`\[`和`\`(例如`\a`转换为`a`)|✅ 仅解析 `\$`和`\"`和`` \` ``和`\\` |❌ 不解析（`\` 只是普通字符）|
|通配符（`*`/`?`/`[]`）|✅ 解析|❌ 屏蔽（成普通字符）|❌ 屏蔽（成普通字符）|
|单词分割（空格 / 制表符）|✅ 分割成多个参数|❌ 屏蔽（视为单个参数）|❌ 屏蔽（视为单个参数）|

> [!NOTE] \n，\t，\r 等不是 Shell 级转义符，Shell只对\转义，直接去掉 `\`，只保留 n，t，r。
```bash
// 1.保留变量解析
# 定义变量
name="test file.txt"
# 不加引号：变量解析，但后续会触发单词分割/通配符（若有）
echo $name  # 输出：test file.txt（这里无通配符，暂时正常）
# 加双引号：变量解析，且保留完整字符串
echo "$name"  # 输出：test file.txt（和不加引号结果一致）
# 加单引号：变量不解析
echo '$name'  # 输出：$name

// 2.保留命令替换
# 不加引号：命令替换，结果分割（若有空格）
echo $(ls *.txt)  # 输出：a.txt b.txt c.txt（分割成多个单词）
# 加双引号：命令替换，结果保留为单个字符串
echo "$(ls *.txt)"  # 输出：a.txt
                    #      b.txt
                    #      c.txt（保留 ls 输出的原始格式）
# 加单引号：命令替换不解析
echo '$(ls *.txt)'  # 输出：$(ls *.txt)

// 3.转义字符仅解析特定符号
# 不加引号：正常解析\"
echo he said \"hello\"! # 输出：he said "hello"!
# 加双引号，正常解析\"
echo "he said \"hello\"!" # 输出：he said "hello"!
# 加单引号，命令替换不解析
echo 'he said \"hello\"!' # 输出：he said \"hello\"!

# 不加引号：正常解析\n为n
echo hello\nworld # 输出：hellonworld
# 加双引号，命令替换不解析
echo "hello\nworld" # 输出：hello\nworld
# 加单引号，命令替换不解析
echo 'hello\nworld' # 输出：hello\nworld

// 4.屏蔽通配符
# 假设当前目录有 a.txt、b.txt
# 不加引号：* 被解析为所有 .txt 文件
echo *.txt  # 输出：a.txt b.txt
# 加双引号：* 变成普通字符，不解析
echo "*.txt"  # 输出：*.txt
# 同理，?/[] 也会被屏蔽
echo "file?.txt"  # 输出：file?.txt（而非匹配 file1.txt 等）
echo "file[1-3].txt"  # 输出：file[1-3].txt（而非匹配 file1.txt 等）


// 5.屏蔽单词分割（空格 / 制表符）
# 定义含空格的变量
file="my document.txt"
# 不加引号：分割成两个参数（my 和 document.txt）
ls $file  # 报错：ls: 无法访问 'my': 没有那个文件或目录；ls: 无法访问 'document.txt': 没有那个文件或目录
# 加双引号：视为单个参数（my document.txt），正确匹配文件
ls "$file"  # 输出：my document.txt（若文件存在）
# 加单引号：视为单个参数（my document.txt），正确匹配文件
ls '$file'  # 输出：my document.txt（若文件存在）
```

- 常见情况
```bash
// 处理含空格的文件名 / 路径
# 错误：不加引号，空格分割参数
rm my file.txt  # 试图删除 my 和 file.txt，报错
# 正确：加双引号，视为单个文件
rm "my file.txt"  # 成功删除

// 避免通配符意外匹配
# 需求：输出字符串 "*.txt"，而非匹配所有 .txt 文件
# 错误：不加引号，解析为文件名
echo *.txt  # 输出：a.txt b.txt
# 正确：加双引号，屏蔽通配符
echo "*.txt"  # 输出：*.txt

// 保留命令输出的原始格式
# 需求：查看 ls 输出的完整格式（含换行）
# 错误：不加引号，换行被替换为空格
echo $(ls)  # 输出：a.txt b.txt c.txt
# 正确：加双引号，保留换行
echo "$(ls)"  # 输出：a.txt
              #       b.txt
              #       c.txt
```

## 3. vim

- vim的三种模式：命令模式、编辑模式、末行模式

![](assets/Linux基础.png)

![](assets/Linux基础-5.png)

### 3.1 命令模式

|    功能     | 按键                |
| :-------: | :---------------- |
|    行首     | -；0               |
|    行尾     | $                 |
|    首行     | gg                |
|    尾行     | G                 |
|    指定行    | [行数] gg；n+G；:n；   |
|    上一页    | ctrl+b            |
|    下一页    | ctrl+f            |
|   下一个单词   | w                 |
|    复制     | yy；[行数] yy；       |
|    剪切     | dd；[行数] dd；D；x；dw |
|    粘贴     | p；P               |
|    替换     | r [新内容]           |
|    撤销     | :u；u              |
|    重做     | ctrl+r；.；         |
| 左/下/上/右移动 | h，j，k，l；          |
|  查看手册与定义  | [章节]+K；\[+d；      |

### 3.2 末行模式

|    功能     | 按键                                                         |
| :-------: | :--------------------------------------------------------- |
|    保存     | :w                                                         |
|    另存为    | :w 路径                                                      |
|    退出     | :q                                                         |
|   保存并退出   | :wq；shift+ZZ                                               |
|   自适应退出   | :x                                                         |
|   强制退出    | :q!                                                        |
|   外部命令    | :!外部命令                                                     |
|   查找搜索    | /关键词；N；n；\*；#；                                             |
|   取消高亮    | :nohl                                                      |
|    替换     | :s/关键词/目标词；:s/关键词/目标词/g；                                   |
|           | :%s/关键词/目标词；:%s/关键词/目标词/g（%表示整个文件，g表示全局）:10, 20%s/关键词/目标词； |
|    分屏     | :sp；:vsp；ctrl+ww；:q；:qall；                                 |
|   显示行号    | :set nu                                                    |
| 查看当前打开的文件 | :files                                                     |
|   切换文件    | :open 文件名                                                  |
| 切换上/下一个文件 | :bp/n                                                      |
|  开启/关闭颜色  | :syntax on/off                                             |

### 3.3 编辑模式

![](assets/Linux基础-2.png)

### 3.4 其他情况

- 配置
	1. 个人配置文件（~/.vimrc）
	2. 全局配置文件（/etc/vimrc）

- 异常退出
	需要删除.swp文件

- 别名机制
	~/.bashrc文件

## 4. 用户权限管理

### 4.1 用户与用户组管理

- Linux系统是一个多用户多任务的操作系统，任何一个要使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。
- 每个用户都有一个用户组，系统可以对一个用户组中的所有用户进行集中管理。不同Linux 系统对用户组的规定有所不同，如Linux下的用户属于与它同名的用户组，**这个用户组在创建用户时同时创建**。
- 新增用户时会创建同名的组和家目录。
- 主组，也叫初始组，是用户登录系统时的组。创建新用户时，如果未明确指定该用户所属的主组，会默认创建一个与用户名相同的组，作为该用户的主组。用户创建文件时，文件的所属权限组就是当前用户的主组。用户有且只能所属一个主组，主组不能被删除，但可以更换主组。
- 附加组是用户登录后可以切换的其他组。用户可以所属零个或多个附加组，附加组和主组可以相同。附加组可以直接被删除而无需关心是否所属于用户，附加组可以新增和移除任意个所属用户。

|文件|作用|
|:---:|:---|
|/etc/passwd|存储用户的关键信息（用户名，编号，组编号，家目录，命令码，目录）|
|/etc/group|存储用户组的关键信息|
|/etc/shadow|存储用户的密码信息（用户名和密码）|

|功能|语法|说明|
|:---:|:---:|:---|
|添加用户|useradd [选项] 用户名|添加新用户|
||-g|表示指定用户的用户主组，可以是用户组的id，也可以是组名（主组在passwd里查看）|
||-G|表示指定用户的用户附加组，可以是用户组的id，也可以是组名（附加组在group里查看）|
||-u|用户的id（用户的标识符），系统默认会从500之后按顺序分配uid|
||-c|加注释|
|修改用户|usermod [选项] 用户名|修改用户信息|
||-g 主组名|表示指定用户的用户主组，可以是用户组的id，也可以是组名（主组在passwd里查看）|
||-G 附加组|表示指定用户的用户附加组，可以是用户组的id，也可以是组名（附加组在group里查看）|
||-u|用户的id（用户的标识符），系统默认会从500之后按顺序分配uid|
||-l|修改用户名|
||-L|锁定用户名|
||-U|解锁用户名|
|设置用户密码|passwd 用户名|给用户设置密码|
|切换用户|su [用户名]|切换要登录的用户|
|删除用户|userdel 用户名|删除指定用户|
||-r|同时删除其家目录|

|功能|语法|说明|
|:---:|:---:|:---|
|查看所在组|groups|查看当前用户所在组|
||groups [用户名]|查看指定用户所在组|
|添加用户组|groupadd [选项] 用户组名|添加新用户组|
||-g|设置自定义的用户组id，系统默认会从500之后按顺序分配|
|修改用户组|groupmod [选项] 用户组名|修改用户组信息|
||-g|设置自定义的用户组id，系统默认会从500之后按顺序分配|
||-n|修改用户组名|
|删除用户组|groupdel 用户组名|删除指定用户组|

### 4.2 属主与属组

- 属主：所属的用户（文件的主人）
- 属组：所属的用户组

### 4.3 身份和权限

- Linux系统一般将文件可存/取访问的身份分为3个类别：owner、group、others，且3种身份各有read、write、execute等权限。
- 身份
	1. Owner身份（文件所有者，默认为文档的创建者）
	2. Group身份（与文件所有者同组的用户）
	3. Others身份（其他人，相对于所有者）
	4. Root用户（超级用户）

- 读权限：
	1. 对于文件夹来说，读权限影响用户是否能够列出目录结构
	2. 对于文件来说，读权限影响用户是否可以查看文件内容

- 写权限：
	1. 对文件夹来说，写权限影响用户是否可以在文件夹下“创建/删除/复制到/移动到”文档
	2. 对于文件来说，写权限影响用户是否可以编辑文件内容

- 执行权限：
	1. 一般都是对于文件来说，特别脚本文件。

- 权限查看：`ls -l，ll`

![](assets/Linux基础-3.png)

### 4.4 权限设置

- 文件（夹）的权限由两部分组成：
	1. ugo
	2. ugo分别对应的rwx权限
- 修改权限方式：
	1. 修改文件（夹）的属主和属组，会被动的影响others的范围
	2. 分别修改ugo对应的rwx权限

#### 4.4.1 修改所属

```bash
chown [选项] 属主[:属组] 文档/文件夹
//其中选项-R表示递归设置权限 （当文档类型为文件夹的时候）
```

#### 4.4.2 修改权限

```bash
chmod [选项] 权限模式 文档/文件夹
//其中选项-R表示递归设置权限 （当文档类型为文件夹的时候）
```
- 如果想要给文档设置权限，操作者要么是root用户，要么就是文档的所有者。
- 当rwx权限全都有的时候，这个文件/文件夹会显示为蓝色（ugo都能执行的是绿色）
- 权限模式：字母形式，数字形式

|选项|字母|介绍(字母模式)|
|:---:|:---:|:---|
|谁|u|用户|
||g|用户组|
||o|其他人|
||a|所有人（包括ugo）,省略默认就是a|
|作用方式|+|增加权限（相对当前的）|
||-|减少权限（相对当前的）|
||=|设置权限（直接设置成指令中）|
|权限|r|可读|
||w|可写|
||x|可执行|

```bash
chmod u+x,g+rx,o+rx test.txt
```
|选项|介绍（数字模式）|
|:---:|:---:|
|0|---|
|1|--x|
|2|-w-|
|3|-wx|
|4|r--|
|5|r-x|
|6|rw-|
|7|rwx|
```bash
chmod 777 test.txt（这个ugo中间不需要加逗号）
```

![](assets/Linux基础-4.png)

- 这两项信息在文档创建的时候会使用创建者的信息（用户名、用户所属的主组名称）。
- 如果有时候去删除某个用户，则该用户对应的文档的属主和属组信息就需要去修改。

|指令|介绍|
|:---:|:---:|
|chown [-R] username 文档路径|更改文档的的所属用户（-R在处理文件夹时递归设置）|
|chgrp [-R] groupname 文档路径|更改文档的所属用户组|
|chown [-R] username:groupname 文档路径|更改文档的的所属用户和所属用户组|

### 4.5 sudo

- reboot、shutdown、init、halt、user管理，在普通用户身份上都是操作不了，但是有些特殊的情况下又需要有执行权限。又不可能让root用户把自己的密码告诉普通用户。
- sudo可以让管理员（root）事先定义某些特殊命令谁可以执行
- /etc/sudoers，编辑需要用visudo sudoers。
- 配置格式： root ALL=(ALL) ALL

|选项|说明|
|:---:|:---|
|root|表示用户名，如果是用户组，则可以写成“%组名”|
|ALL|表示允许登录的主机（地址白名单）|
|(ALL)|表示以谁的身份执行，ALL表示root身份|
|ALL|表示当前用户可以执行的命令，多个命令可以使用“,”分割|

- 在写sudo规则的时候不建议写直接形式的命令，而是写命令的完整路径。可以用which来找指令的二进制文件地址：adminwwq ALL=(ALL)  /usr/sbin/useradd, /usr/bin/passwd [A-Za-z]\*, !/usr/bin/passwd "", !/usr/bin/passwd root

- 之后执行配置好的命令需要先加个sudo： sudo useradd admintest

- 普通用户查看自己具有的特殊权限：sudo  -l

## 5. 自有服务

### 5.1 开机自启动

- Windows启动计算机的硬件（BIOS）
	- 读取时间
	- 选择对应的启动模式（USB HDD EFI）
- Linux系统中/boot目录引导系统启动，详细过程如下
	- 电源通电
	- BIOS启动：引导时间，确定启动模式（USB HDD EFI UEFI）
	- /boot引导分区
	- 找配置文件确定运行级别：读取初始配置文件 /etc/inittab（==系统运行级别配置文件==），启动时控制着运行级别runlevel（几乎与上一个boot并行执行）
	- 执行对应启动控制Systemd
	- 启动服务
		- init.d：要启动的服务
		- rc3.d：软连接（S-NUM，K-NUM）
		- rc.local：自定义脚本
	- 启动tty
- 运行级别
	- 0 — 表示关机级别（不要将默认的运行级别设置成这个值）
	- 1 — 单用户模式
	- 2 — 多用户模式，不带NFS（Network File Syetem）
	- 3 — 多用户模式，完全的多用户模式（不带桌面的，纯命令行模式）
	- 4 — 没有被使用的模式（保留模式）
	- 5 — X11，完整的图形化界面模式
	- 6 — 表示重启级别（不要将默认的运行级别设置成这个值）
- 控制运行级别的指令
```bash
systemctl set-default multi-user.target
```

### 5.2 设置主机名

- hostname [新的主机名]：只能暂时修改，重启后恢复默认的localhost
- 修改etc/sysconfig/network，新版为etc/hostname后重启才是永久修改
- hostnamectl set-hostname shiyanshi
- 加入本地地址才能访问127.0.0.1，需要修改/etc/hosts。

### 5.3 网络连接

- 主机模式
- 桥接模式bridged
	- 虚拟出来的操作系统就像是局域网中的一台独立的主机，它可以访问网内任何一台机器（主机IP是192.168.8.100，虚拟机IP是192.168.8.xxx）
	- 但是桥接模式中虚拟机的IP是写死的，极有可能和其他主机IP重复
- 网络地址转换模式NAT
	- 让虚拟系统借助NAT功能通过宿主主机器所在的网阔访问公网。
	- NAT模式下虚拟系统的TCP/IP配置信息由VMnet8(NAT)虚拟网络的DHCP服务器提供的。
	- 虚拟系统无法和本局域网中的其他真实主机进行通讯。

![](assets/Linux基础-7.png)

|指令|介绍|
|:---:|:---|
|ping 主机地址（IP地址/主机名/域名）|测试当前主机与目标主机的连通性|
|netstat -tnlp|查看网络的连接信息(-t：tcp协议，-n：字母转化成数字，-l：列出状态为监听，-p：显示进程相关信息)|
|netstat -an|查看网络的连接信息(-a：全部，-n：字母转化成数字)|
|traceroute  主机地址|查找当前主机与目标主机之间所有的网关（路由器，会给沿途各个路由器发送icmp数据包，路由器可能会不给响应）。
|arp -a|查看本地缓存mac表|
|arp -d 地址|删除指定的缓存记录|
|tcpdump [协议] port 端口号|抓取本机的指定端口的数据包|
|tcpdump host 地址|抓取本机与指定主机的数据包|
|tcpdump -i 网卡设备名|抓取本机的指定网卡的数据包|

### 5.4 SSH服务

- ssh:secure shell 安全外壳协议，可以用于远程连接协议和远程文件传输协议。
- 协议使用端口号：默认是22。配置文件：/etc/ssh/ssh_config。
- 终端工具主要帮助连接远程的服务器，常见终端工具有：Xshell、secureCRT、Putty等。

- `scp`是Linux发行版默认预装（属于OpenSSH套件），Windows需额外配置。
- `pscp` 是 Windows 端安装的工具，Linux 无法使用，只能在Windows端主动推送或拉取，在沟通时作用到Linux端本质是scp。
- `rsync`（Remote Sync）是 Linux 原生的**增量同步工具**，主打 “只传输变化的文件 / 内容”，适合大文件、大目录的跨主机同步，也支持本地同步。核心优势：比 `scp` 更高效（增量传输）、支持断点续传、保留文件属性（权限 / 时间戳）。
`lrzsz` 是基于 **ZModem/YModem/XModem** 协议的轻量级工具，主打 “终端交互式传输”，适合通过 SSH 终端（如 Xshell、SecureCRT）快速传输单个 / 少量文件，不支持目录递归传输。依赖 SSH 终端工具（Xshell、SecureCRT、MobaXterm）支持 ZModem 协议，无需额外开端口。==Linux 端需安装 lrzsz==。
> [!NOTE] 指令格式都可以概括为`scp/pscp/rsync source target`。

|传输场景|工具|说明|
|:---:|:---:|:---:|
|Windows → Linux|scp|Windows 主动发送（Windows需安装支持scp的环境Git Bash/WSL/OpenSSH）|
||scp|Linux主动拉取（Windows作为服务端并开启 OpenSSH 服务器）|
||pscp|Windows 主动发送（Windows装 PuTTY 套件）|
||rsync|Windows 主动发送（Windows需安装rsync,Git Bash或者WSL自带rsync，但无需开启SSH）|
||rsync|Linux主动拉取（Windows作为服务端并开启 OpenSSH 服务器）|
||rz|Linux主动拉取（Receive ZModem）|
|Linux → Windows|scp|Linux 主动推送（Windows 需开 SSH 服务）|
||scp|Windows主动拉取（Windows需安装支持scp的环境）|
||pscp|Windows 用 pscp 主动拉取（Windows 无需开 SSH 服务, Windows装 PuTTY 套件）|
||rsync|Linux 主动推送（Windows 需开启OpenSSH服务器，无需安装rsync）|
||rsync|Windows主动拉取（Windows需安装rsync,Git Bash或者WSL自带rsync，但无需开启SSH）|
||sz|Linux 主动推送（Send ZModem）|
|本地Linux → 远程Linux|scp|本地主动推送|
||rsync|本地主动推送|
|远程Linux → 本地Linux|scp|本地主动拉取|
||rsync|本地主动拉取|

```bash
# Windows->Linux：Windows 主动发送（Windows需安装支持scp的环境Git Bash/WSL/OpenSSH）
scp [选项] Windows本地Unix格式文件路径  Linux用户名@LinuxIP:Linux目标路径

# Windows->Linux：Linux主动拉取（Windows作为服务端并开启 OpenSSH 服务器）
scp [选项] Windows用户名@WindowsIP:Windows文件路径 Linux本地路径

# Linux->Windows：Linux 主动推送（Windows 需开 SSH 服务）
scp [选项] Linux本地路径  Windows用户名@WindowsIP:Windows目标路径（Unix格式）

# Linux->Windows：Windows主动拉取（Windows需安装支持scp的环境）
scp [选项] Linux用户名@LinuxIP:Linux文件路径 Windows本地路径（Unix 格式）

# Windows->Linux：Windows 主动发送（Windows装 PuTTY 套件）
pscp [选项] 本地Windows文件路径  Linux用户名@LinuxIP:Linux目标路径
//选项-r：递归传输目录（传文件夹必须加）；
//选项-P：指定 Linux SSH 端口（默认 22，非默认需加）。

# Linux->Windows：Windows 用 pscp 主动拉取（Windows 无需开 SSH 服务, Windows装 PuTTY 套件）
pscp [选项] Linux用户名@LinuxIP:Linux文件路径  Windows本地路径

# Windows列出远程路径下目录结构
pscp [选项] -ls 用户名@linux主机地址


# Windows->Linux：Windows 主动发送（Windows需安装rsync,Git Bash或者WSL自带rsync，但无需开启SSH）
rsync [选项] 源路径 Linux用户名@LinuxIP:Linux目标路径
rsync -avzP /c/Users/test/Desktop/Rocky.repo root@192.168.1.100:/tmp/
//-a：归档模式（递归传输 + 保留文件权限 / 时间戳 / 软链接），传目录必加
//-v：`|显示详细传输日志（如文件名称、大小），调试 / 查看进度
//-z：传输时压缩数据（节省带宽，跨网络传输推荐），所有场景推荐
//-P：断点续传 + 显示进度条，大文件传输必加
//-e "ssh -p 端口"：指定 Linux SSH 端口，非 22 端口时加
//--delete`：删除目标路径中源路径没有的文件（镜像同步），需完全同步时加（谨慎）
//-avzP：归档（保留权限）+ 详细日志 + 压缩传输 + 断点续传

# Windows->Linux：Linux主动拉取（Windows作为服务端并开启 OpenSSH 服务器）
rsync [选项] Windows用户名@WindowsIP:Windows文件路径（Unix格式） Linux本地目标路径
rsync -avzP test@192.168.1.50:/C/Users/test/Desktop/Rocky.repo /etc/yum.repos.d/

# Linux->Windows：Linux 主动推送（Windows 需开启OpenSSH服务器，无需安装rsync）
rsync [选项] 源路径 Windows用户名@WindowsIP:Windows目标路径（Unix格式）
rsync -avzP /root/Rocky.repo test@192.168.1.50:/C/Users/test/Desktop/

# Linux->Windows：Windows主动拉取（Windows需安装rsync,Git Bash或者WSL自带rsync，但无需开启SSH）
rsync [选项] Linux用户名@LinuxIP:Linux文件路径 Windows本地路径（Unix格式）
rsync -avzP root@192.168.1.100:/root/Rocky.repo /c/Users/test/Desktop/

# Windows->Linux：Linux主动拉取（Receive ZModem），从本地（如 Windows 客户端）上传文件到 Linux 服务器
lz

# Linux->Windows：Linux 主动推送（Send ZModem），从 Linux 服务器下载文件到本地（如 Windows 客户端）
rz Linux本地目标路径

# 本地 Linux -> 远程 Linux：本地主动推送
scp [选项] 本地路径  远程用户名@远程IP:远程路径

# 远程 Linux -> 本地 Linux：本地主动拉取
scp [选项] 远程用户名@远程IP:远程路径 本地路径

# 本地 Linux -> 远程 Linux：本地主动推送
rsync [选项] 本地路径  远程用户名@远程IP:远程路径

# 远程 Linux -> 本地 Linux：本地主动拉取
rsync [选项] 远程用户名@远程IP:远程路径 本地路径
```

- 用于两台服务器间免密钥直接访问（不对称加密）
```bash
//默认访问需要ssh xxx

//生成公钥
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa

//发送公钥
ssh-copy-id ~/.ssh/id_rsa.pub root@188.188.188.189
```

### 5.5 NTP时间

- 用于对计算机的时间同步管理操作
- 根时间服务器[全球可用的NTP服务器列表与解析服务](http://www.ntp.org.cn/)
- ntpdate 时间服务器的域名/ip
- 有了ntp服务后就可以自动设置时间了
```bash
yum install ntp -y
ntpdate cn.ntp.org.cn

//也可以使用逻辑运算
yum info ntp &&ntpdate cn.ntp.org.cn
```

|作用|时间日期指令|
|:---:|:---|
|日期时间|date；|
||date +%F；date "+%Y-%m-%d"；|
||date +%T；date "+%H:%M:%S"；|
||date "+%F %T"；date "+%Y-%m-%d %H:%M:%S"；|
||date -d "-1 day" "+%Y-%m-%d %H-%M-%S"；|
|日历|cal；cal -1；cal -3；|
||cal -y 2030；|
||cal -s；cal -m；cal -my 2030；|

### 5.6 进程管理

|语法|说明|
|:---:|:---|
|ps|查看当前终端中的所有进程|
|ps -ef|查看所有进程|
|ps -ef \| grep panel|查看指定的进程信息|
|ps -aux|查看详细的进程信息，包括进程的CPU与内存使用情况|
|ps -aux --sort -pcpu|根据cpu使用率排序展示|
|pstree；pstree -p|进程树|
|top|动态的进程使用情况（VIRT虚拟内存 RES 常驻内存 SHR 共享内存）|
|kill PID|杀死进程|
|kill -9 PID|-9选项可以杀死整个进程组|
|ping xxx >>text.txt &|把该指令执行的进程放到后台执行，不阻塞当前终端|
|nohup ping xxx>>test.txt 2>&1 &|nohup可以防止后台进程被挂起|
|jobs|查看后台进程|

|进程字段|意义|
|:---:|:---|
|UID|用户id|
|PID|进程id，最初始系统引导的进程PID为0
|PPID|当前进程的父进程的id|
|CMD|对应的命令|

### 5.7 服务管理

 - 服务本质上也是进程，但通常是**长期在后台运行的守护进程**（Daemon）。其独立于终端，常用于提供系统功能（如sshd、mysqld）。

> [!NOTE] 程序是蓝图，进程是蓝图的运行实例。服务是特殊类型的进程，通常随系统启动并在后台运行，等待请求。一个服务可能由一个或多个进程组成（如多进程Web服务器）。

|指令|作用|
|:---:|:---|
|chkconfig --list|查看自启动服务的信息（chkconfig用于管理自启动服务）|
|chkconfig --del 服务名|删除自启动的服务|
|systemctl list-dependencies [target]|查看自启动服务的信息|
|systemctl enable crond.service|开机启动服务 .service可以省略|
|systemctl disable crond.service|禁止开机启动|
|systemctl status crond.service|查看服务状态|
|systemctl start crond.service|启动服务|
|systemctl stop crond.service|停止服务|
|systemctl restart crond.service|重启服务|
|systemctl is-enabled crond.service|查看某个服务是否开机启动|

### 5.8 防火墙

- 用于防范网络攻击，可分为硬件防火墙和软件防火墙。国家长城防火墙GFW。
- 选择性让网阔请求通过（iptables，firewalld）
- 其服务操作使用systemctl，查看规则使用iptables -L -n。其中-L表示列出规则，-n表示以数字形式展示。
- 本质上是保护端口
- 防火墙也是一个服务。

|指令|说明|
|:---|:---:|
|systemctl status firewalld(.service)|查看服务的状态|
|systemctl start firewalld|启用firewalld服务|
|systemctl stop firewalld|临时禁止firewalld服务，重启后恢复原本的设置|
|systemctl enable firewalld|设置永久启用firewalld服务|
|systemctl disable firewalld|设置永久禁止firewalld服务|
|firewall-cmd --add-port=80/tcp --permanent|永久添加8080端口例外(全局)|
|firewall-cmd --remove-port=80/tcp --permanent|永久删除8080端口例外(全局)|

### 5.9 计划任务

- 首先需要启动crond服务

|指令(cron/crontab)|说明|
|:---|:---:|
|crontab -l|list，列出指定用户的计划任务列表|
|crontab -e|edit，编辑指定用户的计划任务列表|
|crontab -u|user，指定的用户名，如果不指定，则表示当前用户|
|crontab -r|remove，删除指定用户的计划任务列表|

- 编辑任务

|分|时|日|月|周|命令|
|:---|:---|:---|:---|:---|:---|
|1-59|0-23|1-31|1-12|0-6（0表示星期日）|执行的命令|

- 计划任务的规则与格式
	1. 以行为单位，一行表示一个计划
	2. 分 时 日 月 周 需要执行的命令(0 0 * * * reboot表示每天0点0分重启)
	3. 可以用正则表达式，例如每10分钟可以写为\*/10 1,2,6 1-3 * * reboot
	4. `*`表示任意时间段，就是每的意思
	5. `-`表示区间，是一个范围
	6. `,`表示列出多个时段
	7. `/n`表示每隔n执行一次(\*和/1都可以表示每)

- 黑名单配置（禁止其中的用户配置计划任务）： /etc/cron.deny里写用户名，每行一个
- 白名单配置（允许其中的用户配置计划任务）： /etc/cron.allow里写用户名，每行一个

### 5.10 设备挂载

|指令|说明|
|:---|:---|
|lsblk|查看块状设备的信息（Name:名称，Size:设备大小，Type:类型，MountPoint:挂载点）|
|umount 当前设备的挂载点（路径）|解除挂载|
|mount 设备原始地址 要挂载的位置路径|挂载（所有块状内存设备原始地址都在/dev/下，新地址建议放在/mnt/下）|

## 6. 软件安装

### 6.1 环境变量

- 命令能执行的前提是内核能找到这个命令，内核首先从当前路径下找，若找不到则从环境变量开始找指定的命令，没找到就会提示没有这样的指令。
- 系统的环境变量路径是：/etc/profile
- 用户的环境变量路径是：~/.bash_profile
- Linux路径与路径之间用冒号连接（Windows是分号）
- 修改了环境变量之后需要重新加载文件：`source /etc/profile`

### 6.2 安装方式

- 解压直接使用
- 自行下载安装包安装
- 使用统一的软件包管理器帮助安装
- 源码编译安装

### 6.3 解压安装

- 自行选择路径，一般放到opt或者usr中(例如安装Tomcat)
```bash
tar -zxvf ...
移动到/opt/
```

### 6.4 RPM安装

- RedHat Package Manager，属于红帽的一种包管理方式
- 用来安装.rpm格式的软件安装包(例如安装jdk)，包管理系统简单，只需要几个命令就可以实现包的安装，升级，查询和卸载；但.rpm安装包是经过编译的，不再可以看到源代码了。
- 对于linux服务器上的软件包及逆行管理操作：查询、安装（更新）、卸载

|指令|说明|
|:---|:---:|
|rpm -qa\|grep 关键词；rpm -qa 关键词|查询|
|rpm -e 软件的名称|删除|
|rpm -e 软件的名称 --nodeps|强制删除,不检查其他包是否依赖它|
|rpm -ivh 软件包名称|软件安装|
|rpm -Uvh 软件包名称|软件更新|
|rpm -qf 文件路径|查询指定文件属于哪个包|

- 安装jdk举例
```bash
rpm -ivh jdk-8.0.44.rpm
vi /etc/profile
vi> export JAVA_HOME=/usr/java/jdk1.8-amd64
vi> export PATH=$JAVA_HOME/bin:$PATH
source /etc/profile
```
> [!NOTE] 安装完之后可能需要到/etc/profile下配置环境变量，然后重新加载文件

- deb分支的安装方式

|指令|说明|
|:---|:---:|
|dpkg -info 软件包名称.deb|查询|
|dpkg -L 软件包名称.deb|查看文件拷贝详情|
|dpkg -r 软件包名称.deb|删除|
|dpkg -r --purge 软件的名称.deb|连同配置文件一起删除|
|dpkg -i 软件包名称.deb|软件安装|
|dpkg -l|查看系统中已安装的包的信息|
|dpkg-reconfigure 软件包名称|重新配置软件包命令|

### 6.5 YUM安装

- 利用包管理器来管理RPM包和安装软件，一般情况下会默认安装在/usr/lib/.
- 无需自己寻找安装包，也无需自己管理软件/包之间的依赖关系(例如安装mysql)

|指令|说明|
|:---|:---:|
|yum search 软件/包名|搜索指定的关键词的包|
|yum info 软件/包名|查看包信息|
|yum list [installed]|列出所有安装的包|
|yum list 查找匹配的表达式|列出想要查找的包，eg: yum list \*jdk\*|
|yum install 软件/包名|安装包|
|yum update 软件/包名|更新指定的包，不指定包则更新全部软件|
|yum remove 软件/包名|卸载指定的包|

- 更改yum源
```bash

//将系统原始配置文件失效
mv rocky.repo recky.repo.backup

//获取阿里云的yum源配置文件。注意路径差异（9.x 使用 /rocky 代替 /rockylinux）
sed -e 's|^mirrorlist=|#mirrorlist=|g' -e 's|^#baseurl=http://dl.rockylinux.org/$contentdir|baseurl=https://mirrors.aliyun.com/rockylinux|g' -i.bak /etc/yum.repos.d/rocky*.repo

//清空yum源的缓存
yum clean all

获取阿里云yum的缓存
yum makecache
```
- 安装mysql举例
```sql

//安装相关包
yum install perl net-tools
yum install libaio

//解压tar包
tar -xvf mysql-8.0.44-1.el9.x86_64.rpm-bundle.tar

//依次安装rpm
rpm -ivh mysql-community-common-8.0.44-1.el9.x86_64.rpm
rpm -ivh mysql-community-client-plugins-8.0.44-1.el9.x86_64.rpm
rpm -ivh mysql-community-libs-8.0.44-1.el9.x86_64.rpm
rpm -ivh mysql-community-icu-data-files-8.0.44-1.el9.x86_64.rpm
rpm -ivh mysql-community-client-8.0.44-1.el9.x86_64.rpm
rpm -ivh mysql-community-server-8.0.44-1.el9.x86_64.rpm

//启动mysqld服务
systemctl start mysqld

//找默认生成的初始密码
cat /var/log/mysqld.log |grep password

//登录
mysql -u root -p

//改密码
mysql> set global validate_password.policy=LOW;
mysql> set global validate_password.length=6;
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '123456' PASSWORD EXPIRE NEVER;
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';

//查看所有库
mysql> show databases;
mysql> use mysql;

//查看所有表
mysql> show user;

//修改链接地址,实现远程连接数据库，有利于数据库和服务分离。host字段为“%”表示任意主机ip。
mysql> select host,user from user;
mysql> update user set host='%' where user='root';
mysql> commit;

systemctl restart mysqld.service
```

- deb分支的安装方式

| 指令                    |              说明               |
| :-------------------- | :---------------------------: |
| apt-get update        |           更新镜像源的包列表           |
| apt-get install 软件/包名 |              安装包              |
| apt-get remove 软件/包名  |            卸载指定的包             |

### 6.6 源码安装

- 优点：
	1. 开源，如果有足够的能力，可以修改源代码；
	2. 编译安装，更加适合自己的系统，稳定高效
- 缺点：
	1. 安装步骤较多，容易出错；
	2. 编译过程时间较长

- 源码包安装：解包（解压缩）->配置（config/configure/bootstrap）->编译（make/bootstrapd）->安装（make install/bootstrapd install）

- 配置操作主要是指定软件的安装目录、需要的依赖在什么地方、指定不需要可选依赖、配置文件的路径、通用数据存储位置等等。
- 编译主要是用gcc编译c文件为机器文件
- 安装主要是根据先前的配置要求安装软件，并生成相应的指令bin目录，用户手册等

|功能|指令|说明|
|:---:|:---|:---|
|解包|tar -zxvf \*.tar.gz|-z或--gzip或--ungzip：通过gzip指令处理文件；-x或--extract或--get：从文件中还原文件；-v：显示操作过程；-f或--file：指定一个文件；|
|解包|tar -jxvf \*.tar.bz2|-j：支持bzip2解压文件；；-x或--extract或--get：从文件中还原文件；-v：显示操作过程；-f或--file：指定一个文件；|
|配置|./configure --prefix=/usr/local/ncurses|指定了安装路径。指定安装的路径：--prefix=路径；需要依赖的路径：--with-PACKAGE名=[包所在的路径]；不需要依赖：--without-PACHAGE名|
|编译|make|前提是得在这个路径下|
|安装|make install|前提是得在这个路径下|
|编译且安装|make && make install|前提是得在这个路径下|

## 7. Shell编程

### 7.1 Shell概要

- Shell：是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Kernel是Linux的内核，它主要和硬件打交道，程序员使用时直接通过Kernel操控硬件太繁琐了，所以套一层壳即shell，用户通过shell间接操作内核实现功能。
- Shell本质上是一个应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。概括可以说是一个命令解释器（==command interpreter==）
- Shell同时也可以看作是一种命令语言，一种程序设计语言，可以用来写脚本。
- 脚本：简单地说就是一条条的文字命令，这些文字命令是可以看到的（如可以用记事本打开查看、编辑）。（例如JavaScript（JS，前端），VBScript， ASP，JSP，PHP（后端），SQL（数据库操作语言），Perl，Shell，python，Ruby，JavaFX，Lua等。）脚本本身是没有状态的，需要触发才能执行。

- 两大主流：sh和csh
	1. sh
		- Bourne shell(sh)：Solaris，hpux默认shell
		- Bourne again shell(bash)：Linux系统默认shell
	2. csh
		- C shell(csh)
		- tc shell(tcsh)

- 在Linux中有很多类型的shell，不同的shell具备不同的功能，shell还决定了脚本中函数的语法，Linux中默认的shell在/bin/bash，流行的shell有ash、bash、ksh、csh、zsh等，不同的shell都有自己的特点以及用途。
- 大多数Linux系统默认使用的shell/就是bin/bash，它还是sh的升级版。

### 7.2 编写规范

- 代码规范

```shell
#!/bin/bash [指定告知系统当前这个脚本要使用的shell解释器,相当于声明]
Shell相关指令
```

- 文件命名规范

```shell
文件名.sh [.sh是linux下bash shell 的默认后缀]
```

- 创建sh文件

```shell
touch test.sh
vim test.sh
```

- 编写sh文件

```shell
#!/bin/bash
echo 'helloworld'
#输出的内容如果包含字母和符号（不包含变量），则需要用引号包括起来。如果是纯数字可以包也可以不包。
```

- 执行shell脚本

```shell
# 必须得有执行权限
./test.sh

# 没有执行权限时，root和文件所有者可以通过以下方式执行
bash /oo/test.sh
sh test.sh 
. test.sh
source test.sh

```

> [!note] 这里在运行时一定要写成 ./test.sh，而不是 test.sh，运行其它二进制的程序也一样，直接写 test.sh，Linux 系统会去 PATH（环境变量） 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。

### 7.3 变量共享

- 上一节执行shell脚本中前三种方法会新开一个bash（.sh），不同bash中的变量无法共享。后两种方法是在同一个shell中执行的。可以通过ps -ef查看到。
```shell

//hello.sh
#!/bin/bash
echo "helloworld"
echo $name

//bash
name=libai
bash hello.sh # 输出helloworld
source hello.sh # 输出helloworld libai
```
- export可以将当前进程中的变量传递给子进程中使用。一般配置profile的时候所有的变量全部export
```shell

//hello.sh
#!/bin/bash
echo "helloworld"
echo $name

//bash
name=libai
export name
bash hello.sh # 输出helloworld libai
source hello.sh # 输出helloworld libai
```

### 7.4 参数传递

- 在脚本中可以用“$1”来表示第一个选项参数，“$2”来表示第二个选项，以此类推。其实$1、$2本质是变量，但是只能用，用户创建这样的用户名是不合法的。

```shell
#!/bin/bash
echo $# # 传递到脚本的参数个数
echo $* # 以一个单字符串显示所有向脚本传递的参数

echo $0 # 当前脚本执行的文件名
echo $1 # 第一个参数
echo $2
echo $3

echo $$ # 脚本运行的当前进程ID号
echo $? # 显示命令的最后退出状态，0表示没有错误，其他任何值表示有错误
echo $! # 后台运行的最后一个进程的ID号
./test.sh a b c
```

### 7.5 输入输出

- 输入语法：read  -p 提示信息 变量名
- 输出语法：echo ...。默认输出到控制台
```shell
#! /bin/bash
# 输入
read -p '请输入文件名：' filepath

# 输出
echo 'helloworld'
echo "helloworld"
echo '10'
echo "20"
echo 100
echo 10 20
echo "tmp" 123
name=zhangsan
echo $name
echo ${name} # 和上一行效果相同

echo -e "OK \c" #处理所有转义字符
echo "helloworld" > filename
echo "helloworld" >> filename

# 注释 
# 井号用于单行注释
:<<EOF
多行注释内容...
多行注释内容...
多行注释内容...
EOF
```

### 7.6 变量

- 变量名后面的等号左右不能有空格
- 当在脚本中需要执行一些指令并且将执行的结果赋给变量的时候需要使用“反引号”(指令要用反引号，变量要用$)
- let 是 Bash 中用于执行整数运算的命令，支持自增、自减等操作。它允许对变量进行直接数学运算，且无需在变量前加$符号。
- 只读变量不能被修改，也不能删除
- bash支持一维数组，不支持多维数组，并且没有限定数组的大小
- 数组元素的下标从0开始编号，获取数组中的元素要利用下标，下标可以时整数或者算数表达式，其值应该大于等于0。
```shell
#! /bin/bash

# 变量赋值(默认相当于字符串)
name=zhangsan
echo "$name `date +"%F %T"`" # 输出变量的值用$,输出指令的结果用``
let a=10
let a=10 b=20 c=a+b
echo c # 输出30

# 变量自增自减
counter=5
let counter-=1
(( counter-- ))

# 只读变量：readonly 变量名
a=10
readonly a
a=20 # 报错
echo $a

# 删除变量
unset name # 没有这个变量则输出空行

# 字符串
str1='helloworld'
str2="helloworld"
str3="hello $name" # 双引号中可以解析变量
echo str3
str4='hello $name' # 单引号中不会解析变量
echo str4
echo ${str1:1:5}
echo ${#str1} # 输出字符串长度

# 数组
favs=("football" "basketball" "pingpang")
echo ${favs[1]}
echo ${#favs[1]}
echo ${#favs} # 默认时第0个元素的长度
echo ${favs[@]} or echo ${favs[*]}
echo ${#favs[@]} or echo ${#favs[*]}
```

### 7.7 运算符

- 运算符和其他编程脚本语言一样，常见的有==算数运算符==、==关系运算符==、==逻辑运算符==、==字符串运算符==、==文件测试运算符==等
- 原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。expr 是一款表达式计算工具，使用它能完成表达式的求值操作。
- 表达式和运算符之间要有空格 `expr 2+2`不对，应该是`expr 2 + 2`，写在shell中要用反引号包起来。
- 关系运算符只支持数字，不支持字符串，除非字符串的值是数字。
- test命令用于检查某个条件是否成立，它可以进行数值、字符和文件三个方面的测试。
- \==和-eq是一样的，但是大于小于只能用指令。
- 权限rwx的判断，如果只有一个部分符合，则认为是有权限的。

> [!note] 除了赋值时等号两边不允许有空格，剩下情况符号周围都得空格。
|运算符|说明|举例(a=10,b=20,c="abc",d="efg", file="/etc/oo",file="/etc/oo/test.txt")|
|:---:|:---|:---|
|算数运算符|||
|+|加法|`expr $a + $b` 结果为 30。|
|-|减法|`expr $a - $b` 结果为 -10。|
|\*|乘法|`expr $a \* $b` 结果为  200。|
|/|除法|`expr $b / $a` 结果为 2。|
|%|取余|`expr $b % $a` 结果为 0。|
|=|赋值|a=$b 将把变量 b 的值赋给 a。|
|\==|相等。用于比较两个数字，相同返回true| [ $a == $b ] 返回 false|
|!=|不相等。用于比较两个数字，不相同返回true| [ $a != $b ] 返回 true。|
|关系运算符|||
|-eq|检测是否相等|[ $a -eq $b ] 返回 false。|
|-ne|检测是否不相等|[ $a -ne $b ] 返回 true。|
|-gt|是否大于|[ $a -gt $b ] 返回 false。|
|-lt|是否小于|[ $a -lt $b ] 返回 true。|
|-ge|是否大于等于|[ $a -ge $b ] 返回 false。|
|-le|是否小于等于|[ $a -le $b ] 返回 true。|
|逻辑运算符|||
|!|非运算|[ ! false ] 返回 true。|
|-o, \|\||或（或者）运算|[ $a -lt 20 -o $b -gt 100 ] 返回 true。|
|-a, &&|与（并且）运算|[ $a -lt 20 -a $b -gt 100 ] 返回 false。|
|字符串运算符|||
|=|检测两个字符串是否相等|[ $c = $d ] 返回 false。|
|!=|检测两个字符串是否不相等|[ $c != $d ] 返回 true。|
|-z|检测字符串长度是否为0|[ -z $a ] 返回 false。|
|-n|检测字符串长度是否不为0|[ -n $a ] 返回 true。|
|str|检测字符串|[ $a ] 返回 true。|
|文件测试运算符|||
|-r file|检测文件是否可读|[ -r $file ] 返回 true。|
|-w file|检测文件是否可写|[ -w $file ] 返回 true。|
|-x file|检测文件是否可执行|[ -x $file ] 返回 true。|
|-s file|检测文件（文件大小是否大于0）|[ -s $file ] 返回 true。|
|-e file|检测文件（包括目录）是否存在|[ -e $file ] 返回 true。|
|-d file|检测文件是否是目录|[ -d $file ] 返回 false。|
|-f file|检测文件是否是普通文件（既不是目录，也不是设备文件）|[ -f $file ] 返回 true。|
|-b file|检测文件是否是块设备文件，如果是，则返回 true。|[ -b $file ] 返回 false。|
|-c file|检测文件是否是字符设备文件，如果是，则返回 true。|[ -c $file ] 返回 false。|
|-g file|检测文件是否设置了 SGID 位|[ -g $file ] 返回 false。|
|-k file|检测文件是否设置了粘着位(Sticky Bit)|[ -k $file ] 返回 false。|
|-u file|检测文件是否设置了 SUID 位|[ -u $file ] 返回 false。|
|-p file|检测文件是否是有名管道|[ -p $file ] 返回 false。|

```shell
#!/bin/bash
a=10
b=20
echo "a=$a"
echo "b=$b"
echo "a + b = " `expr $a + $b`
echo "a - b = " `expr $a - $b`
echo "a * b = " `expr $a \* $b`
echo "a / b = " `expr $a / $b`
echo "a % b = " `expr $a % $b`
```

### 7.8 逻辑结构

- if
- case
- for
- while
- break
- continue

```shell
#!/bin/bash

a=10
b=20

read -p '请输入数字：' number
echo $number
echo
# if then
if [ $number == $a ]
then
        echo $a
fi

#if then
if [ $number == $a ]; then echo "$a"; fi

#if elif else
if [ $number == $a ]
then
        echo $a
elif [ $number == $b ]
then
        echo $b
else
        echo $number
fi

# test可以处理字符串的关系运算
if test $[a] -eq $[b]
then
	echo $a
else
	echo $b
fi

# case
case $a in
1)
	echo A
;;
2)
	echo B
;;
name)
	echo C
	echo D
*)
	echo error
;;
esac

# for
for loop in 1 2 name 120
do
	echo $loop
done

for loop in 'This is a string' "string2"
do
	echo $loop
done

# while
while(( $a <= 5 ))
do
	echo $a
	let "a++"
done
while true
do
	ping 192.168.188.188
done

# break
while :
do
	read num
	case $num in
		1|2|3|4) echo $num
		;;
		*) echo error
			break
		;;
	esac
done

# continue
while :
do
	read num
	case $num in
		1|2|3|4) echo $num
		;;
		*) echo error
			continue
		;;
	esac
done

```

### 7.9 函数

- 用户定义函数，可以带function fun()定义，也可以直接fun()定义不带任何参数
- 参数返回可以显示加return 返回值，如果不加将以最后一条命令运行结果作为返回值。
- return后跟数值n(0-255)
```shell
#! /bin/bash
demoFun(){
	echo "demoFun"
	echo $1 # 第一个参数
	a=10
	b=20
	return $(($a + $b))
}
demoFun 10 20
```

## 10. 项目上线

- 流程：
	1. 购买服务器
	2. 购买域名
	3. 域名备案
	4. 域名解析
	5. 配置生产环境
	6. 上传代码

- 域名解析：将域名绑定到一个服务器地址的操作
- DNS：domain name server，用于将域名转化成ip地址的服务器。

![](../assets/Linux%E5%9F%BA%E7%A1%80-6.png)
- Nginx：是一款比较流行的web服务器软件，类似于Apache。
- 使用在服务器端下载的方式进行下载(直接发请求)：wget 地址
- nginx默认也占用80端口，所以需要先把httpd停掉
- 启动命令：/usr/local/nginx/sbin/nginx
- 重载，重载配置文件后启动：/usr/local/nginx/sbin/nginx -s reload
- 关闭：只能kill进程
- 卸载编译安装的软件：rm -rf 软件的安装目录
- 注意：卸载一个编译安装的软件的时候必须先停止。

- LAMP：Linux+Apache+MySQL+PHP
- LNMP：Linux+Nginx+MySQL+PHP-fpm
- LNMP：Linux+Nginx+MySQL+PHP+Apache

- 默认的Apache站点目录：/var/www/html，网页要展示的内容文件放在该目录下
