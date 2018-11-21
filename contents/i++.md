# ++i和i++有什么区别？
在C中，使用`++i`和`i++`之间有什么区别，哪个应该在`for`循环的增量块中使用？

## 高票回答：
- `++i`将增加`i`的值，然后返回增加后的值。
  ```c
   i = 1;
   j = ++i;
   (i is 2, j is 2)
  ```
- `i++`将增加`i`的值，但返回`i`在增加之前保持的原始值。
  ```c
   i = 1;
   j = i++;
   (i is 2, j is 1)
  ```
对于`for`循环，两者都可适用。`++i`似乎更常见，也许是因为这是[K＆R](https://en.wikipedia.org/wiki/The_C_Programming_Language)中所使用的。

在任何情况下，遵循指南“喜欢`++i`而不是`i++`”，你就不会出错。

关于`++i`和`i++`的效率有几点评论。在任何非学生项目编译器中，都没有性能差异。您可以通过查看生成的代码来验证这一点，这些代码是相同的。

效率问题很有意思......这是我尝试回答的问题：[C++中的i++和++i之间是否存在性能差异？](https://stackoverflow.com/questions/24886/is-there-a-performance-difference-between-i-and-i-in-c)

正如 *On Freund* 所说，对于C++对象来说它是不同的，因为`operator++()`是一个函数，编译器无法知道如何优化临时对象的创建来保存中间值。

[原文链接](https://stackoverflow.com/questions/24853/what-is-the-difference-between-i-and-i)