# \#include \<filename>和\#include "filename"有什么区别？
在C和C++编程语言中，在`include`语句中使用尖括号和使用引号有什么区别，如下所示？

1. `#include <filename>`
2. `#include "filename"`

## 高票回答：
实际上，区别在于预处理器搜索included文件的位置。

对于`#include <filename>`，预处理器以依赖于实现的方式搜索，通常在编译器/ IDE预先指定的搜索目录中搜索。此方法通常用于include标准库头文件。

对于`#include "filename"`，预处理器首先在与包含该指令的文件相同的目录中进行搜索，然后按照用于`#include <filename>`表单的搜索路径进行搜索。此方法通常用于include程序员定义的头文件。

在GCC的[搜索路径文档](https://gcc.gnu.org/onlinedocs/cpp/Search-Path.html)中对此提供了更完整的描述。

[原文链接](https://stackoverflow.com/questions/21593/what-is-the-difference-between-include-filename-and-include-filename)