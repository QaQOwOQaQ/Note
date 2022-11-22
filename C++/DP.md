# Design Patterns

# 0x01. 依赖转置原则

## ref1

例如人吃巧克力：

``` C++
public interface IChocolates{}

public class Oreo implements IChocolates {}

public class Dove implements IChocolates {}

public interface Person { void eat( IChocolates chocolates ); }
```



上面的例子中人对巧克力产生了依赖，那人吃的行为依赖其实跟巧克力没有关系，在巧克力出现之前就已经存在了，因此吃的动作依赖的接口应该是人本身内部的概念，这个接口的归属权应该属于人，概念应该为可食用的（edible）。因此人对巧克力的依赖关系应该倒置为巧克力对可食用接口的依赖。这样倒置之后对人来说具有了更好的扩展性，不仅可以吃各种不同的巧克力，还可以吃饼干，米饭，鱼肉等等其它任何可吃的东西。

``` C++
public interface IChocolates extends IEdible{}

public class Oreo implements IChocolates {}

public class Dove implements IChocolates {}

public interface Person { void eat( IEdible edible ); }
```

## ref2

[REF](https://blog.csdn.net/li1358159/article/details/123306664)



## ref3

[REF](https://blog.csdn.net/u012346890/article/details/111034907?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-111034907-blog-123306664.pc_relevant_aa2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-111034907-blog-123306664.pc_relevant_aa2&utm_relevant_index=1)

在例子中，如果我们让上层 dirver 依赖于底层的细节 car，那么就完全错误了。

我们应该让 diver 依赖于抽象的 CAR，让具体的 car 继承自抽象的 CAR。 



# 0x02 装饰模式

## ref1

[原文链接](https://blog.csdn.net/qq_40594696/article/details/107611074)

1.什么是装饰模式

装饰模式是一种用于替代继承的技术。它无需定义子类却可以给对象动态增加职责，使用对象之间的关联关系取代类之间的继承关系。

举个栗子：一张照片，不改变照片本身，给它增加一个相框，使他增加防潮的功能，这就是装饰模式。

装饰模式是一种对象结构型模式，他以对客户透明的方式动态的给一个对象附加上更多的责任，可以在不需要创建更多子类的情况下让对象的功能得以扩展。

 

2.装饰模式的结构

（1）**Component（抽象构件）：**它是具体构件和抽象装饰类的共同父类，声明了在具体构建中实现的业务方法，它的引入可以使客户端以一致的方式处理未被装饰的对象以及装饰之后的对象，实现客户端的透明操作。

（2）**ConcreteComponent（具体构件）：**它是抽象构件的子类，用于定义具体的构建对象，实现了在抽象构建中声明的方法，装饰类可以给它增加额外的职责。

（3）**Decorator（抽象装饰类）：**他也是抽象构件的子类，用于给具体构件增加职责，但是具体职责在其子类中实现。它维护一个指向抽象构件对象的引用，通过该引用可以调用装饰之前构件对象的方法，并通过其子类扩展该方法，以达到装饰的目的。

（4）**ConcreteDecorator（具体装饰类）：**它是抽象装饰类的子类，负责向构件添加新的职责。每一个具体装饰类都定义了一些新的行为，他可以调用在抽象装饰类中定义的方法，并可以增加新的方法用于扩充对象的行为。

## ref2

[refreence](https://blog.csdn.net/zhshulin/article/details/38665187)

###### [更好的 ref – c++ 实例](https://blog.csdn.net/NEXUS666/article/details/115672452)

# 0x03 代理模式

[引用计数](https://blog.csdn.net/starlee/article/details/1647412)

[引用计数](https://blog.csdn.net/u012344185/article/details/114497309)

[jk时间 – 好文](https://time.geekbang.org/column/article/169263)



# 0x04 工厂模式

现实生活中，工厂是负责生产产品的；同样在设计模式中，简单工厂模式我们可以理解为**负责生产对象**的一个类，称为“工厂类”。
