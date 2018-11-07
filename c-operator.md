# C语言中的??!??!运算符做了什么？
我看到一行C代码看起来是这样的：
```c
!ErrorHasOccured() ??!??! HandleError();
```
它编译正确，似乎运行正常。看起来它正在检查是否发生了错误，如果有错误，它会处理它。但我不确定它在做什么或者它是如何做的。看起来程序员正试图表达他们对错误的看法。

我从来没有见过`??!??!`在使用任何编程语言之前，我无法在任何地方找到它的文档。（在Google上搜索`??!??!`没有任何帮助）。它到底做了什么以及这段代码示例是如何工作的？

## 高票回答：
`??!`是一个翻译为`|`的[三字符组](https://en.wikipedia.org/wiki/Digraphs_and_trigraphs#C)。所以它其实是：
```c
!ErrorHasOccured() || HandleError();
```
由于短路特性，相当于：
```c
if (ErrorHasOccured())
    HandleError();
```
[Guru of the Week](http://www.gotw.ca/gotw/086.htm)（用于处理C++，但与此相关）中提到了这一点，我选择了这个链接。

[三字符组的可能起源](http://www.archivum.info/comp.std.c/2007-11/00083/Re-extended-operators.html)或着正如@DwB在评论中所指出的，很可能是由于EBCDIC再次变得困难。[这篇](http://wwwprdw.ibm.com:443/developerworks/community/forums/thread.jspa)在IBM developerworks上的讨论似乎支持这一理论。

参考来自ISO/IEC 9899:1999 §5.2.1.1，脚注12（h/t @Random832）：
>三字符序列允许输入未在ISO/IEC 646中描述的不变代码集中定义的字符，ISO/IEC 646是七位US ASCII代码集的子集。

[原文链接](https://stackoverflow.com/questions/7825055/what-does-the-c-operator-do)