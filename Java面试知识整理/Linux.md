# Linux

**Ubuntu打开终端快捷键：Ctrl+Alt+T**

**Linux是一套免费使用和自由传播的类Unix操作系统，是一个基于POSIX和UNIX的多用户、多任务、支持多线程和多CPU的操作系统。**

**Linux发行版实则是将Linux内核与应用软件做一个打包。目前较知名的发行版有：Ubuntu、RedHat、CentOS、Debian等**



### VIM三个模式  

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/VIM.png)  

- 一般指令模式：VIM默认模式，可以用于移动游标查看内容
- 编辑模式：按下“i”等按键后进入，可以对文本进行编辑
- 指令列模式：按下":"按键后进入，用于保存退出等操作

指令列模式下，用于离开或保存文件的命令：

| 命令 | 作用                                                         |
| ---- | ------------------------------------------------------------ |
| :w   | 写入磁盘                                                     |
| :w!  | 当文件为只读时，强制写入磁盘；能不能写入与用户对该文件的权限有关 |
| :q   | 离开                                                         |
| :q!  | 强制离开不保存                                               |
| :wq  | 写入磁盘后离开                                               |
| :wq! | 强制写入磁盘后离开                                           |



### 部分重要的Linux命令

**1. 文件与目录基本操作**

mkdir：创建目录

ls：列出文件或目录信息，目录的信息就是其中包含的文件

cd：更换当前目录

rmdir：删除目录，目录必须为空

touch：建立新文件或者更新文件时间

cp：复制文件（cp a b：将目录下的a文件复制一份为b文件）

rm：删除文件

mv：移动文件  

pwd：显示工作目录(绝对路径)  

pwd：显示工作目录（绝对路径）

**2. 获取文件内容**

cat：取得文件内容

tac：cat反向操作，从最后一行开始打印

more：可以一页一页查看文件内容，比较适合大文件查看

less：和more类似，多了一个向前翻页功能

head：取得文件前几行（head -2 filename：获取文件前两行内容）

tail：head反向操作，取得后几行

od：以字符或十六进制形式显示二进制文件

**3. 指令与文件搜索**

which：指令搜索（which ls：返回/bin/ls)

whereis：文件搜索，速度较快，只搜索几个特定目录

locate：文件搜索，可用关键字或正则表达式搜索

find：文件搜索，使用文件的属性和权限进行搜索

**4. 压缩与打包**

gzip：Linux使用最广压缩指令（gzip filename：压缩，gzip -d filename：解压缩）

bzip2：提供比gzip更高压缩比（bzip2 -d filename：解压缩）

xz：提供比bzip2更佳压缩比

```shell
$ tar [-z|-j|-J] [cv] [-f 新建的 tar 文件] filename...  ==打包压缩
$ tar [-z|-j|-J] [tv] [-f 已有的 tar 文件]              ==查看
$ tar [-z|-j|-J] [xv] [-f 已有的 tar 文件] [-C 目录]    ==解压缩
-z ：使用 zip；
-j ：使用 bzip2；
-J ：使用 xz；
-c ：新建打包文件；
-t ：查看打包文件里面有哪些文件；
-x ：解打包或解压缩的功能；
-v ：在压缩/解压缩的过程中，显示正在处理的文件名；
-f : filename：要处理的文件；
-C 目录 ： 在特定目录解压缩。
```

tar -zcv -f a.tar.gz a.txt b.txt：将目录下的a.txt和b.txt文件打包压缩为a.tar.gz

tar -ztv -f a.tar.gz：查看压缩包内文件内容

tar -zxv -f a.tar.gz：解压缩

**5. 进程管理**

**查看进程**

ps：查看某个时间点的进程信息（ps -l：查看自己的进程）

pstree：查看进程树（pstree -A：查看所有进程树）

top：实时显示进程信息（top -d 2：每两秒钟刷新一次、按q退出）

netstat：查看占用端口的进程

kill：杀死进程（kill -9 4394：-9代表exit，不会被系统阻塞，4394为进程号）

**6. 正则表达式**

grep：使用正则表达式进行全局查找并打印

```shell
$ grep [-acinv] [--color=auto] 搜寻字符串 filename
-c ： 统计匹配到行的个数
-i ： 忽略大小写
-n ： 输出行号
-v ： 反向选择，也就是显示出没有 搜寻字符串 内容的那一行
--color=auto ：找到的关键字加颜色显示
```

grep -n 'a' a.txt（匹配a.txt文件中含有a的行，并且输出行号）

正则表达式 a{m,n} 匹配字符a m-n次

```shell
$ grep -n 'a\{2,5\}' regular_express.txt
```

printf：格式化输出，不属于管道命令，在给printf传数据时需要使用$()形式

awk：每次处理一行，处理的最小单位是字段，每个字段的命名方式为：$n，n为字段号，从1开始，$0表示一整行

awk变量：

| 变量名 | 代表意义                   |
| ------ | -------------------------- |
| NF     | 每一行拥有的字段数         |
| NR     | 目前所处理的是第几行数据   |
| FS     | 目前的分割字符，默认空格键 |

```shell
$ last -n 5 | awk '{print $1 "\t" $3}'
```

```shell
$ last -n 5 | awk '{print $1 "\t lines: " NR "\t columns: " NF}'
dmtsai lines: 1 columns: 10
dmtsai lines: 2 columns: 10
dmtsai lines: 3 columns: 10
dmtsai lines: 4 columns: 10
dmtsai lines: 5 columns: 9
```

**7. 常用基本操作**

--help：指令的基本用法与选项介绍

man：将指令的具体信息显示出来（man ls）

info：与man类似，info将文档分成一个个页面，每个页面可以跳转

doc：/usr/share/doc存放着软件的一整套说明文件

who：关机前用who命令查看有没有其他用户在线

sync：为加快对磁盘文件读写速度，位于内存中的文件数据不会立即同步到磁盘，因此关机前需要先进行sync同步操作

sudo：允许一般用户使用root可执行的命令，只有在/etc/sudoers配置文件中添加的用户才能使用该指令  

ifconfig：显示或设置网络设备  

ifconfig：显示或设置网络设备



### Shell

**Shell是一个应用C语言编写的程序，它是用户使用Linux的桥梁。Shell既是一种命令语言，又是一种程序设计语言。Shell是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务**



**shell传递参数**

| 参数处理 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| $#       | 传递到脚本的参数个数                                         |
| $*       | 以一个单字符串显示所有向脚本传递的参数，如以“$1 $2 ... $n"形式输出所有参数 |
| $$       | 脚本运行的当前进程ID号                                       |
| $!       | 后台运行的最后一个进程的ID号                                 |
| $@       | 与$*相同，但是使用时加引号，并在引号中返回每个参数，如以”$1""$2"..."$n"形式输出所有参数 |
| $-       | 显示Shell使用的当前选项，与set命令功能相同                   |
| $?       | 显示最后命令的退出状态，0表示没有错误，其他任何值表明有错误  |
| $n       | n代表一个数字，$0表示执行的文件名，$1表示执行脚本的第一个参数，$2...以此类推 |

```shell
echo "$0"
echo "$1"
echo "$2"
echo "$#"
echo "$*"
echo "$$"
echo "$!"
echo "$@"
echo "$-"
echo "$?"
```

```
$ vi a.sh
$ sh a.sh 1 2 3 4 5 6
a.sh
1
2
6
1 2 3 4 5 6
1631

1 2 3 4 5 6

0
```

