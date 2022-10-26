# OSTEP jyy

## 0x 01 reference

[官网](http://jyywiki.cn/OS/2022/)

[教学视频](https://www.bilibili.com/video/BV12L4y1379V/?spm_id_from=333.788&vd_source=38033fe3a1f136728a1d6f8acf710b51)

----

### 1.why learn OS

```c
1.解决一个问题的步骤：why-what-how
为什么要学习操作系统
什么是操作系统
怎样学习操作系统

2.三个根本问题
（1）操作系统服务谁
程序=状态机
课程涉及：多线程Linux应用程序
（2）（乘机/应用视角）操作系统为程序提供什么服务
操作系统=对象+API（用来操纵对象)
课程涉及：POSIX+部分Linux特性
（3）（实现/硬件角度）如何实现操作系统提供的服务
操作系统=C程序
课程涉及：xv6,自制迷你操作系统
```

### 2.code

| 说明                         | 操作                               |
| ---------------------------- | ---------------------------------- |
| 查看用户名                   | uname -a                           |
| 传送ACWing配置               | scp .bashrc .vimrc .tmux.conf：... |
| 下载tldr(too long dont read) | sudo apt-get install tldr          |



### 3. wrong when install tldr

报错，出现用户名 is not in the sudoers file.
是因为当前的用户没有加入到sudo的配置文件里
解决方案：

| 步骤           | 方法                                                         |
| -------------- | ------------------------------------------------------------ |
| 切换到root用户 | su - root                                                    |
| 修改配置文件   | vim /etc/sudoers                                             |
| 修改           | 增加配置, 在打开的配置文件中，找到ununtu/root ALL=(ALL) ALL, 在下面添加一行：your_user_name ALL=(ALL) ALL，wq!退出，这个文件是只读的，所以需要加！在ubuntu下是最后一行 |



### 4. some jyy wonderful operating

| 功能                                                         | 命令                                      |
| ------------------------------------------------------------ | ----------------------------------------- |
| 在vim里面编译C/C++                                           | :!gcc/g++  %  -o  main (% 获取当前文件名) |
| 在vim里面编译并运行C++                                       | :!g++ % -o main && ./main                 |
| wget用于从网络上下载资源，没有指定目录，下载资源回默认为当前目录。wget支持HTTP，HTTPS和FTP协议，可以使用HTTP代理 | wget url                                  |
|                                                              |                                           |



## 0x 02 what is Programs and Compilers

### 1. notes

> 程序就是状态机
>
> > 数字系统是状态机，程序运行在数字系统上，因而程序也应该是状态机。
> >
> > 在 C 语言中，程序中所有的东西（堆，栈）就是程序的状态，每执行一条一句，程序的状态就会变一下。
>
> include的形式语义就是复制粘贴，它是在预编译阶段处理的，例如我们 ```#include <tets.c>``` , 在预处理阶段，会从一个目录找到 include 的 test.c 文件，然后将它的内容复制粘贴到源代码中。



### 2. more instruction

```shell
gdb layout src
asm volatile(“rdrand %rax”);
info proc mappings :查看进程信息
unist: unix standard library
```

### 3. bug

```	c
#define FOE_EACH(_) _(X) _(Y);
#define DEFINE(X) int X, X1;

FOE_EACH(DEFINE)
/*
    我们的本意是执行:
    int X, X1; int Y, Y1;
    但是我们执行是：
    int X, X1; int Y, X1;
  	这里的X1重复定义了
  	这是因为X1的X并不能被识别，它会被认为是一个字符串
	正确的写法是:
	#define DEFINE(X) int X, X##1
	这样X##1的X就可以被识别
*/
```

### 4. Unix’s philosophy

- Make each program do one thing well
- Expect the output of every program to become the input to anothe









