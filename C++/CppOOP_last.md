# .Appendix

## 0x01 Prologue

一、二：object based

三：some addition

四、：object oritened



## 0x02 English



oop：object oriented programming，面向对象编程

layout：布局

torment：折磨，煎熬

overload：重载

override：覆写，覆盖

scopre：作用域

interlude：插叙

inheritance：继承

composition：复合

delegation：委托

composite：复合

prototype：原型

explicit：明白的，明去的，清楚的

signatrue：签名

## 0x03 Tips

模版会造成代码的膨胀，但这是必要的，并不是缺点。

编译器会对 function template 进行 实参推导（argument dedution）。即选择类型。



## 0x04 [ref1](https://blog.csdn.net/qq_42247231/article/details/105109709)

今天我们来谈一谈面试 C++ 工程师时经常被谈到的一个问题：为什么析构函数必须是虚函数？为什么默认的析构函数不是虚函数？

首先，我们看一下百度百科对虚函数是怎么定义的:

在某基类中声明为 virtual并在一个或多个派生类中被重新定义的成员函数，用法格式为：virtual 函数返回类型 函数名 ( 参数表 ) { 函数体 }；实现多态性，通过指向派生类的基类指针或引用，访问派生类中同名覆盖成员函数。

好了，现在我们大概知道什么是虚函数，虚函数就是类中使用关键 virtual修饰的成员函数，其目的是为了实现多态性。

那么什么是多态性呢？

所谓多态性，顾名思义就是“多个性态”。更具体一点的就是，用一个名字定义多个函数，这些函数执行不同但相似的工作。最简单的多态性的实现方式就是函数重载和模板，这两种属于静态多态性。还有一种是动态多态性，其实现方式就是我们今天要说的虚函数。

回归正题。

一、为什么析构函数必须是虚函数？

当然了，这么说其实是不太严谨的，因为我完全可以将析构函数定义成非虚函数。这个我们后面再说。

首先我们需要知道析构函数的作用是什么。析构函数是进行类的清理工作，具体来说就是释放构造函数开辟的内存空间和资源，当然我们完全可以在析构函数中进行任何我们想要的操作，比如下面我们给出的示例代码，就在析构函数中打印提示信息。

前面我们在介绍虚函数的时候就说到，为实现多态性，可以通过基类的指针或引用访问派生类的成员。也就是说，声明一个基类指针，这个基类指针可以指向派生类对象。

下面我们来看一个例子：

```C++
#include <iostream>

using namespace std;

class Father {
public:
    ~Father() {
        cout << "class Father destroyed" << endl;
    }
};

class Son : public Father {
public:
    ~Son() {
        cout << "class Son destroyed" << endl;
    }
};

int main() {
    Father* p = new Son;
    delete p;

    return 0;
}
```

运行结果：

``` SHELL
运行结果：
class Father destroyed
```


上面的示例程序中，我们定义了两个类，一个基类，一个派生类，派生类公有继承父类。为了描述简单，这两个类只定义了析构函数，并在析构函数中输出提示信息。在主函数中，我们声明了一个基类的指针，并用一个派生类的实例去初始化这个基类指针，随后删除这个指针。我们看到程序运行的结果，只有基类的析构函数被调用。

为什么会这样呢？指针明明指向的是派生类对象，那删除这个指针，为何只有基类的析构函数被调用，而派生类的析构函数却没有调用呢？

我们先把问题留在这里，接下来我们看看，若析构函数被定义成虚函数会怎么样呢？

``` c++
#include <iostream>

using namespace std;

class Father {
public:
    virtual ~Father() {
        cout << "class Father destroyed" << endl;
    }
};

class Son : public Father {
public:
    ~Son() {
        cout << "class Son destroyed" << endl;
    }
};

int main() {
    Father* p = new Son;
    delete p;

    return 0;
}
```

运行结果：

``` shell
class Son destroyed
class Father destroyed
```

当基类的析构函数被定义成虚函数时，我们再来删除这个指针时，先调用派生类的析构函数，再调用基类的析构函数，很明显这才是我们想要的结果。因为指针指向的是一个派生类实例，我们销毁这个实例时，肯定是希望即清理派生类自己的资源，同时又清理从基类继承过来的资源。而当基类的析构函数为非虚函数时，删除一个基类指针指向的派生类实例时，只清理了派生类从基类继承过来的资源，而派生类自己独有的资源却没有被清理，这显然不是我们希望的。

所以说，如果一个类会被其他类继承，那么我们有必要将被继承的类（基类）的析构函数定义成虚函数。这样，释放基类指针指向的派生类实例时，清理工作才能全面进行，才不会发生内存泄漏。

二、为什么默认的析构函数不是虚函数？

那么既然基类的析构函数如此有必要被定义成虚函数，为何类的默认析构函数却是非虚函数呢？

首先一点，语言设计者如此设计，肯定是有道理的。

原来是因为，虚函数不同于普通成员函数，当类中有虚成员函数时，类会自动进行一些额外工作。这些额外的工作包括生成虚函数表和虚表指针，虚表指针指向虚函数表。每个类都有自己的虚函数表，虚函数表的作用就是保存本类中虚函数的地址，我们可以把虚函数表形象地看成一个数组，这个数组的每个元素存放的就是各个虚函数的地址。
这样一来，就会占用额外的内存，当们定义的类不被其他类继承时，这种内存开销无疑是浪费的。

这样一说，问题就不言而喻了。当我们创建一个类时，系统默认我们不会将该类作为基类，所以就将默认的析构函数定义成非虚函数，这样就不会占用额外的内存空间。同时，系统也相信程序开发者在定义一个基类时，会显示地将基类的析构函数定义成虚函数，此时该类才会维护虚函数表和虚表指针。



# 一出手就是大家之范

# 一、class with no pointer

## 0x01 Introduce

### 1. C vs C++

由于 C 语言没有提供足够的关键字，因此 C 语言中能够被函数处理的变量一定是全局变量（局部变量没有意义，它在函数结束之后就释放了）。但这就意味着所有的函数都可以使用这个全局变量。

### 2. class based or class oriented



只要你写的 class 之间有关系，就可以称为**面向对象**。

Class based: 单一 class

class oriented：多个class，class之间有连续

### 3. C++ code frame

.h：classes declaration

.h：standard library 

.cpp：one main and others

### 4. guard declaration of header

头文件防卫式声明：

避免重复 include。

```shell
#ifndef __XXX_GUARD__
#define __XXX_GUARD__
...;
#endif
```

### 5. layout of header 

``` shell
# <guard>

// 0. forward declarations

// 1. class declarations

// 2. class definition

# </guard>
```

### 6. about a = b

对于类似 `a = b;` 类型的语句，你应该说： 

`b assigned to a `，而不是： 

`a 等于 b`



## 0x02 Construtor

### 1. access level

class 中一般根据访问级别划分为两大段：public, private。（当然你也可以划分为多个public，private….）

数据部分一般划分为 private，因为我们的数据需要封装起来，不能随便为外界所看到。

函数根据是否需要开放给外界可以划分为 private，也可以划分为 public。

当然，还有另一种 access level：protected。

### 2. constructor（ctor）

initialization list（初值列，初始列），只有构造函数才有的语法。当我们使用构造函数时，能使用初值列，不要在大括号内初始化。

因为一个数据的设定有两个阶段：初始化（初值列）和赋值（大括号内）。你如果不在初值列不设定数值，而在大括号内设定数值，就会浪费初始化这一步骤。（这是效率的问题，你明明能一步搞定，为啥走两步呢？

当我们为一个构造函数的所有参数都提供默认值时，此时不能再提供默认的没有参数的构造函数。因为前面的构造函数已经实现了默认构造函数的功能。



### 3. tmp var

局部变量，没有名字。

[参考](https://blog.popkx.com/why-can-c-language-functions-be-return-local-variables-and-local-variables-are-not-released/)

[参考](https://blog.csdn.net/xiaomingZeng/article/details/89319861)

[好文！](https://www.cnblogs.com/lsfv/p/6016764.html)

[临时变量的常量性](https://www.3dwindy.com/article/177572)

 临时变量应该在导致临时变量创建的"完整表达式"求值过程的最后一个步骤被析构

> 如果函数返回值是一个对象，要考虑 return 语句的效率。例如   
>   		return String(s1 + s2);
>  	 	这是临时对象的语法，表示“创建一个临时对象并返回它” 。
>
>   不要以为它与“先创建一个局部对象 temp 并返回它的结果”是等价的，如
> 			String temp(s1 + s2);
> 			return temp;
>
>   实质不然，上述代码将发生三件事。首先，temp 对象被创建，同时完成初始化；然后拷贝构造函数把 temp 拷贝到保存返回值的外部存储单元中；最后，temp 在函数结束时被销毁（调用析构函数） 。
>
>   然而“创建一个临时对象并返回它”的过程是不同的，**编译器直接把临时对象创建并初始化在外部存储单元中**，省去了拷贝和析构的化费，提高了效率。
>
>   类似地，我们不要将 
> return int(x + y); // 创建一个临时变量并返回它
> 写成
> int temp = x + y;
> return temp;
>
>   由于内部数据类型如 int,float,double 的变量不存在构造函数与析构函数， 虽然该 “临时变量的语法”不会提高多少效率，但是程序更加简洁易读。

当返回一个变量的时候，我们只想返回这个变量的值，而不是这个变量。

虽然局部变量和临时变量在函数结束后被销毁，但其值仍存放在寄存器 rax 中。

临时对象在表达式完成之后就会被销毁。但如果将一个临时变量返回给一个引用，它的生命周期会延长。

**编译器直接把临时对象创建并初始化在外部存储单元中**。这句话我们可以这样理解：返回局部变量时，我们返回的是一个对象，而对象需要创建和销毁。返回临时变量时，我们直接返回了一个值，



`typename(...)；`

不可以对临时对象 returnby reference，因为，它们返回的必定是个 local object。





## 0x03 Const

### 1. overload by const

``` C++
double imag() const { return im; }
double imag() { im = -1; return im; }
```



### 2. by value vs. by reference(to const)

上面的（to const）表示可选。

pass/return by value or pass/return by reference(to const)

引用（reference）为 C++ 特有。引用的底部就是一个指针。传引用就是传指针，但是它更漂亮。

注意并不是传指针一定比传引用快，例如我们只是传一个char，一个字节，而指针则需要四个字节。

当我们传指针（引用）只是为了效率，要加上 const。



### 3. friend

将一个函数设置为 friend 之后，该函数可以拿到该对象的 private 数据成员，虽然有时我们也可以通过开放的函数看到 private 成员。但这样做的一个好处是减少通过函数调用拿到成员的函数调用开销。



**同个 class 的各个 objects 互为 friends**

```c++
class test
{
    public:
        test(int r) : val(r) {}
    	int func(test &x) { return x.val; }
	private:
    	int val;
};
```

> 在上面的例子中，我们在 func 中直接拿到了另一个对象 x 的私有成员。这是合法的。不需要将 func 声明为 friend。



## 0x04 Operator overload

### 1. this *

所有的成员函数都带有一个隐藏的成员参数 – *this，谁调用这个函数，谁就是 this，这个参数一般放在参数列表的最左侧。当成员调用成员函数的时候就会把它的地址放到 this 中。



### 2. return way

传送者无需知道接收者是否以 reference 形式接受。但是在 C 中，传送者必须知道接收者是否以 pointer 形式接受来决定返回数值还是地址。



### 3. 连串赋值的返回类型

因为赋值运算符（=，+=，-+ …）的优先级顺序自右向左，因为它可能会出现这种形式：

`a += b += c`

`b+=c` 的结果会作为左值交给 `a+=`

此时如果我们重载的 `+=` 返回了一个 void，那事情就严重了。我们应该返回 `b+c` 的类型



对任意 a op b，都是 b 作用 op 于 a 身上，即右侧的对象作用到左侧，C++ 不存在左侧的对象作用在右侧的语法。



当我们重载 output(<<) 运算符的时候，不可以重载为成员函数。

> 如果我们重载为成员函数，那么 class 对象会作为隐式的最左侧的 operand，而 ostream 对象会作为右 operand，此时如果我们调用该函数：
>
> ```c++
> class Complex;
> Complex c(1, 2);
> c << cout;
> ```
>
> 这显然是不合理的，cout 竟然在右侧！





# 二、class with pointer

## 0x01 Big Three 

### 1 what's three?

 copy structure function 

copy assignment struction

destruction function



### 2. how copy 

copy struction

`string s1(s2);`

`string s1 = s2`;

copy assignment

`s1 = s2;`

> 当一个对象第一次出现并利用另一个对象赋值的时候，它会调用拷贝构造函数。
>
> 如果对象已经出现，此时再用另一个对象赋值，它会调用拷贝赋值。

如果你没有在类中提供拷贝构造和拷贝赋值函数，编译器会默认使用自己的那一套函数，就是一个 bit 一个 bit 的copy 过去，也就是说，对于指针类型，它只会把指针 copy 过去，这显然是不合理的，但是对于**非指针**，这是可行的。

因此，对于指针，你必须有你自己的 copy structure function and copy assignment function

拷贝构造函数：构造函数的参数对象是它自己

拷贝赋值函数：重载赋值运算符，参数对象是它自己。当我们使用拷贝赋值时，一定要检查是否是自己赋值给自己。这不仅是效率的问题，还关乎程序的正确性。

`delete [] ptr;` 的 [] 理解为**“关门”**很形象啊。

> 总而言之，用同类对象为该对象初始化 or 赋值的操作就是拷贝初始化 or 赋值。
>
> 但是默认拷贝只是单纯拷贝 val，对于指针来说，它的 val 就是 只想对象的 address。
>
> 这可能（绝大数情况下）并非我们想要的，因此我们需要重载拷贝（初始化 and 赋值）。
>
> 就是这么简单。

### 3. ctor and dtor

构造和析构函数

`complex();` 创建一个临时对象

`complex c1;` 调用默认构造函数创建一个全局/局部对象c1



## 0x02  memory management

### 1. Local object 

also named **auto object**, because it’s memory will be cleaned up automatically by stack.



### 2. new and delete

> new：先分配空间，再调用构造函数

当我们执行语句：`complex *p = new colplex(1,2);`的时候，编译器优化为：

``` c++
void *mem = operator new(sizeof(complex)); // 分配内存
pc = static_cast<complex*>(mem); // 类型转换
pc->complex::complex(1,2);	// call dtor
```

其中，operator new 在内部调用的是 C 的 malloc

complex(1,2) 转换为 complex(pc,1,2);



>  delete：先调用 dtor，再释放内存

当我们执行语句 `delete []pc;` 的时候，编译器会优化为：

``` c++
complex::~colplex(ps);	// 析构函数
operator delete(ps);	// 释放内存
```

operator delete 内部调动 free();



> array new && array delete

![IMG](https://s3.bmp.ovh/imgs/2022/10/20/3d23ec94b22a0b36.png)



如果我们使用了 array new 而没有使用 array delete

那么编译器只会调用一次 dtor。





# 三、Interlude



## 0x01 static

### 1. 静态成员变量

#### 1.1 性质

静态变量是 一个**类的成员变量**，而不是**类的实例的成员变量**。

静态变量调用的时候可以通过对象调用,也可以通过类直接调用。

>  不能在构造对象的构造函数中初始化静态变量。不然的话你就是把静态变量视为类的实例的一个成员变量，这就与它的定义矛盾了。

> 设计模式中的**单例模式**就是只有一个实例对象，这个实例对象是静态的，并且它的构造函数被放在 private 中。

#### 1.2 内存分配

静态就是在程序编译时就分配存储空间，而不是等到运行时才知道，也就是程序加载之后，它的存储空间就已经分配了。这是因为**我们有时候希望即便类的实例销毁了，但我却希望保存它的某些数据**，那么这个时候，静态成员变量就是一个很好的选择。对于被所有类共享的数据可声明成静态变量，它在程序运行前就已经存在了(所以一定要初始化)，它被放到静态存储区，按照出现次序被初始化。 它被所有该类的对象共享，静态变量可以被改变。



#### 1.3 静态成员变量一定要初始化吗

先上答案：绝绝绝大多数情况下，需要初始化！暂时还不知道不不初始化的情况。

我们知道C++类的[静态成员](https://so.csdn.net/so/search?q=静态成员&spm=1001.2101.3001.7020)变量是需要初始化的，但为什么要初始化呢。

其实这句话“静态成员变量是需要初始化的”是有一定问题的，应该说“静态成员变量需要定义”才是准确的，而不是初始化。

两者的区别在于：**初始化是赋一个初始值并且同时分配内存，而定义是只分配[内存](https://so.csdn.net/so/search?q=内存&spm=1001.2101.3001.7020)。**

静态成员变量在类中仅仅是声明，没有定义，所以要在类的外面定义，实际上是给静态成员变量分配内存。

如果我们没有在类的外部定义静态成员变量，那么我们此时就仅仅只是声明了它，如果我们尝试使用它就会导致编译错误。

当然你也可以不定义它，前提是你不会使用它，但既然你不使用它，你为何声明它，并且声明为静态的，这不是搞笑吗？因此，只要在类中声明了一个静态成员变量，你就有很大的必要在类外部定义它（可以不赋值，此时它应该会被默认初始化为0）。



#### 1.4 为什么 static 成员变量一定要在类外初始化

先上答案：**不一定！**

前面提到，我们要在外部定义（注意不一定是初始化，我们可以不给它赋值）静态成员函数。那么我们可以不可以在类中初始化它呢？ 

因为静态成员属于整个类，而不属于某个对象，如果在类内初始化，会导致每个对象都包含该静态成员，这是矛盾的。

《c++primer》里面说在类外定义和初始化是保证[static](https://so.csdn.net/so/search?q=static&spm=1001.2101.3001.7020) 成员变量**只被定义一次**的好方法。 但为什么 static const int 就可以在类里面初始化呢？ 

想起 C 中一个函数里定义一个 static 变量是为了保证只初始化（分配内存并赋值）一次。

那么，是否可以这样理解: static 数据成员在类外定义和初始化是为了**保证只被定义和初始化一次**,这样编译器就不必考虑类的函数里面第一个对 static 变量的 ’=’ 操作是赋值还是初始化了。 static const int 可以在类里面初始化，是因为它既然是 const 的，那程序就不会再去试图初始化了。

什么东西能在类内初始化 ？
能在类中初始化的成员只有一种，那就是**静态常量成员**。

> 因为静态常量成员是常量，不允许修改，这种情况下是否所有的对象共享同一份数据已经不重要了，因为都是同一常量数据，而且如果不允许直接赋值，那么这个常量就没有意义了，直接就是系统默认的值了。
>
> 另外常量在声明时必须同时初始化，否则编译器报错。

> 在我的测试中，发现即使在类中初始化静态成员变量，它也不会算到类的实例的空间当中。例如：
>
> ```c++
> class test
> {
>     public:
>         int y;
>         const int z = 0;
>         static const int x = 3;
> };
> 
> int main()
> {
>     test a;
>     cout << sizeof(a) << end;
> }
> // 4
> ```
>
> Sizeof(a) 的大小是 4 而不是 8，这谁就说明 static 仍然属于类。

> c++11现在支持在类内初始化 const 常量成员了。`const int count =0;`是可以的。

### 2. 静态成员函数

#### 2.1 性质

1. 静态函数没有 this 指针。 （它不属于类的实例）

2. 静态函数只能访问类中的静态成员变量。
3. 静态函数不需要类的实例就可以调用。
4. 静态成员函数不属于任何对象，而是属于类的。



#### 2.2 访问权限

调用**静态函数**效率要高，因为不需要找类实例的数据就可以执行（所以自然不需要标记类实例地址的 this 指针了，不需要穿参），同时由于没有隐含的this指针，自然无法访问类的其他变量，而**只能访问静态变量**了。



#### 2.3 static & virtual & const

(1) static 成员函数不能为 virtual：       

` virtual static void fun() { }//错误`

原因：

1. static成员不属于任何类对象或类实例，故即使给此函数加上virutal也是没意义的。

2. 静态成员函数没有this指针。 



(2) static成员函数不能为const函数：

  `static void fun() const { } //编译错误`  

 `const static void fun() {}  //可以的` 

原因：当声明一个非静态成员函数为 const 时，对 this 指针会有影响。对于一个 Test 类中的 const 修饰的成员函数，this 指针相当于 Test const , 而对于非 const 成员函数，this 指针相当于 Test . 而 static 成员函数没有 this 指针，所以使用const 来修饰 static 成员函数没有意义。 volatile 的道理也是如此。



#### 2.4 为啥要引入静态成员函数

> 假如我们需要将对静态成员变量的一系列操作封装起来作为一个成员函数（注意不是静态的），那么如果我们想要调用该函数，就必须实例化一个类的对象。而对象的实例化与销毁是需要开销的，更何况我们只是想操作静态成员变量，仅此而已，为此我们实例化一个对象，这太浪费了。

我们知道在C++中调用类的成员函数，会传递一个this指针，将类的实例化对象的首地址传递给成员函数，函数操作会根据对象首地址计算其成员变量的地址，然后进行操作。然而静态成员变量并不保存在对象的内存布局中，而是保存在数据段中，因而没有必要用到 this 指针。所以需要一种独立于对象之外对对象静态成员变量的访问操作。在早期 C++ 引入静态成员函数之前，你会看到下面这样奇怪的写法：（假设有类 test，静态成员函数 `sfunc()`）

`((test *)0)->sfunc();`

编译器生成的代码是先将 ecx 清零，然后直接调用 `test::sfunc()` 函数。这种写法的优点就在于不用对进行对象实例化操作从而节省了时间和空间。这种写法最早由贝尔实验室的成员 Jonathan Shopiro 提出，他本人也是引入静态成员函数的主要倡导者。

另外，当我们不存在实例并且也不想额外实例化一个对象的时候，静态成员函数为我们提供了操作静态成员变量的方法，这种场景很多。

但注意，它不能操作非静态成员变量，否则当不存在类实例的时候，我们可以调用非静态成员变量，但此时非静态成员变量尚不存在，显然是不合法的。而静态成员变量存储在数据段中，所以它是存在的。

> 还有如下好处：
>
> 1. 与类实例无关。
>
> 2. 把类名当成namespace用。
>
> 3. 控制该函数的访问权限。
>
> 4. 控制类内的static变量。





## 0x02 namespace

### 1. reference

[里面谈到了全局命名空间](https://codeantenna.com/a/gIRJK7VV7N)

[知乎的讨论](https://blog.51cto.com/aiyc/4998037)

[stack overflow 讨论中文翻译](http://129.226.226.195/post/602.html)

### 2. using namespace std

为什么不要在**头文件**中引入 `using namespace std;`

>  **本来只需要一棵树，你缺砍了整个森林。**

这句解释太形象了，有时候你只是想要 cout，cin，但你却把整个标准库 using 进来，太不合理了。

你引入的太多，发生冲突的概率也就越大。

另外，注意我们是不要在 头文件（.h）而不是源文件（.cpp）里面。因为头文件需要开放给他人使用，而 .cpp 文件并不需要开放给他人。



> 只是千万不要在头文件里面using而已。
>
> 主要是命名冲突，而且冲突以后，编译器给的错误信息基本是妖魔，牛头不对马嘴，难以追查。有些根本编译就没错误和警告，但是就是运行错误。
>
> 跟萌心们解释这些很麻烦，而且效果也不是很好，所以一般直接硬性规定头文件不准出现任何一个using。



比较好的做法应该是自己用一个命名空间封装std，然后using 自己的那个命名空间

### 3. Examples

举个例子1：变量冲突

``` c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

namespace name1
{
    int a = 2;
}

namespace name2
{
    int a = 3;
}

using namespace name1;
using namespace name2;

//int a = 10; // (1)

int main()
{
    printf("a = %d", a);
}
```

在命名空间 name1 和 name2 中我们都定义了一个 a，此时如果把他们全部引入到程序当中，此时再使用 a，就会出现编译错误–命名冲突，但如果我们加入 （1）处语句，程序会成功运行并输出 10，这是因为（1）处的 a 处于**全局命名空间**，他会取代 using 的 a，就像我们 using namespace std 之后，我们再写一个函数 max，它会覆盖标准库的 max 函数。



例子2：函数冲突

``` c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

namespace name1
{
    void f()
    {
        std::cout << "name1";
    }
}

namespace name2
{
    void f()
    {
        std::cout << "name2";
    }
}

using namespace name1;
using namespace name2;

int main()
{
    f();
}
```

``` shell
a.cpp: In function 'int main()':
a.cpp:28:7: error: call of overloaded 'f()' is ambiguous
   28 |     f();
      |       ^
a.cpp:9:10: note: candidate: 'void name1::f()'
    9 |     void f()
      |          ^
a.cpp:17:10: note: candidate: 'void name2::f()'
   17 |     void f()
      |          ^
```



例子3：与 std 发生冲突

``` c++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;
int max = 1;
int main()
{
    std::cout << max << std::endl;
}
```

如果我们将 using namespace std 注释掉，程序正确运行并输出 1，但如果不注释掉，max 会命名冲突。

别问我为什么定义一个 max 变量，只要我不引用 std，不就是正确的吗？

但是我引用了你的头文件，而你的头文件中引用了 std，那我就绷不住了 T.T。

## 0x03 [Virtual function table](https://leehao.me/C-%E8%99%9A%E5%87%BD%E6%95%B0%E8%A1%A8%E5%89%96%E6%9E%90/)

虚函数表就是一个 **指针数组**，数组的元素就是指向一个虚函数的指针。

虚函数表内的条目，即虚函数指针的赋值发生在编译器的编译阶段，也就是说在代码的编译阶段，虚表就可以构造出来了。

## 0x04 typename

[Reference](https://blog.csdn.net/iotflh/article/details/114789270)

# 四、Oritened object

## 0x01 Class Relation

> 面向对象的思想就是 class 和 class 之间有各种各样的联系

### 1. Composition（has a）

复合表示一种 “has-a” 的关系。即，“我中有你”。

在人类的理解中，一个东西的构造肯定是由内而外的，只有里面构造好了，东西才扎实。同样，C++ 面向对象也要和人类的理解相似。

> 复合关系下的构造和析构。
>
> 假设现在有一个 Container 类包含 Component 类。

**构造由内而外：**

Container 的构造函数首先调用 Component 的 default 构造函数，然后才执行自己。

`Container::Container(...) : Component() { ... };`

**析构由外而内：**

Container 的析构函数首先执行自己，然后才调用 Component 的析构函数。

`Container::~Container(...) { .. ~Component() };`

如果包含了多个 class，执行的顺序编译器会帮我们实现。

> 关于这里的执行顺序问题，在下面 [四 .0X02 .1] 中我们把 component 比作房子中的装饰品，把 container 比作房子。这其实对于这里的理解不太好，因为不都是先建好房子，再装饰房子吗？
>
> 我们可以换一种角度，只有把房子装饰好了，才能说这个房子是完整的，而我们要的是一个完整的房子，不是一个空壳。
>
> 因此说，我们先构造 compoent，再构造 container。

### 2. Inheritance（is a）

is a，表示 “是一种” 的关系。

C++ 给我们提供了三种继承方式（Java 只有 public 一种继承方式，因此在 Java 中，public 可以不写）。

> 单纯的继承价值并不大，只有与 “虚函数” 搭配时，继承才能发挥它最大的价值。



> 继承关系下的构造与析构。
>
> 假设现在类 Derived 继承自基类（父类） Base。

**构造由内而外：**

Derived 的构造函数首先调用 Base 的 default 构造函数，然后才执行自己。

`Derived::Derived(...) : Base() { ... };`

**析构由外而内：**

Derived 的析构函数首先执行自己，然后才调用 Base 的析构函数。

`Derived::~Derived( ... ) { ...~Base() };`



> Base class 的 dtor 必须是 virtual，否则会出现 undefined behavior。

[参考本文 Appendix 中的 0x04]

文中提到了：

1. 静态多态性 和 动态多态性：函数重载与模版实现静态多态性。虚函数实现动态多态性。
2. 为什么需要将析构函数设置为虚拟函数：避免内存泄漏。
3. 为什么默认的构造函数不是虚函数：避免不必要的内存浪费。



> 为什么析构函数不设置为虚函数会导致内存泄漏？ —— 动态绑定
>
> **virtual function is the basis for dynamic binding** 

 直接的讲，C++中基类采用virtual虚析构函数是为了防止内存泄漏。具体地说，如果派生类中申请了内存空间，并在其析构函数中对这些内存空间进行释放。 **“假设基类中采用的是非虚析构函数，当删除基类指针指向的派生类对象时就不会触发动态绑定，因而只会调用基类的析构函数，而不会调用派生类的析构函数”**。 那么在这种情况下，派生类中申请的空间就得不到释放从而产生内存泄漏。所以，为了防止这种情况的发生，C++中基类的析构函数应采用virtual虚析构函数。

### 3. Delegation

**composition by reference**

> 也有人把 delegation 和 composition 视为一类，这是术语上的差异，明白其**“精神”**即可，怎么称呼其实无关紧要。delegation 的 “精神” 就是 composition by reference。

> by pointer
>
> The way to pass value is divided into two ways: pass by value and pass by reference. 
>
> We find that there is no pass by pointer. why? Because that is what academia(学术界) calls it. **Although we pass by pointer, we still call it pass by reference.**

通过在 class 中引用其他 class 可以提高程序的弹性，我们的源 class 不需要变化（这意味着我们开放给用户的接口不变），而只需要修改引用的 class 即可。

![img](https://s3.bmp.ovh/imgs/2022/10/20/bcce1dd5388a77e9.png)

Pimpl: pointer to implement 

## 0x02 virtual function



> **virtual function is the basis for dynamic binding**



![img](https://s3.bmp.ovh/imgs/2022/10/20/56c751f20ee7e7c0.png)

this template is not that key template

### 1. Inheritance and composition

当继承和复合都存在的时候，构造和析构的顺序：

这里有两种情况：

1. **class** inherit 继承**class** fa，**class** inherit 包含 **class** com
2. **class** inhert 继承 **class** fa，**class** fa 包含 **class** com：这种情况比较明显，**class** inherit contain **class** fa, **class** fa contain **class** com

所以我们考虑第一种情况：

构造函数执行顺序：fa -> com -> inherit

析构函数执行顺序：herit->com->fa

> 为什么总是先调用父类的呢？

我们可以这样理解：把子类比作一个房子。

子类继承自父类，父类就相当于 “地基”，而子类中的复合类就相当于 “房子中的装饰”，例如地板，墙壁，装饰品等，我们首先得把地基搭好了，然后才能开始建房子，所以说父类肯定在子类之前。

而我们只有把地基搭好，才能装饰我们的房子吧，所以说，复合类也应该在父类之前。

> 不过无需太关心这个，而且这个一般和编译器相关。

### 2. Inheritance and delegation

候捷这里举了一个很经典的例子，来说明继承和委托的典型应用：画图。

在一些画图软件中，我们可能有这样的功能选项：系统给定了你一些图形（圆形，三角等），你可以自己设置这些图形的参数（长宽高，半径等）。

想要用类来表达这个功能，我们首先需要将所有的图形抽象出来，作为一个抽象的 **class** shape，那么 **class** 三角，**class** 圆形等都继承自 **class** shape。然后我们可以把我们的数据传送（委托）给具体的子类（**class** 圆形，**class** 三角形），让它替我们完成数据的处理并描绘出具体的图形。 

这就是通过委托和继承我们将一份数据（图形的参数信息）表达为多种不同的形态（三角形 or 圆形 .. ）。 



### 3. composite

设计模式之 complsite（复合）模式。



### 4. prototype 模式

设计模式之 prototype（原型） 模式



# 勿在浮沙筑高台



# 五、object modle

## 0x01 conversion function

格式：`operator TYPE() const { return XX_TYPE; }`

其中 TYPE 就是我们想要转换成的类型，返回值的类型也是 TYPE，注意**不能有参数和返回类型**。

作为一个转换函数当然不需要参数。没有返回类型是因为返回值的类型和 TYPE 必然相同，C++ 编译器说：“你不用写返回类型，因为 TYPE 已经指出返回类型了，而你写还有可能写错”。

后面通常加上 const，你不加也没错，但是你该加就要加，因为类型转换一般不会改变成员变量。

转换函数和内置类型的转换函数一样，都可以隐式调用。

例如：

``` C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

class Fraction
{
public:
    Fraction(int _num, int _den = 1) : num(_num), den(_den) {}
    
    // 如果是这个 ctor，直接产生解释2
    // explicit Fraction(int _num, int _den = 1) : num(_num), den(_den) {}

    operator double() const  {
        return (double)num / den;
    }
    operator string() const {
        return string(to_string(num) + '/' + to_string(den));
    }
    Fraction operator +(const Fraction &f) {
        return Fraction(num + f.num, den + f.den);
    }
private:
    int num;
    int den;
};

int main()
{
    Fraction f(2, 4);
    double d = 4 + f;
    //double d = f + 4; // ambiguous overload for 'operator+' (operand types are 'Fraction' and 'int')
    cout << d << endl;
        
    // extension
    string s = f;
    cout << s << endl;
    return 0;
}
```

注意转换函数可能会与我们重载的其它运算符发生冲突。在上面的例子中，如果我们以 `double d = f + 4;` 的形式调用就会产生二义性，这是因为 `f + 4` 可以有两种解释形式：

1. 调用 `operator+` 解释 `f+4` 为一个` fraction` 类型（因为在  `fraction` 的构造函数中，我们只需要传入一个参数 `num` 就可以调用构造函数，所以这里的 `4` 调用了构造函数），然后调用转换函数，将 `fraction(f+4)` 转换为 `double` 类型。
2. 调用转换函数将 `f` 解释为 `double`。

> 我们可以通过如下方式调用构造函数：
>
> `Fraction f = 4;`

不过如果我们不适用` f+4` 这种形式就不会出现这种错误（自己骗自己），但是要留意这种二义性错误。

> 应用：
>
> `vector` 的 `[]` 就是通过构造函数实现的

## 0x02 non-explicit-one-argument ctor

one argument 表示该 ctor “至少” 需要一个实参，也就是说可以是多个实参，但是其他都有默认值，我们只需要一个参数就可以调用 ctor。

explicit 是一个 **key word**，它的意思是：明白的，明确的。用它修饰一个函数（例如构造函数），就是在告诉编译器，我就是一个构造函数，不要那我干其他事情，例如用我做类型转换等。

这个 key word 很少用到，并且百分之九十五的情况下看到这个关键字都是在 ctor 的前面，其它在模版里一个很小的地方也会用到。

其实主要就是为了解决 0x01 中的二义性错误，在上面的例子中，之所以产生二义性错误的主要原因是因为我们的语句：`double d = f + 4;` 的 `4` 会调用构造函数，这并不是我们所希望的，我们没有任何显式的表示要让这个 `4` 作为 `Fraction` 类型，而且我们的本意也是说 4 是一个 `double` ，毕竟我们的 `d` 声明的也是一个 `double` 类型。

但是编译器“默默”为我们做了这些事，那么有没有什么办法阻止编译器“替”我们调用构造函数吗？有。

通过为构造函数加上关键字 `explicit`，就可以让 ctor 做他该做的事情（初始化对象），而不是在这里将表达式中的一个变量作为对象并调用构造函数。

这也说明了为啥是 one-argument，因为我们的运算对象通常都是一个变量，你不会一下子 op 两个对象把？`a op (b,c)` 这不太符合直觉。



----



## 0x03 xxx-like classes

### 1.  pointer-like classes

一个像 pointer 的 class，所谓 **smart pointer**。

为什么把一个 class 设计的像 pointer？因为我们像让它比单纯的指针多做一些事情。

一个智能指针里面一定包含一个真的指针，也就是说智能指针就是对普通指针的封装，并提供了更多的功能，但是它应该实现真的指针的功能（解引用 `*` 和 `->`）。

例如：

``` C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

struct Foo
{
    int data;
    void method()
    {
        cout << "you call Foo::method." << endl;
    }
};

template<typename T>
class shared_ptr
{
public:
    shared_ptr(T *obj) : px(obj) {}
    T& operator *() const 
    {
        return *px;
    }
    T* operator ->() const 
    {
        // otherway, call operator* but create new
        //return &(operator *());	
        return px;
    }
private:
    T* px;
    long *pn;
};

int main()
{
    shared_ptr<Foo> p(new Foo); 
    // 把指向shared_ptr类的指针p当做一个指向Foo对象的指针使用即可
    (*p).data = 1024;
    p->method();
    cout << p->data << endl;
    
    Foo *f = new Foo;
    f->method();
    
    return 0;
}
```

在上面的对 std 中的 shared_ptr 简化模拟中，我们可以发现，shared_ptr 内部包含了一个成员变量 - 指针 px，这体现了智能指针其实就是对普通指针的封装。我们还可以在 shared_ptr 中添加一个额外的功能。（但是我并没有。。懒）

然后可以发现我们重载了 `*` 和 `->` 这两个指针必需的运算符

1.  `*` 的返回类型是一个对象的引用（`&`），这我们可以理解。在上面的例子中，我们的语句 `(*p).data = 1024;` 会被转换为 `(*px).data = 1024;`  很合理。
2. `->` 的返回类型是一个指针（`*`）。在上面的例子中，我们的语句 `p->method();` 会被转换为 `pxmethod();` what？这非常不合理，我们想要的是：`px->method();`  但是我们的 `->` 不见了！那我们还怎么调用 `method`。别担心，`->` 有一个性质，就是当它被解释后，它会继续执行执行下去，什么意思呢？在这里的重载函数中，我们实际上是把 `->` 解释没了，但其实，它仍然存在。好像有点麻烦，不过你不需要纠结这些，你只要记住 `->` 被解释之后也会继续作用在它原本指向的对象就行了。

小 tips：

> 在重载 `->` 之后，我们返回 `T*`。不要傻乎乎的把 `T*` 直接在源表达式替换：`(*px)->method();`
>
> 在返回的时候，我们要加上 `*`表示这是一个指针，但是在使用 “指针”（注意不是指针所指向的对象）的时候，我们不能加上 `*`，否则就会被解释为指向所指向的对象。
>
> 指针其实就是对象的地址的另一种表达方式，同理，地址也是表示指针指向对象的一种表达方式。不要刻意区分他们。

应用：

> 迭代器。

例如：

``` C++
#include <iostream>
#include <cstring>
#include <algorithm>

template<class T>
struct list_node {
    void *prev;
    void *next;
    T data;
};

template<class T, class Ref, class Ptr>
struct list_iterator {
  typedef list_iterator<T, Ref, Ptr> self;
  typedef Ptr pointer;
  typedef Ref reference;
  typedef list_node<T>* link;
  link node;
  
  bool operator ==(const self &x) const { return node == x.node; }
  bool operator !=(const self &x) const { return node != x.node; }
  
  reference operator  *() const { return (*node).data; }
  pointer   operator ->() const { return &(operator *()); }
  
  self& operator ++() { // pre: ++ x
    node = (link)((*node).next);
    return *this;
  }
  self operator ++(int)  { // post: x ++ 
    self tmp = *this;
    ++ (*this);
    return tmp;
  }
  self& operator --() { // pre: -- x
    node = (link)((*node).prev);
    return *this;
  }
  self operator --(int) { // post: x --
    self tmp = *this;
    -- (*this);
    return tmp;
  }
};

using namespace std;

int main()
{
    
    return 0;
}
```



### 2. function-like classes

一个像 function 的 class，所谓 **仿函数**，**函数对象（function object）**。

例如：

``` C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

template<class T>
struct indetity {
    // 重载运算符：()
    // 返回类型是：const T&
    // 参数是：T &x
    // 是一个常量成员函数
    const T& 
    operator ()(const T &x) const {
        return x;
    }
};

template<class Pair>
struct select1st {
    const typename Pair::first_type& 
    operator ()(const Pair& x) const {
        return x.first;
    }
};

template<class Pair>
struct select2st {
    const typename Pair::second_type&
    operator ()(const Pair &x) const {
        return x.second;
    }
};

/*Pair域下的两个类型变量
	Pair::first_type
	Pair::second_type
*/

int main()
{
    select1st<pair<double,int>> s1;
    pair<double,int> p = {1, 2};
    cout << s1(p) << endl;
    return 0;
}
```

标准库中的仿函数的模拟实现：

```C++
#include <iostream>

template<class T>
struct minus {
    T operator ()(const T &x, const T &y) { return x - y; }
};

template<class T>
struct equal_to {
    bool operator ()(const T &x, const T &y) { return x == y; }  
};

template<class T>
struct less {
    bool operator ()(const T &x, const T &y) { return x < y; }
};

template<class T>
struct plus {
    T operator ()(const T &x, const T &y) { return x * y; }
};

template<class T, class eleType>
eleType test(eleType a, eleType b, T func)
{
    return func(a,b);
}

int main()
{
    int a = 2, b = 4;
    // std::cout << plus<int>()(a,b) << std::endl;
    // std::cout << minus<int>()(a,b) << std::endl;
    // std::cout << equal_to<int>()(a,b) << std::endl;
    // std::cout << less<int>()(a,b) << std::endl;
    std::cout << test(a, b, plus<int>()) << std::endl;
    std::cout << test(a, b, minus<int>()) << std::endl;
    std::cout << test(a, b, equal_to<int>()) << std::endl;
    std::cout << test(a, b, less<int>()) << std::endl;
    return 0;
}
```



### 3. CLASS_NAME obj();

这个写法是一个经典的错误写法。

下面通过一个例子再深刻理解一下。

``` C++
#include <iostream>
using namespace std;

class test {
public:
    test() { cout << "I am ctor" << endl; }
    void func() { cout << "I am func!" << endl; }
};

int print(int x) { cout << "I am " << x << endl; }

int main()
{
}
```

在上面的代码中，我们定义了一个函数 print 和一个类 test，main 函数内容为空，程序可以通过编译。

（1）现在，如果我们想定义一个类的对象 p：

``` C++
test p();
```

程序依然可以通过编译。

（2）接下来，我们希望通过这个对象 p 调用类中的函数 func：

``` C++
test p(); 
p.func();
```

发现程序报错：

``` SHELL
error: request for member 'func' in 'p', which is of non-class type 'test()'
   26 |     p.func();
```

大致意思就是说我们的 p 不是一个 class 类型，所以它不存在成员函数，咦，我们不是定义了一个 class 对象 p 并且编译通过了吗？怎么现在又报错呢？

这是因为 C++ 兼容 C 的缘故，编译器看到 p 后面跟着一个 () ，就会将 `test p();` 解释为声明一个函数，函数名是 p，返回值类型是 class test。这当然没问题。

但是我们现在通过函数名调用 class 的成员函数 func 就不合理了。

> 小插曲：可以在函数的内部声明函数吗？
>
> 这关系到 **C 语言的标准规范**和**具体编译器的实现**问题。
>
> 　从标准规范来看：
>
> 1. C语言**允许函数里声明函数**，但是不建议这么写，因为不利于多文档的组织。
> 2. C语言**禁止函数里定义函数**，但是个别编译器却没遵循标准。
> 3. **C语言的标准**和**编译器的实现**并不是完全统一，为了可移植性，建议一切都按 C 语言标准中推荐的做法来写。
>
> 综上所述，在函数内声明函数是合法的，并且在链接到另外一个文件中的函数时，也可以在函数内部声明。但是这种语法不应该被我们使用，因为它不利于代码的移植性和规范性。
>
> 例如下面两个文件：
>
> ``` C++
> /* declare.cpp*/
> #include <iostream>
> int print(int x)
> {
>     std::cout << "I am " << x << std::endl;
>     return x;
> }
> 
> /* main.cpp */
> #include <iostream>
> using namespace std;
> int main()
> {
>     int print(int x);
>     cout << "MAIN: " << print(1024) << endl;
>     return 0;
> }
> 
> //  g++ declare.cpp main.cpp -o app
> //  输出：
> //  MAIN: I am 1024
> //  1024
> ```
>
> 经过测试发现可以正常执行。

（3）但是在上一节的例子中，我们的语句 `std::cout << test(a, b, plus<int>()) << std::endl;` 可以正确执行。

 `plus<int>()` 的作用是创建一个 “没有名字的” 临时对象，它会调用默认构造函数并且不会解释为函数。

而且如果我们声明语句： `less<int> tmp();` 依然会解释为一个函数。

（4）如何声明一个类的临时对象？

在上面的说明中，之所以编译器会将` less<int> tmp();` 解释为一个函数，其实主要是因为我们指定了“这个东西的名字”！如果我们不指定“名字”，而是直接： `less<int> ();` 那么编译器就会将这句话看做是声明一个 class less 的临时对象。

综上，创建临时对象的语法：`CLASS[<>] ();`

这就是通过 `()` 调用默认构造函数，注意不要与上面的东西混淆了，他们的区别仅仅在于是否有名字，但是解释后的结果却截然相反，一个是函数，一个是对象。

## 0x04 template

### 1. class template

不多做介绍。

### 2. function template

模版在编译后得到的是一个 “半成品”，它不一定能保证使用。意思就是模版本身编译可以通过，当真正使用时会再编译一次（这次实参的类型已经确定了），这时能不能通过就不确定了。

### 4. member template

成员模板。

向上转型（up-cast）：在将继承关系画成图的时候，我们通常将父类画在图的上方，子类画在图的下方，子类指向父类。因为子类到父类的转换我们形象的称其为 “向上转型”。

如图：（我们通常用用△来表示继承关系，用◇箭头表示 point 关系，point 关系的 ◇ 在指针一方而不是指向的对象一方，继承关系的△在父类乙方）![IMG](https://s3.bmp.ovh/imgs/2022/10/25/90eda9ffa0c8f118.png)

例子：

``` C++
#include <iostream>

using namespace std;

template <class T1, class T2>
class Pair
{
public:
    typedef T1 first_type;
    typedef T2 second_type;
    
    T1 first;
    T2 second;
    
    Pair() : first(T1()), second(T2()) { cout << "ctor1" << endl; }
    Pair(const T1 &a, const T2 &b) : first(a), second(b) { cout << "ctor2" << endl; }
    
    template<class U1, class U2>
    Pair(const Pair<U1, U2> &p) : first(p.first), second(p.second) { cout<< "ctor3" << endl; }
    
    void print() { cout << "print" << endl; }
};

class Base {};
class Derived : public Base {};

int main()
{
    /* test 1 */
    Pair<char, double> p1 = {'A', 23.32};
    cout << p1.first << ' ' << p1.second<< endl;
    
    // Pair<int, int> p2 = p1;
    Pair<int, int> p2 = Pair<char, double>('A', 23.32);
    cout << p2.first << ' ' << p2.second<< endl;
   
    /* test 2 */
    // 向上转型
    Pair<Base, Base> p3;
    Pair<Derived, Derived> p4;

    Pair<Base, Base> p5 = Pair<Derived, Derived>(); // up-cast
    Pair<Base, Base> p6 = p4;   // up-cast
    // Pair<Derived, Derived> p7 = p3;  // cant up-cast
    
    /* test3 */
    // 声明一个临时对象
    Pair<int,int> ().print();

    return 0;
}
```



### 5. specialization

一些名词：

* (full) specialization：模版特化（全特化）

* paritical specialization：模版偏特化（局部特化）

* template template parameter：模版模版参数

模板全特化的例子：

``` C++
#include <iostream>

using namespace std;

template<class key>
class Hash {
public:
    void f(key x) { cout << "key: " << x << endl; }
};

template<>
class Hash<int> {
public:
    void f(int x) { cout << "int: " << x << endl; }
};

template<>
class Hash<char> {
public:
    void f(char ch) { cout << "char: " << ch << endl; }
};

int main()
{
    Hash<int> h1;
    h1.f(56);
    Hash<double> h2;
    h2.f(56);
    Hash<char> h3;
    h3.f(56);
    return 0;
}
```

[reference](https://blog.csdn.net/roufoo/article/details/116267068)

[reference](https://www.jianshu.com/p/e78d9d3bf535)



## 0x05 reference VS. pointer

引用：代表

指针：指向

引用的底层实现就是指针

reference 就是一种漂亮的 pointer

[[1]](https://blog.csdn.net/cnmilan/article/details/7464233?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-7464233-blog-78511858.pc_relevant_3mothn_strategy_and_data_recovery&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-7464233-blog-78511858.pc_relevant_3mothn_strategy_and_data_recovery&utm_relevant_index=1)

[[3]](https://zhuanlan.zhihu.com/p/384591552)

[[2]](https://blog.csdn.net/weixin_33868027/article/details/86379309?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-86379309-blog-7464233.pc_relevant_layerdownloadsortv1&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-86379309-blog-7464233.pc_relevant_layerdownloadsortv1&utm_relevant_index=1)

[[4]](https://blog.csdn.net/coutamg/article/details/61924902?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-61924902-blog-100904422.pc_relevant_layerdownloadsortv1&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

signature签名, const算函数签名，但是引用不算

## 0x06 vptr && vtbl

![img](https://s3.bmp.ovh/imgs/2022/10/25/e00b2a81977cdfb5.png)

虚指针和虚表:放的都是函数指针

在继承中，成员变量直接把内存也继承了过来，而成员函数只是继承了使用权（它不会在生成一份），因此，如果父类有虚函数，子类也一定有（不管你有没有实现）。

``` C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

class A
{
    public:
        virtual void vfunc1() {}
        virtual void vfunc2() {}
        void func1();
        void func2();
    private:
        int data1;
};

class B : public A
{
    public:
        virtual void vfunc1() {}
        void func2();
    private:
        int data2;
};

class C : public B
{
    public:
        virtual void vfunc1() {}
        void func2();
        int data1;
    private:
        int data3;
};

int main()
{
    A a;
    B b;
    C c;
    cout << sizeof(a) << ' ' << sizeof(b) << ' ' << sizeof(c) << endl;

    return 0;
}
// 我们可以发现 sizeof(a)=16，这是因为虚指针占用8字字节并且对其的原因
```

打印虚函数的地址

``` c++
#include <iostream>

using namespace std;
 
class Base
{
public :
    int base_data;
    Base() { base_data = 1; }
    virtual void func1() { cout << "base_func1" << endl; }
    virtual void func2() { cout << "base_func2" << endl; }
    virtual void func3() { cout << "base_func3" << endl; }
};
 
class Derive : public Base
{
public :
    int derive_data;
    Derive() { derive_data = 2; }
    virtual void func1() { cout << "derive_func1" << endl; }
    virtual void func2() { cout << "derive_func2" << endl; }
};
 
typedef void (*func)();
 
int main()
{
    Base base;
    cout << "&base: " << &base << endl;
    cout << "&base.base_data: " << &base.base_data << endl;
    cout << "----------------------------------------" << endl;
 
    Derive derive;
    cout << "&derive: " << &derive << endl;
    cout << "&derive.base_data: " << &derive.base_data << endl;
    cout << "&derive.derive_data: " << &derive.derive_data << endl;
    cout << "----------------------------------------" << endl;
    
    /* 对象和第一个数据之间的地址差了8，这个8应该存放的时虚指针 */
 
    for(int i=0; i<3; i++)
    {
        // &base : base首地址
        // (unsigned long*)&base : base的首地址，vptr的地址
        // (*(unsigned long*)&base) : vptr的内容，即vtable的地址，指向第一个虚函数的slot的地址
        // (unsigned long*)(*(unsigned long*)&base) : vtable的地址，指向第一个虚函数的slot的地址
        // vtbl : 指向虚函数slot的地址
        // *vtbl : 虚函数的地址
        unsigned long* vtbl = (unsigned long*)(*(unsigned long*)&base) + i;
        cout << "slot address: " << vtbl << endl;
        cout << "func address: " << *vtbl << endl;
        func pfunc = (func)*(vtbl);
        pfunc();
    }
    cout << "----------------------------------------" << endl;
 
    for(int i=0; i<3; i++)
    {
        unsigned long* vtbl = (unsigned long*)(*(unsigned long*)&derive) + i;
        cout << "slot address: " << vtbl << endl;
        cout << "func address: " << *vtbl << endl;
        func pfunc = (func)*(vtbl);
        pfunc();
    }
    cout << "----------------------------------------" << endl;
    
    return 0;
}
```



## 0x07 class VS. startct in C++

[C++](http://c.biancheng.net/cplus/) 中保留了C语言的 struct 关键字，并且加以扩充。在C语言中，struct 只能包含成员变量，不能包含成员函数。而在C++中，struct 类似于 class，既可以包含成员变量，又可以包含成员函数。

C++中的 struct 和 class 基本是通用的，唯有几个细节不同：

- 使用 class 时，类中的成员默认都是 private 属性的；而使用 struct 时，结构体中的成员默认都是 public 属性的。
- class 继承默认是 private 继承，而 struct 继承默认是 public 继承。
- class 可以使用模板，而 struct 不能（《[模板、字符串和异常](http://c.biancheng.net/cpp/biancheng/cpp/rumen_14/)》一章会讲解模板）。


C++ 没有抛弃C语言中的 struct 关键字，其意义就在于给C语言程序开发人员有一个归属感，并且能让C++编译器兼容以前用C语言开发出来的项目。

**在编写C++代码时，我强烈建议使用 class 来定义类，而使用 struct 来定义结构体，这样做语义更加明确。**

































## ??? 

up-cast

语法糖

动态绑定的三个条件：指针调用。向上转型。调用虚函数。

string 内部是一个指针

``` C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

int main()
{
    string s = "sddsdasa";
    cout << sizeof(s) << endl;
    return 0;
}
```



`x=::a;`  a 是全局global作用域下的

new：

new ，operator new，array new，placement new

STRUCT

``` c++
#include <stdio.h>
#include <stdlib.h>

void f()
{
    puts("func::f");
}

int g(int x)
{
    puts("self[x]");
    return x;
}

typedef struct
{
    int data;
    void (*p1)();
    int (*p2)(int);
} node;

int main()
{
   node s = {
      .data = 1,
      .p1 = f,
      .p2 = g
   };

    printf("data: %d\n", s.data);
    s.p1();
    printf("p2: %d\n", s.p2(1024));
   
   
   return 0;
}
```





typename / class 历史遗留问题



# 六、 C++ 2.0

C++ standard之演化

C++98 (1.0)

C++03

C++11(2.0)

C++14







































































































