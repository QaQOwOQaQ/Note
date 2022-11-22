

# Essential C++

## 0x00 附录

### 1. 站在巨人的肩膀上

[Oracle C++用户指南](https://docs.oracle.com/cd/E19205-01/820-1214/index.html)

### 2. 英语 

parameter：参数列表

classes hierarchy：类层次体系

string literal：字符串常量

character literal：字符常量

dereferencing：(指针)解引用，提领

placehold：占位符

function prototype：函数原型

debugger：调试器

scopre：作用域

predicate：谓词

unary：一元

partition：分区

populate：填充 

###  3. 基于对象的编程风格

基于对象（Object Based）：使用了“对象“的概念，把状态和行为封装在一个对象的内部。

面向对象（Object Oriented）：使用了“对象”的概念，同时还有“继承”和“多态”的概念。

所以，**面向对象**基本上是**基于对象**的一个子集。同时由于面向对象很重视“继承”和“多态”，所以也可以认为面向对象更多是“**面向类**”的。

候捷：基于对象中，类之间的关系比较弱，而面向对象中，类之间的关系很密切。

## 0xf2 函数与头文件

### 1. 随机数

如果希望使用(伪)随机数，可以通过 C 标准库的 srand() 和 rand() 函数实现。srand() 用来初始化随机数种子(seed)，一般使用时间来初始化：`srand(time(NULL));` 。而 rand() 则返回一个 int 范围内的随机数。这两个函数的声明位于头文件 `<cstdlib>` 中。

### 2. exit()

exit() 的声明在 `<cstdlib>` 头文件中。 

### 3. 最值

在头文件 `<limits>` 中的 **class** numeric_limits 中，由很多返回最值的函数。例如：

``` c++
#include <iostream>
#include <limits>

using namespace std;

int main()
{
    int a = numeric_limits<int>::max();
    int b = numeric_limits<int>::min();
    double c = numeric_limits<double>::max();
    double d = numeric_limits<double>::min();
    double e = numeric_limits<char>::max();
    double f = numeric_limits<char>::min();
    
    cout << a << ' ' << b << ' ' << c << ' ' << d << ' ' << e << ' ' << f << endl;
    
    return 0;
}
```

### 4. string

string 居然还有 empty() 函数

## 0x01 C++ 编程基础

简要介绍了一些 C++ 的基础知识。

### 1.1 类

class 机制，赋予了我们增加 **“程序内之类型抽象化层次”** 的能力。

> 例如，如果我们要表示一个立方体的特征（体积特征和颜色特征），在最原始最基本的情况下，我们可以用三个浮点数来表示长宽高，一个整数表示颜色。以这么低级的方式来编程，势必让我们的思考不断在 “立方体的抽象类型” 和 “相应于立方体的三个浮点数和一个整数” 之间反复来回。
>
> class 机制，赋予了我们增加 **“程序内之类型抽象化层次”** 的能力。我们可以定义一个 Volumn class 用来表示体积特征，定义一个 Color class 用来表示颜色特征，最后，用一个 Rectangle class 来包含这两个 class。这样，虽然我们同样使用三个浮点数和一个整数，但是我们的思考不在直接面对七个浮点数，而是转为对 Rectangle class 的思考。
>
> **我们要记住，数据类型虽然为我们的编程任务提供了基石，但它们并非程序的重心所在。**

一般来说，class 的定义分为两部分，分别写在不同的文件中。其一是所谓 “头文件”，用来声明该 class 的各种操作行为。另一个文件，程序代码文件，则包含了这些操作行为的实现内容。既，**“机制与策略分离的思想”**

**delete 是语言关键字。** 这也正是 string class 采用 erase() 而非 delete() 来表示 “删去一个字符” 的原因。

### 1.2 输入和输出

数据的输入和输出，并非 C++ 语言本身定义的一部分（次精神同 C 语言），而是由 C++ 的一套面向对象的类层次体系（classes hierarchy）提供支持，并作为 C++ 标准库的一员。

**无论是标准输入输出，还是文件的输入输出，都是通过对象进行的。**

例如标准输入对象（cin），标准输出对象（cout）和标准错误输出对象（cerr）。我们的内容都是对对象执行读入和写入操作的。

标准错误输出和标准输出一样，都将输出结果定向到用户的终端。两者的唯一差别是：cerr 的输出结果并无缓冲区 —- 它会立即显示到用户的终端。（使用 iostream library 提供的操作符 ‘endl’ 可以输出并刷新缓冲区）。

C++ 中用于实现数据输入输出的流类及其继承关系。 

![img](https://s1.328888.xyz/2022/10/09/gk6tw.png)



> istream：常用于接收从键盘输入的数据；
> 		ostream：常用于将数据输出到屏幕上；
> 		ifstream：用于读取文件中的数据；
> 		ofstream：用于向文件中写入数据；
> 		iostream：继承自 istream 和 ostream 类，因为该类的功能兼两者于一身，既能用于输入，也能用于输出；
> 		fstream：兼 ifstream 和 ofstream 类功能于一身，既能读取文件中的数据，又能向文件中写入数据。



因此，如果我们需要使用文件来进行输入输出，我们首先需要实例化相应的文件输入输出对象。然后将标准输入输出对象换成文件输入输出对象即可。

文件的读写：

1. 为了打开一个可供输出的文件，我们定义一个 ofstream 对象，并将文件名传入：`ofstream outfile("a.txt");` 。便创建并构造函数初始化了一个 ofstream 对象 outfile。
2. 为了打开一个可供输入的文件，我们定义一个 ifstream 对象，并将文件名传入：`ifstream infile("a.txt");` 。便创建并构造函数初始化了一个 ifstream 对象 infile。
3. 如果想同时读写一个文件，我们得定义一个 fstream 对象，并将文件名和模式信息传入：`fstream iofile("a.txt", ios_base::app || ios_base::in);` 。其中 ios_base 定义了流打开模式信息。详细信息参考[官方文档](https://www.apiref.com/cpp-zh/cpp/io/ios_base.html)



### 1.3 数据与运算符

<< output 运算符

\<\< input 运算符

. dot 成员选择运算符

-> arrow 成员选择运算符

字符常量分为两类：

1. 第一类是大打印字符。
2. 第二类是不可打印字符，例如换行符(‘\n’)或制表符(‘\t’)。由于不可打印字符并无直接的表示法（这表示我们无法使用单一而可显示的字符来独立表示），所以必须以两个字符所组成的字符序列来表示。

数据类型决定了对象所能持有的数值范围，同时也决定了对象应该占用多少内存空间。

相较于 C 语言只能采用 =（assignment 运算符）来初始化，C ++ 为什么要额外增加构造函数语法来初始化对象呢？这主要是因为 C++ 增加了 class 的机制，例如 complex class，它的初始化需要两个值（一个实部一个虚部），而 assignment 运算符无法完成这个任务。于是增加构造函数来处理“多值初始化”

我们知道，windows下的路径表示使用 ‘\\’，而 linux 下的路径表示使用 ‘/’，由于 ‘\\’ 是转义字符，所以说用字符串保存 windows 下的路径的时候，要使用两个 ‘\\’。例如：

`string path = F:\\essential\\prigram\\chapter1\\demo1.cpp;`

数组的大小是一个**常量表达式**，它必须在编译时确定（常量可以在编译时确定），而普通变量只能在运行时确定。

### 1.4 错误

在 C++ 中，如果你没有在 main() 的末尾写下 return 语句，会自动加上，

off-by-one错误。

> **差一错误**（英语：**Off-by-one error**，缩写**OBOE**）是在计数时由于边界条件判断失误导致结果多了一或少了一的错误，通常指[计算机编程](https://zh.m.wikipedia.org/wiki/计算机编程)中[循环](https://zh.m.wikipedia.org/wiki/程序循环)多了一次或者少了一次的程序错误，属于[逻辑错误](https://zh.m.wikipedia.org/wiki/逻辑错误_(程序设计))的一种。比如，程序员在循环中进行比较的时候，本该使用“小于等于”，但却使用了“小于”，或者是程序员没有考虑到一个序列是从0而不是1开始（许多程序语言的数组下标都是这样）。在[数学](https://zh.m.wikipedia.org/wiki/数学)领域，此错误也时有发生。

### 1.5 模版

在模版中，数据类型作为一个参数存在，在对数据类型赋值时，要用尖括号（<>）包围。

### 1.6 指针

指针可以为程序提供一层间接性。从而为程序提供弹性（在第二章和第三章将深切体会）。

`p->a` 等价于 `(*p).a` ，arrow 成员选择运算符主要是为了我们方便使用指针而提供的。

在 C++ 动态分配时也可以使用构造函数（将 a 初始化为 3）:`int *a = new int(3);`

C++ 没有为没有提供任何语法让我们得以从 heap 分配数组的同时为其元素设定初值（C 语言可以使用 malloc）。

`int *a = new int[3];`

释放为数组分配的空间（[ ]必须放在变量名的前面，delete的后面）：`delete []a;`

### 1.7 命名空间

把命名空间和操作系统的文件管理下不同文件夹下文件名的管理练习起来，就很容易理解了。

[菜鸟教程](https://www.runoob.com/cplusplus/cpp-namespaces.html)

[微软文档](https://learn.microsoft.com/zh-cn/cpp/cpp/namespaces-cpp?view=msvc-170)

练习：

```c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

namespace xjy {
    void func() {
        cout << "namespace xjy!" << endl;
    }
}

namespace cs {
    void func() {
        cout << "namespace cs!" << endl;
    }
}

namespace xjy {
    void refunc() {
        xjy::func();
        cout << "rexjy" << endl;
    }
}

namespace rexjy {
    using namespace xjy;
    void refunc() {
        func();
        xjy::refunc(); 
        // 如果我们using引入的命名空间和当前命名空间存在相同的函数
        // 并且我们还没有使用namespace::加以区分的话
        // 默认使用自己命名空间下的函数
    }
}


void func() {
    cout << "std" << endl;
}


int main()
{
//     func();
//     xjy::func();
//     xjy::refunc();
    rexjy::refunc();   
    return 0;
}
```



## 0x02 面向过程的编程风格

在原书中，通过一步步的优化，将一个简单的低级的函数优化为一个复杂的高级的函数。

### 2.1 如何编写函数

在规模较大的程序当中，将一部分的代码封装成函数独立出来又很多好处：

1. 用函数调用操作取代编写相同的程序代码，可以使代码更容易读懂。
2. 可以在不同的程序中调用这些函数。
3. 可以更容易地将工作分配给协作开发团队。

函数参数扮演者占位符的角色。

函数必须先声明，然后才能被调用。函数的声明让编译器得以检查后续出现的使用方式是否正确 —- 是否有足够的参数，参数类型是否正确，等等。函数证明不必提供函数题，但必须指明返回类型，函数名以及参数列表。此即所谓的**函数原型**。

函数声明中可以忽略参数名，但是不推荐这样做。因为参数名可以为我们提供很多有用的信息。在编写文档的时候，为函数的声明提供参数名也是必要的（不然别人看了你的文档中的某个函数就会一头雾水）。

如果函数的最后一条语句不是 return，那么最后一条语句之后便是该函数的**隐式退出点**。当然，前提是函数的返回类型是 void 你才可以这么做。



### 2.2 调用函数

#### 2.2.1 传引用

by reference（传引用，传址）:所有的引用占用同一个地址（被引用元素的地址）。

C++ 不允许我们改变 reference 所代表的对象，它们必须**从一而终**。reference也必须只想一个对象。

面对 reference 的所有操作和面对 “reference所代表的对象” 所进行的操作一般无二。

例如：

``` C++
#include <iostream>
using namespace std;
int main()
{
    int a = 1, b = 4;
    int &p1 = a;
    int &p2 = a; 
    cout << a << ' ' << p1 << ' ' << p2 << endl;
    cout << &a << ' ' << &p1 << ' ' << &p2 << endl;
    p1 = 2;
    cout << a << ' ' << p1 << ' ' << p2 << endl;
    a = 3;
    cout << a << ' ' << p1 << ' ' << p2 << endl;
    // &p1 = b; // 编译错误
    //int &p3; //Error: 'p3' declared as reference but not initialized
    return 0;
}
```

```
1 1 1
0x7fffc7134da8 0x7fffc7134da8 0x7fffc7134da8
2 2 2
3 3 3
```



当我们以 by reference 方式将对象作为函数参数传入时，对象本身并不会复制出另一份 —— 复制的是对象的地址。函数中对该对象进行的任何操作，都是相当于对传入的对象进行**间接操作**。

以 by reference 的方式传入对象有两个理由：

1. 得以直接对所传入的对象进行修改。
2. 降低复制大型对象的额外负担以提高程序效率。在这种情况下，我们可能并不想修改对象，为了保证程序的安全性，我们需要将参数同时声明为 const 类型来必然修改对象的操作。

在传递参数时，使用 reference 或 point 的区别大致是用法不同罢了，reference 更简单？但是这两种方式传入的参数区别就很大了！point 可能是个空指针，因此当我们 deference 的时候，一定要先确定其值不为空。至于 reference，则必定会代表某个对象，所以不需要做额外的检查。

如果我们想要在函数中返回一个 vector 对象，无论是 point 还是 reference 都是不正确的，因为函数中的 vector 对象是分配在程序堆栈（program stack）上的，在函数结束时就会被释放（实际上是从 stack 中 pop掉了）。但是我们可以在函数中以传值方式返回该 vector 对象，因此返回的乃时对象的副本，它在函数之外依然存在。如下 func1()：

``` C++
#include <iostream>
#include <vector>
using namespace std;
vector<int> func1()
{
    vector<int> a(3, 1);
    return a;
}
int
int main()
{
    vector<int> a = func();
    for(auto &x : a)    cout << x << ' ';
    return 0;
}
```

> 对于数组我们没法以 by value 的方式返回，因为数组的本质就是一个指针（by point）。
>
> 大多数 C++ 编译器对于 “以 by value 方式返回的 class object” ，都会通过优化程序，加上额外的 reference 参数。

#### 2.2.2 冒泡排序

所谓冒泡排序，就是将大的数据或者小的数据冒泡到数据的顶部（我们假设下标为 0 的位置为顶部）。

``` c++
void bubble_sort(vector<int> vec) // 升序排序
{
    int n = vec.size();
    for(int i = 0; i < n; i ++ )
        for(int j = i + 1; j < n; j ++ )
            if(vec[i] > vec[j])
                swap(vec[i], vec[j]);
}
/*
	再该函数中，每次for:i，就可以确定下标为i的位置经过排序后的值。它的思路是那未经排序时该位置的值和所有未确定位置([i+1,j])的值进行比较，如果比未确定位置的值大就交换，最后就可以找到一个最小值然后放在该位置。
	整个过程就好像把所有数值小的元素冒泡到数组顶部一样。
*/
```



#### 2.2.3 作为开关的参数

​								**——- 为参数提供默认值**

在 C++ 中，我们可以为参数提供默认值，所有提供了默认值的参数都必须放在参数列表的末尾。

我们可以利用这个语法，将函数的参数作为一个开关。

例如上面我们编写的冒泡排序。如果我们想要打印排序的流程，即在函数中插入一些 printf() 函数，我们总不能为了这个功能而额外添加一个函数吧，毕竟这个功能可能很少用到。我们也不能在原函数直接添加 printf() 函数吧，当元素很多时，大量的打印信息会造成极大的浪费。

一个方便的且拓展性好的方式是为函数增加一个 “开关” 参数，这个参数作为是否打印信息的 “开关”，默认情况下，这个开关是关闭（设定一个初始值）的，我们甚至可以 “忽略这个参数”，即视这个参数不存在。只有当我们需要打印信息时，传入一个我们设定好的开关参数，函数就会打印我们想要的信息。如下：

```c++
void bubble_sort(vector<int> vec, bool flag = false) // 升序排序
{
    int n = vec.size();
    for(int i = 0; i < n; i ++ )
        for(int j = i + 1; j < n; j ++ )
        {
            if(flag == true)	
            {
                // do something
            }
            if(vec[i] > vec[j])
                swap(vec[i], vec[j]);
        }
}
```

> 我们甚至可以将 bool 类型替换为 int 类型来为这个参数增加更多的功能。

不过为函数参数提供默认值会给我们带来一个问题，那就是这个默认值是在函数声明时提供，**但不能同时在声明和定义处提供**，还是都提供呢？

> **为什么不能在声明和定义处都提供呢？**
>
> 声明是用户可以看到的部分，**客户非常信任**地使用这个特性，希望得到一定的结果，但是你在实现里使用了不同的[缺省值](https://so.csdn.net/so/search?q=缺省值&spm=1001.2101.3001.7020)，那么将是灾难性的。因此编译器禁止声明和定义时同时定义缺省参数值。
>
> **结论：在声明处提供默认值更好！**
>
> 若声明时没有定义缺省参数值，那么在定义成员函数时可以定义缺省参数值。但这种情况通常**用户是看不见的**，因此应避免。
>
> **核心：用户就是上帝！**

在我看来，为参数提供默认值更像是一种**机制**，而你是否使用这个对象（你可以不为这个对象提供参数值，看起来就是好像没有使用这个对象一样），或者是否使用这个默认值更像是一种**策略**。（结合上面冒泡排序的例子理解）



### 2.3 使用局部静态对象

**为了节省函数之间的通信而将对象定义于 file scope，永远都是一种冒险**。通常，file scope 对象会打乱不同函数之间的**独立性**，使他们难以理解。

> 能不定义全局对象，就不要定义全局对象。

一种好的方式是声明静态对象。



### 2.4 声明 inline 函数

将函数声明为 inline，表示**要求**编译器在每个函数函数调用点上，将函数的内容展开。面对一个 inline 函数调用操作，编译器可将该函数的调用操作该为以一份该函数的副本代替。

将函数指定为 inline，只是对编译器提出的一种 **“要求”** 或者说 **“建议”**，也就是说 inline 并不是一种强制性操作。编译器是否执行这项请求，需视编译器而定。

inline 函数的定义，通常放在头文件中。由于编译器必须在它被调用的时候加以展开，所以这个时候其定义必须时有效的。（也就是声明和定义必须在同一个文件当中）

> 实测 inline 函数不会再初始化后展开。

[参考](https://www.cnblogs.com/my_life/articles/4089184.html)



### 2.5 重载函数

编译器无法根据类型来区分两个具有相同名称的函数，因为返回类型无法给我们一个足以区分不同重载函数的语境。（你 TM 知道我想返回什么类型？）



### 2.6 模版函数

函数模版是一种 **“机制”**，让我们得以将单一函数的内容与希望显示的各种类型绑定（bind）起来。

function template 将参数列表中指定的全部（或部分）参数的类型信息抽离了出来。

function template 以关键字 template 开场，其后紧接着成对尖括号（< >）包围起来的一个或多个标识符。这些标识符表示我们希望 **“推迟决定”** 的数据类型。这些标识符事实上扮演着占位符的角色，用来放置函数参数列表及函数题中的某些实际数据类型。

例如：

```c++
#include <iostream>

using namespace std;

template <typename Ta, typename Tb>
Ta MAX(Ta a, Tb b)
{
    return max(a, b);
}

int main()
{
    int a = 1, b = 2;
    double c = 2.43, d = 4.34;
    cout << MAX(a, b) << endl << MAX(c, d) << endl;
}
```



当我们以某种类型调用模版函数时，编译器会将类型与我们模版中的定义绑定（bind）起来，然后生成一份函数实例（参数类型确定）。

> 同 inline 函数，这应该是在编译时做的操作。



### 2.7 函数指针数组

直接举例子：

``` C++
#include <iostream>

using namespace std;

int func1(int x) { return x;     }
int func2(int x) { return x * x; }
int func3(int x) { return x * 2; }

// typedef个一个type，type的实例是一个返回值类型为int
// 有一个int类型参数的函数值怎
typedef int (*handler)(int); 


// 定义一个函数指针数组
handler cal[3] = {
    &func1, &func2, &func3
};

int main()
{
    int val = 4;
    for(int i = 0; i < 3; i ++ )
        cout << cal[i](val) << endl;
}
```

在定义函数指针数组的时候，最好将函数指针 typedef 成一个单独的类型，这样方便我们编写代码。否则，看看下面的形式：

```c++
int (*cal[3])(int) = {
    &func1, &func2, &func3
};
```

还好理解吗？更复杂一点的呢？（例如返回值类型为指针 or 指向指针的指针。。。）

一定要区分**函数指针** 和 **指针函数**

> `int (*cal)(int, int);`	– 函数指针，是一个指针，指针指向一个函数
>
> `int *cal(int,int);`		– 指针函数，是一个函数，函数的返回值类型为一个指针
>
> 差异的根源在于同是单目运算符的优先级从右向左。



### 2.8 设定头文件

头文件的拓展名习惯上是 `.h` 。标准库例外，它没有拓展名。

程序的定义可以有多份，但是声明只能有一份。（机制只能有一个，但是基于机制的策略可以有很多种）。但是有个例外：inline 函数的定义。为了能够拓展 inline 函数的内容，在每个调用点上，编译器都取得其定义。这意味着我们必须将 inline 函数的定义放在头文件中，而不是把它放在各个不同的程序代码文件中。还有个例外就是 const object。和 inline 一样，const 也是 “一次定义” 规则下的例外。**const object 的定义只要一出文件之外便不可见**。这意味着我们可以在多个程序代码文件中加以定义，不会导致任何错误。

> 关于 const object 的定义一出文件之外便不可见是 C ++ 独有的性质， C并不具备这个性质。
>
> **原因：c++中 const 具有内部链接性，c 中 const默认外部链接性。**
>
> 我们可以测试一下，有两个文件：
>
> 1.c/1.cpp
>
> ```c++
> #include <stdio.h>
> const int val = 2038;
> void func();
> int main()
> {
>     func();
>     printf("1.c val = %d\n", val);
> }
> ```
>
> 2.c/2.cpp
>
> ``` c++
> #include <stdio.h>
> const int val = 222;
> void func()
> {
>     printf("2.c val = %d\n", val);
> }
> ```
>
> 当我们以 `.c` 的后缀编译这两个文件的时候，编译器会给出重定义错误，但如果我们以 `.cpp` 的后缀编译这两个文件，就可以正常执行。

一个关于 C++ 中的 const 变量定义在源文件还是头文件的建议：

>作者：雨落惊风
>链接：https://www.zhihu.com/question/21622061/answer/2373761045
>来源：知乎
>著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
>
>
>
>以下仅适用于c++中（c中有区别，原因：c++中const具有内部链接性，c中const默认外部链接性）
>
>const/constexpr定义放在头文件中，
>
>```cpp
>优点：编译器在进行分离编译时，由于在每个文件中都可以看到const的定义，
>      从而可以进行编译优化
>缺点：如果编译优化并未发生，
>    1. 由于const的内部链接性，每个文件都保存了一份const的副本，
>         编译后，内存占用可能过大。
>      2. 当对const有变更需求时，每个包含头文件的.cpp文件都需要重新编译，
>         编译时间可能很长。
>```
>
>const定义放在源文件中，并在.h头文件中包含对其声明，使用const的.cpp文件包含头文件
>
>```cpp
>优点：1. extern const作为共享变量，仅仅在定义的.cpp文件被实例化1次，
>         使用const的.cpp文件只是包含头文件，
>         链接器负责链接到const定义。所以，内存空间占用相对变小。
>      2. 由于分离编译，当有需求变更const变量时，仅需要重新编译
>         定义const的.cpp源文件即可，其他使用const的.cpp文件，由链接器
>         代劳链接到重新编译好的const上，无需对使用const的文件重新编译(g++ -c)。
>         所以，编译时间相对变少。
>缺点：当头文件被预处理器展开后，由于其中只有const变量的声明，编译器在
>      分离编译时无法获取const的定义值，所以存在无法进行编译优化的问题，
>      比如替换const为对应值（做不到啊，const值是啥！）
>```
>
>所以，
>
>如果你在意你程序内存的大小以及出版本时的编译速度（不用加班，oho），你就将const定义放在源文件中，const声明放在头文件中；
>
>```text
>xxx.cpp
>extern const int noWantJiaBan = ...
>
>xxx.h
>extern const int noWantJiaBan;
>
>xxx.cpp
>#include "xxx.h"
>```
>
>如果你在意编译优化，那么const定义就放直接放在头文件中.
>
>```text
>xxx.h
>const int optimizable = ...
>
>xxx.cpp
>#include "xxx.h" 
>```

在 file scope 定义的对象，如果可能被多个文件访问，就应该声明于头文件中。因为如果我们没有在程序中声明某个对象，便无法加以访问。

关于头文件由 <> 或者 “” 包围的说明

> 更有技术含量的回答是：
>
> 如果此文件被认定为标准的成项目的或项目专属的头文件，我们便以尖括号将文件名括住；当编译器搜索此文件时，会先在某些默认的磁盘目录中寻找。
>
> 如果此头文件由成对的双引号括住，此文件便被认为是一个用户提供的头文件；搜索此文件时，会由要包含此文件的文件的所在磁盘目录开始找起。





## 0x03 范型编程风格

### 1. STL

STL：standard template library

组成：

1. container：
   1. 顺序容器：vector, list, deque, ..
   2. 关联容器：map, set, ..
   3. 容器适配器：stack, queue, ..
2. generic algorithm：



顺序容器

> 顺序容器内元素的地址并不一定连续，但是它的元素位置一定连续。
>
>  vector 和 deque 支持随机索引。
>
> list 和 deque 支持双向插入。



插入（insert）

> push_back 和 push_pop 是特殊的插入操作。
>
> insert 的四种变形：
>
> 1. 



范型算法系通过 function template 技术，达到 “与操作对象的类型相互独立” 的目的。而**实现 “与容器无关” 的诀窍，就是不要直接在容器身上进行操作。** 而是借由一对 iterator（first 和 last），表示我们要进行迭代的元素范围。

> 对于 iterator last，我们一般将其设置为容器的**终点位置（最后一个元素的下一个位置）**，而不是最后一个元素的位置。并称其为 “哨兵”。



### 2. 编写你的模板函数 - find

通过模版来屏蔽元素类型

如何屏蔽容器类型：如果我们使用指针来查找元素的话，那么我们传入某个容器的指针。如果我们能够把指针抽象，无论是哪个容器的指针，我们都能把他们看作普通的 “指针”，这样我们就实现了屏蔽容器的类型。

> vector != array
>
> 虽然可以用 array 给 vector 初始化，但是 vector 和 array 仍然是两个概念。





**< — 暂停更新，实在是太费时间，接下来只记录一些书上没有明确说明的nearing – >**



### 3. 根据 const 重载

可以根据一个函数是否是 const 的重载，这里的 const 修饰的是函数而不是返回类型。

``` c++
class test
{
    public:
        const int get()  
        { 
            return 1;
        }
        int get()
        {
            return 1;
        }
};
// compile error
```

``` c++
class test
{
    public:
		int get() const
        { 
            return 1;
        }
        int get()
        {
            return 1;
        }
};
// accept
```



### 4. static in C/Cpp

static 在 C 中一般作为静态全局/局部变量和静态函数存在。

静态函数：

1. 静态函数的作用域为当前的源文件（屏蔽功能），而局部函数的作用域为整个工程。

2. 静态函数会被一直放在一个一直使用的存储区，直到退出应用程序实列，避免了调用函数时的压栈和出栈，速度快很多。

在 C++ 的 class 中，static 拓展出了 static member function 和 static member variable



### 5. cerr && cout

std::cerr是标准错误输出流，不需要缓存，直接显示在显示器上，而且只能显示到显示器上，即std::cerr流不能转向。

std::cout需要缓冲区（目的是为了减少屏幕刷新次数，一次全部显示），std::cout流可以转向，例如可将cout流流向文件操作中去。

1.区别
cout对应于标准输出流，默认情况下是显示器。这是一个被缓冲的输出，可以被重定向。
cerr对应标准错误流，用于显示错误消息。默认情况下被关联到标准输出流，但它不被缓冲，也就说错误消息可以直接发送到显示器，而无需等到缓冲区或者新的换行符时，才被显示。一般情况下不被重定向。

2.为什么要用cerr
比如，你的程序遇到调用栈用完了的威胁（无限，没有出口的递归）。
你说，你到什么地方借内存，存放你的错误信息？
所以有了cerr。其目的，就是在你最需要它的紧急情况下，还能得到输出功能的支持。 缓冲区的目的，就是减少刷屏的次数——比如，你的程序输出圣经中的一篇文章。不带缓冲的话，就会每写一个字母，就输出一个字母，然后刷屏。有了缓冲，你将看到若干句子“同时”就出现在了屏幕上（由内存翻新到显存，然后刷新屏幕）。

> by xjy
>
> 因此说 cerr 更像是一种需要在紧急情况下做输出的东西，他并不会终止程序的运行。



### 6. enum 的作用域

enum 内元素的作用域是全局的，例如：

``` c++
enum E1 {
    a, b
};
enum E2 {
    a, b
};
/* compile error
error: 'a' conflicts with a previous declaration
error: 'b' conflicts with a previous declaration
*/
```

如果我们确实需要在不同的 enum 内声明重名的枚举量，C++ 11 位我们提供了一种新的枚举类型（enum class），其枚举的作用域为类。

这样编译就不会出错，但是使用的时候要加上枚举名来限定枚举量。例如：

``` c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

enum class e1{
    d,a,b,c
};

enum class e2{
    b,c,e,a
};

int main()
{
    e1 var1 = e1::a;
    e2 var2 = e2::a;
    
    // 必须强制类型转换
    cout << (int)var1 << endl;
    cout << (int)var2 << endl;
    
    
    return 0;
}
```

> 关于更详细的 enum 和 enum class 的比较 和 enum class 的介绍：[[reference]](https://blog.csdn.net/weixin_42817477/article/details/109029172?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-109029172-blog-78535754.pc_relevant_recovery_v2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-2-109029172-blog-78535754.pc_relevant_recovery_v2&utm_relevant_index=3)



### 7. 将临时对象作为参数传给引用

试看下面的代码：

``` C++
#include <iostream>
void g(int &x)
{
    std::cout << "getx: " << x << std::endl;
}
void f(const int &x)
{
    std::cout << "getx: " << x << std::endl;
}
int main()
{
    int a = 1, b = 2;
    g(a+b);
    //f(a+b);
    return 0;
}

```



上面的两个调用之前，a+b 的值会存在一个临时变量中，当把这个临时变量传给函数 g 时，由于 g 的声明中，参数是 int&，不是常量引用，所以产生以下编译错误：
```shell
a.cpp:8:7: error: cannot bind non-const lvalue reference of type 'int&' to an rvalue of type 'int'
    8 |     g(3);
```

意思就是我们不能将一个作为左值 int 引用和一个 int 绑定。

而在 f(a+b) 中，由于 f 定义的参数是 const int&，编译通过。  问题是为什么临时变量作为引用参数传递时，必须是常量引用呢？很多人对此的解释**是临时变量是常量，不允许赋或者改动**，所以当作为非常量引用传递时，编译器就会报错。这个解释在关于理解临时变量不能作为非 const 引用参数这个问题上是可以的，但不够准确。事实上，临时变量是可以被作为左值(LValue)并被赋值的，请看下面的代码：
```c++
#include <iostream>
using namespace std;

class test
{
    friend test operator+(const test &p1, const test &p2);
    friend ostream& operator<<(ostream &os, const test &p);
    public:
        test() {}
        test(int _x) : x(_x) {}
    private:
        int x;
};

test operator+(const test &p1, const test &p2)
{
    test p3;
    p3.x = p1.x + p2.x;
    cout << "you get here!" << endl;
    return p3;
}

ostream& operator<<(ostream &os, const test &p)
{
    os << p.x;
    return os;
}

int main()
{
    test a(2), b(3), c(4);
    cout << ((a + b) = c) << endl;  // 临时对象作为左值
    
    // int x = 1, y = 2, z = 3;
    // cout << ((x + y) = z) << endl; // 编译不通过
    
    return 0;
}
```



上面的程序编译通过，而且运行结果是：
``` C++
you get here!
4
```



临时变量确实被赋值，而且成功了。

> 至于为什么能成功，作者并没有说，但是从我们在 operator+ 重载函数插入的 cout 语句以及输出可以看出，应该是在 a+b 时调用了该函数，然后生成了一个 “对象”，该对象可以作为左值。
>
> 而在 main 函数的最后两条语句，(x+y)=z 编译就不通过，大概是因为 x+y 生成的不算是一个 “对象”，而只是一个单纯的值罢了。

所以，临时变量不能作为非 const 引用参数，不是因为他是常量，而是因为 c++ 编译器的一个关于语义的限制。如果一个参数是以非 const 引用传入，c++ 编译器就有理由认为程序员会在函数中修改这个值，并且这个被修改的引用在函数返回后要发挥作用。

但如果你把一个临时变量当作非 const 引用参数传进来，由于临时变量的特殊性，程序员并不能操作临时变量，而且临时变量随时可能被释放掉，所以，一般说来，修改一个临时变量是毫无意义的，据此，c++ 编译器加入了临时变量不能作为非const 引用的这个语义限制，意在限制这个非常规用法的潜在错误。

还不明白？OK，我们说直白一点，如果你把临时变量作为非 const 引用参数传递，一方面，在函数申明中，使用非常量型的引用告诉编译器你需要得到函数对某个对象的修改结果，可是你自己又不给变量起名字，直接丢弃了函数的修改结果，编译器只能说：“大哥，你这是干啥呢，告诉我把结果给你，等我把结果给你了，你又直接给扔了，你这不是在玩我呢吗？”所以编译器一怒之下就不让过了。这下大家明白了吧？



> 所以说，临时对象只能作为右值的理解是片面的。
>
> 一个对象必然有左值和右值，系统是否提供给用户使用则是另外一码事



## 0x04 基于对象的编程风格

 ### 1. operand ->\*  &&  .\*

[rerference - 微软](https://learn.microsoft.com/zh-cn/cpp/cpp/pointer-to-member-operators-dot-star-and-star?view=msvc-170)

[rerference - 菜鸟教程](https://www.runoob.com/w3cnote/cpp-func-pointer.html)

`->* 和 .*`：指向成员的指针运算符

> 当我们的成员函数或者成员变量被记录为指针时，不能再通过 `. 或者 ->` 直接调用该成员，需要使用指向成员的指针运算符。

例如：

``` c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

class BaseClass
{
public:
    void func1()
    {
        cout << "helloBase" << endl; 
    }
};

void (BaseClass::*Bptr)() = &BaseClass::func1;

class Derived : public BaseClass
{
public:
    void func2()
    {
        cout << "HelloDerived" << endl;
    }
};

void (Derived::*Dptr)() = &Derived::func2;

int main()
{
    BaseClass b;
    BaseClass *pb = new BaseClass;
    (b.*Bptr)();
    (pb->*Bptr)();
    
    Derived d;
    Derived *pd = new Derived;
    (d.*Dptr)();
    (pd->*Dptr)();
    
    //
    // (pb->*Dptr)();
    (pd->*Bptr)();
    // (b.*Dptr)();
    (d.*Bptr)();
    
    
    return 0;
}
```

 



## 0x05 面向对象的编程风格

[析构函数与虚函数](https://www.cnblogs.com/yuanch2019/p/11625460.html)

 

局部对象在生命周期结束之后会自动调用 dtor，但是分配在 heap 上的对象在函数结束之后也不会调用 dtor，必须 delete 才会调用。

子类包含父类继承的成员，但是子类不能直接初始化这些成员，需要调用父类的构造函数，让每个类自己控制自己成员的初始化过程。

只要调用基类的构造函数和析构函数，就一定会调用父类的构造函数和析构函数。

如果类 A 有个虚函数，那么类 B 继承类 A，类 C 继承类 B，那么 B 也可以重载这个虚函数。并且父类 A 的指针也可以指向子类 C。

子类可以不实现父类的纯虚函数。存在纯虚函数的类不能被实例化。
