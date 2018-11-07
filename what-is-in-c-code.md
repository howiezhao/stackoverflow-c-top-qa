# C代码中的":-!!"是什么？
我在[/usr/include/linux/kernel.h](https://github.com/torvalds/linux/blob/ff2d8b19a3a62559afba1c53360c8577a7697714/include/linux/kernel.h#L677-L682)中碰到了这个奇怪的宏代码：
```c
/* Force a compilation error if condition is true, but also produce a
   result (of value 0 and type size_t), so the expression can be used
   e.g. in a structure initializer (or where-ever else comma expressions
   aren't permitted). */
#define BUILD_BUG_ON_ZERO(e) (sizeof(struct { int:-!!(e); }))
#define BUILD_BUG_ON_NULL(e) ((void *)sizeof(struct { int:-!!(e); }))
```
`:-!!`究竟做了什么呢？

## 高票回答：
实际上，这是**一种检查表达式e是否可以被评估为0的方法，如果不是，则构建失败。**

这个宏有点名不副实；它应该更像`BUILD_BUG_OR_ZERO`，而不是`...ON_ZERO`。（这里[偶尔会讨论这是否是一个令人困惑的名字](http://lkml.iu.edu/hypermail/linux/kernel/0703.1/1546.html)。）

你应该像下面这样读这个表达式：
```c
sizeof(struct { int: -!!(e); }))
```
1. `(e)`：计算表达式`e`。
2. `!!(e)`：逻辑否定两次：如果`e == 0`则为`0`；否则为`1`。
3. `-!!(e)`：对第2步中的表达式取负：如果它是`0`则为`0`；否则为`-1`。
4. `struct{int: -!!(0);} --> struct{int: 0;}`：如果它为零，那么我们声明一个结构体，其中包含一个宽度为零的匿名整数位域。一切都很好，我们正常进行。
5. `struct{int: -!!(1);} --> struct{int: -1;}`：另一方面，如果它*不*为零，那么它将是一些负数。声明任何具有负宽度的位域都是编译错误。

因此，我们要么结束于一个结构体中宽度为0的位域，这很好，要么是负宽度的位域，这是一个编译错误。然后我们获取该字段的`sizeof`，因此我们得到一个具有适当宽度的`size_t`（在`e`为零的情况下将为零）。

---
有人问：**为什么不使用`assert`呢？**

[keithmo的答案](https://stackoverflow.com/questions/9229601/what-is-in-c-code/9230305#9230305)在这里得到了很好的响应：
>这些宏实现了编译时测试，而assert()是一个运行时测试。

非常正确。你不会希望在运行时检测到*内核*中可能早先发现的问题！它是操作系统的关键部分。无论在何种程度上，在编译时可以检测到问题，那就再好不过了。

[原文链接](https://stackoverflow.com/questions/9229601/what-is-in-c-code)