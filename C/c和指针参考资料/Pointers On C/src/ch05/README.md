# 第五章：操作符和表达式

## 本章总结及注意点

![1](https://raw.githubusercontent.com/Y-puyu/picture/main/images/20201227173531.png)

![20201216193211](https://raw.githubusercontent.com/Y-puyu/img/main/images/20201216193211.png?token=AK7TQWWRMWAPSML2AOIVGDK73HYJI)

![20201216193244](https://raw.githubusercontent.com/Y-puyu/img/main/images/20201216193244.png?token=AK7TQWUCJLN4INJFJ4UIJSK73HYJ6)

---

## 部分课后习题解答

### 5.8 问题

1. 值为 2.0，类型 `float`

2. 根据编译器而异。第一个函数的调用可能在乘法前，也可能在乘法后。具体可看：

    ![20201216195211](https://raw.githubusercontent.com/Y-puyu/img/main/images/20201216195211.png?token=AK7TQWQFUXNRGMB2YTWEF6C73H2S2)

3. 不清楚。个人一般在算法题中被二进制数相关题目疯狂乱秀！

    参考答案：它们通常用于对设备控制器进行编程，以设置或测试特定的位位置。如果有人有其他好的答案，请发邮件给我
    kar@cs.rit.edu！

4. 速度相当。

5. `if ((year % 4 == 0 && year % 100 != 0) || (year % 400 == 0))`

6. 参考答案:

    ![20201216195149](https://raw.githubusercontent.com/Y-puyu/img/main/images/20201216195149.png?token=AK7TQWX5KUTF7T6OXBCKKNS73H2RI)

7. 经典问题了。`1<=a` 返回真，真即为 1，然后 `1 <= 10` 显然成立。

8. 我觉得根本没必要写成这个样子。参考答案：

    ![20201216195515](https://raw.githubusercontent.com/Y-puyu/img/main/images/20201216195515.png?token=AK7TQWXRDEOLKDL4LTKFWZK73H26E)

9. 不能。如果数组中包含的非零值恰好和为零，则失败。

10. 参考答案：

    ![20201216200458](https://raw.githubusercontent.com/Y-puyu/img/main/images/20201216200458.png?token=AK7TQWRZNDNNFR26P7V64T273H4CU)

11. 参考答案：

    ![20201216200630](https://raw.githubusercontent.com/Y-puyu/img/main/images/20201216200630.png?token=AK7TQWTOX3IYTKA2JUPUMT273H4IO)

12. 声明一个有符号整数，给它赋值，右移它一位，然后打印结果。如果为负，则使用算术移位；正表示逻辑移位。

### 5.9 编程练习

1. 答案给出了 `tolower()` 这个函数，专门处理字母大小写转换问题。也可以循环判断每个字符，若是字母则进行大小写转换即可。见 `demo03.c`。

2. 这不就是凯撒密码的加密方式吗...主要还是要处理后 13 个字母的溢出情况，这里就需要用到取模运算符了。见 `demo04.c`。

3. 其实就是针对二进制位的一个反转。双指针扫一遍就行了。见 `demo05.c`。

4. 没啥意思，我也不知道对不对hh，见 `demo06.c`。

5. 挺不错的一道题。`gets` 接收整行输入字符串，`strcmp` 比较两字符串相等，`strcpy` 进行字符串拷贝更新。尤其需要注意判断 `flag` 排除掉后续重复字符串仍继续打印的错误情况。见 `demo12.c`。

6. 没读懂题...

    参考答案：这个程序利用 `limits.h` 文件来计算一个整数。如果可移植性不是问题的话，这可能是硬编码的。见 `demo07.c`

### 随笔

本章节也比较简单。操作符和表达式在概念上来讲是很好理解的，但是在 `C` 语言中 **整形提升**、**隐式类型转换** 等操作会给报出五花八门的错误。这点在编程实践中会经常遇见。

1. 关于 `%` 操作符

    其不两个操作数都必须是整数。且右操作数不能为 0，和 `\` 一样，除数不能为。

2. 关于字符型计算过程中转整形

    这个就涉及 `C` 语言的隐式类型转换。字符型在计算过程中会被隐式转成整形进行计算，再赋值给字符型变量时，若溢出，则截断处理。

3. 关于左值和右值

    不知道在哪听过这样一个结论来区分左值和右值：**能取地址的就是左值，不能取地址的就是右值。**

4. 关于运算符优先级

    课后习题貌似有充足练习。该加括号的加括号准没错！

5. 关于 `sizeof`

    `sizeof` 首先是个操作符，这是很明确的。`strlen()` 是函数。`sizeof` 用以判断它的操作数的类型长度。经典例子：`sizeof(a = b + 1)` 它没有给 `a` 赋值，只是计算 `a` 类型的长度

6. 关于逗号操作符

    经常采用压行处理算法代码，因为逻辑清晰，且很爽，很帅。其实就是用的逗号操作符，其要求它的操作数都得是表达式，所以常见的 `break, rrturn` 就不能和一些表达式代码一起愉快的压行了。

### 疑问

没啥疑问，很基础的讲解。**编程题最后一道，没读懂题...**

有关负数移位操作有点小蒙...如 `-25 >> 3`，首先 25 的补码是 0001 1001，那么 -25 的补码是 1110 0111。右移 3 位：1111 1100，再将其转换为原码为：1000 0011 + 1 == 1000 0100 = -4，ok，搞定。负数的补码到原码转换，取反加一和减一再取反是一致的。
