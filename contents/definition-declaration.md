# 定义和声明之间有什么区别？
两者的含义使我无法理解。

## 高票回答：
***声明***（declaration）引入标识符并描述其类型，无论是类型，对象还是函数。声明是 ***编译器*** 接受对该标识符的引用 ***所需的*** 。这些是声明：
```c
extern int bar;
extern int g(int, int);
double f(int, double); // 函数声明可以省略extern
class foo; // 类型声明允许没有extern
```
***定义***（definition）实际上实例化/实现此标识符。这是 ***链接器*** 将引用链接到这些实体 ***所需的*** 内容。这些是与上述声明相对应的定义：
```c
int bar;
int g(int lhs, int rhs) {return lhs*rhs;}
double f(int i, double d) {return i+d;}
class foo {};
```
可以使用定义来代替声明。

可以根据需要随时 *声明* 标识符。因此，以下内容在C和C++中是合法的：
```c
double f(int, double);
double f(int, double);
extern double f(int, double); // 与上面两个相同
extern double f(int, double);
```
但是，它必须只 *定义* 一次。如果您忘记定义已在某处声明和引用的内容，则链接器不知道将引用链接到哪些内容并抱怨符号丢失。如果您多次定义某些内容，则链接器不知道将哪些定义链接引用并抱怨符号重复。

----

由于不断出现关于C++中类 *声明* 和类 *定义* 的争论（在其他问题的回答和评论中），我将在此处粘贴引用自C++标准的内容。在C++03，3.1/2中说：
>声明是一个定义，除非它[...]是一个类名声明[...]。

3.1/3之后举了几个例子。其中包括：
```c
[Example: [...]
struct S { int a; int b; }; // 定义S，S::a，和S::b [...]
struct S; // 声明S
—end example
```
总结一下：C++标准认为`struct x;`是一个 *声明* 并且`struct x {};`是一个 *定义* 。（换句话说， ***“前向声明”用词不当*** ，因为在C++中没有其他形式的类声明。）

感谢[litb（Johannes Schaub）](https://stackoverflow.com/users/34509/johannes-schaub-litb)在他的一个答案中找出了实际章节和语句。

[原文链接](https://stackoverflow.com/questions/1410563/what-is-the-difference-between-a-definition-and-a-declaration)