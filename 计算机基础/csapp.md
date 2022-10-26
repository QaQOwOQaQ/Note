# 深入理解计算机系统

## 0x ff 杂项

Instruction set Architecture：ISA,指令集体系架构

软件和硬件之间的一层抽象层

冯诺依曼计算机，即程序**存储型**计算机

重要思想：程序就是一系列被编码了的字节序列（看上去和数据一模一样）

----



https://www.cnblogs.com/SovietPower/p/14877143.html

## 0x 00 参考资料 && lab

official：

[官网](http://csapp.cs.cmu.edu/3e/students.html)

[实验](http://csapp.cs.cmu.edu/3e/labs.html)

----

note:

[视频详解](https://www.bilibili.com/video/BV17K4y1N7Q2?spm_id_from=333.999.0.0&vd_source=38033fe3a1f136728a1d6f8acf710b51) 

[笔记参考视频的源码](https://github.com/yangminz/bcst_csapp)

-----

lab：

[比较详细的Attack，Data，Boom Lab参考](https://github.com/wuxueqian14/CSAPP-Lab#%E5%AE%9E%E9%AA%8C%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA)

[Boom，Attack，Shell Lab](https://kazamayc.github.io/2021/02/05/csapp-lab/)

[全部实验的详细参考–知乎](https://zhuanlan.zhihu.com/p/505497911)

[全部实验的详细参考–CSDN](https://blog.csdn.net/weixin_43362650/article/details/122890142)

[全部实验的详细参考–Github](https://github.com/Exely/CSAPP-Labs)

-----

video:

[导读](https://www.bilibili.com/video/BV1RK4y1R7Kf?spm_id_from=333.999.0.0&vd_source=38033fe3a1f136728a1d6f8acf710b51)        [导读笔记](https://fengmuzi2003.gitbook.io/csapp3e/)

[小视频复习](https://www.bilibili.com/video/BV1cD4y1D7uR?spm_id_from=333.788.top_right_bar_window_custom_collection.content.click&vd_source=38033fe3a1f136728a1d6f8acf710b51)

----

book：

[学生版重点知识](https://hansimov.gitbook.io/csapp/)

[讲师版重点知识](instructor)

-----

``` shell
lab操作流程
# 1.datalab：
在源文件 bits.c 中完善函数即可
./dlc bits.c 	 // 用于检查程序是否合法，是否使用了程序规定的符号
make btest   	 // btest是评分(检查对错工具)，每次执行btets前都要重新make一下
./btest bits.c   // 评分

# 2.bomblab
./bomb
输入答案
导读P3-52分钟有第一关的实操

# 3.attacklab
./hex2raw < att1.txt > attraw1.txt // 将字节序列at t1转换为字符串attraw1
./ctarget -q -i attraw1.txt     //测试答案
// (https://github.com/wuxueqian14/csapp-lab/tree/master/Attack%20Lab)
```



## 0x 01 二进制

内存中存储的是电压，然后通过（不知道）某种方式**抽象**为数字01，然而计算机的内存太大了，以致于01的个数实在太多了，于是，我们把原有的0和1分块，并再次抽象为0,1…。

![img](file:///C:\Users\24072\AppData\Roaming\Tencent\Users\2407217576\QQ\WinTemp\RichOle\7E[W6J9]YPX$8MS~3CCM[DG.png)



加入内存中有n bit,每m bit分为一块，则最多可以分为2^m块，因为m bit的排列组合数为2 ^ n个序列（sequence）

例如十进制数字123，它应该表示为`1*10^2 + 2*10^1 + 3*10^0`，所以这里的123准确来说应该是一个sequence，而不是一个数。

数是一个比较唯心的抽象的概念，你说一个数3，它可以是十进制序列3，也可以是二进制序列11…，3和11都是这个真正的（唯心的）3，这些序列之间是一一对应的，不仅如此，他们的运算也是一一对应的。十进制的序列1+2，对应的二进制下序列为1+01

**取反对称**：对称轴的两侧是相反数

对于1,2,3,4，他们分别取反对称于-1，-2，-3，-4

对于二进制000,001,010,011，他们分别取反对称于111,110,101,100

![IMAGE](https://s1.328888.xyz/2022/08/29/CrzXC.png)



## 0x 02 二进制运算

**位运算的循环圈：**

![IMG](https://s1.328888.xyz/2022/09/06/5OAqi.png)

​										（int类型有符号数）

![img](https://s1.328888.xyz/2022/09/06/5jEa0.png)

​										(int类型无符号数)

通过这张图，你可能会更好地理解补码和无符号数运算是在mod 2^n 下计算的意义。



看一下树状数组lowbit函数

``` c
int lowbit(int x) {
    return x & -x; // <==> x & (~x + 1);
}
```

这个函数为什么能求得最后一个1所在位置的代表的权值呢？

首先 -x，其实就是x的补码。关于补码，我们有一个求补码的方法：从右到左直到第一个1保持不变，后面的位取反，我们将x和x的补码做与运算，最后得到的结果一定是这样的形式：00..010..0，最后一个1左侧全为0，右侧也全为0。

``` c
#include <iostream>
using namespace std;

unsigned func1(unsigned x) {
    // 输出一个无符号数x，判断x在十六进制下的的每一位是不是字母
    // 如果该位是字母就返回1，否则返回0
    // 并以一个16进制数的形式返回
    unsigned x1 = (x & 0x22222222) >> 1;
    unsigned x2 = (x & 0x44444444) >> 2;
    unsigned x3 = (x & 0x88888888) >> 3;
    // printf("[1]:%04x\n[2]:%04x\n[3]:%04x\n", x1, x2, x3);
    return x3 & (x2 | x1);
}

unsigned func2(unsigned x) {
    // 输出一个无符号数x，判断x在十六进制下的每一位是不是字母
    // 如果所有位都是字母返回1，否则返回0
    x = func1(x); //得到了每一位的结果
    x = x & (x >> 16); // 每次判断一半
    x = x & (x >>  8);
    x = x & (x >>  4);
    return x;
}

unsigned func3(unsigned x) {
    // bigCount
    unsigned c;
    c = (x & 0x55555555) + ((x >>  1) & 0x55555555);
    c = (c & 0x33333333) + ((c >>  2) & 0x33333333);
    c = (c & 0x0f0f0f0f) + ((c >>  4) & 0x0f0f0f0f);
    c = (c & 0x00ff00ff) + ((c >>  8) & 0x00ff00ff);
    c = (c & 0x0000ffff) + ((c >> 16) & 0x0000ffff);
    return c;
}

int main()
{
    unsigned x = 0x1;
    // printf("0x%X = %X\n", x, func1(x));
    // printf("0x%X = %X\n", x, func2(x));
    printf("0x%X = %d\n", x, func3(x));
    
    return 0;
}
```



## 0x 03 浮点数

为什么 IEEE 754浮点数Float类型的bias=127而不是128？

其实这也没有一个官方的说法，不过为了让自己接受这个设定，我们可以从两个角度考虑：

1. 首先，bias采用127时绝对值的范围比较对称
2. 其次，bias采用127时最大的指数是127比bias=128时的126大，虽然只大1，但是我们直到指数的增长是“爆炸”的，因此其表示的范围也大得多。



浮点的根据exp和frac分为三种情况：

1. exp=111..1，指数全1。此时又分为两种情况：（1）当frac全0时表示无穷大，根据符号位又分为正无穷和负无穷。（2）frac不全为0，表示NaN，一种未定义行为。（可以这样区分无穷和NaN，由于未定义的行为有很多，因此需要根据frac进一步区分，所以frac不是固定的全0，（胡乱猜的），可以这样记忆）。
2. exp=000..0，指数全0。表示不规格化的浮点数。这里的主要目的是为了拓展精度和范围（往值小的方向）。
3. else，规格化浮点数。



将一个无符号数转换为一个浮点数的表示形式并保存在一个无符号数字中

[IEEE 754浮点数十六进制相互转换](https://lostphp.com/hexconvert/)

[关于浮点数舍入的讨论](https://www.zhihu.com/question/68131179)

``` c
#include <iostream>
#include <cstring>
#include <stdint.h>
#include <algorithm>

using namespace std;

uint32_t uint2float(uint32_t u){ // 将一个服务号数u转换成浮点数存储的形式
    
    // 特判
    if (u == 0x00000000)
    {
        return 0x00000000;
    }
    
    // 找到最后一个1的后面的一个位置，求得该1后面还有多少个数
    int n = 31;
    while (n >= 0 && (((u >> n) & 0x1) == 0x0))
    {
        n = n - 1;
    }
    cout << "n: " << n << endl;
    
    uint32_t e, f; // exp, frac
    // <= 0000 0000 1.111 1111 1111 1111 1111 1111 : 32位
    // u的位数<=24，此时再隐藏一个1，就<=23位，于是frac就可以保存所有位，不需要舍入
    if (u <= 0x00ffffff)
    {
        // no need rounding
        uint32_t mask = 0xffffffff >> (32 - n); // mask就是frac的掩码
        f = (u & mask) << (23 - n);             // f = u & mask得到frac，但还需要左移移动到最右侧[frac00..0]，而不是[00..0frac]
        e = n + 127;
        printf("e: 0x%x, f: 0x%x\n", e, f);
        return (e << 23) | f;
    }
    // >= 0000 0001 0000 0000 0000 0000 0000 0000 
    // 总位数>=25，一位可以隐藏，还剩下至少24位，frac无法全部保存，需要舍入(rounding)
    else
    {
        // expand to 64 bit for situations like 0xffffffff
        uint64_t a = 0;
        a += u;
        // compute g, r, s
        uint32_t g = (a >> (n - 23)) & 0x1;
        uint32_t r = (a >> (n - 23 - 1)) & 0x1;
        uint32_t s = 0x0;
        for (int j = 0; j < n - 23 - 1; ++ j)
        {
            s = s | ((u >> j) & 0x1);
        }
        // compute carry
        a = a >> (n - 23);
        // 0    1    ?    ... ?
        // [24] [23] [22] ... [0]
        if (r & (g | s) == 0x1)
        {
            a = a + 1;
        }
        // check carry
        if ((a >> 23) == 0x1) /
        {
            // 0    1    ?    ... ?
            // [24] [23] [22] ... [0]
            f = a & 0x007fffff; // 0x0000 0000 0111 1111 1111 1111 1111 1111只保留frac
            e = n + 127;
            return (e << 23) | f;
        }
        else if ((a >> 23) == 0x2) 
        {
            // 1    0    0    ... 0
            // [24] [23] [22] ... [0]
            e = n + 1 + 127;
            return (e << 23);
        }
    }
    // INF as default error
    return 0x7f800000; // 0 1111 1111 000 0000 0000 0000 0000 0000
}

int main()
{
    int x;  cin >> x;
    printf("%x", uint2float(0x10000000));
    
    return 0;
}
```



## 0x 04 时序电路和组合电路



[原文链接：](https://blog.csdn.net/sinat_20265495/article/details/41314577)

----

**数字电路**根据逻辑功能的不同特点，可以分成两大类，一类叫**组合逻辑电路**（简称组合电路），另一类叫做**时序逻辑电路**（简称时序电路）。组合逻辑电路在逻辑功能上的特点是任意时刻的输出仅仅取决于该时刻的输入，与电路原来的状态无关。而时序逻辑电路在逻辑功能上的特点是任意时刻的输出不仅取决于当时的输入信号，而且还取决于电路原来的状态，或者说，还与以前的输入有关。




时序电路，是由最基本的逻辑门电路加上反馈逻辑回路（输出到输入）或器件组合而成的电路，与组合电路最本质的区别在于时序电路具有记忆功能。

时序电路的特点是：输出不仅取决于当时的输入值，而且还与电路过去的状态有关。它类似于含储能元件的电感或电容的电路，如触发器、锁存器、计数器、移位寄存器、存储器等电路都是时序电路的典型器件，时序逻辑电路的状态是由存储电路来记忆和表示的。

时序电路和组合电路的区别：
时序电路具有**记忆功能**。时序电路的特点是：输出不仅取决于当时的输入值，而且还与电路过去的状态有关。组合逻辑电路在逻辑功能上的特点是任意时刻的输出仅仅取决于该时刻的输入，与电路原来的状态无关

时序电路是 时序 逻辑 电路。时序，时间 顺序，是在时钟的推动下工作的，cpu就是一个复杂的时序电路。

组合逻辑电路和时序逻辑电路的最根本区别在于：组合逻辑电路的输出在任一时刻只取决于当时的输入信号；而时序逻辑电路的输出，不仅和当前的输入有关，还和上时刻的输出有关，它具有记忆元件（触发器），可以记录前一时刻的输出状态，它可以没有输入，仅在时钟的驱动下，给出输出。

时序电路的基本结构：

![img](https://img-blog.csdn.net/20151210201333026?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


结构特征：*电路由组合电路和存储电路组成*，电路存在反馈 





## 0x 05 缓冲区漏洞实验

```c
#include <stdio.h> //bomb.c

void echo()
{
	char buffer[4];
	gets(buffer); //缓冲区溢出的关键
	puts(buffer);
}

int main()
{
	puts("pls input: ");
	echo();
	return 0;
}
```

```shell
操作步骤：
1. gcc bomb.c -o main -fno-stack-protector -g
# -fno-stack-protector取消栈保护？
# -g调试模式，因为后面还需要调试

2. gdb main
2.1 在echo函数的gets函数加上一个断点：b 6
# echo函数位于main.c的第六行
2.2 r
# run运行程序，此时会在断点gets函数停下
2.3 info f 
# 显示栈信息，如下方图-栈信息所示
# 在这些信息中，我们需要注意三个地址：
# (1)frame at 0x7ff.f3d0
# (2)rbp at   0x7ff.f3c0
# (3)bip at.  0x7ff.f3c8
# 其中frame at的地址是函数echo占用栈的地址
# 此时，返回地址rip和旧的栈顶指针rbp已经入栈
# 由此可见，程序还没运行，返回地址和旧的栈顶指针就会入栈
2.4 p/a &buffer[0]
# 打印数组buffer的首地址
# 通过结构图，我们可以发现，数组与返回地址rip之间差了12（c8-bc）字节，如果我们gets的数组大于等于12字节，那么返回地址的数据就会被破坏，


```

![image-20220907100422585](/Users/epoch/Library/Application Support/typora-user-images/image-20220907100422585.png)

(图-栈信息)

![13C288AA-6A07-463D-A689-CC7FEF2DCB91](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/2407217576/QQ/Temp.db/13C288AA-6A07-463D-A689-CC7FEF2DCB91.png)

(图-数组地址)

![img](https://1484576603-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-MV9vJFv4kmvRLgEog6g%2F-MZuBW3GNKr5zld5JHFH%2F-MZuER6OHxdJ7z3gtPNL%2Fimage.png?alt=media&token=ecba360a-06d3-4734-9430-aab5fd84cbee)

(图-视频测试运行gets前的栈)

![img](https://1484576603-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-MV9vJFv4kmvRLgEog6g%2F-MZrajFxkCIYEw7i-1-F%2F-MZrrKB09-F7NqAWNBKB%2Finitpintu_%E5%89%AF%E6%9C%AC.jpg?alt=media&token=35b89f3f-e6d2-4aa9-b7d0-2da8ad86450b)

（图-视频测试运行gets后的栈）



## 0x 06 Computer English

----

common：注释

override：覆盖

entry：入口，条目，输入

Place holder：站位

ascending：升序

descending：降序

comma：逗号

brackets：括号

determine: 确定，决定，判定，下决心

deterministic: 确定行

finite: 有限的

infinite: 无限的

automaton: 自动机

positive: 正数

negative: 负数

decimal: 十进制

hexadecimal：十六进制

octal: 八进制

optimazation：优化

pruning：剪枝

decode:译码

instance: 例子，实例

cpu和memory 就组成了一个状态机

operand 操作数

opreator：操作符

memory：内存/存储器

recursion：递归

reduce：归约

iterate: 迭代

transistor：晶体管

complement：补充，补运算(～)，辅

parse: 解析

simulator: 模拟器

simulate: 模拟，仿真，假装

converter：转换器

verbose: 冗长的，啰嗦

handler: 管理者，处理程序

illustrate: 说明

universal: 通用的

pecuilar:  特有，奇特，一场

----



## 0x 07 [makefile](https://subingwen.cn/linux/makefile/#3-1-%E8%87%AA%E5%AE%9A%E4%B9%89%E5%8F%98%E9%87%8F)

### .1 规则

（1）make命令具有**自动推导**的功能，例如依赖中的.o文件，即使不存在，make会使用内部默认的构造规则生成这些.o文件。

（2）make后面**不带参数**默认执行第一条命令

（3）mak的**时间戳规则**：

> make 命令执行的时候会根据文件的时间戳判定是否执行 makefile 文件中相关规则中的命令。
>
> 1. 目标是通过依赖生成的，因此正常情况下：目标时间戳 > 所有依赖的时间戳 , 如果执行 make 命令的时候检测到规则中的目标和依赖满足这个条件，那么规则中的命令就不会被执行。
> 2. 当依赖文件被更新了，文件时间戳也会随之被更新，这时候 目标时间戳 < 某些依赖的时间戳 , 在这种情况下目标文件会通过规则中的命令被重新生成。
> 3. 如果规则中的目标对应的文件根本就不存在， 那么规则中的命令肯定会被执行。

（4）对于不生成目标文件的目标称为伪目标，为了避免微伪目标的名字和真实的文件名重复，我们可以在伪目标的前面加上关键字：.PHONY(假) 例如：

``` makefile
.PHONY: clean
clean: 
	rm *.o
```

声明位伪目标主要是避免这种情况：

> 如果目标不存在规则的命令肯定被执行， 如果目标文件存在了就需要比较规则中目标文件和依赖文件的时间戳，满足条件才执行规则的命令，否则不执行。
>
> 加入目标是clean，而恰好有一个真实的clean文件，只要clean文件不更新，那么clean目标就无法执行。



（提醒）**目录连接到博客中的实例6可以好好看看👀**

### .2 变量

make中的变量分为三种：

**1.自定义变量**：即用户自己定义的变量，makefile中的变量是**没有类型**的，直接创建变量然后给其赋值就可以了。通过$(obj) 可以取出自定义的obj变量。

````
obj = main.c
target = main
depend = main.o

$(target): $(depend)
	gcc $(obj) -o $(target)

# --------------
# 上面的命令等价于下面：

main: main.o
	gcc main.c -o main
````

**2.预定义变量：**在makefile中有一些已经定义好的变量，用户可以直接使用这些变量，不用进行定义，预定义变量的名字一般是大写的。

![96D31374-3040-4B27-8A65-B9DE685E3351](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/2407217576/QQ/Temp.db/96D31374-3040-4B27-8A65-B9DE685E3351.png)



**3.自动变量：**makefile智能鼓的规则语句经常会出现目标文件和依赖文件，**自动变量用来代表这些规则中的目标文件和依赖文件，并且衙门只能在规则的命令总使用。**

![DC05ED8E-B70B-44FB-A799-E6D0C938CF7F](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/2407217576/QQ/Temp.db/DC05ED8E-B70B-44FB-A799-E6D0C938CF7F.png)



### .3 模式匹配

模式匹配常常与自动变量结合使用，用来简化makefile，减少冗余和重复书写。



### .4 函数

1.wildcard：通配符，用来匹配制定目录下的文件

``` makefile
# 使用举例: 分别搜索三个不同目录下的 .c 格式的源文件
src = $(wildcard /home/robin/a/*.c /home/robin/b/*.c *.c)  # *.c == ./*.c
# 返回值: 得到一个大的字符串, 里边有若干个满足条件的文件名, 文件名之间使用空格间隔
/home/robin/a/a.c /home/robin/a/b.c /home/robin/b/c.c /home/robin/b/d.c e.c f.c
```



2.patsubst：pattern subsitude，匹配代替，用来替换文件名的后缀

``` makefile
src = a.cpp b.cpp c.cpp e.cpp
# 把变量 src 中的所有文件名的后缀从 .cpp 替换为 .o
obj = $(patsubst %.cpp, %.o, $(src)) 
# obj 的值为: a.o b.o c.o e.o
```



## 0x 08 gdb

### .0 参考

![9523F5A0-416A-4635-99DB-47685282748F](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/2407217576/QQ/Temp.db/9523F5A0-416A-4635-99DB-47685282748F.png)



[本文档参考来源，功能基础而简单](https://subingwen.cn/linux/gdb/#5-3-3-next)

[设计多线程，多进城等高级功能，较为复杂](https://mp.weixin.qq.com/s/XxPIfrQ3E0GR88UsmQNggg)

[知乎](https://www.yanbinghu.com/2019/04/20/41283.html)

### .1 添加命令行参数

```set args …```	启动gdb后，在程序启动之前设置参数

```show args```	查看设置的命令行参数

### .2 启动程序

在整个gdb调试过程中，启动饮用程序的命令只能使用一次。

```run```		可以缩写为 `r`，如果程序中设置了断点会停在第一个断点的位置，如果没有设置断点，程序就执行完了。

`start` 	启动程序，最终会阻塞在main函数的第一行，等待输入后续其他 gdb 命令。

> start 是要开始运行， run 是真的运行。



### .3 退出 gdb

`quit`  缩写为 `q`

### .4 查看代码

`list`	可以缩写为 `l` ，通过这个命令可以查看项目中任意一个文件中的内容，并且还可以通过文件行号，函数名等方式查看。

``` shell
(gdb) list
(gdb) list 行号
(gdb) list 函数名
```

一个项目通常由多个源文件构成，默认情况下通过 list 查看的是程序入口 main 函数对应的文件。

``` shell
(gdb) list 文件名：行号
(gdb) list 文件名：函数名
```

默认情况下 list 之显示 10 行的内容。如果想显示更多，可以通过 `set listsize` 设置，同时如果想查看当前显示的行数可以通过 `show listsize` 查看。这里的 	`listsize` 可以缩写为 `list`

``` shell
(gdb) set listsize 行号
(gdb) show listsize
```



### .5 断点操作

如果想通过 gdb 掉时某一行或者得到某个变量在运行状态下的实际值，就需要在这一行设置断点，程序指定到断点的位置就会阻塞。我们就可以通过 gdb 的调试命令得到我们想要的信息了。

设置断点：`break` 缩写为 `b`

断点的设置方式由两种：

1. 常规断点：程序只要运行到这个位置就会阻塞
2. 条件断点：只有指定的条件被满足了程序才会在断点处阻塞

``` shell
# 设置普通断点到当前文件
(gdb) b 行号
(gdb) b 函数名 # 停在函数的第一行
# 设置普通断点到某个非当前文件
(gdb) b 文件名：行号
(gdb) b 问价名：函数名 # 停在函数的第一行
# 设置条件断点
# 通常情况下，在循环中条件断点用的比较多
(gdb)  b 行号 if 变量名 == 某个值
```

----

查看断点：`info break` ，其中 `info` 可以缩写为 `i` , `break` 可以缩写为 `b`

>info break 查看断点信息时的一些常用的属性：Num:   断点的编号，删除断点或者设置断点状态的时候都需要使用
>Enb:    当前断点的状态，y 表示断点可用，n 表示断点不可用
>What:  描述断点被设置在了哪个文件的哪一行或者哪个函数上

----

如果确定设置的某个断点不再被使用了，可用将其删除，删除命令是 `delete` 断点编号 , 这个 `delete` 可以简写为 `del` 也可以再简写为 `d`。

删除断点的方式有两种: **删除(一个或者多个)指定断点**或者**删除一个连续的断点区间**，具体操作如下：

``` shell
# delete == del == d
# 需要 info b 查看断点的信息, 第一列就是编号
(gdb) d 断点的编号1 [断点编号2 ...]
# 举例: 
(gdb) d 1          # 删除第1个断点
(gdb) d 2 4 6      # 删除第2,4,6个断点

# 删除一个范围, 断点编号 num1 - numN 是一个连续区间
(gdb) d num1-numN
# 举例, 删除第1到第5个断点
(gdb) d 1-5
```



----

如果某个断点只是临时不需要了，我们可以将其设置为不可用状态，设置命令为 `disable` 断点编号，当需要的时候再将其设置回可用状态，设置命令为 `enable` 断点编号。

```
# 让断点失效之后, gdb调试过程中程序是不会停在这个位置的
# disable == dis
# 设置某一个或者某几个断点无效
(gdb) dis 断点1的编号 [断点2的编号 ...]

# 设置某个区间断点无效
(gdb) dis 断点1编号-断点n编号
```

```
# 查看断点信息
(gdb) i b
Num     Type           Disp Enb Address            What
2       breakpoint     keep y   0x0000000000400cce in main() at test.cpp:14
4       breakpoint     keep y   0x0000000000400cdd in main() at test.cpp:16
5       breakpoint     keep y   0x0000000000400d46 in main() at test.cpp:23
6       breakpoint     keep y   0x0000000000400d4e in main() at test.cpp:25
7       breakpoint     keep y   0x0000000000400d6e in main() at test.cpp:28
8       breakpoint     keep y   0x0000000000400d7d in main() at test.cpp:30

# 设置第2, 第4 个断点无效
(gdb) dis 2 4

# 查看断点信息
(gdb) i b
Num     Type           Disp Enb Address            What
2       breakpoint     keep n   0x0000000000400cce in main() at test.cpp:14
4       breakpoint     keep n   0x0000000000400cdd in main() at test.cpp:16
5       breakpoint     keep y   0x0000000000400d46 in main() at test.cpp:23
6       breakpoint     keep y   0x0000000000400d4e in main() at test.cpp:25
7       breakpoint     keep y   0x0000000000400d6e in main() at test.cpp:28
8       breakpoint     keep y   0x0000000000400d7d in main() at test.cpp:30

# 设置 第5,6,7,8个 断点无效
(gdb) dis 5-8

# 查看断点信息
(gdb) i b
Num     Type           Disp Enb Address            What
2       breakpoint     keep n   0x0000000000400cce in main() at test.cpp:14
4       breakpoint     keep n   0x0000000000400cdd in main() at test.cpp:16
5       breakpoint     keep n   0x0000000000400d46 in main() at test.cpp:23
6       breakpoint     keep n   0x0000000000400d4e in main() at test.cpp:25
7       breakpoint     keep n   0x0000000000400d6e in main() at test.cpp:28
8       breakpoint     keep n   0x0000000000400d7d in main() at test.cpp:30
```

让无效的断点生效：

``` shell
# enable == ena
# 设置某一个或者某几个断点有效
(gdb) ena 断点1的编号 [断点2的编号 ...]

# 设置某个区间断点有效
(gdb) ena 断点1编号-断点n编号
```



### .6 调试命令

如果调试的程序被断点阻塞了又想让程序继续执行，这时候就可以使用 `continue` 命令。程序会继续运行，直到遇到下一个有效的断点。``continue` 可以缩写为 `c`。

在 gdb 调试的时候如果需要打印变量的值， 使用的命令是 `print`, 可缩写为 `p`。如果打印的变量是整数还可以指定输出的整数的格式，格式化输出的整数对应的字符表如下：

![9BDD57D6-6D87-4080-B269-951C45DEC259](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/2407217576/QQ/Temp.db/9BDD57D6-6D87-4080-B269-951C45DEC259.png)

`printf` 的语法格式如下：

```shell
# print == p
(gdb) p 变量名

# 如果变量是一个整形, 默认对应的值是以10进制格式输出, 其他格式请参考上表
(gdb) p/fmt 变量名
```

例如：

``` shell
# 举例
(gdb) p i       # 10进制
$5 = 3
(gdb) p/x i     # 16进制
$6 = 0x3
(gdb) p/o i     # 8进制
$7 = 03
```

如果在调试过程中需要查看某个变量的类型，可以使用命令 `ptype`, 语法格式如下:

``` shell
# 语法格式
(gdb) ptype 变量名
```

举例：

``` shell
# 打印变量类型
(gdb) ptype i
type = int
(gdb) ptype array[i]
type = int
(gdb) ptype array
type = int [12]
```

---

单步调试

`step` 命令可以缩写为 `s`, 命令被执行一次代码被向下执行一行，如果这一行是一个函数调用，那么程序会进入到函数体内部。

如果通过 `s` 单步调试进入到函数内部，**想要跳出这个函数体， 可以执行 `finish` 命令。如果想要跳出函数体必须要保证函数体内不能有有效断点，否则无法跳出。**

`next` 命令和 `step` 命令功能是相似的，只是在使用 `next` 调试程序的时候不会进入到函数体内部，`next` 可以缩写为 `n`

通过 `until` 命令可以直接跳出某个循环体，这样就能提高调试效率了。如果想直接从循环体中跳出，必须要满足以下的条件，否则命令不会生效：





## 0x e5 结构体字节对齐规则

结构体的大小绝大部分情况下不会直接等于各个成员大小的总和，编译器为了优化对结构体成员的访问总会在结构体中插入一些空白字节，有如下结构体：

``` c
struct align_basic
{
	char c;
	int i;
	double d;
};
```

那么此时``sizeof(align_basic)``的值会是``sizeof(char)+sizeof(int)+sizeof(double)``的值么？

![img](https://pic3.zhimg.com/80/v2-dca7f4f607fdbc884079e30c10ceb7ae_1440w.png)

如上图经过测试我们发现其大小为16个字节并不等于1+4+8=13个字节，可知编译器给align_basic结构体插入了另外3个字节，接下来我们将分析编译器对齐字节的规则以及结构体在内存中的结构，首先感谢[结构体在内存中的对齐规则 - 咕唧咕唧shubo.lk的专栏 - 博客频道 - CSDN.NET](https://link.zhihu.com/?target=http%3A//blog.csdn.net/liukun321/article/details/6974282)这篇文章的作者，在此之前我对内存对齐也是一知半解，很多时候也解释不明白。

==规则一：结构体中元素按照定义顺序依次置于内存中，但并不是紧密排列。从结构体首地址开始依次将元素放入内存时，元素会被放置在其自身对齐大小的整数倍地址上。==这里说的地址是元素在结构体中的偏移量，结构体首地址偏移量为0。

在align_basic中元素c是第一个元素，那么它的地址为0，第二个元素i不会被放在地址1处，int的对齐大小为4个字节，此时虽然元素c只占据一个字节，但是由于i的地址必须在4字节的整数倍上，所以地址必须再向后在移动三个字节，故而需要放在地址4上，此时前两个元素已经占据了8个字节的空间，第三个元素d会被直接放在地址8上，因为double的对齐大小为8个字节，而前面两个元素已经占据了8个字节，正好是double对齐大小的整数倍，所以元素d不需要再往后移动。说了这么多也不如让机器给我们验证下有说服力：

`printf("%d %d %d %d\n", sizeof(align_basic), &align_basic::c, &align_basic::i, &align_basic::d);`

![img](https://pic3.zhimg.com/80/v2-d729bea11322919cc59e03e03d3e221a_1440w.png)

![img](https://pic2.zhimg.com/80/v2-25407fa9c8f77c2f3d17a12546793a0d_1440w.png)

那么这样就够了吗，会不会太简单？我们把元素i和d的位置交换下，此时结构体的大小会是20吗，我们仍然先让机器说话，(⊙o⊙)…毕竟后面打脸有证据：

``` c
struct align_basic
{
	char c;
	double d;
	int i;
};
printf("%d\n", sizeof(align_basic));
```

![img](https://pic4.zhimg.com/80/v2-cbfea1a547f205a2d4b2306748da3953_1440w.png)

我们发现此时结构体的大小并不是20而是24，那么多出来的这4个字节如何解释？我们引出第二条规则。

==规则二：如果结构体大小不是所有元素中最大对齐大小的整数倍，则结构体对齐到最大元素对齐大小的整数倍，填充空间放置到结构体末尾。==

运用规则一，此时c仍然是第一个元素，其地址为0，第二个元素地址为8， 第三个元素地址为16，然后运用规则二，c，d，i中d的对齐大小为8最大所以整个结构必须对齐到8的整数倍，前面是三个元素已经占据了20个字节的空间，只需要在结构体的尾部填充4个字节的空间就是8的倍数了，所以此时整个结构体的大小为24个字节。

`printf("%d %d %d %d\n", sizeof(align_basic), &align_basic::c, &align_basic::d, &align_basic::i);`

![img](https://pic3.zhimg.com/80/v2-c66b0aa643b60eecb9dbf06916c56482_1440w.png)

![img](https://pic4.zhimg.com/80/v2-0294208ed70bb94feb9868310a191eb3_1440w.png)

==规则三：基本数据类型的对齐大小为其自身的大小，结构体数据类型的对齐大小为其元素中最大对齐大小元素的对齐大小。== 规则三可以由规则二推导出来。

char类型的对齐大小为1字节，short类型的对齐大小为2字节，int类型的大小为4字节，double的对齐大小为8字节，align_basic结构体中最大对齐大小元素为d是double类型，所以align_basic的对齐大小是8。有人会问如果结构体中有数组呢？很简单将数组看做是连续数个相同类型的元素即可。





## 0x e6 第一章小结

深入理解计算机系统的“系统”，并不是操作系统，这个系统包括了硬件，操作系统，网络，编译等等

学习计算机系统应该具备的三个**抽象能力**：问题抽象，系统抽象（csapp），数据抽象

计算机系统是由硬件和**系统软件**组成的。

数字的机器表示方法是对真值的**有限近似值**。

指令的执行：

1. 从磁盘读取指令和数据到内存
2. 从内存送到cpu中去执行
3. 将返回的数据送到屏幕





## 0x e7 bomb lab

### .1 phase1

```assembly
disas main，可以发现我们输入的字符串赋值给了 $rdi
并且之后调用了函数<phase_1>
disas phase_1
发现没有修改寄存器 $rdi 的值
然后把一个立即数 0x402400 传给了寄存器 $esi
之后调用函数 <strings_not_euqal>
在之后test $eax $eax
如果 je，即 $eax = 0
调用函数 <eoplode_bomb>，炸弹爆炸
否则正常返回

进入函数 <strings_not_equal>
该函数又会调用 <string_length> 函数
这个函数会计算 $rdi 内字符串的长度

p/x $rdx :以x(16进制)方式打印寄存器$rdx的值
x $rdx 检查(examine) $rdx内存中的值
```

[watch = sepcial break](https://www.zhihu.com/question/36809923)



### .2 phase2





### .3 phase3

![78E9B95E-D7EC-4E49-8A30-94EF4B0A4D48](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/2407217576/QQ/Temp.db/78E9B95E-D7EC-4E49-8A30-94EF4B0A4D48.png)



```c
# phase_3
if(eax > 1)
{
    if(7 < rsp + 8)
    {
        eax = rsp + 0x8; // first input
        switch(eax)
        {
            case 0: 
                eax = 0xcf;
                if(rsp + 0xc == eax)    return Accept;
                else    return BOOM!!!;
            case 1:
                eax = 0x137;
                if(rsp + 0xc == eax)    return Accept;
                else    return BOOM!!!;
            case 2:
                eax = 0x2c3;
                if(rsp + 0xc == eax)    return Accept;
                else    return BOOM!!!;
            case 3:
                eax = 0x100;
                if(rsp + 0xc == eax)    return Accept;
                else    return BOOM!!!;
            case 4:
                eax = 0x185;
                if(rsp + 0xc == eax)    return Accept;
                else    return BOOM!!!;
            case 5:
                eax = 0xce;
                if(rsp + 0xc == eax)    return Accept;
                else    return BOOM!!!;
            case 6:
                eax = 0x2aa;
                if(rsp + 0xc == eax)    return Accept;
                else    return BOOM!!!;
        }
        	case 7:
        		eax = 0x147;
                if(rsp + 0xc == eax)    return Accept;
                else    return BOOM!!!;       		
    }
    else
    {
        return BOOM!!!
    }
}
else
{
    return BOOM!!!;
}
```

有多组答案：注意第二个参数不能输入十六进制数，只能输入10进制数，因为这里的数据的读如是采用sscanf，把我们的输入作为str,如果我们的第二个参数是个十六进制数，那么一定以0x开头，结果0会被读取到第二个参数，读到x不合法就结束了。

| 第一个参数 | 第二个参数 |
| ---------- | ---------- |
| 0          | 207        |
| 1          | 311        |
| 2          | 707        |
| 3          | 256        |
| 4          | 389        |
| 5          | 206        |
| 6          | 682        |
| 7          | 327        |



### .4 phase4



第一个参数

```c
.func4:
eax = edx
eax -= edx
ecx = eax
ecx >>= 0x1f // unsigned
eax += ecx
eax >>= 1
ecx = &(rax+rso+1)
if(ecx <= edi)  
{
    eax = 0;
    if(exc >= edi)  return 0; // 只有当 ecx<=edi<=ecx，即edi=ecx=7时可以正常退出并返回0
    else
    {
        。。。
    }
}
else
{
    edx = &(rcx - 1)
    call func4
}
// goal: make eax = 0
```



第二个参数看phrase4的汇编很容易得出为0



### .5 phase5

[reference](https://github.com/wuxueqian14/csapp-lab/tree/master/Bomb%20Lab)



### .6 phase6

不想做了



### .7 phase7

no

### .8 answer(2016)



```
Border relations with Canada have never been better.
1 2 4 8 16 32
7 327
7 0
)/.%&'

```

### 



## 0x 09 Assembly实验

![BE9A5FC6EBB55797FF78C5D5105D31DF](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/BE9A5FC6EBB55797FF78C5D5105D31DF.png)

如上图，我们用(gdb) x mingling打印 0x7fffffffe3b0附近的值，这个地址是个虚拟地址，它在内存中的值为0x0



栈指针是会浮动的！但是rsp和rbp的差值应该是不变的。



gdb(ni) ：会跳出函数执行

gdb(si)：会进入函数执行



![A44D5C36-2816-49B3-9E01-23E15BC5DA72](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/2407217576/QQ/Temp.db/A44D5C36-2816-49B3-9E01-23E15BC5DA72.png)

小端存储的又一个例子啊，我们把寄存器 %rbp(0x7fffffffe3d0) 放入 %rsp，观察可以发现，0x00007ffff倍放在了后面的地址，而0xffffe3d0被放在了前面的地址。x命令打印的地址从左到右，从上到下是以4为单位递增的，





## 0x 0a ld_preload环境变量劫持函数

首先在目录下创建两个文件 main.c 和 txt

``` c
#include <stdio.h>
int main() // main.c
{
	FILE *fd = fopen("txt", "r");
	if(fd == NULL)
	{
		printf("*** open file error!\n");
		return 1;
	}
	printf("open file success!\n");
	return 0;
}
```

正常来说最后程序会正确执行

但如果我们更改动态链接库

先创建一个trik动态链接库

``` c
#include <stdio.h> // trik.c
FILE *fopen(const char *path, const char *mode)
{
	printf("*** Always open error!");
	return NULL;
}
```

``` shell
gcc -shared -fPIC trik.c -o trik.so
LD_PRELOAD=$PWD/trik.so ./a.out
```

最后文件会打开失败

![53DE4182-359A-4F3E-80BB-4B97508E7F9B](/Users/epoch/Library/Containers/com.tencent.qq/Data/Library/Application Support/QQ/Users/2407217576/QQ/Temp.db/53DE4182-359A-4F3E-80BB-4B97508E7F9B.png)

原理就是通过自己写的库函数劫持系统的库函数，使得程序执行我们的库函数。





## 0x 0b attack lab





## 0x0c 链接 points

1.引入哑节点dummy

2.引入数据结构–elf

3.静态链接的过程：elf定位到符号->符号解析->重定位

4.\*.o, elf 都是二进制文件

5.unix下大部分工具都在/usr/bin或者/bin目录下的。使用hexdump可以查看二进制文件

6.第一个section的name为空（其实叫做 undefine section），且数据全为0，里面存放的内容是undefine的数据。

7.将函数定义为一个弱符号：`attribute__((weak)) int add*() {}` ,这里的 `add` 函数被定义为一个弱符号，它可以被强符号函数 `add` 覆盖。

8.对于 `C Language` 来说，出现 `Warning` 说明你的语句有**歧义** ，但是 C 语言为你选择了一种结果，注意这种结果可能与你的本意不同！

9.对于初始化为 `0` 的全局变量和静态变量，也被划分到 `.bss`，这是因为全局变量和静态变量默认初始化就是 `0`。

10.为什么在可重定位目标文件中有 `COMMON`，在可执行目标文件中就没有 `COMMON` 了呢。

>  回想一下`COMMON`的定义，对于未初始化的全局变量， 属于`COMMON`。
>
> 对于未初始化的全局变量， 在链接之后它有三种可能的情况（假设这里有两个文件 `s1.c`, `s2.c`，在 `s1.c` 中定义有未初始化的全局变量 `g`）
>
> 1. 如果在 `s2.c` 中也定义了一个全局变量 `g` 并且初始化为 `0`，则 `g` 属于 `.bss` 节
>
> 2. 如果初始化不是 `0`，就属于 `.data`
>
> 3. 如果 `s2.c` 没有定义 `g` ，那么 s2 就属于 `.bss`
>
>    因为有如上三种（合法）情况，所以把它划分到 `COMMON`，而之所以在可执行目标文件中没有了 `COMMON` ,是因为此时已经链接完了，`g` 属于那个节已经很明确了，因此也就不需要了。
>
>    

11.

## 0x0d 修改 ROF 信息的实验

首先编译源文件 `add.c` 生成可重定位目标文件 `add.o`

```c
int addcnt = 0;

int add(int a, int b)
{
	addcnt ++ ;
	return a + b;
}
```



使用 `hexdump -S add.o` 查看 `Section Headers`

``` shell	
Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .text             PROGBITS         0000000000000000  00000040
       000000000000003c  0000000000000000  AX       0     0     4
  [ 2] .rela.text        RELA             0000000000000000  00000228
       0000000000000060  0000000000000018   I       9     1     8
  [ 3] .data             PROGBITS         0000000000000000  0000007c
       0000000000000000  0000000000000000  WA       0     0     1
  [ 4] .bss              NOBITS           0000000000000000  0000007c
       0000000000000004  0000000000000000  WA       0     0     4
  [ 5] .comment          PROGBITS         0000000000000000  0000007c
       0000000000000027  0000000000000001  MS       0     0     1
  [ 6] .note.GNU-stack   PROGBITS         0000000000000000  000000a3
       0000000000000000  0000000000000000           0     0     1
  [ 7] .eh_frame         PROGBITS         0000000000000000  000000a8
       0000000000000030  0000000000000000   A       0     0     8
  [ 8] .rela.eh_frame    RELA             0000000000000000  00000288
       0000000000000018  0000000000000018   I       9     7     8
  [ 9] .symtab           SYMTAB           0000000000000000  000000d8
       0000000000000138  0000000000000018          10    11     8
  [10] .strtab           STRTAB           0000000000000000  00000210
       0000000000000018  0000000000000000           0     0     1
  [11] .shstrtab         STRTAB           0000000000000000  000002a0
       0000000000000059  0000000000000000           0     0     1
```

可以发现下标为 `1` 的节是 `.text` 节

我们现在要修改 `add.o` 使其显示为 `.ext` 节

首先需要下载 `hexedit`

然后拷贝一份 `add.o` 的副本 `badadd.o`

（不在源文件上直接修改是个好习惯）

然后执行命令`hexdump -c badadd.o` 找到 `.text` 的位置。

通过 `elf header` 中的信息可以得到 `Section header table` 的 `offset` 为 `0x300`，其中每个条目(`entry`) 的 `size` 为 `0x40` ，由此可以得到第二个条目（下标为1）的 `.text` 节的位置为 `0x340`，并通过 `struct elf64_shdr` 得到前 `4` 个字节为 `name`。

`00000340  20 00 00 00 01 00 00 00  06 00 00 00 00 00 00 00  | ...............|`

`name = 0x00000020` ,我们只需要修改其为 `0x00000022`，就可以实现 `name` 往后偏移两个字节

这样 `name` 就从 ``.text` 变成了 `ext`

执行命令：`hexedit badadd.o` 找到位置并修改即可。

按 `F10` 退出

最后结果如下：

`readelf -S badadd.o`

``` shell
[Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] ext               PROGBITS         0000000000000000  00000040
       000000000000003c  0000000000000000  AX       0     0     4
  [ 2] .rela.text        RELA             0000000000000000  00000228
       0000000000000060  0000000000000018   I       9     1     8
  [ 3] .data             PROGBITS         0000000000000000  0000007c
       0000000000000000  0000000000000000  WA       0     0     1
  [ 4] .bss              NOBITS           0000000000000000  0000007c
       0000000000000004  0000000000000000  WA       0     0     4
  [ 5] .comment          PROGBITS         0000000000000000  0000007c
       0000000000000027  0000000000000001  MS       0     0     1
  [ 6] .note.GNU-stack   PROGBITS         0000000000000000  000000a3
       0000000000000000  0000000000000000           0     0     1
  [ 7] .eh_frame         PROGBITS         0000000000000000  000000a8
       0000000000000030  0000000000000000   A       0     0     8
  [ 8] .rela.eh_frame    RELA             0000000000000000  00000288
       0000000000000018  0000000000000018   I       9     7     8
  [ 9] .symtab           SYMTAB           0000000000000000  000000d8
       0000000000000138  0000000000000018          10    11     8
  [10] .strtab           STRTAB           0000000000000000  00000210
       0000000000000018  0000000000000000           0     0     1
  [11] .shstrtab         STRTAB           0000000000000000  000002a0
       0000000000000059  0000000000000000           0     0     1
```







## 0x0e vim tabe

vim中的分页命令，多窗口vim

通过`help tab-page-intro`命令，可以获得关于标签页使用的更多信息。

| `:tabnew`                | 新建标签页                             |
| ------------------------ | :------------------------------------- |
| `:tabs`                  | 显示已打开标签页的列表                 |
| `:tabc`                  | 关闭当前标签页                         |
| `:tabe <filename>`       | 打开新文件(tabedit)                    |
| `:tabp`                  | 移动到上一个标签页                     |
| `:tabn`                  | 移动到下一个标签页(tabnext)            |
| `:gt`                    | 移动到下一个标签页                     |
| `:tabr`                  | 移动到第一个标签页(tabrewind,tabfirst) |
| `:tabl`                  | 移动到最后一个标签页(tablast)          |
| `$vim -p <f1> <f2> <f3>` | vim开启多个标签页                      |





## 0x0f bilbili 链接

链接步骤：

> 1. parse text
> 2. symbol parse
> 3. Relocation
>
> 2 和 3 都依赖于 1 的 text



