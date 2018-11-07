# C中的“static”是什么意思？
我在C代码中的不同地方看到过单词`static`；这就像C＃中的静态函数/类（实现跨对象共享）吗？

## 高票回答：
1. 函数内的静态变量在调用之间保持其值。
2. 静态全局变量或函数仅在其被声明的文件中被“看到”

（1）如果你是新手，这是一个更外围的话题，所以这有一个例子：
```c
#include <stdio.h>

void foo()
{
    int a = 10;
    static int sa = 10;

    a += 5;
    sa += 5;

    printf("a = %d, sa = %d\n", a, sa);
}


int main()
{
    int i;

    for (i = 0; i < 10; ++i)
        foo();
}
```
这将打印：
```c
a = 15, sa = 15
a = 15, sa = 20
a = 15, sa = 25
a = 15, sa = 30
a = 15, sa = 35
a = 15, sa = 40
a = 15, sa = 45
a = 15, sa = 50
a = 15, sa = 55
a = 15, sa = 60
```
这对于函数需要在调用之间保持某种状态并且你不想使用全局变量的情况下很有用。但要注意，应该非常谨慎地使用此功能 - 它会使你的代码不是线程安全的，且更难理解。

（2）广泛用作“访问控制”功能。如果你有一个.c文件实现了某些功能，它通常只向用户公开一些“公共”函数。其余的函数应该是`static`的，这样用户就无法访问它们。这就是封装，一个很好的实践。

引用自[维基百科](https://en.wikipedia.org/wiki/Static_variable)：
>在C编程语言中，static与全局变量和函数一起使用，以将其范围设置为文件内。在局部变量中，static用于将变量存储在静态分配的内存中，而不是自动分配的内存中。虽然该语言没有规定任何类型的内存的实现，但是静态分配的内存通常在编译时保留在程序的数据段中，而自动分配的内存通常被实现为临时的调用栈。

有关详细信息，请参见[此处](http://os.camden.rutgers.edu/c_resources/c_manual/C/CONCEPT/storage_class.html)和[此处](http://os.camden.rutgers.edu/c_resources/c_manual/C/SYNTAX/static.htm)。

并回答你的第二个问题，它和C＃中的不一样。

但是，在C++中，`static`也用于定义类属性（在同一个类的所有对象之间共享）和方法。在C中没有类，所以这个功能是无关紧要的。

[原文链接](https://stackoverflow.com/questions/572547/what-does-static-mean-in-c)