# const int*，const int * const和int const *之间有什么区别？
我总是混淆如何正确使用`const int*`，`const int * const`和`int const *`。是否有一套规则定义了你能做什么和不能做什么？

我想知道在赋值，函数传递等方面所能做和不能做的所有事情。

## 高票回答：
向后阅读（由[顺时针/螺旋规则](http://c-faq.com/decl/spiral.anderson.html)驱动）：
- `int*` - 指向int的指针
- `int const *` - 指向const int的指针
- `int * const` - 指向int的const指针
- `int const * const` - 指向const int的const指针

现在第一个`const`可以在类型的任何一侧，所以：
- `const int *` == `int const *`
- `const int * const` == `int const * const`

如果你想更疯狂，你可以做这样的事情：
- `int **` - 指向（指向int的指针）的指针
- `int ** const` - 指向（指向int的指针）的const指针
- `int * const *` - 指向（指向int的const指针）的指针
- `int const **` - 指向（指向consnt int的指针）的指针
- `int * const * const` - 指向（指向int的const指针）的const指针
- ...

到此为止请确保你清楚const的含义
```c
const int* foo;
int *const bar; //note, you actually need to set the pointer 
                //here because you can't change it later ;)
```
`foo`是一个指向常量整数的指针变量。这使你可以更改指向的内容，但不能更改指向的值。大多数情况下会看到C风格的字符串，其中有一个指向`const char`的指针。你可以更改指向的字符串，但不能更改这些字符串的内容。当字符串本身位于程序的数据段中且不应更改时，这很重要。

`bar`是指向可以更改的值的常量或固定指针。这就像没有额外语法糖的引用。因为这个事实，通常你会使用一个引用来使用`T* const`指针，除非你需要允许`NULL`指针。