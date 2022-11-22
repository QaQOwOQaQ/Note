# prelogue



## 0x01 导读 

候捷 STL 与 范型编程学习笔记。

## 0x02 英语

identity：同一，本身

## 0x03 problem

[1](https://blog.csdn.net/nirendao/article/details/115815599?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-115815599-blog-110736211.pc_relevant_3mothn_strategy_and_data_recovery&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

[2](https://blog.csdn.net/luolaihua2018/article/details/110736211)

[3](https://blog.csdn.net/u013317445/article/details/89680330)

[4](https://www.jianshu.com/p/6b526aa481b1)

# 一、container

大家风范：用前置++，–实现后置++，–

仿函数：函数对象

关联：有key和value

array就是把数组包装成一个class

deque：dai ke

set, map:红黑树.c++并未规定必须使用红黑树，只不过红黑树效率高，因此各大制定标准库的公司都使用红黑树。

set的key就是value，value就是key。



哈希表由一个个桶（bucket）组成，每个桶都是一个链表（link）。现在哈希表大多采用分离链表发（yxc：拉链法）实现。



头文件有保护机制，多次引入没问题



vector空间两倍增长(成长)

（2）vector 有一个机制是这样的，如果新加入一个元素，比如通过push_back()，但是size 大于了capacity，那么vector 就会重新找一块更大的地方再把数据放进去。重新分配的过程：**申请一块新的内存 > 拷贝数据 > 释放原内存**。

 2、vector扩容怎么拷贝？

经常问的一个问题，vector怎么扩容？最简单的回答就是先申请内存，再拷贝，最后销毁原来的内存，然后是1.5倍还是2倍，解释一下。

但是！我看到有人问，怎么拷贝？？？what？？？

没想到吧，看书还是看漏了。

拷贝用的是uninitialized_copy，如果拷贝的是POD（标量型别，也就是trivial）调用的是copy（自己去看STL 的copy实现），如果是non-POD使用for循环遍历，调用construct，一个一个的构造，针对char*和wchar_t*，uninitialized_copy直接用memmove来执行复制行为，更加快。

版权声明：本文为CSDN博主「m0_60126088」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/m0_60126088/article/details/119616254

虽然标准库提供了sort函数，但是一些函数也提供了自己的sort，当容器存在自己的sort的时候，优先使用容器自己的，它自己的肯定更好，不然它提供这个函数干嘛。

forward_list 和 list 提供了自己的 sort

forward_list使用头插法。因此只能在链表头插入元素。



[deque容器底层实现](http://c.biancheng.net/view/6908.html)



queue 和 stack 由于元素进出的顺序是死的（固定的），因此他们没有迭代器，如果有迭代器的话，我们就有可能通过迭代器改变元素进出的顺序，这是不合理的。

set，map底层是红黑树（自平衡二叉查找树）



multimap不可以用下标做 iterator

hashtable 的 bucket 比 元素个数还有要多是合理的，因为元素中间会空出很多 bucket。事实上，bucket的个数肯定比元素个数多，否则的话 bucket 就要重新扩充变为原来的（大约）两倍。（经验法则，避免 bucket 的长度太长）（以空间换时间）

map的底部就是一个 pair

map，set 会丢掉重复元素

从测试结果我们可以看出，使用insert()插入元素的方式并不能覆盖掉相同key的值；而使用[]方式则可以覆盖掉之前的值。为什么会出现这样的结果呢？

原因分析
我们可以通过源码来找原因，在map的源码中，insert方法是这样定义的：

``` C++
pair<iterator,bool> insert(const value_type& __x) 
    { return _M_t.insert_unique(__x); }
```

他调用_M_t.insert_unique(_x)方法，该方法会首先遍历整个集合，判断是否存在相同的key，如果存在则直接返回，放弃插入操作。如果不存在才进行插入。
而[]方式是通过重载[]操作符来实现的，它直接进行插入或覆盖



>  今天调试程序，弄得有点纠结。无意间，和一帮同事讨论起一个问题：进程结束操作系统会回收new的内存吗？在自己的印象中，一直固执地认为，在使用C++操作分配对象内存后，如果程序员自己不用相应的delete操作回收的话，这块从堆内存是一直存在。在讨论中，有同事提醒说，在进程结束后，new操作的内存会被回收。但也只是结论，也说不出具体理由。
>
>       没关系，何不google一下，一查下去，答案已是清晰：
>     
>       “内存泄漏不是系统无法回收那片内存，而是你自己的应用程序无法使用那片内存。当你程序结束时，你所有分配的内存自动都被系统回收，不存在泄漏问题。但是在你程序的生命期内，如果你分配的内存都不回收，你将很快没内存使用。”再用自己的一句话来概括的话就是：操作系统本身就有内存管理的职责，一般而言，用malloc、new操作分配的内存，在进程结束后，操作系统是会自己的回收的。但某些系统态的资源，用特殊的系统API申请的内存就不一定了，比如：linux中的shmget申请的共享内存，就与进程结束无关了。
>     
>        经过这一番查找，不禁自己为自己汗颜了一把，之前还以为自己对内存管理理解较深，现在才明白：在没有认真深入地钻研过操作系统内核原理之前，所有对内存管理的理解还是浮在表面上的。这次讨论也分外地提醒自己，自己知识的盲区还有许多，还要不断的踏踏实实地努力学习啊！
> ------------------------------------------------
> 版权声明：本文为CSDN博主「stanjiang2010」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/stanjiang2010/article/details/5386647



不建议使用分配器。

oop企图将data和method关联在一起

gp企图将data和method分离开



[容器内存分配](https://blog.csdn.net/y1196645376/article/details/52938474)

```C++
#include <iostream>
#include <cstring>
#include <algorithm>
#include <set>
#include <map>
#include <queue>
#include <vector>

using namespace std;

void test1();
void test2();
void test3();
void test4();

class test 
{
    public:
        typedef pair<int,int> PII;
        PII p;
};

template<typename T>
class complex
{
    public:
        complex(T _val) : val(_val) {}
        T val;
        template<typename U>
        friend complex<U> cal(complex<U> a, complex<U> b);
};

template<typename T>
complex<T> cal(complex<T> a, complex <T> b)
{
    complex<T> c(a.val + b.val);
    return c;
    // return complex(a.val + b.val);
}


int main()
{
    test3();
    return 0;
}

/*====================================================*/

void test3()
{
    test::PII p = {1,2};
    cout << p.first << ' ' << p.second << endl;
    complex<int> a(1);
    complex<int> b(2);
    cout << a.val << ' ' << b.val << endl;
    complex<int> c = cal(a, b);
    cout << c.val << endl;
}

void test1()
{
    vector<int> vec;
    cout << vec.capacity() << endl;
    cout << &vec << endl;
    vec.push_back(1);   // add -> cap:1
    cout << vec.capacity() << endl;
    cout << &vec << endl;
    vec.push_back(1);   // add -> cap:2
    cout << vec.capacity() << endl;
    cout << &(vec[1]) << endl;
    vec.push_back(1);   // add -> cap:4
    cout << vec.capacity() << endl;
    cout << &(vec[1]) << endl;
    vec.push_back(1);
    vec.push_back(1);   // add -> cap:8
    cout << vec.capacity() << endl;
    cout << &(vec[1]) << endl;
    for(int i = 0; i < 1000000; i ++ )   vec.push_back(i);
    cout << &vec << endl;
}

void test2()
{
    map<int,int> m;
    m.insert({1,1});
    cout << m[1] << endl;
    m.insert({1,2});
    cout << m[1] << endl;
    cout << m.size() << endl;
    m[2] = 3;
    cout << m.size() << endl;
    m[1] = 3;
    cout << m[1] << endl;
}
```



# 二、alloactor 



容器的幕后英雄。

所有的分配和释放动作最后都会跑到 malloc 和 free 去。

分配器的 allocate 和 deallocate 最终都是使用 malloc 和 free

直接使用分配器在释放内存的时候，还需要指出需要释放的内存大小。

因此并不建议直接使用分配器分配内存。

``` C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

void* operator new(size_t size, string s)
{
    cout << "s: " << s << endl;
    return malloc(size);
}

int main()
{
    int *x = new("hello") int;
    auto a = allocator<int>();   
    int *p = a.allocate(512, 0);
    a.deallocate(p, 512);
    return 0;
}
```





# 三、list





不允许 x++ ++

允许 ++ ++ x

所有的容器区间都是前闭后开

在链表中为了保证这个要求，必须要设置一个虚节点（为空）





# 四、vector

内存的扩充一般不会是原地扩充，它会在另一个地方找内存。

``` c++
vector<int> vec;
cout << sizeof(vec) << endl; // 24 = 3 * 8(point)
```

public 继承表示一种 is-a 的关系



# 五、array

array 内部的数据就是一个真的数组，它没有tors

# 六、deque

号称自己拥有连续空间^^

连续是假象，分段是事实。

deque（dei ke）其实是分段连续的。deque的数据由许多个段组成，段与段之间不连续，每个段内连续。

``` C++
#include <bits/stdc++.h>

using namespace std;

int main()
{
    deque<int> q;
    for(int i = 0; i < 10000; i ++ )    q.push_back(i);
    for(int i = 0; i < 10000 - 1; i ++ )
    {
        int *x = &q[i + 1], *y = &q[i];
        if(y - x != -1)  
            cout << y - x << ' ' << &q[i] << ' ' << &q[i + 1] << endl;
    }
    return 0;
}
```

``` c++
// 测试结果
-5 0x19d4e6c 0x19d4e80
-5 0x19d507c 0x19d5090
-5 0x19d528c 0x19d52a0
-5 0x19d549c 0x19d54b0
-45 0x19d56ac 0x19d5760
```

deque 的底部实现是一个 vector（称为 map ？？	），vector 的每个元素是一个指针，每个指针指向一个缓冲区 buffer。



其迭代器是一个类

1. first：当前所在 buffer 的起点
2. last：当前所在 buffer 的重点
3. node：处于（deque控制中心） 的那个段
4. cur：处于当前 buffer 的位置

first 和 last 相当于两个哨兵[first，last) 前闭后开

迭代器在移动的过程中，每次移动都需要判断是否移动到了当前段的末尾，来决定是否需要调到下一个段。（效率低！每次都需要判断一下）



和 vector 一样，deque 也是 2 倍增长的，因此它也有 capacity，事实上标准库并未规定容器的增长细节，但是大多数实现都使用了 2 倍增长。

``` C++
#include <bits/stdc++.h>

using namespace std;

const int N = 1000010;

int main()
{
    deque<int> q;
    vector<int> vec;
    clock_t start;
    //
    start = clock();
    for(int i = 0; i < N; i ++ )    q.push_back(i);
    cout << (clock() - start) << endl;
    //
    start = clock();
    for(int i = 0; i < N; i ++ )    vec.push_back(i);
    cout << (clock() - start) << endl;
    //
    start = clock();
    int sum = 0;
    for(int i = 0; i < N; i ++ )    sum = q[i];
    cout << (clock() - start) << endl;
    //
    start = clock();
    for(int i = 0; i < N; i ++ )    sum = vec[i];
    cout << (clock() - start) << endl;
    //
    return 0;
}
```



``` C++
15584
16521
79156
3326
```

deque 在拓展的时候 copy 方式是 copy 到新空间的中间位置，以方便前面和后面的扩充。



# 八、queue

`queue<int,  vector<int>> q;` 可以通过编译

但是当我们使用 `q.pop();` 编译器报错

这说明编译器对模板不会进行一个全面的、完整的检测

只有当你用到时才会进行检测



# 九、stack





# 十、Red-Black tree

关联式容器甚至可以看做一个小型数据库

关联式容器的两个重要的底层数据结构：红黑树和哈希表

红黑树就是一颗高度平衡的二叉搜索树

红黑树提供遍历操作和迭代器

编译器会将 size = 0 的class 的size 设为 1

同 link，rb_tree 也设置了一个空节点实现前闭后开区间。

红黑树有【元素自动排序】特性

value[key,date]

``` C++
#include <bits/stdc++.h>

using namespace std;

void test1()
{
    set<int, greater<int>> s; 
    s.insert(1);
    s.insert(2);
    s.insert(3);
    for(auto &x : s)    cout << x << endl;
    set<int>::value_type x = 23.2332;
    set<int>::key_type y = 2.3232;
    cout << x << ' ' << y << endl; 
}
 
 void test2()
 {
    map<int,int>::value_type p = {1,2};
    map<int,int>::value_type::first_type px = 1;
    map<int,int>::value_type::second_type py = 2;
    map<char,char>::key_type c1 = 65;
    map<char,char>::mapped_type c2 = 97;
    
    cout << p.first << ' ' << p.second << endl;
    cout << px << ' ' << py << endl;
    cout << c1 << ' ' << c2 << endl;
 }
 
int main()
{  
    test1();
    test2();
    return 0;
}
```



# 十一、hashtable

hashtable 中有很多经验值

经验法则：如果某个 bucket 的元素个数比 bucket 的个数还要多，就要两倍扩充 bucket 并重新放入元素（rehashing）。





# 十二、 STL 六大部件

容器替我们处理了内存分配上的问题，我们只管往容器里面添加删除元素，至于数据在内存上是怎样分配的，容器替我们解决了。

容器本身并不能处理内存，它是通过分配器来实现内存的分配与回收的。

算法通过迭代器操作容器。

1. 容器
2. 分配器
3. 算法
4. 迭代器
5. 仿函数
6. 适配器



































































































































































