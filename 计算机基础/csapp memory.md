# 一、cache

## 0x01 一种初始化方式

```c
#include <stdio.h>

typedef struct Node
{
    int l, r;
    char s[100];
} node_t;

int main()
{
    node_t p = {
        .l = 100,
        .r = 200,
        .s = "hello,world!",
    };
    
    printf("node: %d %d %s\n", p.l, p.r, p.s);
    
    return 0;
}
```



## 0x02 note

我们知道内存是分页的，cache的 line 只会存在于某一个页，它不会跨页存在。



## 0x03 true/fake sharing

罪魁祸首：MESI 协议

false sharing有一个问题，就是对于sum求和这个例子，虽然我们设置sum1和sum2分别求和，但是sum1和sum2都是分配在栈上的，并且地址十分接近，所以它们可能在同一个cache当中，这样不管是sum1修改还是sum2修改，都会触法 MESI 的同步协议，这样 false sharing的速度和true sharing相差几乎无几。

## 0x04 MESI protocol

exclusive：独有的

exclusive 和 shared 不能共存

四种状态：（由于读数据不会产生数据一致性问题，因此这里只考虑写数据操作）

M: (exclusive) modify, like dirty. 物理地址被缓存到某一个 cache，并且数据已经被修改

E:  exclusive (clean).物理地址被缓存到某一个 cache，并且数据没有被修改

S:  (exclusive) shared clean.物理地址被缓存到 cache，并且多个 cache 共享。

> 如果修改一个状态为 s 的 cache，它会发送一个广播，将所有其他状态为 s 的 cache 的状态修改为 invalid（具体方法是将其拥有数据写入到 dram，然后修改状态为 invalid），然后将自己的状态修改为 M，这样就可以保证全局状态下只有一个 M，也就是 exclusive的。

I: invalid.物理地址并没有缓存到 cache。

> 此时如果发生 cache write
>
> 1. 如果其他 cache 的状态都是 invalid，从内存 load 数据，修改器状态为 M。
> 2. 如果存在 (shared)S状态 的 cache，将它们的数据写入到 dram，然后修改状态为 invalid。



每个处理器的cache line都是 dram 的 cache line 的拷贝





# 二、page table



## 0x01 tips

地址翻译由硬件实现，操作系统为应用提供这个功能。

TLB 也是一个 cache。

现在 64 位的处理器(cpu)的虚拟地址一般其实只有 48 位，剩下的 16 位属于内核。

虚拟地址空间呈现局部密集，整体稀疏的特征。

多级页表在最坏的情况下（满映射，每页都必须有有效数据）是一棵完全二叉树，此时页表条目会比朴素页表多出来一倍。但这种情况几乎不可能出现（虚拟地址空间的稀疏性和程序的局部性）。

页表分配在操作系统的内核态。

在windows下，资源管理器的内存中可以看到：分页缓冲池和非分页缓冲池。分页缓冲池指的是可以和磁盘进行换入(page in)和换出(page out)的页，而非分页并不是指不分页，而是不能喝磁盘进行 swap。

## 0x02 how to reflect va2pa

在我们编写的地址转换函数中，我们简单的通过去模数将物理地址转换为虚拟地址，然而，这是极为不合理的，例如：

1. 产生不合法的地址（地址越界）。例如：0x200(1024)%0x200=0x000，它产生了一个地址为 0 的地址，这显然是错误的。
2. 不同进程间地址冲突的问题。因为每个进程的地址都是从 0x00400000 开始的，而相同地址取模之后的值是相同的，这就会导致地址冲突。

一种可行的方法是使用 hashmap 完成物理地址到虚拟地址地址映射。它解决了使用取模方法产生的冲突和越界问题，但是，它又会产生以下两个问题：

1. 内存浪费严重。在 hashmap 中，我们需要额外的两份空间来分别存储物理地址和虚拟地址以记录他们的映射关系，并且，由于 hashmap 并不是全部使用的，它的内部会有空闲，因此我们还需要乘上一个空闲率 k(k>=1)，因此 hashmap 就需要额外的 2k 倍的额外内存空间要保存映射信息。
2. 破坏程序的局部性。由于 hashmap 的映射是离散的，这就会导致程序会被离散化，破坏程序的局部性。

但是，hashmap 产生的这两个问题属于 性能 问题，它只是导致程序运行效率不好，并不会导致程序运行错误。而取模方法则会导致程序运行出错。

现在我们再来想，hashmap 中记录如此之多的映射信息是否有必要？

肯定是有必要的，不然我们就无法找到物理地址了。但是！如果我们通过虚拟地址映射到物理地址不是离散的，例如：

> 虚拟地址 0x1,0x2，通过 hashmap 地址映射为物理地址：0xa, 0xabcd。如果我们想找到这两个物理地址，我们必须保存映射信息，因为 0xa, 0xabcd 之间毫无关联。但是这种离散性是毫无必要的，如果我们将地址映射为 0xa, 0xb 这种连续的地址的话，它不仅可以避免破坏程序的局部性，还能减少地址映射需要保存的信息。
>
> 比如虚拟地址 [0x0, 0xffff] 这一块区域，如果我们采用 hashmap，它需要 0xffff 份映射信息，这也太多了。但是，如果 hashmap 映射的地址是连续的，我们就可以通过三元组(va0, pa0, offset) （offset表示偏移量）来找到这个区域内任意一个地址的映射，并且仅仅只需要一份映射信息，对于任意 va，pa=pa0+va-va0（va >= va0 && va <= va0 + offset）。
>
> 现在， 完成地址映射需要的额外信息由 2k 变成了 3M，M 就是上述三元组的数量，这个 M 远小于地址的数量。

这就是分段思想。

当然，分段也是有问题的，例如：

1. 碎片。内部碎片和外部碎片。
2. 每次计算都需要比较 va 是否越界。(va >= va0 && va <= va0 + offset)
3. 不方便拓展。当我们的段太大或者或许频繁拓展的时候，寻找一个合适的空间比较麻烦。

所以说，我们需要把 offset 变成一个较小且固定的数值，这就是分页思想。



## 0x03 address transfer

[ARM64架构下地址翻译相关的宏定义](http://www.wowotech.net/memory_management/arm64-memory-addressing.html)

[else](https://www.cnblogs.com/muahao/p/10297852.html)

[else](https://cloud.tencent.com/developer/article/1374439)



## 0x04 page falult

MM: main memory，主存

page table is the **cache** from disk to main memory

交换空间：当我们页表缓存的页满了之后，我们想再往内存映射一页，此时需要将该页 page out，但是如果该页的数据被修改了 dirty，我们该怎么办？

1. 不管它，这肯定不行
2. 将该页写回文件 program file，这也肯定不行，我们不应该修改源文件。
3. 放到别的地方 – swap space。

将一页从 mm 放到 swap space 的过程就叫做 swap out

相反的，将页从 swap space 再放到 mm 的过程叫做 swap in

所以说，一个文件占用的空间包括了 mm 和 swap

swap space 也在磁盘

**demand paging**:  waiting until the miss to copy the page to DRAM is konwn as deman paging

程序的代码文件，例如 .data 段它是存储在磁盘当中的，所以它与内存之间可以存在映射关系，但是 .data 段，stack, heap 不是存储在磁盘当中的，当我们需要把这些短存放在磁盘当中时，我们需要放入 swap space 中。它们又称为**“匿名页”**(在磁盘中没有文件与它对应)。	



# 三、virtual memory overview

​		virtual memory 主要是为了解决物理内存和进程所看到的虚拟内存不匹配的问题，所以说每个 virtual memory 肯定是提供给每一个**进程**的。

每个进程就是一段 active 的内存，例如：

1. .text 是死的
2. .data 是活的，因为它需要写入操作等

如果区分 user 的虚拟地址空间和 kernel 的虚拟地址空间：kernel 的64位虚拟地址的最高位是1，user 的64位虚拟地址的最高位是 0。

我们通常看到的程序的虚拟地址空间图中， user 的虚拟地址空间地址的高部分都被 stack 占用了，但是这通常是作者的简化，实际上地址的最高部分被 kernel 部分占用了，只不过一半不标识出来。

只有第一级页表可以区分user mode or kernel mode，因为只有第一级页表可以得到地址的最高位。

用户的虚拟地址空间中的 user 部分映射到程序的虚拟地址空间的user 部分，映射方法为：0x0 + addr，kernel mode 部分的映射方法为：0xffff + addr，user的虚拟地址空间的地址最高为2^48。0xffff正好是16位。

pgb 在 kernel 中只有唯一一份。

kernel 的虚拟地址从 2^47？

内核的地址翻译全局一致。




# 四、TLB



hardware acceleration：硬件加速

TLB is the cache of va2pa

我们可以把 cache 看作一个 key-value 库
