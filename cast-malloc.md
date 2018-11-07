# 我是否应该强制类型转换malloc的结果？
在[这个问题](https://stackoverflow.com/questions/571945/getting-a-stack-overflow-exception-when-declaring-a-large-array)中，有人在[评论](https://stackoverflow.com/questions/571945/getting-a-stack-overflow-exception-when-declaring-a-large-array#comment388297_571961)中建议我**不**应该强制类型转换malloc的结果，即
```c
int *sieve = malloc(sizeof(int) * length);
```
比下面更好：
```c
int *sieve = (int *) malloc(sizeof(int) * length);
```
为什么会这样呢？

## 高票回答：
不；你*不能*强制转换结果，因为：
- 这是没必要的，因为在这种情况下，`void *`会自动安全地提升为任何其他指针类型。
- 它增加了代码的混乱，转换不是很容易阅读（特别是如果指针类型很长）。
- 它让你做了重复的事，这通常很糟糕。
- 如果你忘记include `<stdlib.h>`，它可能会隐藏错误。这可能会导致崩溃（或者更糟糕的是，直到稍后在代码的某些完全不同的部分中才导致崩溃）。考虑如果指针和整数的大小不同会发生什么；你通过强制转换隐藏了一个警告，可能会丢失你的返回地址的位数。注意：从C11开始，隐式函数从C中消失，并且这一点不再相关，因为没有自动假设未声明的函数返回`int`。

作为澄清，请注意我说的是“你不能强制转换”，而不是“你不*需要*强制转换”。在我看来，增加强制类型转换就是一个错误，即使你运行正确。这样做没有任何好处，还带来了一堆潜在的风险，包括强制类型转换表明你不知道风险。

还要注意，正如评论区指出的那样，上面谈到的是纯的C，而不是C++。我非常坚信C和C++是不同的语言。

进一步补充，你的代码没必要地重复可能导致错误的类型信息（`int`）。最好取消引用用于存储返回值的指针，将两者“锁定”在一起：
```c
int *sieve = malloc(length * sizeof *sieve);
```
这也将`length`移到前面以增加可见性，并使用`sizeof`删除多余的括号；只有在参数是类型名称时*才需要*它们。许多人似乎不知道（或忽略）这一点，这使得他们的代码更加冗长。请记住：`sizeof`不是函数！:)

---
虽然在某些极少数情况下向前移动`length`*可能*会增加可见性，但是在一般情况下，应该注意将表达式编写为：
```c
int *sieve = malloc(sizeof *sieve * length);
```
由于首先保持`sizeof`，在这种情况下，确保乘法至少使用`size_t`数学。

比较：`malloc(sizeof *sieve * length * width)`与`malloc(length * width * sizeof  *sieve)`当`width`和`length`小于`size_t`时，第二个可能会溢出`length*width`。

[原文链接](https://stackoverflow.com/questions/605845/do-i-cast-the-result-of-malloc)