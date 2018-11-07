# 为什么C预处理器将单词“linux”解释为常量“1”？
为什么GCC中的C预处理器将单词`linux`（小写字母）解释为常量`1`？
test.c：
```c
#include <stdio.h>
int main(void)
{       
    int linux = 5;
    return 0;
}
```
`$ gcc -E test.c`的结果（在预处理阶段后停止）：
```
....
int main(void)
{
    int 1 = 5;
    return 0;
}
```
当然，这会产生错误。
（顺便说一下：在stdio.h文件中没有`#define linux`）

## 高票回答：
在旧时代（ANSI之前），预定义符号（如`unix`和`vax`）是一种允许代码在编译时检测正在编译的系统的方法。当时没有官方语言标准（超出第一版K＆R背面的参考资料），任何复杂性的C代码通常是`#ifdef`的复杂迷宫，以允许系统之间的差异。这些宏定义通常由编译器本身设置，而不是在库的头文件中定义。由于没有关于实现可以使用哪些标识符以及为程序员保留哪些标识符的真正规则，编译器编写者可以随意使用像`unix`这样的简单名称，并假设程序员只是为了自己的目的而避免使用这些名称。

1989 ANSI C标准引入了限制实现可以合法预定义的符号的规则。编译器预定义的宏只能使用以两个下划线开头的名称，或者下划线后跟一个大写字母，这样程序员就可以自由地使用不匹配该模式但未在标准库中使用的标识符。

因此，任何预定义`unix`或`linux`的编译器都是不符合的，因为它无法编译使用类似`int linux = 5;`的完全合法的代码。

碰巧的是，默认情况下gcc是不符合的 - 但是可以使用正确的命令行选项让它符合（相当不错）：
```
gcc -std=c90 -pedantic ... # or -std=c89 or -ansi
gcc -std=c99 -pedantic
gcc -std=c11 -pedantic
```
有关详细信息，请参阅[gcc手册](https://gcc.gnu.org/onlinedocs/gcc-4.8.1/gcc/Standards.html)。

gcc将在未来版本中逐步淘汰这些定义，因此你不应编写依赖于它们的代码。如果你的程序需要知道它是否正在为Linux目标编译，它可以检查是否定义了`__linux__`（假设你正在使用gcc或与之兼容的编译器）。有关更多信息，请参阅[GNU C预处理器手册](https://gcc.gnu.org/onlinedocs/cpp/System-specific-Predefined-Macros.html)。

一个基本上无关紧要的话题：1987年[国际C语言混乱代码大赛](http://www.ioccc.org/)的“Best One Liner”获奖者David Korn（他是Korn Shell的作者）利用了预定义的`unix`宏：
```c
main() { printf(&unix["\021%six\012\0"],(unix)["have"]+"fun"-0x60);}
```
它会打印“unix”，但原因与宏名称的拼写完全无关。

[原文链接](https://stackoverflow.com/questions/19210935/why-does-the-c-preprocessor-interpret-the-word-linux-as-the-constant-1)